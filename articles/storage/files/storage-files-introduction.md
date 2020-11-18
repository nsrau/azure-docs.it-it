---
title: Introduzione a File di Azure | Microsoft Docs
description: Panoramica di File di Azure, un servizio che consente di creare e usare condivisioni file di rete nel cloud usando il protocollo SMB standard di settore.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9ce2044f3ee0bff3335f46d8cf712e64394af2d3
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628867"
---
# <a name="what-is-azure-files"></a>Informazioni su File di Azure
File di Azure offre condivisioni file completamente gestite nel cloud, accessibili tramite il [protocollo SMB (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) o il [protocollo NFS (Network File System)](https://en.wikipedia.org/wiki/Network_File_System) standard di settore. Le condivisioni file di Azure possono essere montate simultaneamente da distribuzioni cloud o locali. Le condivisioni file SMB di File di Azure sono accessibili da client Windows, Linux e macOS. Le condivisioni file NFS di File di Azure sono accessibili da client Linux o macOS. Le condivisioni file SMB di File di Azure possono anche essere memorizzate nella cache in Windows Server con Sincronizzazione file di Azure per l'accesso rapido in prossimità della posizione in cui vengono usati i dati.

## <a name="videos"></a>Video
| Introduzione a Sincronizzazione file di Azure | File di Azure con sincronizzazione (Ignite 2019)  |
|-|-|
| [![Screencast del video di introduzione a Sincronizzazione file di Azure (fare clic per riprodurre)](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Screencast della presentazione su File di Azure e la sincronizzazione (fare clic per riprodurre)](./media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

Ecco alcuni video sui casi d'uso comuni di File di Azure:
* [Sostituire il file server con un'istanza serverless di Condivisione file di Azure](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
* [Introduzione ai contenitori di profili FSLogix in File di Azure in Desktop virtuale Windows che sfrutta l'autenticazione di Azure AD](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="why-azure-files-is-useful"></a>Vantaggi offerti da File di Azure
Le condivisioni file di Azure possono essere usate per:

* **Sostituire o integrare file server locali**:  
    È possibile usare File di Azure per sostituire completamente o integrare i dispositivi NAS o i file server locali tradizionali. I sistemi operativi più diffusi, come Windows, macOS e Linux, possono montare direttamente condivisioni file di Azure ovunque nel mondo. Le condivisioni file SMB di File di Azure possono anche essere replicate nei server Windows con Sincronizzazione file di Azure, in locale o nel cloud, per ottenere prestazioni elevate e per una memorizzazione nella cache efficiente e distribuita dei dati nella posizione in cui vengono usati. Con il rilascio recente dell'[autenticazione AD di File di Azure](storage-files-active-directory-overview.md), le condivisioni file SMB di File di Azure possono continuare a funzionare con AD in locale per il controllo di accesso. 

* **Applicazioni "lift-and-shift"** :  
    File di Azure semplifica il trasferimento nel cloud in modalità lift-and-shift di applicazioni che prevedono una condivisione file per archiviare i dati delle applicazioni o degli utenti. File di Azure consente sia lo scenario lift-and-shift "classico", in cui l'applicazione e i dati vengono trasferiti in Azure, che lo scenario lift-and-shift "ibrido", in cui i dati dell'applicazione vengono trasferiti in File di Azure e l'applicazione continua a essere eseguita in locale. 

* **Semplificare lo sviluppo per il cloud**:  
    È possibile usare File di Azure in molti modi per semplificare i nuovi progetti di sviluppo per il cloud. Ad esempio:
    * **Impostazioni delle applicazioni condivise**:  
        Un modello comune per le applicazioni distribuite consiste nell'inserire i file di configurazione in una posizione centralizzata in cui siano accessibili da numerose istanze dell'applicazione. Le istanze dell'applicazione possono caricare la propria configurazione tramite l'API REST di File e gli utenti possono accedere alle istanze in base alle esigenze mondando la condivisione SMB in locale.

    * **Condivisione di diagnostica**:  
        Una condivisione file di Azure è un pratico strumento che permette alle applicazioni cloud di scrivere log, metriche e dump di arresto anomalo del sistema. I log possono essere scritti dalle istanze dell'applicazione tramite l'API REST di File e gli sviluppatori possono accedervi montando la condivisione file nel computer locale. Ciò consente una notevole flessibilità perché gli sviluppatori possono dedicarsi allo sviluppo cloud senza dover abbandonare gli strumenti che già conoscono e preferiscono.

    * **Sviluppo/test/debug**  
        Quando gli sviluppatori o gli amministratori usano VM nel cloud, spesso necessitano di un set di strumenti o di utilità. La copia di tali strumenti e utilità in ogni macchina virtuale può richiedere molto tempo. Montando una condivisione file di Azure in locale nelle macchine virtuali, sviluppatori e amministratori possono accedere rapidamente a strumenti e utilità, senza che siano necessarie attività di copia.
* **Containerizzazione**:  
    Le condivisioni di File di Azure possono essere usate come volumi permanenti per i contenitori con stato. I contenitori offrono funzionalità per "compilazione unica ed esecuzione globale" che consentono agli sviluppatori di accelerare l'innovazione. Per i contenitori che accedono a dati non elaborati a ogni avvio, è necessario un file system condiviso per consentire a questi contenitori l'accesso al file system indipendentemente dall'istanza in cui vengono eseguiti.

## <a name="key-benefits"></a>Vantaggi principali
* **Accesso condiviso**. Le condivisioni file di Azure supportano i protocolli SMB e NFS standard di settore. Di conseguenza, è possibile sostituire facilmente le condivisioni file locali con condivisioni file di Azure senza preoccuparsi della compatibilità delle applicazioni. La possibilità di condividere un file system tra più computer, applicazioni e istanze è un vantaggio importante di File di Azure per le applicazioni che richiedono condivisibilità. 
* **Soluzione completamente gestita**. È possibile creare condivisioni file di Azure senza dover gestire l'hardware o un sistema operativo. Di conseguenza, non è necessario applicare patch del sistema operativo server con aggiornamenti critici della sicurezza o sostituire dischi rigidi difettosi.
* **Script e strumenti**. È possibile usare i cmdlet di PowerShell e l'interfaccia della riga di comando di Azure per creare, montare e gestire le condivisioni file di Azure nell'ambito dell'amministrazione delle applicazioni Azure. Le condivisioni file di Azure possono essere create e gestite con il portale di Azure e Azure Storage Explorer. 
* **Resilienza**. File di Azure è stato progettato per garantire disponibilità costante. Sostituendo le condivisioni file locali con File di Azure non sarà più necessario occuparsi di problemi di rete o interruzioni dell'alimentazione a livello locale. 
* **Programmabilità nota**. Le applicazioni eseguite in Azure possono accedere ai dati nella condivisione tramite le [API di I/O del file system](/dotnet/api/system.io.file). Gli sviluppatori possono quindi riutilizzare il codice esistente e le competenze acquisite per eseguire la migrazione delle applicazioni esistenti. Oltre alle API di I/O di sistema, è possibile usare [librerie client di archiviazione di Azure](/previous-versions/azure/dn261237(v=azure.100)) o l'[API REST di Archiviazione di Azure](/rest/api/storageservices/file-service-rest-api).

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni sui protocolli di condivisione file disponibili](storage-files-compare-protocols.md)
* [Creare una condivisione file di Azure](storage-how-to-create-file-share.md)
* [Connettere e montare una condivisione SMB in Windows](storage-how-to-use-files-windows.md)
* [Connettere e montare una condivisione SMB in Linux](storage-how-to-use-files-linux.md)
* [Connettere e montare una condivisione SMB in macOS](storage-how-to-use-files-mac.md)
* [Come creare una condivisione NFS](storage-files-how-to-create-nfs-shares.md)