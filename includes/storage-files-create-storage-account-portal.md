---
title: storage-files-create-storage-account-portal
description: Creare un account di archiviazione per File di Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: a09e9206b3ecd23a6208572a88c16b149033f123
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
ms.locfileid: "31433348"
---
Un account di archiviazione è un pool condiviso di spazio di archiviazione in cui è possibile distribuire una condivisione file di Azure o altre risorse di archiviazione, ad esempio BLOB o code. Un account di archiviazione può contenere un numero illimitato di condivisioni. In una condivisione può essere archiviato un numero illimitato di file, fino ai limiti di capacità dell'account di archiviazione.

Per creare un account di archiviazione:

1. Nel menu a sinistra selezionare **+** per creare una risorsa.
2. Nella casella di ricerca immettere **account di archiviazione**, selezionare **Account di archiviazione: BLOB, File, Tabelle, Code** e quindi fare clic su **Crea**.
    ![Screenshot della voce dell'account di archiviazione nella finestra di dialogo per la ricerca delle risorse](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. In **Nome** digitare *mystorageacct* seguito da alcuni numeri casuali fino a visualizzare un segno di spunta verde indicante che si tratta di un nome univoco. Un nome di account di archiviazione deve contenere solo caratteri minuscoli ed essere globalmente univoco. Prendere nota del nome dell'account di archiviazione perché sarà necessario più avanti. 
4. In **Modello di distribuzione** lasciare il valore predefinito **Resource Manager**. Per altre informazioni sulle differenze tra Azure Resource Manager e il modello di distribuzione classica, vedere [Comprensione dei modelli di implementazione e dello stato delle risorse](../articles/azure-resource-manager/resource-manager-deployment-model.md).
5. In **Tipologia account** selezionare **Archiviazione v2**. Per altre informazioni sulle diverse tipologie di account di archiviazione, vedere [Informazioni sugli account di archiviazione di Azure](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
6. In **Prestazioni** mantenere il valore predefinito **Archiviazione Standard**. File di Azure supporta attualmente solo l'archiviazione Standard. Anche se si seleziona l'archiviazione Premium, la condivisione file viene salvata nell'archiviazione Standard.
7. In **Replica** selezionare **Archiviazione con ridondanza locale**. 
8. In **Trasferimento sicuro obbligatorio** è consigliabile selezionare sempre **Abilitato**. Per altre informazioni su questa opzione, vedere [Informazioni sulla crittografia dei dati in transito](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
9. In **Sottoscrizione** selezionare la sottoscrizione usata per creare l'account di archiviazione. Se è presente una sola sottoscrizione, sarà quella predefinita.
10. Per **Gruppo di risorse** selezionare **Crea nuovo**. Per il nome immettere *myResourceGroup*.
11. In **Località** selezionare **Stati Uniti Orientali**.
12. In **Reti virtuali** lasciare l'opzione predefinita **disabilitata**. 
13. Selezionare **Aggiungi al dashboard** per trovare più facilmente l'account di archiviazione.
14. Al termine, fare clic su **Crea** per avviare la distribuzione.