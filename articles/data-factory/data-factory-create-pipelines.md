---
title: Créer et planifier des pipelines et des activités à la chaîne dans Data Factory | Microsoft Docs
description: Apprenez à créer un pipeline de données dans Azure Data Factory pour déplacer et transformer des données. Créez un flux de travail piloté par les données pour produire des informations prêtes à l’emploi.
keywords: pipeline de données, flux de travail piloté par les données
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: shlo

---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines et activités dans Azure Data Factory
Cet article vous aide à comprendre les pipelines et les activités dans Azure Data Factory, et à les utiliser dans l’optique de créer des workflows pilotés par les données de bout en bout pour vos scénarios de déplacement des données et de traitement des données.  

> [!NOTE]
> Cet article suppose que vous avez parcouru les articles [Présentation du service Azure Data Factory](data-factory-introduction.md). Si vous n’avez pas d’expérience pratique de création de fabriques de données, le didacticiel [Créer votre première fabrique de données](data-factory-build-your-first-pipeline.md) vous aidera à mieux comprendre cet article.  
> 
> 

## <a name="what-is-a-data-pipeline?"></a>Qu’est-ce qu’un pipeline de données ?
Le **pipeline** est un ensemble **d’activités** reliées logiquement. Il est utilisé pour regrouper des activités dans une unité qui exécute une tâche. Pour mieux comprendre les pipelines, vous devez d'abord comprendre une activité. 

## <a name="what-is-an-activity?"></a>Qu'est-ce qu'une activité ?
Les activités définissent les actions à effectuer sur les données. Chaque activité accepte ou non des [jeux de données](data-factory-create-datasets.md) en tant qu'entrées et produit au moins un jeu de données en tant que sortie. 

Par exemple, vous pouvez utiliser une activité de copie pour orchestrer la copie de données d’une banque de données vers une autre. De même, vous pouvez utiliser une activité HDInsight Hive pour exécuter une requête Hive sur un cluster Azure HDInsight afin de transformer vos données. Azure Data Factory fournit un large éventail d’activités de [transformation des données](data-factory-data-transformation-activities.md) et de [déplacement des données](data-factory-data-movement-activities.md). Vous pouvez également choisir de créer une activité .NET personnalisée pour exécuter votre propre code. 

## <a name="sample-copy-pipeline"></a>Exemple de pipeline de copie
Dans l’exemple de pipeline suivant, il existe une activité de type **Copy** in the **d’activités** . Dans cet exemple, [l’activité de copie](data-factory-data-movement-activities.md) copie des données d’un stockage blob Azure vers une base de données SQL Azure. 

    {
      "name": "CopyPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2016-07-12T00:00:00Z",
        "end": "2016-07-13T00:00:00Z"
      }
    } 

Notez les points suivants :

* Dans la section des activités, il existe une seule activité dont le **type** a la valeur **Copy**.
* L’entrée de l’activité est définie sur **InputDataset** et la sortie sur **OutputDataset**.
* Dans la section **typeProperties**, **BlobSource** est spécifié en tant que type de source et **SqlSink** en tant que type de récepteur.

Pour une procédure pas à pas complète de création de ce pipeline, consultez [Didacticiel : Copie de données de stockage blob vers une SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

## <a name="sample-transformation-pipeline"></a>Exemple de pipeline de transformation
Dans l’exemple de pipeline suivant, il existe une activité de type **HDInsightHive** in the **d’activités** . Dans cet exemple, [l’activité Hive HDInsight](data-factory-hive-activity.md) transforme les données d’un stockage blob Azure en exécutant un fichier de script Hive sur un cluster Hadoop Azure HDInsight. 

    {
        "name": "TransformPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }

Notez les points suivants : 

* Dans la section des activités, il existe une seule activité dont le **type** a la valeur **HDInsightHive**.
* Le fichier de script Hive, **partitionweblogs.hql**, est stocké dans le compte de stockage Azure (spécifié par le service scriptLinkedService, appelé **AzureStorageLinkedService**) et dans le dossier **script** du conteneur **adfgetstarted**.
* La section **defines** est utilisée pour spécifier les paramètres d’exécution passés au script Hive comme valeurs de configuration Hive (par exemple ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

Pour une procédure pas à pas complète de création de ce pipeline, consultez [Didacticiel : Générer votre premier pipeline pour traiter les données à l’aide du cluster Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="chaining-activities"></a>Chaînage des activités
Si vous avez plusieurs activités dans un pipeline et que la sortie d’une activité n’est pas une entrée dans une autre activité, les activités peuvent s’exécuter en parallèle si les segments de données d’entrée pour les activités sont prêts. 

Vous pouvez chaîner deux activités en utilisant le jeu de données de sortie d’une activité en tant que jeu de données d’entrée de l’autre activité. Les activités peuvent être dans le même pipeline ou dans des pipelines différents. La seconde activité s’exécute uniquement quand la première se termine correctement. 

Considérez l’exemple suivant :

1. Le pipeline P1 contient l’activité A1 nécessitant le jeu de données d’entrée externe D1 et produit le jeu de données de **sortie** **D2**.
2. Le pipeline P2 contient l’activité A2 nécessitant le jeu de données **d’entrée** **D2** et produit le jeu de données de sortie D3.

Dans ce scénario, l’activité A1 s’exécute lorsque les données externes seront disponibles et que la fréquence de disponibilité planifiée sera atteinte.  L’activité A2 s’exécute lorsque les tranches planifiées de D2 seront disponibles et que la fréquence de disponibilité planifiée sera atteinte. S’il existe une erreur dans l’une des tranches du jeu de données D2, A2 n’est pas exécutée pour cette tranche jusqu’à ce que celle-ci devienne disponible.

Vue schématique :

![Chaînage des activités dans deux pipelines](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Vue schématique avec les deux activités dans le même pipeline : 

![Chaînage des activités dans le même pipeline](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Pour plus d’informations, consultez [Planification et exécution](#chaining-activities). 

## <a name="scheduling-and-execution"></a>Planification et exécution
Vous savez désormais en quoi consistent les pipelines et les activités. Vous avez également observé comment ils sont définis et vous avez obtenu un aperçu des activités dans Azure Data Factory. À présent, examinons leur exécution. 

Un pipeline est actif uniquement entre son heure de début et son heure de fin. Il n'est pas exécuté avant l'heure de début, ni après l'heure de fin. Lorsque le pipeline est suspendu, il n’est pas exécuté, quelle que soit son heure de début et de fin. Pour qu'un pipeline soit exécuté, il ne doit pas être suspendu. En fait, ce n'est pas le pipeline qui est exécuté. Ce sont les activités dans le pipeline qui sont exécutées. Toutefois, cela se produit dans le contexte global du pipeline. 

Consultez [Planification et exécution](data-factory-scheduling-and-execution.md) pour comprendre le fonctionnement de planification et de l’exécution dans Azure Data Factory.

## <a name="create-pipelines"></a>Créer des pipelines
Azure Data Factory fournit plusieurs mécanismes pour créer et déployer des pipelines (qui à leur tour contiennent une ou plusieurs activités). 

### <a name="using-azure-portal"></a>En passant par le portail Azure
Vous pouvez utiliser l’éditeur Data Factory dans le portail Azure pour créer un pipeline. Pour connaître la procédure de bout en bout, consultez [Prise en main d’Azure Data Factory (Data Factory Editor)](data-factory-build-your-first-pipeline-using-editor.md) . 

### <a name="using-visual-studio"></a>Utilisation de Visual Studio
Vous pouvez utiliser Visual Studio pour créer et déployer des pipelines dans Azure Data Factory. Pour connaître la procédure de bout en bout, consultez [Prise en main d’Azure Data Factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) . 

### <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell
Vous pouvez utiliser Azure PowerShell pour créer des pipelines dans Azure Data Factory. Par exemple, vous avez défini le pipeline JSON dans un fichier sous c:\DPWikisample.json. Vous pouvez le télécharger dans votre instance Azure Data Factory comme indiqué dans l’exemple suivant :

    New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

Pour connaître la procédure de bout en bout de création d’une fabrique de données avec un pipeline, voir [Créer votre première fabrique de données Azure à l’aide d’Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) . 

### <a name="using-.net-sdk"></a>Utilisation du kit de développement logiciel (SDK) .NET
Vous pouvez aussi créer et déployer le pipeline avec le kit de développement logiciel (SDK) .NET. Ce mécanisme peut être utilisé pour créer des pipelines par programme. Pour plus d’informations, consultez [Créer, surveiller et gérer des fabriques de données Azure à l’aide du Kit de développement logiciel (SDK) Data Factory .NET](data-factory-create-data-factories-programmatically.md). 

### <a name="using-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager
Vous pouvez créer et déployer le pipeline à l’aide d’un modèle Azure Resource Manager. Pour plus d’informations, consultez [Didacticiel : concevoir votre première fabrique de données Azure à l’aide du modèle Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md). 

### <a name="using-rest-api"></a>Utilisation de l'API REST
Vous pouvez également créer et déployer le pipeline avec les API REST. Ce mécanisme peut être utilisé pour créer des pipelines par programme. Pour plus d’informations, consultez [Création ou mise à jour de pipeline](https://msdn.microsoft.com/library/azure/dn906741.aspx). 

## <a name="monitor-and-manage-pipelines"></a>Surveiller et gérer les pipelines
Une fois qu’un pipeline est déployé, vous pouvez gérer et surveiller vos pipelines, segments et exécutions. Pour plus d’informations, consultez l’article [Surveiller et gérer les pipelines](data-factory-monitor-manage-pipelines.md).

## <a name="pipeline-json"></a>Pipeline JSON
Examinons de plus près la définition d’un pipeline au format JSON. La structure générique d'un pipeline se présente comme suit :

    {
        "name": "PipelineName",
        "properties": 
        {
            "description" : "pipeline description",
            "activities":
            [

            ],
            "start": "<start date-time>",
            "end": "<end date-time>"
        }
    }

La section **Activités** peut contenir une ou plusieurs activités définies. Chaque activité possède la structure de niveau supérieur suivante :

    {
        "name": "ActivityName",
        "description": "description", 
        "type": "<ActivityType>",
        "inputs":  "[]",
        "outputs":  "[]",
        "linkedServiceName": "MyLinkedService",
        "typeProperties":
        {

        },
        "policy":
        {
        }
        "scheduler":
        {
        }
    }

Le tableau suivant décrit les propriétés dans les définitions JSON du pipeline et de l'activité :

| Tag | Description | Requis |
| --- | --- | --- |
| name |Nom de l'activité ou du pipeline. Spécifiez un nom qui représente l’action que l’activité ou le pipeline est configuré(e) à exécuter<br/><ul><li>Nombre maximal de caractères : 260</li><li>Doit commencer par une lettre, un chiffre ou un trait de soulignement (_)</li><li>Les caractères suivants ne sont pas autorisés : « . », « + », « ? », « / », « < », « > », « * », « % », « & », « : », « \\ »</li></ul> |Oui |
| Description |Texte décrivant la raison motivant l’activité ou le pipeline. |Oui |
| type |Spécifie le type de l'activité. Consultez les articles [Activités de déplacement des données](data-factory-data-movement-activities.md) et [Activités de transformation des données](data-factory-data-transformation-activities.md) pour en savoir plus sur les différents types d’activités. |Oui |
| inputs |Les tables d’entrée utilisées par l’activité<br/><br/>// une table d’entrée<br/>« inputs » : [ { « name » : « inputtable1 » } ],<br/><br/>// deux tables d’entrée <br/>« inputs » : [ { « name » : « inputtable1 » }, { « name » : « inputtable2 » } ], |Oui |
| outputs |Les tables de sortie utilisées par l’activité.// une table de sortie<br/>« outputs » :  [ { « name » : « outputtable1 » } ],<br/><br/>//deux tables de sortie<br/>« outputs » :  [ { « name » : « outputtable1 » }, { « name » : « outputtable2 » }  ], |Oui |
| linkedServiceName |Nom du service lié utilisé par l’activité. <br/><br/>Une activité peut nécessiter que vous spécifiiez le service lié à l’environnement de calcul requis. |Oui pour les activités HDInsight et de calcul de score Azure Machine Learning  <br/><br/>Non pour toutes les autres |
| typeProperties |Les propriétés de la section typeProperties dépendent du type de l’activité. |Non |
| policy |Stratégies affectant le comportement d’exécution de l’activité. Si aucune valeur n’est spécifiée, les stratégies par défaut sont utilisées. |Non |
| start |Date et heure de début du pipeline. Doit se trouver au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. <br/><br/>Il est possible de spécifier une heure locale, par exemple une heure EST. Voici un exemple : « 2016-02-27T06:00:00**-05:00** », qui correspond à 6h EST.<br/><br/>Ensemble, les propriétés de début et de fin spécifient la période active du pipeline. Les tranches de sortie sont uniquement générées pendant cette période active. |Non<br/><br/>Si vous spécifiez une valeur pour la propriété end, vous devez en spécifier une pour la propriété start.<br/><br/>Les heures de début et de fin peuvent être toutes les deux non renseignées pour créer un pipeline. Vous devez spécifier les deux valeurs pour définir une période active d’exécution du pipeline. Si vous ne spécifiez pas d’heures de début et de fin lors de la création d’un pipeline, vous pouvez les définir à l’aide de l’applet de commande Set-AzureRmDataFactoryPipelineActivePeriod ultérieurement. |
| end |Date et heure de fin du pipeline. Si spécifiée, doit être au format ISO. Par exemple : 2014-10-14T17:32:41Z  <br/><br/>Il est possible de spécifier une heure locale, par exemple une heure EST. Voici un exemple : « 2016-02-27T06:00:00**-05:00** », qui correspond à 6h EST.<br/><br/>Pour exécuter le pipeline indéfiniment, spécifiez 9999-09-09 comme valeur pour la propriété end. |Non <br/><br/>Si vous spécifiez une valeur pour la propriété start, vous devez en spécifier une pour la propriété end.<br/><br/>Consultez les remarques relatives à la propriété **start** . |
| isPaused |Si la valeur est true, le pipeline ne s’exécute pas. Valeur par défaut = false. Vous pouvez utiliser cette propriété pour activer ou désactiver. |Non |
| scheduler |La propriété « scheduler » est utilisée pour définir la planification souhaitée pour l’activité. Ses sous-propriétés sont les mêmes que celles de la [propriété de disponibilité dans un jeu de données](data-factory-create-datasets.md#Availability). |Non |
| pipelineMode |La méthode de planification des exécutions pour le pipeline. Les valeurs autorisées sont : scheduled (par défaut) et onetime.<br/><br/>« scheduled » indique que le pipeline s’exécute selon un intervalle de temps spécifié en fonction de sa période active (heure de début et de fin). « Onetime » indique que le pipeline ne s’exécute qu’une seule fois. Une fois créés, les pipelines de type onetime ne peuvent pas être modifiés ni mis à jour pour le moment. Consultez la page [Pipeline onetime](data-factory-scheduling-and-execution.md#onetime-pipeline) pour plus d’informations sur le paramètre onetime. |Non |
| expirationTime |Durée pendant laquelle le pipeline est valide et doit rester configuré. Le pipeline est automatiquement supprimé une fois le délai d’expiration atteint s’il ne contient aucune exécution active, en échec ou en attente. |Non |
| jeux de données |Liste des jeux de données à utiliser par les activités définies dans le pipeline. Cette propriété permet de définir des jeux de données spécifiques à ce pipeline et non définis dans la fabrique de données. Les jeux de données définis dans ce pipeline ne peuvent être utilisés que par ce pipeline et ne peuvent pas être partagés. Pour plus d’informations, consultez la page [Étendue des jeux de données](data-factory-create-datasets.md#scoped-datasets) . |Non |

### <a name="policies"></a>Stratégies
Les stratégies affectent le comportement d'exécution d'une activité, en particulier lors du traitement du segment d'une table. Le tableau suivant fournit les détails.

| Propriété | Valeurs autorisées | Valeur par défaut | Description |
| --- | --- | --- | --- |
| accès concurrentiel |Entier  <br/><br/>Valeur max : 10 |1 |Nombre d’exécutions simultanées de l’activité.<br/><br/>Il détermine le nombre d’exécutions en parallèle de l’activité qui peuvent se produire sur différents segments. Par exemple, si une activité doit passer par un grand ensemble de données disponibles, une valeur de concurrence plus élevée accélère le traitement des données. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Détermine l’ordre des segments de données qui sont traités.<br/><br/>Par exemple, si vous avez 2 segments (l’un se produisant à 16 heures et l’autre à 17 heures) et que les deux sont en attente d’exécution. Si vous définissez executionPriorityOrder sur NewestFirst, le segment à 17 h est traité en premier. De même, si vous définissez executionPriorityOrder sur OldestFIrst, le segment à 16 h est traité en premier. |
| retry |Entier <br/><br/>La valeur max peut être 10 |3 |Nombre de nouvelles tentatives avant que le traitement des données du segment ne soit marqué comme un échec. L'exécution de l'activité pour un segment de données est répétée jusqu'au nombre de tentatives spécifié. La nouvelle tentative est effectuée dès que possible après l'échec. |
| timeout |TimeSpan |00:00:00 |Délai d'expiration de l'activité. Exemple : 00:10:00 (implique un délai d’expiration de 10 minutes)<br/><br/>Si une valeur n’est pas spécifiée ou est égale à 0, le délai d’expiration est infini.<br/><br/>Si le temps de traitement des données sur un segment dépasse la valeur du délai d’expiration, il est annulé et le système tente de réexécuter le traitement. Le nombre de nouvelles tentatives dépend de la propriété « retry ». Quand le délai d’expiration est atteint, l’état est défini sur TimedOut. |
| delay |TimeSpan |00:00:00 |Spécifie le délai avant le début du traitement des données du segment.<br/><br/>L’exécution d’activité pour une tranche de données est démarrée une fois que le délai a dépassé l’heure d’exécution prévue.<br/><br/>Exemple : 00:10:00 (implique un délai de 10 minutes) |
| longRetry |Entier <br/><br/>Valeur max : 10 |1 |Le nombre de nouvelles tentatives longues avant l’échec de l’exécution du segment.<br/><br/>Les tentatives longRetry sont espacées par longRetryInterval. Par conséquent, si vous devez spécifier un délai entre chaque tentative, utilisez longRetry. Si les valeurs Retry et longRetry sont spécifiées, chaque tentative longRetry inclut des tentatives Retry et le nombre maximal de tentatives sera égal à Retry * longRetry.<br/><br/>Par exemple, si nous avons les paramètres suivants dans la stratégie de l’activité :<br/>Retry : 3<br/>longRetry : 2<br/>longRetryInterval : 01:00:00<br/><br/>Supposons qu’il existe un seul segment à exécuter (dont l’état est Waiting) et que l’exécution de l’activité échoue à chaque fois. Au départ, il y aurait 3 tentatives consécutives d'exécution. Après chaque tentative, l’état du segment serait Retry. Une fois les 3 premières tentatives terminées, l’état du segment serait LongRetry.<br/><br/>Après une heure (c’est-à-dire la valeur de longRetryInterval), il y aurait un autre ensemble de 3 tentatives consécutives d’exécution. Ensuite, l'état du segment serait Failed et aucune autre tentative ne serait exécutée. Par conséquent, 6 tentatives ont été exécutées.<br/><br/>Si une exécution réussit, l’état de la tranche est Ready et aucune nouvelle tentative n’est tentée.<br/><br/>La valeur longRetry peut être utilisée dans les situations où les données dépendantes arrivent à des moments non déterministes ou lorsque l’environnement global où le traitement des données se produit est douteux. Dans ces cas, l’exécution de nouvelles tentatives l’une après l’autre peut ne pas être utile et procéder ainsi après un intervalle de temps précis produit la sortie désirée.<br/><br/>Mise en garde : ne définissez pas de valeurs élevées pour longRetry ou longRetryInterval. En règle générale, des valeurs plus élevées impliquent d’autres problèmes systémiques. |
| longRetryInterval |TimeSpan |00:00:00 |Le délai entre les nouvelles tentatives longues |

## <a name="next-steps"></a>Étapes suivantes
* Comprendre [la planification et l’exécution dans Azure Data Factory](data-factory-scheduling-and-execution.md).  
* En savoir plus sur le [déplacement des données](data-factory-data-movement-activities.md) et les [fonctionnalités de transformation des données](data-factory-data-transformation-activities.md) dans Azure Data Factory
* Comprendre [la gestion et la surveillance dans Azure Data Factory](data-factory-monitor-manage-pipelines.md).
* [Générer et déployer votre premier pipeline](data-factory-build-your-first-pipeline.md). 

<!--HONumber=Oct16_HO2-->


