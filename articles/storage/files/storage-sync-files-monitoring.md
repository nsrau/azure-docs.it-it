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
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699296"
---
# <a name="monitor-azure-file-sync"></a>Monitorare Sincronizzazione file di Azure

Usare Sincronizzazione file di Azure per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

Questo articolo descrive come monitorare la distribuzione di Sincronizzazione file di Azure usando monitoraggio di Azure, il servizio di sincronizzazione archiviazione e Windows Server.

Sono attualmente disponibili le opzioni di monitoraggio seguenti.

## <a name="azure-monitor"></a>Monitoraggio di Azure

Usare [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) per visualizzare le metriche e configurare gli avvisi per la sincronizzazione, la suddivisione in livelli cloud e la connettività del server.  

### <a name="metrics"></a>metrics

Le metriche per Sincronizzazione file di Azure sono abilitate per impostazione predefinita e vengono inviate al Monitoraggio di Azure ogni 15 minuti.

Per visualizzare Sincronizzazione file di Azure metriche in monitoraggio di Azure, selezionare il tipo di risorsa **servizi di sincronizzazione archiviazione** .

Le metriche seguenti per la Sincronizzazione file di Azure sono disponibili in Monitoraggio di Azure:

| Nome della metrica | Descrizione |
|-|-|
| Byte sincronizzati | Dimensioni dei dati trasferiti (caricamento e scaricamento).<br><br>Unità: Byte<br>Tipo di aggregazione: Sum<br>Dimensioni applicabili: Nome dell'endopoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |
| Richiamo cloud a livelli | Dimensioni dei dati richiamati.<br><br>**Nota**: Questa metrica verrà rimossa in futuro. Usare la metrica dimensione di richiamo di suddivisione in livelli nel cloud per monitorare le dimensioni dei dati richiamati.<br><br>Unità: Byte<br>Tipo di aggregazione: Sum<br>Dimensioni applicabili: Nome server |
| Dimensioni del richiamo di suddivisione in livelli nel cloud | Dimensioni dei dati richiamati.<br><br>Unità: Byte<br>Tipo di aggregazione: Sum<br>Dimensioni applicabili: Nome del server, nome del gruppo di sincronizzazione |
| Dimensioni del richiamo di suddivisione in livelli nel cloud per applicazione | Dimensioni dei dati richiamati dall'applicazione.<br><br>Unità: Byte<br>Tipo di aggregazione: Sum<br>Dimensioni applicabili: Nome dell'applicazione, nome del server, nome del gruppo di sincronizzazione |
| Velocità effettiva di richiamo di suddivisione in livelli nel cloud | Dimensioni della velocità effettiva di richiamo dei dati.<br><br>Unità: Byte<br>Tipo di aggregazione: Sum<br>Dimensioni applicabili: Nome del server, nome del gruppo di sincronizzazione |
| File non sincronizzati | Numero di file che non è possibile sincronizzare.<br><br>Unità: Conteggio<br>Tipo di aggregazione: Sum<br>Dimensioni applicabili: Nome dell'endopoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |
| File sincronizzati | Numero di file trasferiti (caricamento e scaricamento)<br><br>Unità: Conteggio<br>Tipo di aggregazione: Sum<br>Dimensioni applicabili: Nome dell'endopoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |
| Stato online del server | Numero di heartbeat ricevuti dal server.<br><br>Unità: Conteggio<br>Tipo di aggregazione: Massima<br>Dimensioni applicabili: Nome server |
| Risultato della sessione di sincronizzazione | Risultato della sessione di sincronizzazione (1 = sessione di sincronizzazione con esito positivo; 0 = sessione di sincronizzazione con esito negativo)<br><br>Unità: Conteggio<br>Tipi di aggregazione: Massima<br>Dimensioni applicabili: Nome dell'endopoint server, direzione sincronizzazione, nome gruppo di sincronizzazione |

### <a name="alerts"></a>Avvisi

Per configurare gli avvisi in monitoraggio di Azure, selezionare il servizio di sincronizzazione archiviazione, quindi selezionare la [metrica sincronizzazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) da usare per l'avviso.  

Nella tabella seguente sono elencati alcuni scenari di esempio da monitorare e la metrica appropriata da usare per l'avviso:

| Scenario | Metrica da usare per l'avviso |
|-|-|
| Integrità endpoint server nel portale = errore | Risultato della sessione di sincronizzazione |
| Impossibile sincronizzare i file in un endpoint server o cloud | File non sincronizzati |
| Il server registrato non riesce a comunicare con il servizio di sincronizzazione archiviazione | Stato online del server |
| Le dimensioni di richiamo per la suddivisione in livelli nel cloud hanno superato 500GiB in un giorno  | Dimensioni del richiamo di suddivisione in livelli nel cloud |

Per altre informazioni sulla configurazione degli avvisi in monitoraggio di Azure, vedere [Panoramica degli avvisi in Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="storage-sync-service"></a>Servizio di sincronizzazione archiviazione

Per visualizzare l'integrità del server registrato, l'integrità dell'endpoint server e le metriche, passare al servizio di sincronizzazione archiviazione nel portale di Azure. È possibile visualizzare l'integrità del server registrato nel pannello **Server registrati** e integrità endpoint server nel pannello **gruppi di sincronizzazione** .

### <a name="registered-server-health"></a>Stato server registrato

- Se lo stato del **server registrato** è **online**, il server comunica correttamente con il servizio.
- Se lo stato del **server registrato** viene **visualizzato offline**, verificare che il processo di monitoraggio di sincronizzazione archiviazione (AzureStorageSyncMonitor. exe) nel server sia in esecuzione. Se il server è dietro un firewall o un proxy, vedere [questo articolo](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) per configurare il firewall e il proxy.

### <a name="server-endpoint-health"></a>Integrità endpoint server

- L'integrità dell'endpoint server nel portale si basa sugli eventi di sincronizzazione che vengono registrati nel registro eventi di telemetria nel server (ID 9102 e 9302). Se una sessione di sincronizzazione ha esito negativo a causa di un errore temporaneo, ad esempio l'errore è stato annullato, la sincronizzazione potrebbe essere ancora integra nel portale finché la sessione di sincronizzazione corrente sta procedendo. L'ID evento 9302 viene usato per determinare se i file vengono applicati. Per ulteriori informazioni, vedere [Sync Health](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) and [Sync Progress](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Se nel portale viene visualizzato un errore di sincronizzazione perché la sincronizzazione non sta procedendo, vedere la documentazione relativa alla [risoluzione dei problemi](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) per informazioni aggiuntive.

### <a name="metric-charts"></a>Grafici delle metriche

- I grafici delle metriche seguenti sono visualizzabili nel portale del servizio di sincronizzazione archiviazione:

  | Nome della metrica | DESCRIZIONE | Nome pannello |
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

In Windows Server è possibile visualizzare la suddivisione in livelli nel cloud, il server registrato e l'integrità della sincronizzazione.

### <a name="event-logs"></a>Log eventi

Usare il registro eventi di telemetria nel server per monitorare l'integrità del server registrato, della sincronizzazione e del cloud a livelli. Il registro eventi di telemetria si trova in Visualizzatore eventi in *Applications and Services\Microsoft\FileSync\Agent*.

Integrità sincronizzazione:

- L'ID evento 9102 viene registrato al termine di una sessione di sincronizzazione. Utilizzare questo evento per determinare se le sessioni di sincronizzazione hanno esito positivo (**HRESULT = 0**) e se sono presenti errori di sincronizzazione per elemento. Per ulteriori informazioni, vedere la documentazione sull' [integrità della sincronizzazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e sugli [errori per elemento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) .

  > [!Note]  
  > A volte le sessioni di sincronizzazione hanno esito negativo globale o hanno un PerItemErrorCount diverso da zero. Tuttavia, continuano a avanzare e alcuni file vengono sincronizzati correttamente. È possibile visualizzarlo nei campi applicati, ad esempio AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes. Questi campi indicano la parte della sessione completata. Se più sessioni di sincronizzazione hanno esito negativo in una riga e hanno un conteggio applicato crescente, fornire tempo di sincronizzazione per riprovare prima di aprire un ticket di supporto.

- L'ID evento 9302 viene registrato ogni 5-10 minuti se è presente una sessione di sincronizzazione attiva. Utilizzare questo evento per determinare se la sessione di sincronizzazione corrente sta procedendo (**AppliedItemCount > 0**). Se la sincronizzazione non è in corso, la sessione di sincronizzazione dovrebbe avere esito negativo e verrà registrato un ID evento 9102 con l'errore. Per ulteriori informazioni, vedere la [documentazione sullo stato di avanzamento della sincronizzazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Stato server registrato:

- L'ID evento 9301 viene registrato ogni 30 secondi quando un server esegue query sul servizio per i processi. Se GetNextJob termina con **status = 0**, il server è in grado di comunicare con il servizio. Se GetNextJob termina con un errore, vedere la [documentazione relativa alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) per informazioni aggiuntive.

Integrità di suddivisione in livelli nel cloud:

- Per monitorare l'attività di suddivisione in livelli in un server, usare l'ID evento 9003, 9016 e 9029 nel registro eventi di telemetria, disponibile in Visualizzatore eventi in *Applications and Services\Microsoft\FileSync\Agent*.

  - L'ID evento 9003 fornisce inoltre la distribuzione di un errore per un endpoint server. Ad esempio:  Numero totale di errori e ErrorCode. Un evento viene registrato per codice di errore.
  - L'ID evento 9016 fornisce inoltre risultati di ghosting per un volume. Ad esempio:  Percentuale di spazio disponibile, numero di file fantasma nella sessione e numero di file non riusciti a fantasma.
  - L'ID evento 9029 fornisce informazioni sulla sessione di ghosting per un endpoint server. Ad esempio:  Numero di file tentati nella sessione, numero di file a livelli nella sessione e numero di file già a livelli.
  
- Per monitorare l'attività di richiamo in un server, usare l'ID evento 9005, 9006, 9009 e 9059 nel registro eventi di telemetria, disponibile in Visualizzatore eventi in *Applications and Services\Microsoft\FileSync\Agent*.

  - L'ID evento 9005 offre affidabilità di richiamo per un endpoint server. Ad esempio:  Totale file univoci a cui si accede e totale file univoci con accesso non riuscito.
  - L'ID evento 9006 fornisce inoltre la distribuzione di un errore di richiamo per un endpoint server. Ad esempio:  Totale richieste non riuscite e ErrorCode. Un evento viene registrato per codice di errore.
  - L'ID evento 9009 fornisce informazioni sulla sessione di richiamo per un endpoint server. Ad esempio:  DurationSeconds, CountFilesRecallSucceeded e CountFilesRecallFailed.
  - L'ID evento 9059 fornisce la distribuzione di richiamo delle applicazioni per un endpoint server. Ad esempio:  ShareId, nome dell'applicazione e TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Contatori delle prestazioni

Usare i contatori delle prestazioni di Sincronizzazione file di Azure nel server per monitorare l'attività di sincronizzazione.

Per visualizzare Sincronizzazione file di Azure contatori delle prestazioni nel server, aprire Performance Monitor (Perfmon. exe). È possibile trovare i contatori negli oggetti **operazioni di sincronizzazione** AFS e **trasferiti** .

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
