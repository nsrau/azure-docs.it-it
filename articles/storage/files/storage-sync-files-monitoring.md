---
title: Monitorare Sincronizzazione file di Azure | Microsoft Docs
description: Vedere come monitorare la distribuzione di Sincronizzazione file di Azure usando monitoraggio di Azure, il servizio di sincronizzazione archiviazione e Windows Server.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8b2b62ac4d79964c0a597f40d8154e5f57350f0b
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031082"
---
# <a name="monitor-azure-file-sync"></a>Monitorare Sincronizzazione file di Azure

Usare Sincronizzazione file di Azure per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

Questo articolo descrive come monitorare la distribuzione di Sincronizzazione file di Azure usando monitoraggio di Azure, il servizio di sincronizzazione archiviazione e Windows Server.

In questa guida sono trattati gli scenari seguenti: 
- Visualizzare Sincronizzazione file di Azure metriche in monitoraggio di Azure.
- Creare avvisi in monitoraggio di Azure per notificare in modo proattivo le condizioni critiche.
- Visualizzare l'integrità della distribuzione di Sincronizzazione file di Azure usando il portale di Azure.
- Come usare i registri eventi e i contatori delle prestazioni nei server Windows per monitorare l'integrità della distribuzione del Sincronizzazione file di Azure. 

## <a name="azure-monitor"></a>Monitoraggio di Azure

Usare [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) per visualizzare le metriche e configurare gli avvisi per la sincronizzazione, la suddivisione in livelli cloud e la connettività del server.  

### <a name="metrics"></a>Metriche

Le metriche per Sincronizzazione file di Azure sono abilitate per impostazione predefinita e vengono inviate al Monitoraggio di Azure ogni 15 minuti.

**Come visualizzare Sincronizzazione file di Azure metriche in monitoraggio di Azure**
- Passare al **servizio di sincronizzazione archiviazione** nel **portale di Azure** e fare clic su **metriche**.
- Fare clic sull'elenco a discesa **metrica** e selezionare la metrica che si desidera visualizzare.

Le metriche seguenti per la Sincronizzazione file di Azure sono disponibili in Monitoraggio di Azure:

| Nome metrica | Descrizione |
|-|-|
| Byte sincronizzati | Dimensioni dei dati trasferiti (caricamento e scaricamento).<br><br>Unità: Byte<br>Tipo di aggregazione: Sum<br>Dimensioni applicabili: nome endpoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |
| Richiamo cloud a livelli | Dimensioni dei dati richiamati.<br><br>**Nota**: questa metrica verrà rimossa in futuro. Usare la metrica dimensione di richiamo di suddivisione in livelli nel cloud per monitorare le dimensioni dei dati richiamati.<br><br>Unità: Byte<br>Tipo di aggregazione: Sum<br>Dimensione applicabile: nome del server |
| Cloud tiering recall size (Dimensioni richiamo cloud a livelli) | Dimensioni dei dati richiamati.<br><br>Unità: Byte<br>Tipo di aggregazione: Sum<br>Dimensione applicabile: nome del server, nome del gruppo di sincronizzazione |
| Cloud tiering recall size by application (Dimensioni richiamo cloud a livelli per applicazione) | Dimensioni dei dati richiamati dall'applicazione.<br><br>Unità: Byte<br>Tipo di aggregazione: Sum<br>Dimensione applicabile: nome dell'applicazione, nome del server, nome del gruppo di sincronizzazione |
| Cloud tiering recall throughput (Velocità effettiva richiamo cloud a livelli) | Dimensioni della velocità effettiva di richiamo dei dati.<br><br>Unità: Byte<br>Tipo di aggregazione: Sum<br>Dimensione applicabile: nome del server, nome del gruppo di sincronizzazione |
| File non sincronizzati | Numero di file che non è possibile sincronizzare.<br><br>Unità: Conteggio<br>Tipo di aggregazione: Sum<br>Dimensioni applicabili: nome endpoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |
| File sincronizzati | Numero di file trasferiti (caricamento e scaricamento)<br><br>Unità: Conteggio<br>Tipo di aggregazione: Sum<br>Dimensioni applicabili: nome endpoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |
| Stato online del server | Numero di heartbeat ricevuti dal server.<br><br>Unità: Conteggio<br>Tipo di aggregazione: Massimo<br>Dimensione applicabile: nome del server |
| Risultato della sessione di sincronizzazione | Risultato della sessione di sincronizzazione (1 = sessione di sincronizzazione con esito positivo; 0 = sessione di sincronizzazione con esito negativo)<br><br>Unità: Conteggio<br>Tipi di aggregazione: massimo<br>Dimensioni applicabili: nome endpoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |

### <a name="alerts"></a>Avvisi

Gli avvisi notificano in modo proattivo quando vengono riscontrate importanti condizioni nei dati di monitoraggio. Per altre informazioni sulla configurazione degli avvisi in monitoraggio di Azure, vedere [Panoramica degli avvisi in Microsoft Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

**Come creare avvisi per Sincronizzazione file di Azure**

- Passare al **servizio di sincronizzazione archiviazione** nell' **portale di Azure**. 
- Fare clic su **avvisi** nella sezione monitoraggio e quindi fare clic su **+ nuova regola di avviso**.
- Fare clic su **Seleziona condizione** e fornire le informazioni seguenti per l'avviso: 
    - **Metrica**
    - **Nome dimensione**
    - **Logica di avviso**
- Fare clic su **Seleziona gruppo di azioni** e aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
- Specificare i **Dettagli dell'avviso** , ad esempio il nome, la **Descrizione** e la **gravità**della **regola di avviso**.
- Fare clic su **Crea regola di avviso** per creare l'avviso.  

Nella tabella seguente sono elencati alcuni scenari di esempio da monitorare e la metrica appropriata da usare per l'avviso:

| Scenario | Metrica da usare per l'avviso |
|-|-|
| Integrità endpoint server Visualizza un errore nel portale | Risultato della sessione di sincronizzazione |
| Impossibile sincronizzare i file in un endpoint server o cloud | File non sincronizzati |
| Il server registrato non riesce a comunicare con il servizio di sincronizzazione archiviazione | Stato online del server |
| Le dimensioni di richiamo per la suddivisione in livelli nel cloud hanno superato 500GiB in un giorno  | Cloud tiering recall size (Dimensioni richiamo cloud a livelli) |

Per istruzioni su come creare avvisi per questi scenari, vedere la sezione relativa agli [esempi di avviso](#alert-examples) .

## <a name="storage-sync-service"></a>Servizio di sincronizzazione archiviazione

Per visualizzare l'integrità della distribuzione Sincronizzazione file di Azure nella **portale di Azure**, passare al **servizio di sincronizzazione archiviazione** e sono disponibili le informazioni seguenti:

- Stato server registrato
- Integrità endpoint server
    - File non sincronizzati
    - Attività di sincronizzazione
    - Efficienza di suddivisione in livelli nel cloud
    - File senza suddivisione in livelli
    - Richiama errori
- Metriche

### <a name="registered-server-health"></a>Stato server registrato

Per visualizzare l' **integrità del server registrato** nel portale, passare alla sezione **Server registrati** del servizio di **sincronizzazione archiviazione**.

- Se lo stato del **server registrato** è **online**, il server comunica correttamente con il servizio.
- Se lo stato del **server registrato** è **offline**, il processo di monitoraggio della sincronizzazione archiviazione (AzureStorageSyncMonitor.exe) non è in esecuzione o il server non è in grado di accedere al servizio sincronizzazione file di Azure. Per informazioni aggiuntive, vedere la [documentazione relativa alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity) .

### <a name="server-endpoint-health"></a>Integrità endpoint server

Per visualizzare l'integrità di un **endpoint server** nel portale, passare alla sezione **gruppi di sincronizzazione** del servizio di **sincronizzazione archiviazione** e selezionare un **gruppo di sincronizzazione**.

- L' **attività di sincronizzazione** e **integrità degli endpoint server** nel portale si basa sugli eventi di sincronizzazione registrati nel registro eventi di telemetria sul server (ID 9102 e 9302). Se una sessione di sincronizzazione ha esito negativo a causa di un errore temporaneo, ad esempio l'errore è stato annullato, la sincronizzazione verrà comunque visualizzata come integra nel portale finché la sessione di sincronizzazione corrente sta procedendo (i file vengono applicati). L'ID evento 9302 è l'evento dello stato di avanzamento della sincronizzazione e l'ID evento 9102 viene registrato al termine di una sessione di sincronizzazione.  Per ulteriori informazioni, vedere [Sync Health](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) and [Sync Progress](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session). Se nel portale viene visualizzato un errore perché la sincronizzazione non sta procedendo, vedere la documentazione relativa alla [risoluzione dei problemi](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) per informazioni aggiuntive.
- Il numero di **file non sincronizzati** nel portale è basato sull'ID evento 9121 registrato nel registro eventi di telemetria sul server. Questo evento viene registrato per ogni errore per elemento al termine della sessione di sincronizzazione. Per risolvere gli errori per elemento, vedere [ricerca per categorie verificare se sono presenti file o cartelle specifici che non sono sincronizzati](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).
- Per visualizzare l' **efficienza** di suddivisione in livelli nel cloud nel portale, passare alle **proprietà dell'endpoint server** e passare alla sezione suddivisione in **livelli nel cloud** . I dati forniti per l'efficienza di suddivisione in livelli nel cloud si basano sull'ID evento 9071 registrato nel registro eventi di telemetria sul server. Per altre informazioni, vedere [Suddivisione in livelli cloud](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering).
- Per visualizzare **i file senza** suddivisione in livelli e **richiamare gli errori** nel portale, passare alle **proprietà dell'endpoint server** e passare alla sezione suddivisione in **livelli nel cloud** . La suddivisione in **livelli dei file non** è basata sull'id evento 9003 registrato nel registro eventi di telemetria sul server e gli **errori di richiamo** si basano sull'ID evento 9006. Per esaminare i file che non riescono a eseguire il livello o richiamare, vedere [come risolvere i problemi dei file che non riescono a livello](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#how-to-troubleshoot-files-that-fail-to-tier) e [come risolvere i problemi dei file che non vengono richiamati](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled).

### <a name="metric-charts"></a>Grafici delle metriche

- I grafici delle metriche seguenti sono visualizzabili nel portale del servizio di sincronizzazione archiviazione:

  | Nome metrica | Descrizione | Nome pannello |
  |-|-|-|
  | Byte sincronizzati | Dimensioni dei dati trasferiti (caricamento e scaricamento) | Gruppo di sincronizzazione, Endpoint server |
  | Richiamo cloud a livelli | Dimensione dei dati richiamati | Server registrati |
  | File non sincronizzati | Numero di file che non è possibile sincronizzare | Endpoint server |
  | File sincronizzati | Numero di file trasferiti (caricamento e scaricamento) | Gruppo di sincronizzazione, Endpoint server |
  | Stato online del server | Numero di heartbeat ricevuti dal server | Server registrati |

- Per altre informazioni, vedere [monitoraggio di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > I grafici nel portale di Servizi di sincronizzazione archiviazione sono basati su un intervallo di tempo di 24 ore. Per visualizzare dimensioni o intervalli di tempo diversi, usare Monitoraggio di Azure.

## <a name="windows-server"></a>Windows Server

Nel **server Windows** in cui è installato l'agente di sincronizzazione file di Azure, è possibile visualizzare l'integrità degli endpoint server nel server usando i **registri eventi** e i **contatori delle prestazioni**.

### <a name="event-logs"></a>Log eventi

Usare il registro eventi di telemetria nel server per monitorare l'integrità del server registrato, della sincronizzazione e del cloud a livelli. Il registro eventi di telemetria si trova in Visualizzatore eventi in *Applications and Services\Microsoft\FileSync\Agent*.

Integrità sincronizzazione

- L'ID evento 9102 viene registrato una volta completata una sessione di sincronizzazione. Utilizzare questo evento per determinare se le sessioni di sincronizzazione hanno esito positivo (**HRESULT = 0**) e se sono presenti errori di sincronizzazione per elemento. Per ulteriori informazioni, vedere la documentazione sull' [integrità della sincronizzazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e sugli [errori per elemento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) .

  > [!Note]  
  > A volte le sessioni di sincronizzazione hanno esito negativo globale o hanno un PerItemErrorCount diverso da zero. Tuttavia, continuano a avanzare e alcuni file vengono sincronizzati correttamente. È possibile visualizzarlo nei campi applicati, ad esempio AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes. Questi campi indicano la parte della sessione completata. Se più sessioni di sincronizzazione hanno esito negativo in una riga e hanno un conteggio applicato crescente, fornire tempo di sincronizzazione per riprovare prima di aprire un ticket di supporto.

- Al termine della sessione di sincronizzazione, viene registrato l'ID evento 9121 per ogni singolo elemento. Utilizzare questo evento per determinare il numero di file che non riescono a eseguire la sincronizzazione con questo errore (**PersistentCount** e **TransientCount**). Per esaminare gli errori persistenti degli elementi, vedere [ricerca per categorie verificare se sono presenti file o cartelle specifici che non sono sincronizzati](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

- L'ID evento 9302 viene registrato ogni 5-10 minuti se è presente una sessione di sincronizzazione attiva. Utilizzare questo evento per determinare se la sessione di sincronizzazione corrente sta procedendo (**AppliedItemCount > 0**). Se la sincronizzazione non è in corso, la sessione di sincronizzazione dovrebbe avere esito negativo e verrà registrato un ID evento 9102 con l'errore. Per ulteriori informazioni, vedere la [documentazione sullo stato di avanzamento della sincronizzazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Stato server registrato

- L'ID evento 9301 viene registrato ogni 30 secondi quando un server esegue query sul servizio per i processi. Se GetNextJob termina con **status = 0**, il server è in grado di comunicare con il servizio. Se GetNextJob termina con un errore, vedere la [documentazione relativa alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity) per informazioni aggiuntive.

Integrità di suddivisione in livelli cloud

- Per monitorare l'attività di suddivisione in livelli in un server, usare l'ID evento 9003, 9016 e 9029 nel registro eventi di telemetria, disponibile in Visualizzatore eventi in *Applications and Services\Microsoft\FileSync\Agent*.

  - L'ID evento 9003 fornisce inoltre la distribuzione di un errore per un endpoint server. Ad esempio: numero totale di errori e ErrorCode. Un evento viene registrato per codice di errore.
  - L'ID evento 9016 fornisce inoltre risultati di ghosting per un volume. Ad esempio: percentuale di spazio disponibile, numero di file fantasma nella sessione e numero di file non riusciti a fantasma.
  - L'ID evento 9029 fornisce informazioni sulla sessione di ghosting per un endpoint server. Ad esempio: numero di file tentati nella sessione, numero di file a livelli nella sessione e numero di file già a livelli.
  
- Per monitorare l'attività di richiamo in un server, usare l'ID evento 9005, 9006, 9009 e 9059 nel registro eventi di telemetria, disponibile in Visualizzatore eventi in *Applications and Services\Microsoft\FileSync\Agent*.

  - L'ID evento 9005 offre affidabilità di richiamo per un endpoint server. Ad esempio: totale file univoci a cui si accede e totale file univoci con accesso non riuscito.
  - L'ID evento 9006 fornisce inoltre la distribuzione di un errore di richiamo per un endpoint server. Ad esempio: Totale richieste non riuscite e ErrorCode. Un evento viene registrato per codice di errore.
  - L'ID evento 9009 fornisce informazioni sulla sessione di richiamo per un endpoint server. Ad esempio: DurationSeconds, CountFilesRecallSucceeded e CountFilesRecallFailed.
  - L'ID evento 9059 fornisce la distribuzione di richiamo delle applicazioni per un endpoint server. Ad esempio: ShareId, nome dell'applicazione e TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Contatori delle prestazioni

Usare i contatori delle prestazioni di Sincronizzazione file di Azure nel server per monitorare l'attività di sincronizzazione.

Per visualizzare Sincronizzazione file di Azure contatori delle prestazioni nel server, aprire Performance Monitor (Perfmon.exe). È possibile trovare i contatori negli oggetti **operazioni di sincronizzazione** AFS e **trasferiti** .

I contatori delle prestazioni seguenti per Sincronizzazione file di Azure sono disponibili in Monitoraggio delle prestazioni:

| Oggetto prestazioni\Nome contatore | Descrizione |
|-|-|
| Byte AFS trasferiti\Byte scaricati/sec | Numero di byte scaricati al secondo. |
| Byte AFS trasferiti\Byte caricati/sec | Numero di byte caricati al secondo. |
| Byte AFS trasferiti\Byte totali/sec | Byte totali al secondo (caricamento e download). |
| Operazioni di sincronizzazione AFS\file scaricati per la sincronizzazione/sec | Numero di file scaricati al secondo. |
| Operazioni di sincronizzazione AFS\file caricati per la sincronizzazione/sec | Numero di file caricati al secondo. |
| Operazioni di sincronizzazione AFS\operazioni di sincronizzazione file totali/sec | Numero totale di file sincronizzati (caricamento e download). |

## <a name="alert-examples"></a>Esempi di avviso
In questa sezione vengono forniti alcuni avvisi di esempio per Sincronizzazione file di Azure.

  > [!Note]  
  > Se si crea un avviso ed è troppo rumoroso, modificare il valore di soglia e la logica di avviso.
  
### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>Come creare un avviso se l'integrità dell'endpoint server Visualizza un errore nel portale

1. Nella **portale di Azure**passare al rispettivo **servizio di sincronizzazione archiviazione**. 
2. Passare alla sezione **monitoraggio** e fare clic su **avvisi**. 
3. Fare clic su **+ nuova regola di avviso** per creare una nuova regola di avviso. 
4. Configurare la condizione facendo clic su **Seleziona condizione**.
5. Nel pannello **Configura logica di segnalazione** fare clic su **risultato sessione di sincronizzazione** in nome segnale.  
6. Selezionare la configurazione della dimensione seguente: 
    - Nome Dimensione: **nome endpoint server**  
    - Operatore**=** 
    - Valori Dimensione: **tutti i valori correnti e futuri**  
7. Passare alla **logica di avviso** e completare le operazioni seguenti: 
    - Soglia impostata su **statica** 
    - Operatore: **minore di** 
    - Tipo di aggregazione: **massimo**  
    - Valore soglia: **1** 
    - Valutato in base a: granularità di aggregazione = **24 ore** | Frequenza di valutazione = **ogni ora** 
    - Fare clic su **fine.** 
8. Fare clic su **Seleziona gruppo di azioni** per aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
9. Specificare i **Dettagli dell'avviso** , ad esempio il nome, la **Descrizione** e la **gravità**della **regola di avviso**.
10. Fare clic su **Crea regola di avviso**. 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>Come creare un avviso in caso di errore di sincronizzazione dei file in un endpoint server o cloud

1. Nella **portale di Azure**passare al rispettivo **servizio di sincronizzazione archiviazione**. 
2. Passare alla sezione **monitoraggio** e fare clic su **avvisi**. 
3. Fare clic su **+ nuova regola di avviso** per creare una nuova regola di avviso. 
4. Configurare la condizione facendo clic su **Seleziona condizione**.
5. Nel pannello **Configura logica di segnalazione** , fare clic su **file non sincronizzati** in nome segnale.  
6. Selezionare la configurazione della dimensione seguente: 
     - Nome Dimensione: **nome endpoint server**  
     - Operatore**=** 
     - Valori Dimensione: **tutti i valori correnti e futuri**  
7. Passare alla **logica di avviso** e completare le operazioni seguenti: 
     - Soglia impostata su **statica** 
     - Operatore: **maggiore di** 
     - Tipo di aggregazione: **totale**  
     - Valore soglia: **100** 
     - Valutato in base a: granularità di aggregazione = **5 minuti** | Frequenza di valutazione = **ogni 5 minuti** 
     - Fare clic su **fine.** 
8. Fare clic su **Seleziona gruppo di azioni** per aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
9. Specificare i **Dettagli dell'avviso** , ad esempio il nome, la **Descrizione** e la **gravità**della **regola di avviso**.
10. Fare clic su **Crea regola di avviso**. 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>Come creare un avviso se un server registrato non riesce a comunicare con il servizio di sincronizzazione archiviazione

1. Nella **portale di Azure**passare al rispettivo **servizio di sincronizzazione archiviazione**. 
2. Passare alla sezione **monitoraggio** e fare clic su **avvisi**. 
3. Fare clic su **+ nuova regola di avviso** per creare una nuova regola di avviso. 
4. Configurare la condizione facendo clic su **Seleziona condizione**.
5. Nel pannello **Configura logica di segnalazione** fare clic su **stato online server** in nome segnale.  
6. Selezionare la configurazione della dimensione seguente: 
     - Nome Dimensione: **nome server**  
     - Operatore**=** 
     - Valori Dimensione: **tutti i valori correnti e futuri**  
7. Passare alla **logica di avviso** e completare le operazioni seguenti: 
     - Soglia impostata su **statica** 
     - Operatore: **minore di** 
     - Tipo di aggregazione: **massimo**  
     - Valore soglia (in byte): **1** 
     - Valutato in base a: granularità di aggregazione = **1 ora** | Frequenza di valutazione = **ogni 30 minuti** 
     - Fare clic su **fine.** 
8. Fare clic su **Seleziona gruppo di azioni** per aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
9. Specificare i **Dettagli dell'avviso** , ad esempio il nome, la **Descrizione** e la **gravità**della **regola di avviso**.
10. Fare clic su **Crea regola di avviso**. 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>Come creare un avviso se la dimensione del richiamo di suddivisione in livelli nel cloud ha superato il valore di 500GiB in un giorno

1. Nella **portale di Azure**passare al rispettivo **servizio di sincronizzazione archiviazione**. 
2. Passare alla sezione **monitoraggio** e fare clic su **avvisi**. 
3. Fare clic su **+ nuova regola di avviso** per creare una nuova regola di avviso. 
4. Configurare la condizione facendo clic su **Seleziona condizione**.
5. Nel pannello **Configura logica di segnalazione** fare clic su dimensioni di richiamo suddivisione in **livelli cloud** in nome segnale.  
6. Selezionare la configurazione della dimensione seguente: 
     - Nome Dimensione: **nome server**  
     - Operatore**=** 
     - Valori Dimensione: **tutti i valori correnti e futuri**  
7. Passare alla **logica di avviso** e completare le operazioni seguenti: 
     - Soglia impostata su **statica** 
     - Operatore: **maggiore di** 
     - Tipo di aggregazione: **totale**  
     - Valore soglia (in byte): **67108864000** 
     - Valutato in base a: granularità di aggregazione = **24 ore** | Frequenza di valutazione = **ogni ora** 
    - Fare clic su **fine.** 
8. Fare clic su **Seleziona gruppo di azioni** per aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
9. Specificare i **Dettagli dell'avviso** , ad esempio il nome, la **Descrizione** e la **gravità**della **regola di avviso**.
10. Fare clic su **Crea regola di avviso**. 

## <a name="next-steps"></a>Passaggi successivi
- [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
- [Impostazioni di proxy e firewall di Sincronizzazione file di Azure](storage-sync-files-firewall-and-proxy.md)
- [Distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
- [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md)
- [Domande frequenti su File di Azure](storage-files-faq.md)
