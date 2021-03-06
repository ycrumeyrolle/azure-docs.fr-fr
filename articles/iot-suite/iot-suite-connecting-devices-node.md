---
title: "Connexion d’un appareil à l’aide de Node.js | Microsoft Docs"
description: "Explique comment connecter un périphérique à la solution de surveillance à distance Azure IoT Suite préconfigurée à l’aide d’une application écrite dans Node.js."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 58c0d6590e2fd1684ebeee473029ca4544e223f6


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Connexion de votre appareil à la solution préconfigurée de surveillance à distance (Node.js)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>Générer et exécuter l'exemple de solution node.js
1. Pour cloner le dépôt GitHub des *Kits de développement logiciel (SDK) Azure IoT* et installer le *Kit de développement logiciel (SDK) d’appareil Microsoft Azure IoT pour Node.js* dans votre environnement de bureau, suivez les instructions contenues dans [Préparation de votre environnement de développement][lnk-github-prepare].
2. À partir de votre copie locale du dépôt [azure-iot-sdks][lnk-github-repo], copiez les deux fichiers suivants, contenus dans le dossier noeud/appareil/exemples, dans un dossier sur votre appareil :
   
   * packages.json
   * remote_monitoring.js
3. Ouvrez le fichier remote_monitoring.js et recherchez la variable suivante :
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
4. Remplacez **[chaîne de connexion d’un périphérique de IoT Hub]** par votre chaîne de connexion de périphérique. Vous pouvez trouver les valeurs pour le nom d’hôte IoT Hub, l’ID de périphérique et la clé de périphérique sur le tableau de bord de solution de surveillance à distance. La chaîne de connexion du périphérique suit ce format :
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Si votre nom d’hôte IoT Hub est **contoso** et votre ID d’appareil **mydevice**, votre chaîne de connexion ressemble à la suivante :
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
5. Enregistrez le fichier. Exécutez les commandes suivantes dans une invite de commandes dans le dossier contenant ces fichiers pour installer les packages nécessaires, puis exécutez l'exemple d'application :
   
    ```
    npm install --save
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md



<!--HONumber=Nov16_HO5-->


