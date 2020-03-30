---
title: Considerazioni per il provisioning delle condivisioni file di Azure.Considerations for provisioning Azure file shares.
description: Effettuare il provisioning delle condivisioni file di Azure per l'uso con Sincronizzazione file di Azure.Provision Azure file shares for use with Azure File Sync. Blocco di testo comune, condiviso tra documenti di migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209428"
---
Una condivisione file di Azure viene archiviata nel cloud in un account di archiviazione di Azure.An Azure file share is stored in the cloud in an Azure storage account.
C'è un altro livello di considerazioni sulle prestazioni qui.

Se si dispone di condivisioni altamente attive: condivisioni usate da molti utenti e/o applicazioni, due condivisioni file di Azure potrebbero raggiungere il limite di prestazioni di un account di archiviazione.

La procedura consigliata consiste nel distribuire gli account di archiviazione con una condivisione file ciascuno.
È possibile raggruppare più condivisioni file di Azure nello stesso account di archiviazione, nel caso in cui si disponga di condivisioni di archiviazione o di attività giornaliere.

Queste considerazioni si applicano più all'accesso diretto al cloud (tramite una macchina virtuale di Azure) rispetto a Sincronizzazione file di Azure.These considerations apply more to direct cloud access (through an Azure VM) than they apply to Azure File Sync. Se si prevede di usare solo Sincronizzazione file di Azure in queste condivisioni, raggrupparne diversi in un singolo account di archiviazione di Azure va bene.

Se è stato creato un elenco delle condivisioni, è necessario eseguire il mapping di ogni condivisione all'account di archiviazione in cui risiederanno.

Nella fase precedente è stato determinato il numero appropriato di azioni. In questo passaggio è stato creato un mapping degli account di archiviazione alle condivisioni file. Distribuire il numero appropriato di account di archiviazione di Azure con il numero appropriato di condivisioni file di Azure.

Assicurarsi che l'area di ogni account di archiviazione sia la stessa e corrisponda all'area della risorsa del servizio di sincronizzazione archiviazione già distribuita.

> [!CAUTION]
> Se si crea una condivisione file di Azure con limite di 100 TiB, tale condivisione può usare solo opzioni di ridondanza di archiviazione ridondante localmente ridondante. Considerare le esigenze di ridondanza dell'archiviazione prima di utilizzare 100 condivisioni file TiB.

Le condivisioni file di Azure vengono comunque create con un limite di 5 TiB per impostazione predefinita. Poiché si creano nuovi account di archiviazione, assicurarsi di seguire le [indicazioni per creare account di archiviazione che consentono condivisioni file di Azure con 100 limiti TiB.](../articles/storage/files/storage-files-how-to-create-large-file-share.md)

Un'altra considerazione quando si distribuisce un account di archiviazione è la ridondanza dell'archiviazione di Azure.Another consideration when deploying a storage account, is the redundancy of your Azure storage. Vedere: Opzioni di [ridondanza di Archiviazione di Azure](../articles/storage/common/storage-redundancy.md).

Anche i nomi delle risorse sono importanti. Ad esempio, se si raggruppano più condivisioni per il reparto risorse umane in un account di archiviazione di Azure, è necessario assegnare un nome appropriato all'account di archiviazione. Analogamente, quando si assegna un nome alle condivisioni file di Azure, è consigliabile usare nomi simili a quelli usati per le controparti locali.