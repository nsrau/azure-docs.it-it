---
title: Campi JobConfig.json di Analisi di flusso di AzureAzure Stream Analytics JobConfig.json fields
description: Questo articolo elenca i campi supportati per il file JobConfig.json di Azure Stream Analytics usato per creare processi nel codice di Visual Studio.This article lists the supported fields for the Azure Stream Analytics JobConfig.json file used to create jobs in Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617957"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Campi JobConfig.json di Analisi di flusso di AzureAzure Stream Analytics JobConfig.json fields

I campi seguenti sono supportati nel file *JobConfig.json* usato per creare un processo di Analisi di flusso di [Azure usando Visual Studio Code](quick-create-vs-code.md).

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|Nome|Type|Obbligatoria|valore|
|----|----|--------|-----|
|DataLocale|string|No|Impostazioni locali dei dati del processo di analisi del flusso. Valore deve essere il nome di un oggetto supportato. Il valore predefinito è 'en-US' se non ne viene specificato uno.|
|OutputErrorPolicy (outputErrorPolicy)|string|No|Indica i criteri da applicare agli eventi che arrivano all'output e che non possono essere scritti nell'archivio esterno a causa del formato non corretto (valori di colonna mancanti, valori di colonna di tipo o dimensione errati). - Stop o Drop|
|EventiLateArrivalMaxDelayInSeconds|integer|No|Il ritardo massimo tollerabile in pochi secondi in cui gli eventi che arrivano in ritardo potrebbero essere inclusi. L'intervallo supportato è compreso tra -1 e 1814399 (20.23:59:59 giorni) e -1 viene utilizzato per specificare wait per un tempo indefinito. Se la proprietà è assente, viene interpretata per avere un valore di -1.|
|EventiOutOfOrderMaxDelayInSeconds|integer|No|Il ritardo massimo tollerabile in secondi in cui gli eventi out-of-order possono essere regolati per essere di nuovo in ordine.|
|EventiOutOfOrderPolicyEventsOutOfOrderPolicy|string|No|Indica i criteri da applicare agli eventi che arrivano non in ordine nel flusso dell'evento di input. - Regolare o rilasciare|
|StreamingUnits (Unità streaming)|integer|Sì|Specifica il numero di unità di streaming utilizzate dal processo di streaming.|
|CompatibilityLevel|string|No|Controlla determinati comportamenti di runtime del processo di streaming. - I valori accettabili sono "1.0", "1.1", "1.2"|
|UseSystemAssignedIdentity|boolean|No|Impostare true per consentire a questo processo di comunicare con altri servizi di Azure come se stesso usando un'identità di Azure Active Directory gestita.|
|GlobalStorage.AccountName|string|No|L'account di archiviazione globale viene usato per archiviare il contenuto correlato al processo di analisi del flusso, ad esempio snapshot di dati di riferimento SQL.|
|GlobalStorage.AccountKey|string|No|Chiave corrispondente per l'account di archiviazione globale.|
|DataSourceCredentialDomain|string|No|Proprietà riservata per l'archiviazione locale delle credenziali.|
|Tipo di script|string|Sì|Proprietà riservata per indicare il tipo di questo file di origine. Il valore accettabile è "JobConfig" per JobConfig.json.Acceptable value is "JobConfig" for JobConfig.json.|
|Tag|Coppie chiave-valore JSON|No|I tag sono coppie nome/valore che consentono di classificare le risorse e visualizzare la fatturazione consolidata applicando lo stesso tag a più risorse e gruppi di risorse. Per i nomi dei tag non viene fatta distinzione tra maiuscole e minuscole e i valori dei tag fanno distinzione tra maiuscole e minuscole.|

## <a name="next-steps"></a>Passaggi successivi

* [Creare un processo di Analisi di flusso di Azure nel codice di Visual StudioCreate an Azure Stream Analytics job in Visual Studio Code](quick-create-vs-code.md)
* [TestStream Analytics esegue query localmente con dati di esempio usando il codice di Visual StudioTest Stream Analytics queries locally with sample data using Visual Studio Code](visual-studio-code-local-run.md)
* [Test di analisi di flusso esegue query localmente sull'input](visual-studio-code-local-run-live-input.md)
*del flusso live usando il codice di Visual Studio Distribuire un processo di Azure Stream Analytics usando il[pacchetto CI/CD npm](setup-cicd-vs-code.md)