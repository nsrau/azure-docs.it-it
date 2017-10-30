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
ms.openlocfilehash: 1db3fef17e24022bff59665558f4a354f8c37d1d
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2017
---
# <a name="azure-file-sync-agent-release-notes"></a>Note sulla versione dell'agente di Sincronizzazione file di Azure
Sincronizzazione file di Azure (anteprima) consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Tutto questo avviene trasformando i sistemi Windows Server in una cache rapida della condivisione file di Azure. È possibile usare qualsiasi protocollo disponibile in Windows Server per accedere ai dati in locale (tra cui SMB, NFS e FTPS) ed è possibile scegliere tutte le cache necessarie in tutto il mondo.

Questo articolo illustra le note sulla versione per le versioni supportate dell'agente di Sincronizzazione file di Azure.

## <a name="supported-versions"></a>Versioni supportate
Sincronizzazione file di Azure supporta le versioni seguenti:

| Numero di versione dell'agente | Data di rilascio | Ultima versione supportata |
|----------------------|--------------|------------------|
| 1.1.0.0 | 2017-09-26 | Versione corrente |

### <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

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