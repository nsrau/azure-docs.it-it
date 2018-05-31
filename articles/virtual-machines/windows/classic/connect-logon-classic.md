---
title: Accedere a una VM di Azure classico | Microsoft Docs
description: Usare il portale di Azure per accedere a una macchina virtuale Windows creata con il modello di distribuzione classica.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 87ecc65d2d4802ae826f3260b66b26e0bbe414e6
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012360"
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Accedere a una macchina virtuale di Windows tramite il portale di Azure
Nel portale di Azure, si utilizza il pulsante **Connetti** per avviare una sessione di Desktop remoto e accedere a una macchina virtuale Windows.

Si desidera effettuare la connessione a una macchina virtuale Linux? Vedere [Come accedere a una macchina virtuale che esegue Linux](../../linux/mac-create-ssh-keys.md).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per informazioni sull'accesso a una macchina virtuale usando il modello di Resource Manager, vedere [qui](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale
1. Accedere al portale di Azure.
2. Fare clic sulla macchina virtuale a cui si desidera accedere. Il nome è indicato nel riquadro **Tutte le risorse**.

    ![Virtual-machine-locations](./media/connect-logon/azureportaldashboard.png)

1. Fare clic sul pulsante **Connetti** nella pagina delle proprietà della macchina virtuale. 
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

## <a name="next-steps"></a>Passaggi successivi
* Se il pulsante **Connetti** non è attivo o si verificano altri problemi di connessione per il Desktop remoto, provare a reimpostare la configurazione. Fare clic su **Ripristina accesso remoto** dal dashboard della macchina virtuale.

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* Per problemi con la password, provare a reimpostarla. Fare clic su **Ripristina password** sul lato sinistro del dashboard della macchina virtuale in **Supporto e risoluzione dei problemi**.

    ![Reset-password](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Se le precedenti istruzioni non sono sufficienti o non sono quelle necessarie, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). In questo articolo viene illustrato come diagnosticare e risolvere i problemi più comuni.
