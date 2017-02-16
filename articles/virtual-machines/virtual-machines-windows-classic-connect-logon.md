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
ms.date: 07/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: f8fb116f14f771947f321f59d9880df4cdfe6859


---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>Accedere a una macchina virtuale di Windows tramite il portale di Azure classico
Nel portale di Azure classico, si utilizza il pulsante **Connetti** per avviare una sessione di Desktop remoto e accedere a una macchina virtuale Windows.

Si desidera effettuare la connessione a una macchina virtuale Linux? Vedere [Come accedere a una macchina virtuale che esegue Linux](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Vedere come [eseguire questi passaggi tramite il nuovo portale di Azure](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per informazioni sull'accesso a una macchina virtuale usando il modello di Resource Manager, vedere [qui](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="video-walkthrough"></a>Procedura dettagliata video
Di seguito è riportata una procedura video dettagliata dei passaggi in questa esercitazione. Vengono inoltre descritti gli endpoint e le porte pubbliche e private utilizzate per la connessione a una macchina virtuale di Windows in Azure.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Logging-On-To-VM-Running-Windows-Server-on-Azure/player]



## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale
1. Accedere al portale di Microsoft Azure classico.
2. Fare clic su **Macchine virtuali**, quindi selezionare la macchina virtuale.
3. Nella barra dei comandi in fondo alla pagina, fare clic su **Connetti**.
   
    ![Accesso alla macchina virtuale](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)

> [!TIP]
> Se il **pulsante di connessione** non è disponibile, vedere i suggerimenti sulla risoluzione dei problemi alla fine di questo articolo.
> 
> 

## <a name="log-on-to-the-virtual-machine"></a>Accesso alla macchina virtuale
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Passaggi successivi
* Se il pulsante **Connetti** non è attivo o si verificano altri problemi di connessione per il Desktop remoto, provare a reimpostare la configurazione. Dal dashboard della macchina virtuale, in **Riepilogo rapido** fare clic su **Reimposta configurazione remota**.
* Per problemi con la password, provare a reimpostarla. Dal dashboard della macchina virtuale, in **Riepilogo rapido** fare clic su **Reimposta password**.

Se le precedenti istruzioni non sono sufficienti o non sono quelle necessarie, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). In questo articolo viene illustrato come diagnosticare e risolvere i problemi più comuni.




<!--HONumber=Feb17_HO3-->


