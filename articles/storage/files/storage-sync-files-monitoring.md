---
title: Monitorare Sincronizzazione file di Azure | Microsoft Docs
description: Come monitorare Sincronizzazione file di Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ac09f9b59bc6f47adc9311cc910352c1a0d73b5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699296"
---
# <a name="monitor-azure-file-sync"></a>Monitorare Sincronizzazione file di Azure

Usare Sincronizzazione file di Azure per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

Questo articolo descrive come monitorare la distribuzione di Sincronizzazione file di Azure usando Monitoraggio di Azure, Servizio di sincronizzazione archiviazione e Windows Server.This article describes how to monitor your Azure File Sync deployment by using Azure Monitor, Storage Sync Service and Windows Server.

Sono attualmente disponibili le seguenti opzioni di monitoraggio.

## <a name="azure-monitor"></a>Monitoraggio di Azure

Usare [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) per visualizzare le metriche e configurare gli avvisi per la sincronizzazione, la suddivisione in livelli cloud e la connettività del server.  

### <a name="metrics"></a>Metriche

Le metriche per Sincronizzazione file di Azure sono abilitate per impostazione predefinita e vengono inviate al Monitoraggio di Azure ogni 15 minuti.

Per visualizzare le metriche di Sincronizzazione file di Azure in Monitoraggio di Azure, selezionare il tipo di risorsa **Servizi di sincronizzazione archiviazione.**

Le metriche seguenti per la Sincronizzazione file di Azure sono disponibili in Monitoraggio di Azure:

| Nome metrica | Descrizione |
|-|-|
| Byte sincronizzati | Dimensioni dei dati trasferiti (caricamento e scaricamento).<br><br>Unità: byte<br>Tipo di aggregazione: Somma<br>Dimensioni applicabili: Nome endpoint server, Direzione sincronizzazione, Nome gruppo di sincronizzazione |
| Richiamo cloud a livelli | Dimensioni dei dati richiamati.<br><br>**Nota:** questa metrica verrà rimossa in futuro. Utilizzare la metrica Dimensione di richiamo suddivisione in livelli cloud per monitorare le dimensioni dei dati richiamati.<br><br>Unità: byte<br>Tipo di aggregazione: Somma<br>Dimensione applicabile: Nome server |
| Dimensioni di richiamo livelli cloudCloud tiering recall size | Dimensioni dei dati richiamati.<br><br>Unità: byte<br>Tipo di aggregazione: Somma<br>Dimensione applicabile: Nome server, Nome gruppo di sincronizzazione |
| Dimensione del richiamo della suddivisione in livelli cloud per applicazioneCloud tiering recall size by application | Dimensioni dei dati richiamati dall'applicazione.<br><br>Unità: byte<br>Tipo di aggregazione: Somma<br>Dimensione applicabile: Nome applicazione, Nome server, Nome gruppo di sincronizzazione |
| Velocità effettiva di richiamata dei livelli cloudCloud tiering recall throughput | Dimensioni della velocità effettiva di richiamo dei dati.<br><br>Unità: byte<br>Tipo di aggregazione: Somma<br>Dimensione applicabile: Nome server, Nome gruppo di sincronizzazione |
| File non sincronizzati | Numero di file che non è possibile sincronizzare.<br><br>Unità: conteggio<br>Tipo di aggregazione: Somma<br>Dimensioni applicabili: Nome endpoint server, Direzione sincronizzazione, Nome gruppo di sincronizzazione |
| File sincronizzati | Numero di file trasferiti (caricamento e scaricamento)<br><br>Unità: conteggio<br>Tipo di aggregazione: Somma<br>Dimensioni applicabili: Nome endpoint server, Direzione sincronizzazione, Nome gruppo di sincronizzazione |
| Stato online del server | Numero di heartbeat ricevuti dal server.<br><br>Unità: conteggio<br>Tipo di aggregazione: massimo<br>Dimensione applicabile: Nome server |
| Risultato della sessione di sincronizzazione | Risultato della sessione di sincronizzazione (1 = sessione di sincronizzazione con esito positivo; 0 = sessione di sincronizzazione con esito negativo)<br><br>Unità: conteggio<br>Tipi di aggregazione: Massimo<br>Dimensioni applicabili: Nome endpoint server, Direzione sincronizzazione, Nome gruppo di sincronizzazione |

### <a name="alerts"></a>Avvisi

Per configurare gli avvisi in Monitoraggio di Azure, selezionare il servizio di sincronizzazione archiviazione e quindi selezionare la [metrica Sincronizzazione file](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) di Azure da usare per l'avviso.  

Nella tabella seguente sono elencati alcuni scenari di esempio da monitorare e la metrica appropriata da utilizzare per l'avviso:The following table lists some example scenarios to monitor and the proper metric to use for the alert:

| Scenario | Metrica da utilizzare per gli avvisi |
|-|-|
| Integrità dell'endpoint server nel portale - Errore | Risultato della sessione di sincronizzazione |
| La sincronizzazione dei file con un endpoint server o cloud | File non sincronizzati |
| Il server registrato non riesce a comunicare con il servizio di sincronizzazione archiviazione | Stato online del server |
| La dimensione del richiamo a livelli cloud ha superato 500GiB in un giorno  | Dimensioni di richiamo livelli cloudCloud tiering recall size |

Per altre informazioni sulla configurazione degli avvisi in Monitoraggio di Azure, vedere [Panoramica degli avvisi in Microsoft Azure.To]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)learn more about configuring alerts in Azure Monitor, see Overview of alerts in Microsoft Azure.

## <a name="storage-sync-service"></a>Servizio di sincronizzazione archiviazione

Per visualizzare l'integrità del server registrato, l'integrità degli endpoint del server e le metriche, passare al servizio di sincronizzazione archiviazione nel portale di Azure.To view registered server health, server endpoint health, and metrics, go to the Storage Sync Service in the Azure portal. È possibile visualizzare l'integrità del server registrato nel pannello **Server registrati** e l'integrità degli endpoint server nel pannello **Gruppi di sincronizzazione.**

### <a name="registered-server-health"></a>Integrità del server registrato

- Se lo stato **del server registrato** è **Online**, il server sta comunicando correttamente con il servizio.
- Se lo stato **del server registrato** è Visualizzato **offline**, verificare che il processo monitoraggio sincronizzazione archiviazione (AzureStorageSyncMonitor.exe) nel server sia in esecuzione. Se il server è protetto da un firewall o un proxy, vedere [questo articolo](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) per configurare il firewall e il proxy.

### <a name="server-endpoint-health"></a>Integrità degli endpoint server

- L'integrità dell'endpoint server nel portale si basa sugli eventi di sincronizzazione che vengono registrati nel registro eventi di telemetria nel server (ID 9102 e 9302). Se una sessione di sincronizzazione non riesce a causa di un errore temporaneo, ad esempio l'errore annullato, la sincronizzazione potrebbe comunque apparire integro nel portale finché la sessione di sincronizzazione corrente sta avanzando. L'ID evento 9302 viene utilizzato per determinare se i file vengono applicati. Per ulteriori informazioni, consultate [Integrità della sincronizzazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [Stato della sincronizzazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Se il portale mostra un errore di sincronizzazione perché la sincronizzazione non sta procedendo, vedere la documentazione per la [risoluzione dei problemi](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) per istruzioni.

### <a name="metric-charts"></a>Grafici metrici

- I grafici delle metriche seguenti sono visualizzabili nel portale del servizio di sincronizzazione archiviazione:The following metric charts are viewable in the Storage Sync Service portal:

  | Nome metrica | Descrizione | Nome della lama |
  |-|-|-|
  | Byte sincronizzati | Dimensioni dei dati trasferiti (caricamento e scaricamento) | Gruppo di sincronizzazione, Endpoint server |
  | Richiamo cloud a livelli | Dimensione dei dati richiamati | Server registrati |
  | File non sincronizzati | Numero di file che non è possibile sincronizzare | Endpoint server |
  | File sincronizzati | Numero di file trasferiti (caricamento e scaricamento) | Gruppo di sincronizzazione, Endpoint server |
  | Stato online del server | Numero di heartbeat ricevuti dal server | Server registrati |

- Per altre informazioni, vedere Monitoraggio di Azure .To learn more, see [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > I grafici nel portale di Servizi di sincronizzazione archiviazione sono basati su un intervallo di tempo di 24 ore. Per visualizzare dimensioni o intervalli di tempo diversi, usare Monitoraggio di Azure.

## <a name="windows-server"></a>Windows Server

In Windows Server è possibile visualizzare la suddivisione in livelli cloud, il server registrato e l'integrità della sincronizzazione.

### <a name="event-logs"></a>Log eventi

Usare il registro eventi di telemetria nel server per monitorare l'integrità del server registrato, della sincronizzazione e del cloud a livelli. Il registro eventi di telemetria si trova nel Visualizzatore eventi in *Applicazioni e servizi , ovvero Microsoft , FileSync e Agente*.

Integrità sincronizzazione:

- ID evento 9102 viene registrato al termine di una sessione di sincronizzazione. Utilizzare questo evento per determinare se le sessioni di sincronizzazione hanno esito positivo (**HResult 0**) e se sono presenti errori di sincronizzazione per elemento. Per altre informazioni, vedere la documentazione relativa [all'integrità della sincronizzazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e gli [errori per elemento.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

  > [!Note]  
  > A volte le sessioni di sincronizzazione non riescono nel complesso o hanno un PerItemErrorCount diverso da zero. Tuttavia, fanno ancora avanzare e alcuni file vengono sincronizzati correttamente. È possibile visualizzare questo nei campi Applied come AppliedFileCount, AppliedDirCount, AppliedTombstoneCount, e AppliedSizeBytes.You can see this in the Applied fields such as AppliedFileCount, AppliedDirCount, AppliedTombstoneCount, and AppliedSizeBytes. Questi campi indicano la quantità di sessione riuscita. Se in una riga vengono visualizzate più sessioni di sincronizzazione che hanno un numero di sessioni applicate crescenti, concedere il tempo di sincronizzazione per riprovare prima di aprire un ticket di supporto.

- L'ID evento 9302 viene registrato ogni 5-10 minuti se è presente una sessione di sincronizzazione attiva. Utilizzare questo evento per determinare se la sessione di sincronizzazione corrente sta avanzando (**AppliedItemCount > 0**). Se la sincronizzazione non sta facendo progressi, la sessione di sincronizzazione dovrebbe alla fine non riuscire e un ID evento 9102 verrà registrato con l'errore. Per ulteriori informazioni, vedere la [documentazione sullo stato di sincronizzazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Integrità server registrato:

- L'ID evento 9301 viene registrato ogni 30 secondi quando un server esegue query sul servizio per i processi. Se GetNextJob termina con **lo stato è 0**, il server è in grado di comunicare con il servizio. Se GetNextJob termina con un errore, consultare la documentazione per la [risoluzione dei problemi](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) per istruzioni.

Integrità a livelli cloud:Cloud tiering health:

- Per monitorare l'attività di suddivisione in livelli in un server, utilizzare l'ID evento 9003, 9016 e 9029 nel registro eventi di telemetria, che si trova nel Visualizzatore eventi in *Applicazioni e servizi .*

  - L'ID evento 9003 fornisce inoltre la distribuzione di un errore per un endpoint server. Ad esempio: Total Error Count e ErrorCode. Viene registrato un evento per ogni codice di errore.
  - L'ID evento 9016 fornisce inoltre risultati di ghosting per un volume. Ad esempio: la percentuale di spazio libero è, il numero di file rientrati nella sessione e il numero di file non è stato in grado di indicizzare.
  - L'ID evento 9029 fornisce informazioni sulla sessione di ghosting per un endpoint server. Ad esempio: Numero di file tentati nella sessione, Numero di file a livelli nella sessione e Numero di file già a livelli.
  
- Per monitorare l'attività di richiamo su un server, utilizzare l'ID evento 9005, 9006, 9009 e 9059 nel registro eventi di telemetria, che si trova nel Visualizzatore eventi in *Applicazioni e servizi*.

  - L'ID evento 9005 offre affidabilità di richiamo per un endpoint server. Ad esempio: totale dei file univoci a cui si accede e Totale file univoci con accesso non riuscito.
  - L'ID evento 9006 fornisce inoltre la distribuzione di un errore di richiamo per un endpoint server. Ad esempio: Totale richieste non riuscite e ErrorCode.For example: Total Failed Requests, and ErrorCode. Viene registrato un evento per ogni codice di errore.
  - L'ID evento 9009 fornisce informazioni sulla sessione di richiamo per un endpoint server. Ad esempio: DurationSeconds, CountFilesRecallSucceeded e CountFilesRecallFailed.
  - L'ID evento 9059 fornisce la distribuzione di richiamo delle applicazioni per un endpoint server. Ad esempio: ShareId, Nome applicazione e TotalEgressNetworkBytes.For example: ShareId, Application Name, and TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Contatori delle prestazioni

Usare i contatori delle prestazioni di Sincronizzazione file di Azure nel server per monitorare l'attività di sincronizzazione.

Per visualizzare i contatori delle prestazioni di Sincronizzazione file di Azure nel server, aprire Performance Monitor (Perfmon.exe). È possibile trovare i contatori negli oggetti **Byte AFS trasferiti** e **Operazioni di sincronizzazione AFS.**

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
- [Pianificazione di una distribuzione di Sincronizzazione file di AzurePlanning for an Azure File Sync deployment](storage-sync-files-planning.md)
- [Impostazioni di proxy e firewall di Sincronizzazione file di Azure](storage-sync-files-firewall-and-proxy.md)
- [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
- [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md)
- [Domande frequenti su File di Azure](storage-files-faq.md)
