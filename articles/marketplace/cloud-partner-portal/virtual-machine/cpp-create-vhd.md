---
title: Creare un disco rigido virtuale compatibile con Azure per Azure Marketplace | Microsoft Docs
description: Questo articolo illustra come creare un disco rigido virtuale per un'offerta di macchina virtuale in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/27/2018
ms.author: pbutlerm
ms.openlocfilehash: 5643b7c80307cea36f60bcc116c82ea7b31171f9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096572"
---
# <a name="create-an-azure-compatible-vhd"></a>Creare un disco rigido virtuale compatibile con Azure

Questo articolo descrive in dettaglio i passaggi necessari per creare un disco rigido virtuale per un'offerta di macchina virtuale in Azure Marketplace.  Include inoltre le procedure consigliate per diversi aspetti, ad esempio l'uso di Remote Desktop Protocol (RDP), la selezione di una dimensione per la macchina virtuale, l'installazione degli aggiornamenti di Windows più recenti e la generalizzazione dell'immagine del disco rigido virtuale.  Le sezioni seguenti sono dedicate principalmente sui dischi rigidi virtuali basati su Windows. Per altre informazioni sulla creazione di dischi rigidi virtuali basati su Linux, vedere [Linux in distribuzioni approvate da Azure](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> È consigliabile seguire le indicazioni presenti in questo argomento per usare Azure per la creazione di una macchina virtuale che contiene un sistema operativo pre-configurato e approvato.  Se tali indicazioni non sono compatibili con la soluzione in uso, è possibile creare e configurare una macchina virtuale in locale con un sistema operativo approvato.  È quindi possibile configurare e prepararla per il caricamento, come descritto in [Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Selezionare una base approvata
Il VHD del sistema operativo per l'immagine di macchina virtuale deve essere basato su un'immagine di base approvata per Azure contenente Windows Server o SQL Server.
Per iniziare, creare una macchina virtuale da una delle immagini seguenti, disponibili nel portale di Microsoft Azure:

-   Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/), [2012 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/), [2008 R2 SP1](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/))
-   [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
-   [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!TIP]
> Se si usa l'attuale portale di Azure o PowerShell, sono approvate le immagini di Windows Server pubblicate a partire dall'8 settembre 2014.

In alternativa, Azure offre una gamma di distribuzioni Linux approvate.  Per un elenco aggiornato, vedere [Linux in distribuzioni approvate da Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Creare una macchina virtuale nel portale di Azure 

Nel [portale di Microsoft Azure](https://ms.portal.azure.com/) creare l'immagine di base con eseguendo questa procedura.

1. Accedere al portale con l'account Microsoft per la sottoscrizione di Azure per cui si intende pubblicare l'offerta di macchina virtuale.
2. Creare un nuovo gruppo di risorse e specificare i valori per **Nome del gruppo di risorse**, **Sottoscrizione** e **Località del gruppo di risorse**.  Per altre indicazioni, vedere [Gestire gruppi di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Fare clic su **Macchine virtuali** nella barra dei menu a sinistra per visualizzare la pagina dei dettagli delle macchine virtuali. 
4. In questa nuova pagina fare clic su **+ Aggiungi** per visualizzare il pannello **Calcolo** pannello.  Se non è possibile visualizzare il tipo di macchina virtuale nella schermata iniziale, è possibile cercare il nome della macchina di base, ad esempio:

    ![Pannello di calcolo della nuova macchina virtuale](./media/publishvm_014.png)

5. Dopo aver selezionato l'immagine virtuale appropriata, specificare i valori seguenti:
   * Nel pannello **Informazioni di base** immettere un valore in **Nome** per la macchina virtuale con lunghezza compresa tra 1 e 15 caratteri alfanumerici. Questo esempio usa `DemoVm009`.
   * Immettere un valore in **Nome utente** e una password complessa in **Password**, da usare per creare un account locale nella macchina virtuale.  In questo caso viene usato `adminUser`.  La password deve avere una lunghezza compresa tra 8 e 123 caratteri e soddisfare almeno tre dei quattro requisiti di complessità seguenti: una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale. Per altre informazioni, vedere i [requisiti relativi a nome utente e password](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Selezionare il gruppo di risorse creato in precedenza (in questo caso `DemoResourceGroup`).
   * Selezionare un valore di **Località** per il data center di Azure (in questo caso `West US`).
   * Fare clic su **OK** per salvare questi valori. 

6. Selezionare le dimensioni della macchina virtuale per eseguire la distribuzione seguendo queste indicazioni:
   * Se si prevede di sviluppare il VHD in locale, la dimensione non è rilevante. Valutare l'opportunità di usare una delle macchine virtuali di dimensioni minori.
   * Se si prevede di sviluppare l'immagine in Azure, valutare l'opportunità di usare una delle dimensioni di macchina virtuale consigliate per l'immagine selezionata.
   * Per informazioni sui prezzi, vedere il selettore del **piano tariffario consigliato** visualizzato nel portale. Verranno indicate le tre dimensioni consigliate, specificate dall'autore. In questo caso l'autore è Microsoft.

   ![Pannello delle dimensioni della nuova macchina virtuale](./media/publishvm_015.png)

7. Nel pannello **Impostazioni** impostare l'opzione **Usa dischi gestiti** su **No**.  Ciò consente di gestire manualmente il nuovo disco rigido virtuale. Il pannello **Impostazioni** consente anche di modificare altre opzioni di archiviazione e di rete, ad esempio selezionando **Premium (SSD)** in **Tipo di disco**.  Fare clic su **OK** per continuare.

    ![Pannello Impostazioni della nuova macchina virtuale](./media/publishvm_016.png)

8. Fare clic su **Riepilogo** per verificare le modifiche. Quando viene visualizzato il messaggio **Convalida superata** fare clic su **OK**.

    ![Pannello di riepilogo della nuova macchina virtuale](./media/publishvm_017.png)

Azure avvia il provisioning della macchina virtuale specificata.  È possibile tenere traccia dello stato di avanzamento facendo clic sulla scheda **Macchine virtuali** a sinistra.  Al termine della creazione, lo stato viene modificato in **In esecuzione**.  A questo punto, è possibile [connettersi alla macchina virtuale](./cpp-connect-vm.md).


## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi durante la creazione del nuovo disco rigido virtuale basato su Azure, vedere [Common issues during VHD creation](./cpp-common-vhd-creation-issues.md) (Problemi comuni durante la creazione di un disco rigido virtuale).  In caso contrario, [connettersi alle macchine virtuali create](./cpp-connect-vm.md) in Azure. 
