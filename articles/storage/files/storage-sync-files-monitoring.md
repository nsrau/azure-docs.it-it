---
title: Monitorare Sincronizzazione file di Azure | Microsoft Docs
description: Come monitorare Sincronizzazione file di Azure.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/28/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 032b39846d19e34f2eb87c1311feeb4bb890cb24
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467459"
---
# <a name="monitor-azure-file-sync"></a>Monitorare Sincronizzazione file di Azure

Usare Sincronizzazione file di Azure per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

Questo articolo descrive come monitorare la distribuzione di Sincronizzazione file di Azure usando il portale di Azure e Windows Server.

Attualmente sono disponibili le opzioni di monitoraggio seguenti:

## <a name="azure-portal"></a>Portale di Azure

Nel portale di Azure, è possibile visualizzare lo stato di integrità dei server registrati, l'integrità dell'endpoint server (integrità di sincronizzazione) e le metriche.

### <a name="storage-sync-service"></a>Servizio di sincronizzazione archiviazione

Per visualizzare il server registrato e lo stato di integrità dell'endpoint server, passare al servizio di sincronizzazione archiviazione nel portale di Azure. L'integrità del server registrato è visualizzabile nel pannello del server registrato. Lo stato di integrità dell'endpoint server è visualizzabile nel pannello dei gruppi di sincronizzazione.

Integrità del server registrato
- Se lo stato del server registrato è online, il server comunica correttamente con il servizio.
- Se lo stato del server registrato risulta offline, verificare che il processo di monitoraggio della sincronizzazione archiviazione (AzureStorageSyncMonitor.exe) nel server sia in esecuzione. Se il server è protetto da un firewall o un proxy, configurare il firewall e il proxy secondo la [documentazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy).

Integrità dell'endpoint server
- L'integrità dell'endpoint server nel portale si basa sugli eventi di sincronizzazione che vengono registrati nel registro eventi di telemetria nel server (ID 9102 e 9302). Se una sessione di sincronizzazione non riesce a causa di un errore temporaneo (ad esempio, errore annullato), la sincronizzazione potrebbe risultare ancora integra nel portale, purché la sessione di sincronizzazione corrente sia in stato di avanzamento (l'evento ID 9302 viene usato per determinare se vengono applicati file). Per altre informazioni, vedere la documentazione seguente: [Integrità di sincronizzazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [Stato sincronizzazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Se il portale mostra un errore di sincronizzazione a causa del mancato stato di avanzamento della sincronizzazione, consultare la [Documentazione sulla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) per informazioni.

### <a name="azure-monitor"></a>Monitoraggio di Azure

Usare [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) per monitorare la sincronizzazione, il cloud a livelli e la connettività del server. Le metriche per Sincronizzazione file di Azure sono abilitate per impostazione predefinita e vengono inviate al Monitoraggio di Azure ogni 15 minuti.

Per visualizzare le metriche di Sincronizzazione file di Azure in Monitoraggio di Azure, selezionare il tipo di risorsa di servizi di sincronizzazione archiviazione.

Le metriche seguenti per la Sincronizzazione file di Azure sono disponibili in Monitoraggio di Azure:

| Nome metrica | DESCRIZIONE |
|-|-|
| Byte sincronizzati | Dimensioni dei dati trasferiti (caricamento e scaricamento).<br><br>Unità: Byte<br>Tipo di aggregazione: Somma<br>Dimensioni applicabili: Nome dell'endopoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |
| Richiamo cloud a livelli | Dimensioni dei dati richiamati.<br><br>Unità: Byte<br>Tipo di aggregazione: Somma<br>Dimensioni applicabili: Server Name |
| File non sincronizzati | Numero di file che non è possibile sincronizzare.<br><br>Unità: Conteggio<br>Tipo di aggregazione: Somma<br>Dimensioni applicabili: Nome dell'endopoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |
| File sincronizzati | Numero di file caricati e scaricati.<br><br>Unità: Conteggio<br>Tipo di aggregazione: Somma<br>Dimensioni applicabili: Nome dell'endopoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |
| Heartbeat server | Numero di heartbeat ricevuti dal server.<br><br>Unità: Conteggio<br>Tipo di aggregazione: Massima<br>Dimensioni applicabili: Server Name |
| Risultato della sessione di sincronizzazione | Risultato della sessione di sincronizzazione (1 = sessione di sincronizzazione con esito positivo; 0 = sessione di sincronizzazione con esito negativo)<br><br>Unità: Conteggio<br>Tipi di aggregazione: Massima<br>Dimensioni applicabili: Nome dell'endopoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |

## <a name="windows-server"></a>Windows Server

In Server di Windows, è possibile visualizzare il cloud a livelli, il server registrato e l'integrità della sincronizzazione.

### <a name="event-logs"></a>Log eventi

Usare il registro eventi di telemetria nel server per monitorare l'integrità del server registrato, della sincronizzazione e del cloud a livelli. Il registro eventi di telemetria si trova in Applicazioni e servizi\Microsoft\FileSync\Agente, nel Visualizzatore eventi.

Integrità di sincronizzazione
- L'ID evento 9102 viene registrato una volta completata una sessione di sincronizzazione. Questo evento deve essere usato per determinare se le sessioni di sincronizzazione vengono completate correttamente (HResult = 0) e se sono presenti errori di sincronizzazione per ogni elemento. Per altre informazioni, vedere la documentazione seguente: [Integrità di sincronizzazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [Errori per ogni elemento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

  > [!Note]  
  > In alcuni casi le sessioni di sincronizzazione hanno un esito completamente negativo o hanno un PerItemErrorCount diverso da zero, ma compiono ancora progressi, ovvero alcuni file eseguono correttamente la sincronizzazione. Si può notare nei campi Applied (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes), che indicano quanti file della sessione vengono completati. Se vengono visualizzate più sessioni di sincronizzazione in una riga con esito negativo, ma con un numero crescente di Applied, è necessario assegnare l'ora della sincronizzazione per tentare nuovamente l'operazione prima di aprire un ticket di supporto.

- L'ID evento 9302 viene registrato ogni 5-10 minuti se è presente una sessione di sincronizzazione attiva. Questo evento deve essere usato per determinare se la sessione di sincronizzazione corrente è in stato di avanzamento (AppliedItemCount > 0). Se la sincronizzazione non è in stato di avanzamento, la sessione di sincronizzazione deve avere esito negativo e l'ID evento 9102 verrà registrati con l'errore. Per altre informazioni, vedere la documentazione seguente: [Stato sincronizzazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

Integrità del server registrato
- L'ID evento 9301 viene registrato ogni 30 secondi quando un server esegue query sul servizio per i processi. Se GetNextJob viene completata con stato = 0, il server è in grado di comunicare con il servizio. Se GetNextJob viene completata con un errore, verificare la [Documentazione sulla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) per indicazioni.

Integrità del cloud a livelli
- Per monitorare l'attività di suddivisione in livelli in un server, usare l'ID evento 9003, 9016 e 9029 nel registro eventi di telemetria (che si trova in Applicazioni e servizi\Microsoft\FileSync\Agente in Visualizzatore eventi).

  - L'ID evento 9003 fornisce inoltre la distribuzione di un errore per un endpoint server. Ad esempio, Total Error Count, ErrorCode e così via. Si noti che per ogni codice di errore viene registrato un evento.
  - L'ID evento 9016 fornisce inoltre risultati di ghosting per un volume. Ad esempio, percentuale di spazio disponibile, numero di file fantasma nella sessione, numero di file in cui non è stato possibile eseguire il ghosting e così via.
  - L'ID evento 9029 fornisce informazioni sulla sessione di ghosting per un endpoint server. Ad esempio, numero di file che hanno eseguito un tentativo nella sessione, numero di file archiviati a livelli nella sessione, numero di file già archiviati a livelli e così via.
  
- Per monitorare l'attività di richiamo in un server, usare l'ID evento 9005, 9006 e 9009 e 9059 nel registro eventi di telemetria (che si trova in Registri applicazioni e servizi\Microsoft\FileSync\Agente in Visualizzatore eventi).

  - L'ID evento 9005 offre affidabilità di richiamo per un endpoint server. Ad esempio, numero totale di file univoci a cui si ha avuto accesso, numero totale di file univoci con accessi non riusciti e così via.
  - L'ID evento 9006 fornisce inoltre la distribuzione di un errore di richiamo per un endpoint server. Ad esempio, Totale richieste non riuscite, ErrorCode e così via. Si noti che per ogni codice di errore viene registrato un evento.
  - L'ID evento 9009 fornisce informazioni sulla sessione di ghosting per un endpoint server. Ad esempio, DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, e così via.
  - L'ID evento 9059 fornisce la distribuzione di richiamo delle applicazioni per un endpoint server. Ad esempio, ShareId, nome dell'applicazione e TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Contatori delle prestazioni

Usare i contatori delle prestazioni di Sincronizzazione file di Azure nel server per monitorare l'attività di sincronizzazione.

Per visualizzare i contatori delle prestazioni di Sincronizzazione file di Azure nel server, avviare Monitoraggio delle prestazioni (Perfmon.exe) e i contatori sono disponibili in oggetti AFS byte trasferiti e operazioni di sincronizzazione AFS.

I contatori delle prestazioni seguenti per Sincronizzazione file di Azure sono disponibili in Monitoraggio delle prestazioni:

| Oggetto prestazioni\Nome contatore | DESCRIZIONE |
|-|-|
| Byte AFS trasferiti\Byte scaricati/sec | Numero di byte scaricati al secondo. |
| Byte AFS trasferiti\Byte caricati/sec | Numero di byte caricati al secondo. |
| Byte AFS trasferiti\Byte totali/sec | Byte totali al secondo (caricamento e download). |
| Operazioni di sincronizzazione AFS\file scaricati per la sincronizzazione/sec | Numero di file scaricati al secondo. |
| Operazioni di sincronizzazione AFS\file caricati per la sincronizzazione/sec | Numero di file caricati al secondo. |
| Operazioni di sincronizzazione AFS\operazioni di sincronizzazione file totali/sec | Numero totale di file sincronizzati (caricamento e download). |

## <a name="next-steps"></a>Passaggi successivi
- [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
- [Impostazioni di proxy e firewall di Sincronizzazione file di Azure](storage-sync-files-firewall-and-proxy.md)
- [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
- [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md)
- [Domande frequenti su File di Azure](storage-files-faq.md)
