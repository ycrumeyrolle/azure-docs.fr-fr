---
title: "Déplacer des données vers et depuis un stockage Azure Blob | Microsoft Docs"
description: "Déplacer des données vers et depuis un stockage Azure Blob"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2016
ms.author: bradsev;sachouks
translationtype: Human Translation
ms.sourcegitcommit: a67f2e77d3bc7da35a03b68d7f32fd3a2a42bfcd
ms.openlocfilehash: d19ab9fb96319203b2d90e1d203a26f6453db950


---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Déplacer des données vers et depuis un stockage Azure Blob
[!INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

La méthode la mieux adaptée à vos besoins dépend de votre scénario. L’article sur les [Scénarios d’analyses avancées dans Azure Machine Learning](machine-learning-data-science-plan-sample-scenarios.md) vous aide à déterminer les ressources dont vous avez besoin pour les différents flux de travail utilisés dans le processus d’analyse avancée.

> [!NOTE]
> Pour une présentation complète du stockage d’objets blob Azure, consultez les articles [Fonctionnalités de base des objets blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) et [Service Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

Comme alternative, vous pouvez utiliser [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) pour : 

* créer et planifier un pipeline qui télécharge les données à partir du stockage d’objets blob Azure, 
* transférer ces données vers un service web Azure Machine Learning publié, 
* recevoir les résultats d’analyse prédictifs, et 
* télécharger les résultats dans le stockage. 

Pour plus d’informations, consultez la page [Créer des pipelines prédictifs à l’aide d’Azure Data Factory et Azure Machine Learning](../data-factory/data-factory-azure-ml-batch-execution-activity.md).

## <a name="prerequisites"></a>Composants requis
Ce document suppose que vous disposez d’un abonnement Azure, d’un compte de stockage et de la clé de stockage correspondante pour ce compte. Avant de charger ou télécharger des données, vous devez connaître le nom et la clé de votre compte Azure Storage.

* Pour configurer un abonnement Azure, consultez [Essai gratuit pendant un mois](https://azure.microsoft.com/pricing/free-trial/).
* Pour obtenir des instructions sur la création d’un compte de stockage et pour obtenir des informations de compte et de clé, consultez [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).




<!--HONumber=Nov16_HO3-->


