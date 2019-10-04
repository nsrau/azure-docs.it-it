---
title: Servizi collegati in Azure Data Factory | Microsoft Docs
description: Informazioni sui servizi collegati in Data Factory. I servizi collegati collegano archivi calcolo o dati a una data factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 80e9cee0d973dc8575e9645c537b6b69fbeef700
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70137110"
---
# <a name="linked-services-in-azure-data-factory"></a>Servizi collegati in Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-create-datasets.md)
> * [Versione corrente](concepts-datasets-linked-services.md)

Questo articolo descrive i servizi collegati, il modo in cui vengono definiti in formato JSON e il modo in cui vengono usati nelle pipeline Azure Data Factory.

Se non si ha dimestichezza con Data Factory, vedere [Introduzione al servizio Azure Data Factory](introduction.md).

## <a name="overview"></a>Panoramica
Una data factory può comprendere una o più pipeline. Una **pipeline** è un raggruppamento logico di **attività** che insieme eseguono un compito. Le attività in una pipeline definiscono le azioni da eseguire sui dati. Ad esempio, è possibile usare un'attività di copia per copiare i dati da un Server SQL locale a un'archiviazione BLOB di Azure. Quindi, si può usare un'attività Hive che esegue uno script Hive in un cluster HDInsight di Azure per elaborare i dati dall'archiviazione BLOB per produrre dati di output. Infine, è possibile usare una seconda attività di copia per copiare i dati di output in Azure SQL Data Warehouse per la compilazione delle soluzioni di report di business intelligence (BI). Per altre informazioni su pipeline e attività, vedere [Pipeline e attività](concepts-pipelines-activities.md) in Azure Data Factory.

Un **set di dati** è una visualizzazione dati denominata che punta o fa riferimento ai dati usati come input e output nelle **attività**.

Prima di creare un set di dati, è necessario creare un **servizio collegato** per collegare l'archivio dati alla data factory. I servizi collegati sono molto simili a stringhe di connessione e definiscono le informazioni necessarie per la connessione di Data Factory a risorse esterne. In altre parole, il set di dati rappresenta la struttura dei dati all'interno degli archivi dati collegati e il servizio collegato definisce la connessione all'origine dati. Il servizio collegato Archiviazione di Azure,ad esempio, collega l'account di archiviazione alla data factory. Un set di dati BLOB di Azure rappresenta il contenitore BLOB e la cartella all'interno dell'account di archiviazione di Azure che contiene i BLOB di input da elaborare.

Di seguito è riportato uno scenario di esempio. Per copiare i dati da un'archiviazione BLOB a un database SQL, si creano due servizi collegati: Archiviazione di Azure e database SQL di Azure. Si creano quindi due set di dati: un set di dati BLOB di Azure, che si riferisce al servizio collegato Archiviazione di Azure, e un set di dati della tabella SQL di Azure, che si riferisce al servizio collegato Database SQL di Azure. I servizi collegati Archiviazione di Azure e Database SQL di Azure contengono stringhe di connessione usate da Data Factory in fase di runtime per connettersi rispettivamente all'archiviazione di Azure e al database SQL di Azure. Il set di dati BLOB di Azure specifica il contenitore e una cartella BLOB che contengono i BLOB di input presenti nell'archiviazione BLOB di Azure. Il set di dati della tabella SQL di Azure specifica la tabella SQL del database SQL in cui verranno copiati i dati.

Nel diagramma seguente viene illustrata la relazione tra pipeline, attività, set di dati e il servizio collegato in Data Factory:

![Relazione tra pipeline, attività, set di dati, i servizi collegati](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>JSON servizio collegato
Un servizio collegato in Data Factory viene definito in formato JSON come segue:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

La tabella seguente descrive le proprietà nel codice JSON precedente:

Proprietà | Descrizione | Obbligatorio |
-------- | ----------- | -------- |
name | Nome del servizio collegato. Vedere [Azure Data Factory - Regole di denominazione](naming-rules.md). |  Sì |
type | Tipo di servizio collegato. Ad esempio:  AzureStorage (archivio dati) o AzureBatch (calcolo). Vedere la descrizione di typeProperties. | Sì |
typeProperties | Le proprietà del tipo sono diverse per ogni archivio dati o calcolo. <br/><br/> Per i tipi di archivio dati supportati e le relative proprietà del tipo, vedere la tabella [Tipo di set di dati](concepts-datasets-linked-services.md#dataset-type) in questo articolo. Vedere l'articolo sul connettore dell'archivio dati per informazioni sulle proprietà del tipo specifiche di un archivio dati. <br/><br/> Per i tipi di calcolo supportati e le relative proprietà del tipo, vedere [Servizi collegati di calcolo](compute-linked-services.md). | Sì |
connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No

## <a name="linked-service-example"></a>Esempio di servizio collegato
Il seguente servizio collegato è un servizio collegato di Archiviazione di Azure. Si noti che il valore type è impostato su AzureStorage. Le proprietà del tipo per il servizio collegato di Archiviazione di Azure includono una stringa di connessione. Il servizio Data Factory usa questa stringa di connessione per connettersi all'archivio dati in fase di esecuzione.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Creare servizi collegati

È possibile creare servizi collegati usando uno di questi strumenti o SDK: [API .NET](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [API REST](quickstart-create-data-factory-rest-api.md), Azure Resource Manager modello e portale di Azure

## <a name="data-store-linked-services"></a>Servizi collegati dell'archivio dati
È possibile trovare l'elenco dei dati archiviati supportati da Data Factory dall'articolo [Panoramica del connettore](copy-activity-overview.md#supported-data-stores-and-formats) . Fare clic su un archivio dati per informazioni sulle proprietà di connessione supportate.

## <a name="compute-linked-services"></a>Servizi collegati di calcolo
Gli [ambienti di calcolo](compute-linked-services.md) di riferimento sono supportati per informazioni dettagliate su diversi ambienti di calcolo a cui è possibile connettersi dal data factory e dalle diverse configurazioni.

## <a name="next-steps"></a>Passaggi successivi
Vedere le esercitazioni seguenti per istruzioni dettagliate sulla creazione di pipeline e set di dati tramite uno di questi strumenti o SDK.

- [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Avvio rapido: Creare una data factory tramite .NET)
- [Quickstart: create a data factory using PowerShell](quickstart-create-data-factory-powershell.md) (Avvio rapido: Creare una data factory tramite PowerShell)
- [Quickstart: create a data factory using REST API](quickstart-create-data-factory-rest-api.md) (Avvio rapido: Creare una data factory tramite API REST)
- [Guida introduttiva: creare un data factory usando portale di Azure](quickstart-create-data-factory-portal.md)
