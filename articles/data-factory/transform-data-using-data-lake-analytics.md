---
title: Trasformare dati usando lo script U-SQL - Azure | Documentazione Microsoft
description: Informazioni su come elaborare o trasformare i dati eseguendo gli script U-SQL nel servizio di calcolo di Azure Data Lake Analytics.
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: shengc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f72ee8b6ef51046a64e794b17d1c389a25246358
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Trasformare i dati eseguendo script U-SQL in Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-usql-activity.md)
> * [Versione 2 - Anteprima](transform-data-using-data-lake-analytics.md)

Una pipeline in un'istanza di Data factory di Azure elabora i dati nei servizi di archiviazione collegati usando i servizi di calcolo collegati. Contiene una sequenza di attività in cui ogni attività esegue una specifica operazione di elaborazione. Questo articolo descrive l'**attività U-SQL di Data Lake Analytics** che esegue uno script **U-SQL** in un servizio di calcolo collegato di **Azure Data Lake Analytics**. 

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività U-SQL nella versione 1](v1/data-factory-usql-activity.md).

Creare un account di Azure Data Lake Analytics prima di creare una pipeline con un'attività U-SQL di Data Lake Analytics. Per altre informazioni su Azure Data Lake Analytics, vedere [Introduzione ad Azure Data Lake con l'SDK .NET](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Servizio collegato di Azure Data Lake Analytics
Creare un servizio collegato di **Azure Data Lake Analytics** per collegare un servizio di calcolo di Azure Data Lake Analytics a una Data Factory di Azure. L'attività U-SQL di Data Lake Analytics nella pipeline fa riferimento a questo servizio collegato. 

La tabella seguente fornisce le descrizioni delle proprietà generiche usate nella definizione JSON. 

| Proprietà                 | Descrizione                              | Obbligatorio                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | La proprietà type deve essere impostata su **AzureDataLakeAnalytics**. | Sì                                      |
| **accountName**          | Nome dell'account di Azure Data Lake Analytics.  | Sì                                      |
| **dataLakeAnalyticsUri** | URI di Azure Data Lake Analytics.           | No                                       |
| **subscriptionId**       | ID sottoscrizione di Azure                    | No (se non specificata, viene usata la sottoscrizione della Data factory). |
| **resourceGroupName**    | Nome del gruppo di risorse di Azure                | No (se non specificata, viene usato il gruppo di risorse di Data Factory). |

### <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio
Per connettersi al servizio Azure Data Lake Analytics, il servizio collegato di Azure Data Lake Analytics richiede l'autenticazione di un'entità servizio. Per usare l'autenticazione basata su entità servizio, registrare un'entità applicazione in Azure Active Directory (Azure AD) e concedere a questa l'accesso ai servizi Data Lake Analytics e Data Lake Store che usa. Per la procedura dettaglia, vedere [Autenticazione da servizio a servizio](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:
* ID applicazione
* Chiave applicazione 
* ID tenant

Usare l'autenticazione basata su entità servizio specificando le proprietà seguenti:

| Proprietà                | Descrizione                              | Obbligatorio |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Specificare l'ID client dell'applicazione.     | Sì      |
| **servicePrincipalKey** | Specificare la chiave dell'applicazione.           | Sì      |
| **tenant**              | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì      |

**Esempio: autenticazione basata su entità servizio**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Per altre informazioni sul servizio collegato, vedere [Servizi collegati di calcolo](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Attività U-SQL di Data Lake Analytics
Il frammento JSON seguente definisce una pipeline con un'attività U-SQL di Data Lake Analytics. La definizione dell'attività contiene un riferimento al servizio collegato di Azure Data Lake Analytics creato in precedenza. Per eseguire uno script U-SQL di Data Lake Analytics, Data Factory invia lo script specificato a Data Lake Analytics. I valori di input e output che Data Lake Analytics deve recuperare e generare sono definiti all'interno dello script. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "AzureDataLakeAnalyticsLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "LinkedServiceofAzureBlobStorageforscriptPath",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

Nella tabella seguente vengono descritti i nomi e le descrizioni delle proprietà specifiche per questa attività. 

| Proprietà            | Descrizione                              | Obbligatorio |
| :------------------ | :--------------------------------------- | :------- |
| name                | Nome dell'attività nella pipeline     | Sì      |
| Descrizione         | Testo che descrive l'attività.  | No       |
| type                | Per l'attività U-SQL di Data Lake Analytics, il tipo corrisponde a **DataLakeAnalyticsU-SQL**. | Sì      |
| linkedServiceName   | Servizio collegato ad Azure Data Lake Analytics. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md).  |          |
| scriptPath          | Percorso della cartella contenente lo script U-SQL. Il nome del file distingue tra maiuscole e minuscole. | Sì      |
| scriptLinkedService | Servizi collegati che collegano la risorsa di archiviazione contenente lo script alla Data factory | Sì      |
| degreeOfParallelism | Il numero massimo di nodi usati contemporaneamente per eseguire il processo. | No       |
| priority            | Determina quali processi rispetto a tutti gli altri disponibili nella coda devono essere selezionati per essere eseguiti per primi. Più è basso il numero, maggiore sarà la priorità. | No       |
| parameters          | Parametri per lo script U-SQL          | No       |
| runtimeVersion      | Versione di runtime del motore di U-SQL da usare | No       |
| compilationMode     | <p>Modalità di compilazione di U-SQL. Deve corrispondere a uno dei valori seguenti: **Semantic:** consente di eseguire solo controlli semantici e i controlli di integrità necessari. **Full:** consente di eseguire una compilazione completa, inclusi il controllo della sintassi, l'ottimizzazione, la generazione di codice e così via. **SingleBox:** consente di eseguire una compilazione completa usando SingleBox come impostazione di TargetType. Se per questa proprietà non si specifica alcun valore, il server determina la modalità di compilazione ottimale. | No |

Per la definizione dello script, vedere lo [script SearchLogProcessing.txt](#sample-u-sql-script). 

## <a name="sample-u-sql-script"></a>Script U-SQL di esempio

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

Nello script di esempio sopra riportato i valori di input e di output dello script sono definiti nei parametri  **@in**  e  **@out** . I valori dei parametri **@in** e **@out** dello script U-SQL vengono passati in modo dinamico da Data Factory tramite la sezione "parameters". 

È possibile specificare anche altre proprietà come degreeOfParallelism e priorità nella definizione della pipeline per i processi in esecuzione sul servizio Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parametri dinamici
Nell'esempio di definizione di pipeline i parametri in e out vengono assegnati con valori hardcoded. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

È anche possibile usare parametri dinamici. ad esempio: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

In questo caso, i file di input vengono prelevati dalla cartella /datalake/input e i file di output vengono generati nella cartella /datalake/output. I nomi dei file sono dinamici e si basano sull'ora di inizio della sezione.  

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, che illustrano altre modalità di trasformazione dei dati: 

* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività Pig](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach Execution Activity](transform-data-using-machine-learning.md) (Attività di esecuzione batch di Machine Learning)
* [Attività stored procedure](transform-data-using-stored-procedure.md)

