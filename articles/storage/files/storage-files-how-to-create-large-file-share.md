---
title: Abilitare e creare condivisioni file di grandi dimensioni-File di Azure
description: Questo articolo illustra come abilitare e creare condivisioni di file di grandi dimensioni.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 00db5905c008644bc21704179363849756fa7dcc
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518421"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>Come abilitare e creare condivisioni file di grandi dimensioni

In origine, le condivisioni file standard potevano essere scalate fino a 5 TiB, ora con condivisioni file di grandi dimensioni, che possono essere scalate fino a 100 TiB. Per impostazione predefinita, le condivisioni file Premium vengono ridimensionate fino a 100 TiB. 

Per scalare fino a 100 TiB usando condivisioni file standard, è necessario abilitare l'account di archiviazione per l'uso di condivisioni file di grandi dimensioni. È possibile abilitare un account esistente o creare un nuovo account per usare condivisioni file di grandi dimensioni.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="restrictions"></a>Restrizioni

Gli account abilitati per condivisioni file di grandi dimensioni supportano con ridondanza locale o ZRS. Per il momento, gli account abilitati per le condivisioni file di grandi dimensioni non supportano GZRS, GRS o RA-GRS. L'abilitazione di condivisioni file di grandi dimensioni in un account è un processo irreversibile, una volta abilitato, l'account non può essere convertito in GZRS, GRS o RA-GRS.

## <a name="create-a-new-storage-account"></a>Creare un nuovo account di archiviazione.

Accedere al [portale di Azure](https://portal.azure.com).

1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Account di archiviazione**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Account di archiviazione**.
1. Nella finestra **Account di archiviazione** visualizzata scegliere **Aggiungi**.
1. Selezionare la sottoscrizione in cui creare l'account di archiviazione.
1. Nel campo **Gruppo di risorse** selezionare **Crea nuovo**. Immettere un nome per il nuovo gruppo di risorse, come illustrato nell'immagine seguente.

    ![Screenshot che mostra come creare un gruppo di risorse nel portale](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Immettere quindi un nome per l'account di archiviazione. Il nome scelto deve essere univoco in Azure. Deve avere inoltre una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole.
1. Selezionare una località per l'account di archiviazione, assicurarsi che sia [una delle aree supportate per LFS](storage-files-planning.md#regional-availability).
1. Impostare la replica su archiviazione con **ridondanza locale** o **archiviazione con ridondanza della zona**.
1. Mantenere i valori predefiniti per questi campi:

   |Campo  |Value  |
   |---------|---------|
   |Modello di distribuzione     |Azure Resource Manager         |
   |Performance     |Standard         |
   |Tipo di account     |Archiviazione v2 (utilizzo generico V2)         |
   |Livello di accesso     |Accesso frequente         |

1. Selezionare **Avanzate** e selezionare **abilitata** per le **condivisioni file di grandi dimensioni**.
1. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.

    ![Large-file-Shares-Advanced-Enable. png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Selezionare **Create** (Crea).

## <a name="enable-on-existing-account"></a>Abilita sull'account esistente

È anche possibile abilitare condivisioni di file di grandi dimensioni per gli account esistenti. In tal caso, l'account non sarà più in grado di eseguire la conversione in GZRS, GRS o RA-GRS. Questa scelta è irreversibile in questo account.

1. Aprire il [portale di Azure](https://portal.azure.com) e passare all'account di archiviazione in cui si vuole abilitare le condivisioni file di grandi dimensioni.
1. Aprire l'account di archiviazione e selezionare **configurazione**.
1. Selezionare **abilitato** in **condivisioni file di grandi dimensioni**e quindi selezionare Salva.
1. Selezionare **Panoramica** e selezionare **Aggiorna**.

![Enable-large-file-Shares-on-existing. png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

A questo punto sono state abilitate condivisioni file di grandi dimensioni nell'account di archiviazione.

Se viene visualizzato l'errore seguente: "le condivisioni file di grandi dimensioni non sono ancora disponibili per l'account." È possibile attendere del tempo, in quanto l'area è probabilmente in fase di completamento del rollup o, se si hanno esigenze urgenti, contattare il supporto tecnico.

## <a name="create-a-large-file-share"></a>Creare una condivisione file di grandi dimensioni

La creazione di una condivisione file di grandi dimensioni è quasi identica alla creazione di una condivisione file standard. La differenza principale consiste nel fatto che è possibile impostare una quota fino a 100 TiB.

1. Dall'account di archiviazione selezionare **condivisioni file**.
1. Selezionare **+ Condivisione file**.
1. Immettere un nome per la condivisione file e, facoltativamente, le dimensioni della quota, fino a 100 TiB, quindi selezionare **Crea**. 

![Large-file-Shares-create-share. png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

## <a name="expand-existing-file-shares"></a>Espandi condivisioni file esistenti

Una volta abilitate le condivisioni file di grandi dimensioni nell'account di archiviazione, è possibile espandere le condivisioni esistenti con la quota più elevata.

1. Dall'account di archiviazione selezionare **condivisioni file**.
1. Fare clic con il pulsante destro del mouse sulla condivisione file e scegliere **quota**.
1. Immettere le nuove dimensioni desiderate, quindi fare clic su **OK**.

![Update-large-file-share-quota. png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi e montare una condivisione file: Windows](storage-how-to-use-files-windows.md)
* [Connettersi e montare una condivisione file: Linux](../storage-how-to-use-files-linux.md)
* [Connettersi e montare una condivisione file: macOS](storage-how-to-use-files-mac.md)