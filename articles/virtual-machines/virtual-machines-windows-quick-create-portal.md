---
title: Avvio rapido in Azure - Creare un portale di VM Windows | Documentazione Microsoft
description: Avvio rapido in Azure - Creare un portale di VM Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 56e9deea4e070a99e47b7de7c5686526a4528fb6
ms.lasthandoff: 03/15/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Creare una macchina virtuale Windows con il portale di Azure

È possibile creare macchine virtuali di Azure tramite il portale di Azure. Questo metodo fornisce un'interfaccia utente basata sul browser per la creazione e la configurazione delle VM e di tutte le risorse di Azure correlate.

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-virtual-machine"></a>Crea macchina virtuale

2. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.

3. Selezionare **Calcolo** nel pannello **Nuovo**, selezionare **Windows Server 2016 Datacenter** nel pannello **Calcolo** e poi fare clic sul pulsante **Crea**.

4. Compilare il modulo **Generale** della macchina virtuale. Per **Gruppo di risorse** creare un nuovo gruppo. Un gruppo di risorse è un contenitore logico in cui vengono create e gestite collettivamente le risorse di Azure. Al termine fare clic su **OK**.

    ![Immettere le informazioni di base sulla VM nel pannello del portale](./media/virtual-machine-quick-start/create-windows-vm-portal-basic-blade.png)  

5. Scegliere una dimensione per la VM e fare clic su **Seleziona**. 

6. Nel pannello delle impostazioni selezionare **Sì** in **Usa dischi gestiti**, mantenere i valori predefiniti per le altre impostazioni e fare clic su **OK**.

7. Nella pagina del riepilogo fare clic su **OK** per avviare la distribuzione della macchina virtuale.

8. Per monitorare lo stato di distribuzione fare clic sulla macchina virtuale. La VM è disponibile nel dashboard del portale di Azure o selezionando **Macchine virtuali** nel menu a sinistra. Dopo che la VM è stata creata, lo stato cambia da **Distribuzione in corso** a **In esecuzione**.

## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

Dopo aver completato la distribuzione, creare una connessione desktop remoto con la macchina virtuale.

1. Fare clic sul pulsante **Connetti** nel pannello della macchina virtuale. Verrà creato e scaricato un file Remote Desktop Protocol (file con estensione rdp), che è analogo a un collegamento per la connessione alla macchina virtuale. Aprire questo file per connettersi alla VM.

    ![Portale 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

3. Fare clic su **Connetti** nella finestra della connessione desktop remoto.

    ![Portale 10](./media/virtual-machine-quick-start/portal-quick-start-10.png) 

4. Digitare il nome utente e la password specificati al momento della creazione della macchina virtuale e quindi fare clic su **OK**.

5. Potrebbe essere visualizzato un avviso di certificato, fare clic su **Sì** per procedere con la connessione.

## <a name="delete-virtual-machine"></a>Eliminare una macchina virtuale

Quando non serve più, eliminare il gruppo di risorse e tutte le risorse correlate. A tale scopo selezionare il gruppo di risorse nel pannello della macchina virtuale e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

[Installare un ruolo e configurare l'esercitazione sul firewall](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Esplorare gli esempi dell'interfaccia della riga di comando per la distribuzione della VM](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
