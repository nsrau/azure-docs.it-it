---
title: Risolvere i problemi di latenza usando i log di Analisi archiviazione
description: Identificare e risolvere i problemi di latenza usando i log analitici di archiviazione di Azure e ottimizzare l'applicazione client.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 2197a149235c0dca98a24a57549538b2a4cbb1c8
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196514"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Risolvere i problemi di latenza usando i log di Analisi archiviazione

La diagnostica e la risoluzione dei problemi sono una delle principali competenze per la creazione e il supporto di applicazioni client con archiviazione di Azure.

A causa della natura distribuita di un'applicazione Azure, la diagnosi e la risoluzione dei problemi relativi a errori e prestazioni possono risultare più complesse rispetto agli ambienti tradizionali.

I passaggi seguenti illustrano come identificare e risolvere i problemi di latenza usando i log analitici di archiviazione di Azure e ottimizzare l'applicazione client.

## <a name="recommended-steps"></a>Procedure consigliate

1. Scaricare i [log del analisi archiviazione](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data).

2. Usare lo script di PowerShell seguente per convertire i log in formato non elaborato in formato tabulare:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. Lo script avvierà una finestra GUI in cui è possibile filtrare le informazioni in base alle colonne, come mostrato di seguito.

   ![Finestra parser log analitico archiviazione](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Limitare le voci di log in base a "Operation-Type" e cercare la voce di log creata durante l'intervallo di tempo del problema.

   ![Voci del log del tipo di operazione](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. Nel momento in cui si è verificato il problema, sono importanti i valori seguenti:

   * Operazione-tipo = GetBlob
   * Request-Status = SASNetworkError
   * Latenza end-to-end-in-MS = 8453
   * Latenza server-in-MS = 391

   La latenza end-to-end viene calcolata usando l'equazione seguente:

   * Latenza end-to-end = latenza server + latenza client

   Calcolare la latenza del client usando la voce di log:

   * Latenza client = latenza end-to-end-latenza server

          * Example: 8453 – 391 = 8062ms

   La tabella seguente fornisce informazioni sui risultati di OperationType e RequestStatus a latenza elevata:

   |   |RequestStatus =<br>Operazione completata|RequestStatus =<br>FIRMA NetworkError|Raccomandazione|
   |---|---|---|---|
   |GetBlob|Sì|No|[**Operazione GetBlob:** RequestStatus = operazione riuscita](#getblob-operation-requeststatus--success)|
   |GetBlob|No|Sì|[**Operazione GetBlob:** RequestStatus = (SAS) NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Sì|No|[**Operazione Put:** RequestStatus = operazione riuscita](#put-operation-requeststatus--success)|
   |PutBlob|No|Sì|[**Operazione Put:** RequestStatus = (SAS) NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Risultati stato

### <a name="getblob-operation-requeststatus--success"></a>Operazione GetBlob: RequestStatus = Success

Controllare i valori seguenti come indicato nel passaggio 5 della sezione "procedure consigliate":

* Latenza end-to-end
* Latenza server
* Latenza client

In un' **operazione GetBlob** con **RequestStatus = Success**, se viene impiegato il **tempo massimo** per la **latenza del client**, questo indica che archiviazione di Azure dedica un volume elevato di tempo alla scrittura dei dati nel client. Questo ritardo indica un problema sul lato client.

**Raccomandazione:**

* Esaminare il codice nel client.
* Usare Wireshark, Microsoft Message Analyzer o Tcping per analizzare i problemi di connettività di rete del client. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>Operazione GetBlob: RequestStatus = (SAS) NetworkError

Controllare i valori seguenti come indicato nel passaggio 5 della sezione "procedure consigliate":

* Latenza end-to-end
* Latenza server
* Latenza client

In un' **operazione GetBlob** con **REQUESTSTATUS = (SAS) NetworkError**, se viene impiegato il **tempo massimo** per la **latenza del client**, il problema più comune è che il client si disconnette prima della scadenza del timeout nel servizio di archiviazione.

**Raccomandazione:**

* Esaminare il codice nel client per capire perché e quando il client si disconnette dal servizio di archiviazione.
* Usare Wireshark, Microsoft Message Analyzer o Tcping per analizzare i problemi di connettività di rete del client. 

### <a name="put-operation-requeststatus--success"></a>Operazione Put: RequestStatus = Success

Controllare i valori seguenti come indicato nel passaggio 5 della sezione "procedure consigliate":

* Latenza end-to-end
* Latenza server
* Latenza client

In un' **operazione Put** con **RequestStatus = Success**, se viene impiegato il **tempo massimo** per la **latenza del client**, questo indica che il client impiega più tempo per inviare i dati all'archiviazione di Azure. Questo ritardo indica un problema sul lato client.

**Raccomandazione:**

* Esaminare il codice nel client.
* Usare Wireshark, Microsoft Message Analyzer o Tcping per analizzare i problemi di connettività di rete del client. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Operazione Put: RequestStatus = (SAS) NetworkError

Controllare i valori seguenti come indicato nel passaggio 5 della sezione "procedure consigliate":

* Latenza end-to-end
* Latenza server
* Latenza client

In un' **operazione PutBlob** con **REQUESTSTATUS = (SAS) NetworkError**, se il **tempo massimo** viene impiegato per la **latenza del client**, il problema più comune è che il client si disconnette prima della scadenza di un timeout nel servizio di archiviazione.

**Raccomandazione:**

* Esaminare il codice nel client per capire perché e quando il client si disconnette dal servizio di archiviazione.
* Usare Wireshark, Microsoft Message Analyzer o Tcping per analizzare i problemi di connettività di rete del client.

