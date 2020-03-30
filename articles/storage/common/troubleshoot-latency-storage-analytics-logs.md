---
title: Risolvere i problemi di latenza usando i log di Analisi archiviazione
description: Identificare e risolvere i problemi di latenza usando i log di Azure Storage Analytic e ottimizzare l'applicazione client.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196514"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Risolvere i problemi di latenza usando i log di Analisi archiviazione

La diagnosi e la risoluzione dei problemi è una competenza chiave per la creazione e il supporto di applicazioni client con Archiviazione di Azure.Diagnosing and troubleshooting is a key skill for building and supporting client applications with Azure Storage.

A causa della natura distribuita di un'applicazione Azure, la diagnosi e la risoluzione di errori e problemi di prestazioni possono essere più complesse rispetto agli ambienti tradizionali.

I passaggi seguenti illustrano come identificare e risolvere i problemi di latenza usando i log di Analisi di Azure e ottimizzare l'applicazione client.

## <a name="recommended-steps"></a>Procedure consigliate

1. Scaricare i log di [Analisi archiviazione](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data).

2. Usare lo script di PowerShell seguente per convertire i log di formato non elaborato in formato tabulare:Use the following PowerShell script to convert the raw format logs into tabular format:

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

3. Lo script avvierà una finestra GUI in cui è possibile filtrare le informazioni per colonne, come illustrato di seguito.

   ![Finestra Storage Analytic Log Parser](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Restringere le voci del registro in base al "tipo di operazione" e cercare la voce di log creata durante l'intervallo di tempo del problema.

   ![Voci del log di tipo operazione](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. Durante il periodo in cui si è verificato il problema, sono importanti i seguenti valori:

   * Tipo di operazione: GetBlob
   * stato della richiesta - SASNetworkError
   * End-to-End-Latency-In-Ms - 8453
   * Latenza server -in-Ms - 391

   La latenza end-to-end viene calcolata utilizzando l'equazione seguente:

   * Latenza end-to-end - Latenza server - Latenza client

   Calcolare la latenza del client utilizzando la voce di log:

   * Latenza client - Latenza end-to-end - Latenza server

          * Example: 8453 – 391 = 8062ms

   Nella tabella seguente vengono fornite informazioni sui risultati di OperationType e RequestStatus ad alta latenza:

   |   |Proprietà RequestStatus .<br>Operazione completata|Proprietà RequestStatus .<br>(SAS) Errore di reteNetworkError|Recommendation|
   |---|---|---|---|
   |GetBlob|Sì|No|[**Funzionamento GetBlob:** RequestStatus - Operazione riuscita](#getblob-operation-requeststatus--success)|
   |GetBlob|No|Sì|[**Funzionamento GetBlob:** RequestStatus - (SAS)NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Sì|No|[**Operazione Put:** RequestStatus - Operazione riuscita](#put-operation-requeststatus--success)|
   |PutBlob|No|Sì|[**Operazione Put:** RequestStatus - (SAS)NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Risultati dello stato

### <a name="getblob-operation-requeststatus--success"></a>Operazione GetBlob: RequestStatus : operazione riuscita

Controllare i seguenti valori come indicato nel passaggio 5 della sezione "Passaggi consigliati":

* Latenza end-to-end
* Latenza server
* Latenza client

In **un'operazione GetBlob** con **RequestStatus - Operazione**riuscita, se l'ora **massima** viene impiegata in **Client-Latency**, significa che Archiviazione di Azure sta impiegando un volume elevato di tempo per la scrittura dei dati nel client. Questo ritardo indica un problema lato client.

**Raccomandazione:**

* Esaminare il codice nel client.
* Utilizzare Wireshark, Microsoft Message Analyzer o Tcping per analizzare i problemi di connettività di rete dal client. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>Operazione GetBlob: RequestStatus (SAS)NetworkError

Controllare i seguenti valori come indicato nel passaggio 5 della sezione "Passaggi consigliati":

* Latenza end-to-end
* Latenza server
* Latenza client

In **un'operazione GetBlob** con **RequestStatus (SAS)NetworkError**, se l'ora **massima** viene impiegata in **Client-Latency**, il problema più comune è che il client si disconnette prima della scadenza di un timeout nel servizio di archiviazione.

**Raccomandazione:**

* Esaminare il codice nel client per capire perché e quando il client si disconnette dal servizio di archiviazione.
* Utilizzare Wireshark, Microsoft Message Analyzer o Tcping per analizzare i problemi di connettività di rete dal client. 

### <a name="put-operation-requeststatus--success"></a>Operazione Put: RequestStatus : Operazione riuscita

Controllare i seguenti valori come indicato nel passaggio 5 della sezione "Passaggi consigliati":

* Latenza end-to-end
* Latenza server
* Latenza client

In **un'operazione Put** con **RequestStatus - Success**, se **L'ora massima** viene impiegata in **Client-Latency**, significa che il client sta impiegando più tempo per inviare dati ad Archiviazione di Azure. Questo ritardo indica un problema lato client.

**Raccomandazione:**

* Esaminare il codice nel client.
* Utilizzare Wireshark, Microsoft Message Analyzer o Tcping per analizzare i problemi di connettività di rete dal client. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Operazione Put: RequestStatus (SAS)NetworkError

Controllare i seguenti valori come indicato nel passaggio 5 della sezione "Passaggi consigliati":

* Latenza end-to-end
* Latenza server
* Latenza client

In **un'operazione PutBlob** con **RequestStatus (SAS)NetworkError**, se l'ora **massima** viene impiegata in **Client-Latency**, il problema più comune è che il client si disconnette prima della scadenza di un timeout nel servizio di archiviazione.

**Raccomandazione:**

* Esaminare il codice nel client per capire perché e quando il client si disconnette dal servizio di archiviazione.
* Utilizzare Wireshark, Microsoft Message Analyzer o Tcping per analizzare i problemi di connettività di rete dal client.

