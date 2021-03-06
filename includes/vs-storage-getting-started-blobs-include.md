##### Création d'un conteneur
De la même manière que les fichiers résident dans des dossiers, le stockage des objets blob s'effectue dans des conteneurs. Vous pouvez utiliser un objet **CloudBlobClient** pour référencer un conteneur existant, ou appeler la méthode CreateCloudBlobClient() pour créer un conteneur.

Le code suivant montre comment créer un conteneur de stockage d'objets blob. Ce code commence par créer un objet **BlobClient** vous permettant d'accéder aux fonctions de l'objet, notamment pour créer un conteneur de stockage. Il tente ensuite de référencer le conteneur de stockage nommé « mycontainer ». S'il ne trouve aucun conteneur ainsi nommé, il en crée un.

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “mycontainer.”
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // If “mycontainer” doesn’t exist, create it.
    container.CreateIfNotExists();

Le nouveau conteneur est privé par défaut et vous devez indiquer votre clé d’accès de stockage pour télécharger des objets blob depuis ce conteneur. Si vous voulez que les fichiers du conteneur soient publics, vous pouvez configurer le conteneur en utilisant le code suivant :

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
        BlobContainerPublicAccessType.Blob }); 


**REMARQUE :** ajoutez ce bloc de code avant le code dans les sections suivantes.

##### Charger un blob dans un conteneur.
Pour télécharger un fichier blob vers un conteneur, obtenez la référence du conteneur et utilisez-la pour celle de l'objet blob. Lorsque vous disposez d'une référence d'objet blob, vous pouvez télécharger un flux de données vers cet objet en appelant la méthode **UploadFromStream()**. Si l'objet blob n'existe pas, cette opération entraîne sa création. S'il existe, il est remplacé. L’exemple suivant illustre le téléchargement d’un objet blob dans un conteneur en partant du principe que le conteneur existe déjà.

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

##### Création d'une liste d'objets blob dans un conteneur
Pour créer une liste d’objets blob dans un conteneur, commencez par obtenir une référence pointant vers un conteneur. Vous pouvez ensuite appeler la méthode **ListBlobs()** du conteneur pour récupérer les objets blob et/ou les répertoires qu'il contient. Pour accéder aux nombreuses propriétés et méthodes d’une **IListBlobItem** renvoyée, vous devez l’appeler vers un objet **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. Si vous ne savez pas de quel type d'objet blob il s'agit, vous pouvez lancer une vérification de type pour déterminer la cible de l'appel. Le code suivant illustre l'extraction et la génération de l'URI de chaque élément du conteneur « photos ».

    // Get a reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop through items in the container and output the length and URI for each 
    // item.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

D'autres méthodes permettent de répertorier le contenu d'un conteneur d'objets blob. Pour plus d'informations, consultez la page [Utilisation du stockage d'objets blob à partir de .NET](../articles/storage/storage-dotnet-how-to-use-blobs.md#list-blob).

##### Téléchargement d'un objet blob
Pour télécharger un objet blob, commencez par en obtenir la référence, puis appelez la méthode DownloadToStream(). L'exemple suivant utilise la méthode DownloadToStream() pour transférer le contenu des objets blob vers un objet de flux que vous pouvez enregistrer sous forme de fichier local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Il existe plusieurs façons d'enregistrer les objets blob sous forme de fichiers. Pour plus d'informations, consultez la page [Utilisation du stockage d'objets blob à partir de .NET](../articles/storage/storage-dotnet-how-to-use-blobs.md#download-blobs).

##### Suppression d'un objet blob
Pour supprimer un objet blob, tout d'abord obtenir une référence à l'objet blob, puis appelez la méthode Delete() dessus.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();

[En savoir plus sur Azure Storage](http://azure.microsoft.com/documentation/services/storage/) Voir aussi [Consultation et gestion des ressources de stockage avec l'Explorateur de serveurs](http://msdn.microsoft.com/library/azure/ff683677.aspx).

<!---HONumber=62-->