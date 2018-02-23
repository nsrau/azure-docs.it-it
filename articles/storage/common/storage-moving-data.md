---
title: "Spostamento di grandi quantità di dati da e verso l'archiviazione cloud in Azure | Documentazione Microsoft"
description: Panoramica dei diversi metodi per spostare i dati da e verso Archiviazione di Azure.
services: storage
documentationcenter: 
author: JarrettRenshaw
manager: msmets
editor: tysonn
ms.assetid: 5e3947a9-d99b-4108-9d57-3eb67c03e7ba
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: jarrettr
ms.openlocfilehash: 980e4675c2d2e88716a3133abb027988aecd538f
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="moving-data-to-and-from-azure-storage"></a>Spostamento dei dati da e verso Archiviazione di Azure
Esistono modi diversi per spostare i dati locali in Archiviazione di Azure (o viceversa). Quale sia il metodo adatto dipende dallo scenario. Questo articolo offre informazioni generali su diversi scenari e la soluzione appropriata per ciascuno.

## <a name="building-applications"></a>Compilazione di applicazioni
Se si usa l’API REST o una delle numerose librerie client per la compilazione e lo sviluppo di un’applicazione, è consigliabile spostare i dati da e verso Archiviazione di Azure.

In Archiviazione di Azure sono disponibili librerie client complete per .NET, iOS, Java, Android, piattaforma UWP, Xamarin, C++, Node.JS, PHP, Ruby e Python. Le librerie client offrono funzionalità avanzate, ad esempio la logica di ripetizione dei tentativi, la registrazione e i caricamenti paralleli. È possibile sviluppare usando direttamente l'API REST, che può essere chiamata da qualsiasi linguaggio in grado di eseguire richieste HTTP/HTTPS.

Vedere [Introduzione all'archiviazione BLOB di Azure](../blobs/storage-dotnet-how-to-use-blobs.md) per altre informazioni.

Viene anche offerta la [libreria per lo spostamento dei dati di Archiviazione di Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) che è una libreria progettata per la copia a prestazioni elevate di dati da e verso Azure. Per altre informazioni, consultare la [documentazione](https://github.com/Azure/azure-storage-net-data-movement) sulla nostra libreria per lo spostamento dei dati. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Visualizzazione e interazione rapida dei dati
Per visualizzare semplicemente i dati in Archiviazione di Azure e al tempo stesso poterli caricare e scaricare, è consigliabile usare un Esplora archivi Azure.

Per altre informazioni, consultare l'elenco di [Esplora archivi Azure](../storage-explorers.md) .

## <a name="system-administration"></a>System Administration
Se è richiesta un’utilità da riga di comando o si preferisce usare questa soluzione, ad esempio Amministratori di sistema, considerare le possibilità seguenti:

### <a name="azcopy"></a>AzCopy
AzCopy è un'utilità da riga di comando progettata per offrire prestazioni elevate di copia dei dati da e verso Archiviazione di Azure. È inoltre possibile copiare i dati all'interno di un account di archiviazione o tra account di archiviazione diversi. AzCopy è disponibile in [Windows](storage-use-azcopy.md) e in [Linux](storage-use-azcopy-linux.md).

Per altre informazioni, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md) o [Transfer data with AzCopy on Linux](storage-use-azcopy-linux.md) (Trasferire dati con AzCopy in Linux).

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell è un modulo che offre cmdlet per la gestione dei servizi in Azure. Si tratta di una shell da riga di comando basata su attività e di un linguaggio di scripting progettato appositamente per gli amministratori di sistema.

Per altre informazioni, vedere [Uso di Azure PowerShell con Archiviazione di Azure](storage-powershell-guide-full.md) .

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
L'interfaccia della riga di comando di Azure offre un insieme di comandi open source e multipiattaforma per usare i servizi di Azure. L’interfaccia della riga di comando di Azure è disponibile in Windows, OSX e Linux.

Per altre informazioni, vedere [Utilizzo dell'interfaccia della riga di comando di Azure con archiviazione di Azure](../storage-azure-cli.md) .

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Spostamento di molti dati tramite una rete lenta
Il tempo di trasferimento è una delle difficoltà principali che riguardano lo spostamento di molti dati. Se è necessario spostare da e verso Archiviazione di Azure i dati senza doversi preoccupare dei costi di rete o della scrittura di codice, Importazione/Esportazione di Azure è la soluzione adatta.

Per altre informazioni, vedere [Usare il servizio Importazione/Esportazione di Microsoft Azure per trasferire i dati nell'archiviazione BLOB](../storage-import-export-service.md) .

## <a name="backing-up-your-data"></a>Backup dei dati
Se è necessario semplicemente eseguire il backup dei dati in Archiviazione di Azure, Backup di Azure è la scelta giusta. Si tratta di una soluzione potente per il backup di dati locali e macchine virtuali di Azure.

Per altre informazioni, vedere [Backup di Azure](../../backup/backup-introduction-to-azure-backup.md) .

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Accesso ai dati locali e dal cloud
Per accedere ai dati locali e dal cloud, considerare l’uso di StorSimple, una soluzione di archiviazione cloud ibrida di Azure. Questa soluzione è costituita da un dispositivo StorSimple fisico che, in modo intelligente, archivia i dati utilizzati di frequente in unità SSD, i dati utilizzati occasionalmente in unità HDD e i dati inattivi, di backup e di archiviazione in Archiviazione di Azure.

Per altre informazioni, vedere [StorSimple](../../storsimple/storsimple-overview.md) .

## <a name="recovering-your-data"></a>Ripristino dei dati
In presenza di carichi di lavoro e applicazioni locali, è consigliabile l’uso di una soluzione che sia in grado di garantire continuità aziendale in caso di emergenza. Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali e server fisici. I dati replicati vengono archiviati in Archiviazione di Azure eliminando così la necessità di un centro dati on-site secondario.

Per altre informazioni, vedere [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) .
### <a name="moving-data-faq"></a>Domande frequenti sullo spostamento di dati:
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>È possibile migrare i dischi rigidi virtuali da un'area a un'altra senza eseguire una copia?
L'unico modo per copiare i dischi rigidi virtuali tra aree diverse consiste nel copiare i dati tra gli account di archiviazione di ogni area. A questo scopo è possibile usare AZCopy. Per altre informazioni, vedere Trasferire dati con l'utilità della riga di comando AzCopy . Per grandi quantità di dati è possibile anche usare il servizio Importazione/Esportazione di Azure. Per altre informazioni, vedere [Usare il servizio Importazione/Esportazione di Microsoft Azure per trasferire i dati nell'archiviazione BLOB](https://docs.microsoft.com/azure/storage/storage-import-export-service) .
