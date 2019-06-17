---
title: Monitorare Sincronizzazione file di Azure | Microsoft Docs
description: Come monitorare Sincronizzazione file di Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c0f19e3ea4f5952ac96b589fa267a2136c85e4f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64711646"
---
# <a name="monitor-azure-file-sync"></a>Monitorare Sincronizzazione file di Azure

Usare Sincronizzazione file di Azure per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

Questo articolo descrive come monitorare la distribuzione di sincronizzazione File di Azure usando il portale di Azure e Windows Server.

Le opzioni di monitoraggio seguenti sono attualmente disponibili.

## <a name="azure-portal"></a>Portale di Azure

Nel portale di Azure, è possibile visualizzare lo stato di integrità dei server registrati, l'integrità dell'endpoint server (integrità di sincronizzazione) e le metriche.

### <a name="storage-sync-service"></a>Servizio di sincronizzazione archiviazione

Per visualizzare lo stato dei server registrati, integrità dell'endpoint server e le metriche, passare al servizio di sincronizzazione archiviazione nel portale di Azure. È possibile visualizzare l'integrità del server registrato nel **server registrati** integrità dell'endpoint server e pannello nel **gruppi di sincronizzazione** pannello.

Integrità del server registrato:

- Se il **server registrati** lo stato è **Online**, il server è stata comunicando con il servizio.
- Se il **server registrati** lo stato è **viene visualizzata Offline**, verificare che il processo di monitoraggio di sincronizzazione archiviazione (AzureStorageSyncMonitor.exe) nel server sia in esecuzione. Se il server si trova dietro un firewall o proxy, vedere [questo articolo](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) per configurare il firewall e proxy.

Integrità dell'endpoint server:

- L'integrità dell'endpoint server nel portale si basa sugli eventi di sincronizzazione che vengono registrati nel registro eventi di telemetria nel server (ID 9102 e 9302). Se una sessione di sincronizzazione non riesce a causa di un errore temporaneo, ad esempio errore annullata, sincronizzazione potrebbe continuare ad apparire integra nel portale, purché la sessione di sincronizzazione corrente è sta facendo progressi. ID evento 9302 consente di determinare se i file devono essere applicati. Per altre informazioni, vedere [integrità di sincronizzazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [sincronizzare lo stato di avanzamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Se il portale Visualizza un errore di sincronizzazione perché sincronizzazione mancato avanzamento, vedere la [risoluzione dei problemi documentazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) per materiale sussidiario.

Metriche:

- Nel portale di Servizi di sincronizzazione archiviazione possono essere visualizzate le metriche seguenti:

  | Nome metrica | Descrizione | Nome del pannello |
  |-|-|-|
  | Byte sincronizzati | Dimensioni dei dati trasferiti (caricamento e scaricamento) | Gruppo di sincronizzazione, Endpoint server |
  | Richiamo cloud a livelli | Dimensione dei dati richiamati | Server registrati |
  | File non sincronizzati | Numero di file che non è possibile sincronizzare | Endpoint server |
  | File sincronizzati | Numero di file trasferiti (caricamento e scaricamento) | Gruppo di sincronizzazione, Endpoint server |
  | Stato online del server | Numero di heartbeat ricevuti dal server | Server registrati |

- Per altre informazioni, vedere [monitoraggio di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > I grafici nel portale di Servizi di sincronizzazione archiviazione sono basati su un intervallo di tempo di 24 ore. Per visualizzare dimensioni o intervalli di tempo diversi, usare Monitoraggio di Azure.

### <a name="azure-monitor"></a>Monitoraggio di Azure

Usare [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) per monitorare la sincronizzazione, il cloud a livelli e la connettività del server. Le metriche per Sincronizzazione file di Azure sono abilitate per impostazione predefinita e vengono inviate al Monitoraggio di Azure ogni 15 minuti.

Per visualizzare le metriche di sincronizzazione File di Azure in Monitoraggio di Azure, selezionare la **servizi di sincronizzazione archiviazione** il tipo di risorsa.

Le metriche seguenti per la Sincronizzazione file di Azure sono disponibili in Monitoraggio di Azure:

| Nome metrica | Descrizione |
|-|-|
| Byte sincronizzati | Dimensioni dei dati trasferiti (caricamento e scaricamento).<br><br>Unità: Byte<br>Tipo di aggregazione: Sum<br>Dimensioni applicabili: Nome dell'endopoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |
| Richiamo cloud a livelli | Dimensioni dei dati richiamati.<br><br>Unità: Byte<br>Tipo di aggregazione: Sum<br>Dimensioni applicabili: Server Name |
| File non sincronizzati | Numero di file che non è possibile sincronizzare.<br><br>Unità: Conteggio<br>Tipo di aggregazione: Sum<br>Dimensioni applicabili: Nome dell'endopoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |
| File sincronizzati | Numero di file trasferiti (caricamento e scaricamento)<br><br>Unità: Conteggio<br>Tipo di aggregazione: Sum<br>Dimensioni applicabili: Nome dell'endopoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |
| Stato online del server | Numero di heartbeat ricevuti dal server.<br><br>Unità: Conteggio<br>Tipo di aggregazione: Massima<br>Dimensioni applicabili: Server Name |
| Risultato della sessione di sincronizzazione | Risultato della sessione di sincronizzazione (1 = sessione di sincronizzazione con esito positivo; 0 = sessione di sincronizzazione con esito negativo)<br><br>Unità: Conteggio<br>Tipi di aggregazione: Massima<br>Dimensioni applicabili: Nome dell'endopoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |

## <a name="windows-server"></a>Windows Server

In Windows Server, è possibile visualizzare i cloud a livelli, il server registrato e dell'integrità di sincronizzazione.

### <a name="event-logs"></a>Log eventi

Usare il registro eventi di telemetria nel server per monitorare l'integrità del server registrato, della sincronizzazione e del cloud a livelli. Nel Visualizzatore eventi in cui si trova nel registro eventi di telemetria *Applications e services\microsoft\filesync\agent.* .

Integrità di sincronizzazione:

- ID evento 9102 viene registrato dopo il completamento di una sessione di sincronizzazione. Utilizzare questo evento per determinare se sessioni di sincronizzazione hanno esito positivo (**HResult = 0**) errori di sincronizzazione e se sono disponibili per ogni elemento. Per altre informazioni, vedere la [integrità di sincronizzazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [errori per ogni elemento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) documentazione.

  > [!Note]  
  > In alcuni casi, complessivo di sincronizzazione hanno esito negativo le sessioni o hanno un PerItemErrorCount diverso da zero. Tuttavia, sono ancora fare progressi e alcuni file di sincronizzazione completata. È possibile verificarlo i campi applicati ad esempio AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes. Questi campi indicano in che misura la sessione ha avuto esito positivo. Se viene visualizzato più sessioni di sincronizzazione non riuscire in una riga, e hanno un conteggio applicato crescente, a tempo di sincronizzazione per ripetere l'operazione prima di aprire un ticket di supporto.

- L'ID evento 9302 viene registrato ogni 5-10 minuti se è presente una sessione di sincronizzazione attiva. Utilizzare questo evento per determinare se la sessione di sincronizzazione corrente sta avanzando (**AppliedItemCount > 0**). Se non sta avanzando sincronizzazione, la sessione di sincronizzazione deve avere esito negativo e 9102 un ID evento verranno registrati con l'errore. Per altre informazioni, vedere la [sincronizzazione documentazione continua](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Integrità del server registrato:

- L'ID evento 9301 viene registrato ogni 30 secondi quando un server esegue query sul servizio per i processi. Se GetNextJob termina con **stato = 0**, il server è in grado di comunicare con il servizio. Se GetNextJob termina con un errore, verificare i [risoluzione dei problemi documentazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) per materiale sussidiario.

Cloud di suddivisione in livelli di integrità:

- Per monitorare l'attività di suddivisione in livelli in un server, usare 9003 ID evento, evento 9016 e 9029 nel registro eventi, dati di telemetria che si trova nel Visualizzatore eventi *Applications e services\microsoft\filesync\agent.* .

  - L'ID evento 9003 fornisce inoltre la distribuzione di un errore per un endpoint server. Ad esempio: Numero totale errori e codice di errore. Per ogni codice di errore viene registrato un evento.
  - L'ID evento 9016 fornisce inoltre risultati di ghosting per un volume. Ad esempio:  Percentuale di spazio libero è, numero di file fantasma nella sessione e numero di file non è stato possibile fantasma.
  - L'ID evento 9029 fornisce informazioni sulla sessione di ghosting per un endpoint server. Ad esempio:  Numero di file ha tentato nella sessione, numero di file a livelli nella sessione e numero di file già archiviato a livelli.
  
- Per monitorare l'attività di richiamo su un server, usare 9005 ID evento, 9006, 9009 e nel registro eventi, dati di telemetria che si trova nel Visualizzatore eventi in 9059 *Applications e services\microsoft\filesync\agent.* .

  - L'ID evento 9005 offre affidabilità di richiamo per un endpoint server. Ad esempio: Numero totale di file univoco a cui si accede e totale file univoci con accessi non riusciti.
  - L'ID evento 9006 fornisce inoltre la distribuzione di un errore di richiamo per un endpoint server. Ad esempio:  Totale richieste non riuscite e codice di errore. Per ogni codice di errore viene registrato un evento.
  - L'ID evento 9009 fornisce informazioni sulla sessione di richiamo per un endpoint server. Ad esempio:  DurationSeconds CountFilesRecallSucceeded e CountFilesRecallFailed.
  - L'ID evento 9059 fornisce la distribuzione di richiamo delle applicazioni per un endpoint server. Ad esempio: ShareId, nome dell'applicazione e TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Contatori delle prestazioni

Usare i contatori delle prestazioni di Sincronizzazione file di Azure nel server per monitorare l'attività di sincronizzazione.

Per visualizzare i contatori delle prestazioni di sincronizzazione File di Azure nel server, aprire Performance Monitor (Perfmon.exe). È possibile trovare i contatori nel **AFS byte trasferiti** e **operazioni di sincronizzazione AFS** oggetti.

I contatori delle prestazioni seguenti per Sincronizzazione file di Azure sono disponibili in Monitoraggio delle prestazioni:

| Oggetto prestazioni\Nome contatore | Descrizione |
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
