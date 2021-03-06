---
title: Shared Access Signatures overview | Microsoft Docs
description: What are Shared Access Signatures, how do they work, and how to use them from Node, PHP, and C#.
services: service-bus
documentationcenter: na
author: djrosanova
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2016
ms.author: darosa;sethm

---
# <a name="shared-access-signatures"></a>Shared Access Signatures
*Shared Access Signatures* (SAS) are the primary security mechanism for Service Bus, including Event Hubs, brokered messaging (queues and topics), and relayed messaging. This article discusses Shared Access Signatures, how they work, and how to use them in a platform-agnostic way.

## <a name="overview-of-sas"></a>Overview of SAS
Shared Access Signatures are an authentication mechanism based on SHA-256 secure hashes or URIs. SAS is an extremely powerful mechanism that is used by all Service Bus services. In actual use, SAS has two components: a *Shared Access Policy* and a *Shared Access Signature* (often called a *token*).

You can find more detailed information about Shared Access Signatures with Service Bus in [Shared Access Signature authentication with Service Bus](service-bus-shared-access-signature-authentication.md).

## <a name="shared-access-policy"></a>Shared Access Policy
An important thing to understand about SAS is that it all starts with a policy. For every policy, you decide on three pieces of information: **name**, **scope**, and **permissions**. The **name** is just that; a unique name within that scope. The scope is easy enough: it's the URI of the resource in question. For a Service Bus namespace, the scope is the fully qualified domain name (FQDN), such as `https://<yournamespace>.servicebus.windows.net/`.

The available permissions for a policy are largely self-explanatory:

* Send
* Listen
* Manage

After you create the policy, it is assigned a *Primary Key* and a *Secondary Key*. These are cryptographically strong keys. Don't lose them or leak them - they'll always be available in the [Azure portal][Azure portal]. You can use either of the generated keys, and you can regenerate them at any time. However, if you regenerate or change the primary key in the policy, any Shared Access Signatures created from it will be invalidated.

When you create a Service Bus namespace, a policy is automatically created for the entire namespace called **RootManageSharedAccessKey**, and this policy has all permissions. You don't log on as **root**, so don't use this policy unless there's a really good reason. You can create additional policies in the **Configure** tab for the namespace in the portal. It's important to note that a single tree level in Service Bus (namespace, queue, Event Hub, etc.) can only have up to 12 policies attached to it.

## <a name="shared-access-signature-(token)"></a>Shared Access Signature (token)
The policy itself is not the access token for Service Bus. It is the object from which the access token is generated - using either the primary or secondary key. The token is generated by carefully crafting a string in the following format:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Where `signature-string` is the SHA-256 hash of the scope of the token (**scope** as described in the previous section) with a CRLF appended and an expiry time (in seconds since the epoch: `00:00:00 UTC` on 1 January 1970).

The hash looks similar to the following pseudo code and returns 32 bytes.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

The non-hashed values are in the **SharedAccessSignature** string so that the recipient can compute the hash with the same parameters, to be sure that it returns the same result. The URI specifies the scope, and the key name identifies the policy to be used to compute the hash. This is important from a security standpoint. If the signature doesn't match that which the recipient (Service Bus) calculates, then access is denied. At this point you can be sure that the sender had access to the key and should be granted the rights specified in the policy.

## <a name="generating-a-signature-from-a-policy"></a>Generating a signature from a policy
How do you actually do this in code? Let's take a look at a few of these.

### <a name="nodejs"></a>NodeJS
```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### <a name="java"></a>Java
```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### <a name="php"></a>PHP
```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time();  
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256',             
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
return $token; 
}
```

### <a name="c&#35;"></a>C&#35;
```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="using-the-shared-access-signature-(at-http-level)"></a>Using the Shared Access Signature (at HTTP level)
Now that you know how to create Shared Access Signatures for any entities in Service Bus, you are ready to perform an HTTP POST:

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

Remember, this works for everything. You can create SAS for a queue, topic, subscription, Event Hub, or relay. If you use per-publisher identity for Event Hubs, you simply append `/publishers/< publisherid>`.

If you give a sender or client a SAS token, they don't have the key directly, and they cannot reverse the hash to obtain it. As such, you have control over what they can access, and for how long. An important thing to remember is that if you change the primary key in the policy, any Shared Access Signatures created from it will be invalidated.

## <a name="using-the-shared-access-signature-(at-amqp-level)"></a>Using the Shared Access Signature (at AMQP level)
In the previous section, you saw how to use the SAS token with an HTTP POST request for sending data to the Service Bus. As you know, you can access Service Bus using the Advanced Message Queuing Protocol (AMQP) that is the preferred protocol to use for performance reasons, in many scenarios. The SAS token usage with AMQP is described in the document [AMQP Claim-Based Security Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) that is in working draft since 2013 but well-supported by Azure today.

Before starting to send data to Service Bus, the publisher must send the SAS token inside an AMQP message to a well-defined AMQP node named **$cbs** (you can see it as a "special" queue used by the service to acquire and validate all the SAS tokens). The publisher must specify the **ReplyTo** field inside the AMQP message; this is the node in which the service replies to the publisher with the result of the token validation (a simple request/reply pattern between publisher and service). This reply node is created "on the fly," speaking about "dynamic creation of remote node" as described by the AMQP 1.0 specification. After checking that the SAS token is valid, the publisher can go forward and start to send data to the service.

The following steps show how to send the SAS token with AMQP protocol using the [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) library. This is useful if you can't use the official Service Bus SDK (for example on WinRT, .Net Compact Framework, .Net Micro Framework and Mono) developing in C\#. Of course, this library is useful to help understand how claims-based security works at the AMQP level, as you saw how it works at the HTTP level (with an HTTP POST request and the SAS token sent inside the "Authorization" header). If you don't need such deep knowledge about AMQP, you can use the official Service Bus SDK with .Net Framework applications, which will do it for you.

### <a name="c&#35;"></a>C&#35;
```
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

The `PutCbsToken()` method receives the *connection* (AMQP connection class instance as provided by the [AMQP .NET Lite library](https://github.com/Azure/amqpnetlite)) that represents the TCP connection to the service and the *sasToken* parameter that is the SAS token to send. 

> [!NOTE]
> It's important that the connection is created with **SASL authentication mechanism set to EXTERNAL** (and not the default PLAIN with username and password used when you don't need to send the SAS token).
> 
> 

Next, the publisher creates two AMQP links for sending the SAS token and receiving the reply (the token validation result) from the service.

The AMQP message contains a set of properties, and more information than a simple message. The SAS token is the body of the message (using its constructor). The **"ReplyTo"** property is set to the node name for receiving the validation result on the receiver link (you can change its name if you want, and it will be created dynamically by the service). The last three application/custom properties are used by the service to indicate what kind of operation it has to execute. As described by the CBS draft specification, they must be the **operation name** ("put-token"), the **type of token** (in this case, a "servicebus.windows.net:sastoken"), and the **"name" of the audience** to which the token applies (the entire entity).

After sending the SAS token on the sender link, the publisher must read the reply on the receiver link. The reply is a simple AMQP message with an application property named **"status-code"** that can contain the same values as an HTTP status code. 

## <a name="next-steps"></a>Next steps
See the [Service Bus REST API reference](https://msdn.microsoft.com/library/azure/hh780717.aspx) for more information about what you can do with these SAS tokens.

For more information about Service Bus authentication, see [Service Bus authentication and authorization](service-bus-authentication-and-authorization.md). 

More examples of SAS in C# and Java Script are in [this blog post](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx).

[Azure portal]: https://portal.azure.com


<!--HONumber=Oct16_HO2-->


