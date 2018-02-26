---
title: Note sulla versione dell'agente di Sincronizzazione file di Azure | Microsoft Docs
description: Note sulla versione di Sincronizzazione file di Azure
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: tamram
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 9b6dfec6465482efcbf55d0441e44a0278f44a22
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="azure-file-sync-agent-release-notes"></a>Note sulla versione dell'agente di Sincronizzazione file di Azure
Sincronizzazione file di Azure (anteprima) consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Tutto questo avviene trasformando i sistemi Windows Server in una cache rapida della condivisione file di Azure. È possibile usare qualsiasi protocollo disponibile in Windows Server per accedere ai dati in locale (tra cui SMB, NFS e FTPS) ed è possibile scegliere tutte le cache necessarie in tutto il mondo.

Questo articolo illustra le note sulla versione per le versioni supportate dell'agente di Sincronizzazione file di Azure.

## <a name="supported-versions"></a>Versioni supportate
Sincronizzazione file di Azure supporta le versioni seguenti:

| Numero di versione dell'agente | Data di rilascio | Ultima versione supportata |
|----------------------|--------------|------------------|
| 2.0.11.0 | 2018-02-08 | Versione corrente |
| 1.1.0.0 | 2017-09-26 | 2018-07-30 |

### <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-20110"></a>Agente versione 2.0.11.0
Le note sulla versione seguenti sono relative alla versione 2.0.11.0 dell'agente, resa disponibile il 9 febbraio 2018. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente di Sincronizzazione file di Azure con un server Windows, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure (anteprima)](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure (anteprima)](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione (MSI) dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- Non supportato nelle opzioni di distribuzione Windows Server Core o Nano Server.
- Supportato solo in Windows Server 2016 e 2012 R2.
- L'agente richiede almeno 2 GB di memoria fisica.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato a meno che rispettino l'attributo offline e ignorino il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure (anteprima)](storage-sync-files-troubleshoot.md).
- Come novità di questo aggiornamento, è ora supportato DFS-R.  Per altre informazioni, vedere la [guida alla pianificazione](storage-sync-files-planning.md#distributed-file-system-dfs).
- Non usare gli screening dei file Gestione risorse file server (o altro): gli screening dei file possono generare errori di sincronizzazione dei dati infinita se i file vengono bloccati a causa dello screening dei file.
- La deduplicazione di un server registrato (inclusa la clonazione di una VM) potrebbe causare risultati imprevisti (in particolare, è possibile che la sincronizzazione non converga mai).
- La deduplicazione dei dati e la suddivisione in livelli cloud non sono supportate nello stesso volume.
 
### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- Percorsi che contengono più di 2048 caratteri
- Parte DACL di un descrittore di sicurezza se superiore a 2 KB (il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento)
- Parte SACL di un descrittore di sicurezza (usato per l'auditing)
- Attributi estesi
- Flussi dei dati alternativi
- Punti di analisi
- Collegamenti reali
- La compressione (se impostata in un file server) non verrà mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati 
    
    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati possono essere crittografati come inattivi in Azure.
 
### <a name="server-endpoints"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e gli altri file system attualmente non sono supportati da Sincronizzazione file di Azure.
- Un endpoint server non può essere nel volume di sistema (ad esempio, C:\CartellaUtente non è un percorso accettabile a meno che C:\CartellaUtente non sia un punto di montaggio).
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- L'eliminazione simultanea di un numero elevato di directory (più di 10.000) da un server può causare errori di sincronizzazione. Eliminare le directory in batch di meno di 10.000 unità e verificare che le operazioni di eliminazione vengano sincronizzate correttamente prima di eliminare il batch successivo.
- In questa versione è stato aggiunto il supporto per il percorso radice di sincronizzazione in corrispondenza della radice di un volume.
- Non archiviare un sistema operativo o un file di paging dell'applicazione in un endpoint server.
- Come modifica introdotta in questa versione, sono stati aggiunti nuovi eventi per tenere traccia del runtime totale per la suddivisione in livelli cloud (ID evento 9016), dello stato di caricamento della sincronizzazione (ID evento 9302) e dei file che non sono stati sincronizzati (ID evento 9900).
- Come modifica introdotta in questa versione, le prestazioni di sincronizzazione degli spazi dei nomi per il ripristino di emergenza sono state migliorate notevolmente.
 
### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Come modifica introdotta rispetto alla versione precedente, i nuovi file verranno suddivisi in livelli in un'ora, in base all'impostazione del criterio di suddivisione in livelli (in precedenza, 32 ore). È disponibile un cmdlet di PowerShell per la suddivisione in livelli on demand, in modo da poter valutare la suddivisione in livelli con maggiore efficacia, senza attendere il processo in background.
- Se un file a livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non verrà suddiviso in livelli, ma l'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.
- Come modifica introdotta rispetto alla versione precedente, i file vengono ora scaricati come file a livelli in altri server, a condizione che il file sia nuovo o che sia già un file a livelli.

## <a name="agent-version-1100"></a>Agente versione 1.1.0.0
Le note sulla versione seguenti sono relative alla versione 1.1.0.0 dell'agente rilasciata il 9 settembre 2017. Questa è la versione di anteprima iniziale di Sincronizzazione file di Azure.

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente di Sincronizzazione file di Azure con un server Windows, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure (anteprima)](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure (anteprima)](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione (MSI) dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- Non supportato nelle opzioni di distribuzione Windows Server Core o Nano Server.
- Supportato solo in Windows Server 2016 e 2012 R2.
- L'agente richiede almeno 2 GB di memoria fisica.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato a meno che rispettino l'attributo offline e ignorino il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure (anteprima)](storage-sync-files-troubleshoot.md).
- Non usare gli screening dei file Gestione risorse file server (o altro): gli screening dei file possono generare errori di sincronizzazione dei dati infinita se i file vengono bloccati a causa dello screening dei file.
- La deduplicazione di un server registrato (inclusa la clonazione di una VM) potrebbe causare risultati imprevisti (in particolare, è possibile che la sincronizzazione non converga mai).
- La deduplicazione dei dati e la suddivisione in livelli cloud non sono supportate nello stesso volume.
 
### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- Percorsi che contengono più di 2048 caratteri
- Parte DACL di un descrittore di sicurezza se superiore a 2 KB (il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento)
- Parte SACL di un descrittore di sicurezza (usato per l'auditing)
- Attributi estesi
- Flussi dei dati alternativi
- Punti di analisi
- Collegamenti reali
- La compressione (se impostata in un file server) non verrà mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati 
    
    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati possono essere crittografati come inattivi in Azure.
 
### <a name="server-endpoints"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e gli altri file system attualmente non sono supportati da Sincronizzazione file di Azure.
- Un endpoint server non può essere nel volume di sistema (ad esempio, C:\CartellaUtente non è un percorso accettabile a meno che C:\CartellaUtente non sia un punto di montaggio).
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- L'eliminazione simultanea di un numero elevato di directory (più di 10.000) da un server può causare errori di sincronizzazione. Eliminare le directory in batch di meno di 10.000 unità e verificare che le operazioni di eliminazione vengano sincronizzate correttamente prima di eliminare il batch successivo.
- Non supportati nella radice di un volume.
- Non archiviare un sistema operativo o un file di paging dell'applicazione in un endpoint server.
 
### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Per assicurarsi che i file possano essere richiamati in modo corretto, il sistema potrebbe non suddividere automaticamente i file nuovi o modificati per un periodo massimo di 32 ore, inclusa la prima suddivisione in livelli dopo la configurazione di un nuovo endpoint server. Su richiesta viene fornito un cmdlet di PowerShell per poter valutare in modo più efficace la suddivisione in livelli senza attendere il processo in background.
- Se un file a livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non verrà suddiviso in livelli, ma l'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.
