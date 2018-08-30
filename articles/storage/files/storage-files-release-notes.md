---
title: Note sulla versione dell'agente Sincronizzazione file di Azure | Microsoft Docs
description: Note sulla versione dell'agente Sincronizzazione file di Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 08/21/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 3cd178333ee0d8d92db08fb08cbd02b05112f58b
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2018
ms.locfileid: "42445023"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Note sulla versione dell'agente Sincronizzazione file di Azure
Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Le installazioni Windows Server vengono trasformate in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS e FTPS. Si può usare qualsiasi numero di cache in tutto il mondo.

Questo articolo illustra le note sulla versione per le versioni supportate dell'agente Sincronizzazione file di Azure.

## <a name="supported-versions"></a>Versioni supportate
L'agente Sincronizzazione file di Azure supporta le versioni seguenti:

| Attività cardine | Numero di versione dell'agente | Data di rilascio | Status |
|----|----------------------|--------------|------------------|
| Aggiornamento cumulativo di agosto | 3.2.0.0 | 15 agosto 2018 | Supportato (versione consigliata) |
| Disponibilità generale | 3.1.0.0 | 19 Luglio 2018 | Supportato |
| Aggiornamento cumulativo di giugno | 3.0.13.0 | 29 giugno 2018 | La versione dell'agente scadrà il 4 settembre 2018 |
| Aggiornamento 2 | 3.0.12.0 | 22 maggio 2018 | La versione dell'agente scadrà il 4 settembre 2018 |
| Aggiornamento cumulativo di aprile | 2.3.0.0 | 8 maggio 2018 | La versione dell'agente scadrà il 4 settembre 2018 |
| Aggiornamento cumulativo di marzo | 2.2.0.0 | 12 marzo 2018 | La versione dell'agente scadrà il 4 settembre 2018 |
| Aggiornamento cumulativo di febbraio | 2.1.0.0 | 28 febbraio 2018 | La versione dell'agente scadrà il 4 settembre 2018 |
| Aggiornamento 1 | 2.0.11.0 | 8 febbraio 2018 | La versione dell'agente scadrà il 4 settembre 2018 |
| Aggiornamento cumulativo di gennaio | 1.4.0.0 | 8 gennaio 2018 | La versione dell'agente scadrà il 4 settembre 2018 |
| Aggiornamento cumulativo di novembre | 1.3.0.0 | 30 novembre 2017 | La versione dell'agente scadrà il 4 settembre 2018 |
| Aggiornamento cumulativo di ottobre | 1.2.0.0 | 31 ottobre 2017 | La versione dell'agente scadrà il 4 settembre 2018 |
| Versione di anteprima iniziale | 1.1.0.0 | 26 settembre 2017 | La versione dell'agente scadrà il 4 settembre 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-3200"></a>Agente versione 3.2.0.0
Le note seguenti sulla versione si riferiscono alla versione 3.2.0.0 dell'agente Sincronizzazione file di Azure rilasciata il 15 agosto 2018. Queste note si aggiungono a quelle elencate per la versione 3.1.0.0.

Questa versione include la correzione seguente:
- La sincronizzazione ha esito negativo con un errore di memoria insufficiente (0x8007000e) a causa della perdita di memoria

## <a name="agent-version-3100"></a>Agente versione 3.1.0.0
Le note seguenti sulla versione si riferiscono alla versione 3.1.0.0 dell'agente Sincronizzazione file di Azure (data di rilascio 19 luglio 2018).

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nelle opzioni di distribuzione Windows Server Core o Nano Server.
- L'agente è supportato solo in Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GB di memoria fisica.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Non usare Gestione risorse file server o altri screening dei file. Gli screening dei file possono generare errori di sincronizzazione infinita se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'installazione dell'agente e la registrazione del server devono avvenire dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.
- La deduplicazione dei dati e la suddivisione in livelli cloud non sono supportate nello stesso volume.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inutilizzabili se non vengono richiamati prima di eliminare l'endpoint server.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare il file di paging di un sistema operativo o di un'applicazione in un endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato. Per aggiornare il nome del server nel portale, annullare la registrazione e registrare nuovamente il server.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o account di archiviazione in un gruppo di risorse o in una sottoscrizione diversi. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.

## <a name="agent-version-30130"></a>Agente versione 3.0.13.0
Le note seguenti sulla versione si riferiscono alla versione 3.0.13.0 dell'agente di Sincronizzazione file di Azure (data di rilascio 29 giugno 2018). Queste note si aggiungono a quelle elencate per la versione 3.0.12.0.

Questa versione include la correzione seguente:
- La sincronizzazione ha esito negativo se si aggiunge un server a un gruppo di sincronizzazione esistente quando nel percorso dell'endpoint del server esistono reparse point.

## <a name="agent-version-30120"></a>Agente versione 3.0.12.0
Le note sulla versione seguenti si riferiscono alla versione 3.0.12.0 dell'agente di Sincronizzazione file di Azure (data di rilascio 22 maggio 2018).

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nelle opzioni di distribuzione Windows Server Core o Nano Server.
- L'agente è supportato solo in Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GB di memoria fisica.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Non usare Gestione risorse file server o altri screening dei file. Gli screening dei file possono generare errori di sincronizzazione infinita se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'installazione dell'agente e la registrazione del server devono avvenire dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.
- La deduplicazione dei dati e la suddivisione in livelli cloud non sono supportate nello stesso volume.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati. 
    
    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoints"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare il file di paging di un sistema operativo o di un'applicazione in un endpoint server.
- I file a più livelli diventano inutilizzabili se non vengono richiamati prima di eliminare l'endpoint server.
 
### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.

## <a name="agent-version-2300"></a>Agente versione 2.3.0.0
Le note sulla versione seguenti si riferiscono alla versione 2.3.0.0 dell'agente di Sincronizzazione file di Azure (data di rilascio 8 maggio 2018). Queste note si aggiungono a quelle elencate per la versione 2.0.11.0.

Questa versione include le correzioni seguenti:
- Gli aggiornamenti dell'agente potrebbero bloccarsi se non viene scaricato il driver del filtro di suddivisione in livelli cloud.
- Durante la sincronizzazione di un numero elevato di file, le prestazioni della sincronizzazione potrebbero peggiorare.

## <a name="agent-version-2200"></a>Agente versione 2.2.0.0
Le note seguenti si riferiscono alla versione 2.2.0.0 dell'agente Sincronizzazione file di Azure resa disponibile il 12 marzo 2018.  Queste note si aggiungono a quelle elencate per le versioni 2.1.0.0 e 2.0.11.0

L'installazione della versione 2.1.0.0 per alcuni clienti avrà esito negativo a causa del mancato arresto di FileSyncSvc. Questo aggiornamento contiene una correzione per il problema.

## <a name="agent-version-2100"></a>Agente versione 2.1.0.0
Le note seguenti si riferiscono alla versione 2.1.0.0 dell'agente Sincronizzazione file di Azure resa disponibile il 28 febbraio 2018. Queste note si aggiungono a quelle elencate per la versione 2.0.11.0.

Questa versione include le modifiche seguenti:
- Miglioramenti alla gestione del failover del cluster.
- Miglioramenti per la gestione affidabile dei file a livelli.
- Supporto per l'installazione dell'agente nei computer controller di dominio aggiunti a un ambiente di dominio Windows Server 2008 R2.
- Corretta in questa versione la generazione di una quantità eccessiva di dati di diagnostica in server con molti file.
- Miglioramenti nella gestione degli errori delle sessioni.
- Miglioramenti nella gestione degli errori dei trasferimenti di file.
- Modifica in questa versione: se abilitata in un endpoint del server, l'intervallo predefinito per l'esecuzione della suddivisione in livelli nel cloud è adesso di 1 ora. 
- Problema di blocco temporaneo: spostamento delle risorse di Sincronizzazione file di Azure (Servizio di sincronizzazione archiviazione) in una nuova sottoscrizione di Azure.

## <a name="agent-version-20110"></a>Agente versione 2.0.11.0
Le note seguenti si riferiscono alla versione 2.0.11.0 dell'agente Sincronizzazione file di Azure resa disponibile il 9 febbraio 2018. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione (MSI) dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nelle opzioni di distribuzione Windows Server Core o Nano Server.
- L'agente è supportato solo in Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GB di memoria fisica.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Questa versione aggiunge il supporto per DFS-R. Per altre informazioni, vedere la [guida alla pianificazione](storage-sync-files-planning.md#distributed-file-system-dfs).
- Non usare Gestione risorse file server o altri screening dei file. Gli screening dei file possono generare errori di sincronizzazione infinita se i file sono bloccati a causa dello screening.
- La duplicazione di server registrati (inclusa la clonazione di VM) può produrre risultati imprevisti, in particolare è possibile che la sincronizzazione non converga mai.
- La deduplicazione dei dati e la suddivisione in livelli cloud non sono supportate nello stesso volume.
 
### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati. 
    
    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoints"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- Un endpoint server non può trovarsi nel volume di sistema, ad esempio C:\CartellaUtente non è un percorso accettabile a meno che C:\CartellaUtente non sia un punto di montaggio.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- In questa versione è stato aggiunto il supporto per il percorso radice di sincronizzazione in corrispondenza della radice di un volume.
- Non archiviare il file di paging di un sistema operativo o di un'applicazione in un endpoint server.
- Modifica in questa versione: aggiunta di nuovi eventi per tenere traccia del runtime totale per la suddivisione in livelli nel cloud (ID evento 9016), dello stato di caricamento della sincronizzazione (ID evento 9302) e dei file che non sono stati sincronizzati (ID evento 9900).
- Funzionalità migliorate in questa versione: 
- Notevole miglioramento delle prestazioni di sincronizzazione degli spazi dei nomi per il ripristino di emergenza.
- L'eliminazione di grandi quantità di directory (più di 10.000) non deve più essere eseguita in batch con la versione 2*.
 
### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Modifica rispetto alla versione precedente: i nuovi file vengono suddivisi in livelli entro 1 ora (in precedenza in 32 ore) a seconda dell'impostazione dei criteri di suddivisione in livelli. Su richiesta viene fornito un cmdlet di PowerShell per la suddivisione in livelli. È possibile usare il cmdlet per valutare la suddivisione in livelli in modo più efficiente senza attendere il processo in background.
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.
- Modifica rispetto alla versione precedente: i file vengono ora scaricati come file a livelli in altri server, a condizione che il file sia nuovo o che sia già un file a livelli.

## <a name="agent-version-1100"></a>Agente versione 1.1.0.0
Le note seguenti si riferiscono alla versione 1.1.0.0 dell'agente Sincronizzazione file di Azure resa disponibile il 9 settembre 2017 in versione di anteprima iniziale. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione (MSI) dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nelle opzioni di distribuzione Windows Server Core o Nano Server.
- L'agente è supportato solo in Windows Server 2016 e 2012 R2.
- L'agente richiede almeno 2 GB di memoria fisica.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Non usare Gestione risorse file server o altri screening dei file. Gli screening dei file possono generare errori di sincronizzazione infinita se i file sono bloccati a causa dello screening.
- La duplicazione di server registrati (inclusa la clonazione di VM) può produrre risultati imprevisti, in particolare è possibile che la sincronizzazione non converga mai.
- La deduplicazione dei dati e la suddivisione in livelli cloud non sono supportate nello stesso volume.
 
### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte SACL di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati. 
    
    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoints"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- Un endpoint server non può trovarsi nel volume di sistema, ad esempio C:\CartellaUtente non è un percorso accettabile a meno che C:\CartellaUtente non sia un punto di montaggio.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- L'eliminazione di un numero elevato di directory (più di 10.000) da un server in una sola volta può provocare errori di sincronizzazione. Eliminare le directory in batch contenenti meno di 10.000 directory. Verificare che le operazioni di eliminazione siano state sincronizzate correttamente prima di eliminare il batch successivo.
- Un endpoint server alla radice di un volume non è attualmente supportato.
- Non archiviare il file di paging di un sistema operativo o di un'applicazione in un endpoint server.
 
### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Per assicurare che i file possano essere richiamati in modo corretto, il sistema potrebbe non suddividere automaticamente in livelli i file nuovi o modificati per un massimo di 32 ore. Questo processo include la prima suddivisione in livelli dopo la configurazione di un nuovo endpoint server. Su richiesta viene fornito un cmdlet di PowerShell per la suddivisione in livelli. È possibile usare il cmdlet per valutare la suddivisione in livelli in modo più efficiente senza attendere il processo in background.
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.
