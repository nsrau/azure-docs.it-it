---
title: Creare la prima VM Windows | Microsoft Docs
description: Informazioni su come creare la prima macchina virtuale Windows usando il portale di Azure.
keywords: Macchina virtuale di Windows,creare una macchina virtuale,computer virtuale,configurazione di una macchina virtuale
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 785e17eb-4a13-4f06-b70f-4bd496d0ec5d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: hero-article
ms.date: 01/03/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 425637599df40ac3be23984785e4f3292d72978d
ms.openlocfilehash: 7c7a300b620b1e7bd0cd1b816e575f2d57fee80a


---
# <a name="create-your-first-windows-virtual-machine-in-the-azure-portal"></a>Creare la prima macchina virtuale Windows nel portale di Azure
Questa esercitazione mostra quanto sia facile creare una macchina virtuale Windows in pochi minuti usando il portale di Azure.  

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Scegliere l'immagine della VM dal Marketplace
Come esempio viene usata un'immagine di Windows Server 2016 Datacenter, che è solo una delle molte immagini disponibili in Azure. Le opzioni relative all'immagine dipendono dalla sottoscrizione. Per gli [abbonati a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)sono ad esempio disponibili immagini desktop.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nell'angolo in alto a sinistra fare clic su **Nuovo** > **Calcolo** > **Windows Server 2016 Datacenter**.
   
    ![Screenshot che illustra le immagini di VM di Azure disponibili nel portale](./media/virtual-machines-windows-hero-tutorial/marketplace-new.png)
3. Nel pannello **Windows Server 2016 Datacenter** verificare che in **Selezionare un modello di distribuzione** sia selezionata l'opzione **Resource Manager**. Fare clic su **Crea**.
   
    ![Screenshot che mostra il modello di distribuzione da selezionare per la VM](./media/virtual-machines-windows-hero-tutorial/deployment-model.png)

## <a name="create-the-windows-virtual-machine"></a>Creare la macchina virtuale Windows
Dopo aver selezionato l'immagine, è possibile usare le impostazioni predefinite e creare rapidamente la macchina virtuale.

1. Nel pannello **Informazioni di base** immettere un **Nome** per la macchina virtuale. In questo esempio *HeroVM* è il nome della macchina virtuale. Il nome deve avere una lunghezza compresa tra 1 e 15 caratteri e non può contenere caratteri speciali.
2. Immettere un **Nome utente** e una **Password** complessa, da usare per creare un account locale nella VM. L'account locale viene usato per accedere alla macchina virtuale e gestirla. In questo esempio *azureuser* è il nome utente.
   
    La password deve avere una lunghezza compresa tra 8 e 123 caratteri e soddisfare almeno tre dei quattro requisiti di complessità seguenti: una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale. Sono disponibili altre informazioni sui [requisiti relativi a nome utente e password](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).

3. Selezionare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups) esistente o specificare il nome di un nuovo gruppo. In questo esempio *HeroVMRG* è il nome del gruppo di risorse.

4. Selezionare una **località** per il data center di Azure. In questo esempio *Stati Uniti orientali** è la località. 

4. Al termine, fare clic su **OK** per passare alla sezione successiva. 
   
    ![Screenshot che illustra le impostazioni nel pannello **Informazioni di base** per la configurazione di una VM di Azure](./media/virtual-machines-windows-hero-tutorial/basics-blade.png)
5. Scegliere le [dimensioni](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)per la macchina virtuale e quindi fare clic su **Seleziona** per continuare. In questo esempio *DS1_V2 Standard* è la dimensione della VM.
   
    ![Screenshot del pannello Dimensioni che mostra le dimensioni di VM di Azure disponibili per la selezione](./media/virtual-machines-windows-hero-tutorial/size-blade.png)
6. Nel pannello **Impostazioni** è possibile cambiare le opzioni di archiviazione e di rete. Per questa esercitazione, accettare le impostazioni predefinite. Se sono state selezionate dimensioni di macchina virtuale che lo supportano, è possibile provare il servizio Archiviazione Premium di Azure selezionando **Premium (SSD)** nel **Tipo di disco**. Al termine delle modifiche, fare clic su **OK**.
   
    ![Screenshot del pannello Impostazioni in cui è possibile configurare le funzionalità facoltative per una VM di Azure](./media/virtual-machines-windows-hero-tutorial/settings-blade.png)
7. Fare clic su **Riepilogo** per verificare le modifiche. Quando viene visualizzato il messaggio **Convalida superata** fare clic su **OK**.
   
    ![Screenshot della pagina Riepilogo che mostra le scelte di configurazione effettuate per la VM di Azure](./media/virtual-machines-windows-hero-tutorial/summary-blade.png)
8. Mentre Azure crea la macchina virtuale, è possibile tenere traccia dello stato di avanzamento facendo clic su **Macchine virtuali** a sinistra. Quando la VM è stata creata, lo stato verrà impostato su **In esecuzione**.

## <a name="connect-to-the-virtual-machine-and-sign-on"></a>Connettersi alla macchina virtuale ed effettuare l'accesso
1. A sinistra fare clic su **Macchine virtuali**.
2. Selezionare la macchina virtuale dall'elenco.
3. Nel pannello della macchina virtuale fare clic su **Connetti**. Verrà creato e scaricato un file Remote Desktop Protocol (file con estensione rdp), che è analogo a un collegamento per la connessione alla macchina virtuale. È consigliabile salvare il file sul desktop per semplificare l'accesso. **Aprire** questo file per connettersi alla macchina virtuale.
   
    ![Screenshot del portale di Azure che illustra come connettersi alla VM](./media/virtual-machines-windows-hero-tutorial/connect.png)
4. Verrà visualizzato un avviso che indica che l'autore del file RDP è sconosciuto. Si tratta di una situazione normale. Nella finestra di Desktop remoto, fare clic su **Connetti** per continuare.
   
    ![Screenshot di un avviso relativo a un autore sconosciuto](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)
5. Nella finestra Sicurezza di Windows immettere il nome utente e la password per l'account locale creato quando è stata creata la VM. Il nome utente viene immesso come *nomevm*&#92;*nomeutente*. Fare quindi clic su **OK**.
   
    ![Screenshot relativo all'immissione del nome della VM, del nome utente e della password](./media/virtual-machines-windows-hero-tutorial/credentials.png)
6. Verrà visualizzato un avviso che indica che non è possibile verificare il certificato. Si tratta di una situazione normale. Fare clic su **Sì** per verificare l'identità della macchina virtuale e terminare la procedura di accesso.
   
   ![Screenshot che mostra un messaggio sulla verifica dell'identità della VM](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)

In caso di problemi quando si cerca di eseguire la connessione, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ora è possibile usare la macchina virtuale come un qualsiasi altro server.

## <a name="optional-stop-the-vm"></a>Facoltativo: Arrestare la macchina virtuale
È consigliabile arrestare la VM, in modo che non vengano effettuati addebiti quando non la si usa. Fare clic su **Arresta** e quindi su **Sì**.

![Screenshot che mostra il pulsante per l'arresto di una VM](./media/virtual-machines-windows-hero-tutorial/stop-vm.png)

Fare clic sul pulsante **Avvia** per riavviare la macchina virtuale quando si è pronti per usarla di nuovo.

## <a name="next-steps"></a>Passaggi successivi
* Per provare la nuova macchina virtuale è possibile [installare IIS](virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Questa esercitazione mostra anche come aprire la porta 80 per il traffico Web in ingresso tramite un gruppo di sicurezza di rete (NSG). 
* È anche possibile [creare una macchina virtuale Windows con PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [creare una macchina virtuale Linux](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) tramite l'interfaccia della riga di comando di Azure.
* Per automatizzare le distribuzioni, vedere [Creare una macchina virtuale Windows con un modello di Resource Manager](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Feb17_HO3-->


