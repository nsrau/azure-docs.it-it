---
title: Connettersi a una VM Windows Server | Microsoft Docs
description: Informazioni su come connettersi e accedere a una VM Windows mediante il portale di Azure e il modello di distribuzione Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: cynthn
ms.openlocfilehash: 7c495a74ccbcad3ee18147726742ee59b65f1c0e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012642"
---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows
Per avviare una sessione di Desktop remoto (RDP) da un desktop di Windows, sarà necessario usare il pulsante **Connetti** nel portale di Azure. Effettuare la connessione alla macchina virtuale, quindi accedere al sistema.

Se si sta tentando di effettuare la connessione a una macchina virtuale Windows da un Mac, è necessario installare un client RDP per Mac come [Desktop remoto Microsoft](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale
1. Accedere al [portale di Azure](https://portal.azure.com/), se questa operazione non è già stata eseguita.
2. Nel menu a sinistra fare clic su **Macchine virtuali**.
3. Selezionare la macchina virtuale dall'elenco.
4. Nella parte superiore della pagina della macchina virtuale fare clic ![sull'immagine del pulsante di connessione.](./media/connect-logon/connect.png) .
2. Nella pagina **Connetti a macchina virtuale** selezionare le opzioni appropriate e fare clic su **Scarica file RDP**.
2. Aprire il file RDP scaricato e fare clic su **Connetti** quando richiesto. 
2. Verrà visualizzato un avviso che indica che l'autore del file con estensione `.rdp` è sconosciuto. Si tratta di una situazione normale. Nella finestra di Desktop remoto, fare clic su **Connetti** per continuare.
   
    ![Screenshot di un avviso relativo a un autore sconosciuto.](./media/connect-logon/rdp-warn.png)
3. Nella finestra **Sicurezza di Windows** selezionare **Altre opzioni** e quindi **Usa un altro account**. Digitare le credenziali di un account della macchina virtuale e quindi fare clic su **OK**.
   
     **Account locale** : corrisponde in genere al nome utente e alla password dell'account locale specificati al momento della creazione della macchina virtuale. In questo caso, il dominio è il nome della macchina virtuale e viene immesso come *nomevm*&#92;*nomeutente*.  
   
    **VM aggiunta a un dominio**: se la VM appartiene a un dominio, immettere il nome utente con formato *Dominio*&amp;#92;*Nomeutente*. L'account deve anche appartenere al gruppo degli amministratori o deve possedere privilegi di accesso remoto alla VM.
   
    **Controller di dominio** : se la macchina virtuale è un controller di dominio, digitare nome utente e password di un account amministratore di dominio per tale dominio.
4. Fare clic su **Sì** per verificare l'identità della macchina virtuale e terminare la procedura di accesso.
   
   ![Screenshot che visualizza un messaggio sulla verifica dell'identità della VM.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Se il pulsante **Connetti** nel portale è disattivato e non si è connessi ad Azure tramite una connessione [Express Route](../../expressroute/expressroute-introduction.md) o [VPN da sito a sito](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), per poter usare il protocollo RDP è necessario creare un indirizzo IP pubblico e assegnarlo alla VM. Altre informazioni sono disponibili in [Indirizzi IP pubblici in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 


## <a name="next-steps"></a>Passaggi successivi
In caso di problemi quando si cerca di eseguire la connessione, vedere [Risolvere i problemi di connessioni Desktop remoto](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). In questo articolo viene illustrato come diagnosticare e risolvere i problemi più comuni.

