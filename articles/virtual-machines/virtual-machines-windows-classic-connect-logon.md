---
title: Accedere a una VM di Azure classico | Microsoft Docs
description: Usare il portale di Azure classico per accedere a una macchina virtuale di Windows creata con il modello di distribuzione classico.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 3c542d2fb8b644ead469272c628b64de72a371ad
ms.openlocfilehash: 3051eba69dcba7162ba026f7720c9f8d873c9ea2
ms.lasthandoff: 02/22/2017


---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Accedere a una macchina virtuale di Windows tramite il portale di Azure
Nel portale di Azure, si utilizza il pulsante **Connetti** per avviare una sessione di Desktop remoto e accedere a una macchina virtuale Windows.

Si desidera effettuare la connessione a una macchina virtuale Linux? Vedere [Come accedere a una macchina virtuale che esegue Linux](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per informazioni sull'accesso a una macchina virtuale usando il modello di Resource Manager, vedere [qui](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale
1. Accedere al portale di Azure.
2. Fare clic sulla macchina virtuale a cui si desidera accedere. Il nome è indicato nel riquadro **Tutte le risorse**.

    ![Virtual-machine-locations](./media/virtual-machines-windows-classic-connect-logon/azureportaldashboard.png)

3. Fare clic su **Connetti** sulla barra dei comandi sopra il dashboard della macchina virtuale.

    ![Icona di connessione per la macchina virtuale](./media/virtual-machines-windows-classic-connect-logon/virtualmachine_dashboard_connect.png)

<!-- Don't know if this still applies
     I think we can zap this.
> [!TIP]
> If the **Connect** button isn't available, see the troubleshooting tips at the end of this article.
>
>
-->

## <a name="log-on-to-the-virtual-machine"></a>Accesso alla macchina virtuale
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Passaggi successivi
* Se il pulsante **Connetti** non è attivo o si verificano altri problemi di connessione per il Desktop remoto, provare a reimpostare la configurazione. Fare clic su **Ripristina accesso remoto** dal dashboard della macchina virtuale.

    ![Reset-remote-access](./media/virtual-machines-windows-classic-connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* Per problemi con la password, provare a reimpostarla. Fare clic su **Ripristina password** sul lato sinistro del dashboard della macchina virtuale in **Supporto e risoluzione dei problemi**.

    ![Reset-password](./media/virtual-machines-windows-classic-connect-logon/virtualmachine_dashboard_reset_password.png)

Se le precedenti istruzioni non sono sufficienti o non sono quelle necessarie, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). In questo articolo viene illustrato come diagnosticare e risolvere i problemi più comuni.

