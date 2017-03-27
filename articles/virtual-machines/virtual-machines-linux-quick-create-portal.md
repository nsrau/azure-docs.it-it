---
title: Avvio rapido in Azure - Creare un portale di VM | Documentazione Microsoft
description: Avvio rapido in Azure - Creare un portale di VM
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 348407b57bbb3329d7d27a6f38623e052aecc58b
ms.lasthandoff: 03/14/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Creare una macchina virtuale Linux con il portale di Azure

È possibile creare macchine virtuali di Azure tramite il portale di Azure. Questo metodo fornisce un'interfaccia utente basata sul browser per la creazione e la configurazione delle VM e di tutte le risorse di Azure correlate.

Prima di iniziare sono necessarie una chiave SSH pubblica e una privata. Per informazioni dettagliate sulla creazione delle chiavi SSH per Azure, vedere [Create SSH keys for Azure](./virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Creare chiavi SSH per Azure).

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-virtual-machine"></a>Crea macchina virtuale

2. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.

3. Selezionare **Calcolo** nel pannello **Nuovo**, selezionare **Ubuntu Server 16.04 LTS** nel pannello **Calcolo** e quindi fare clic sul pulsante **Crea**.

4. Compilare il modulo **Generale** della macchina virtuale. Per **Tipo di autenticazione** si consiglia di usare SSH. Quando si incolla la **chiave pubblica SSH**, prestare attenzione a rimuovere gli eventuali spazi iniziali o finali. Per **Gruppo di risorse** creare un nuovo gruppo. Un gruppo di risorse è un contenitore logico in cui vengono create e gestite collettivamente le risorse di Azure. Al termine fare clic su **OK**.

    ![Immettere le informazioni di base sulla VM nel pannello del portale](./media/virtual-machine-quick-start/create-vm-portal-basic-blade.png)  

5. Scegliere una dimensione per la VM e fare clic su **Seleziona**. 

    ![Selezionare una dimensione per la VM nel pannello del portale](./media/virtual-machine-quick-start/create-vm-portal-size-blade.png)

6. Nel pannello delle impostazioni selezionare **Sì** in **Usa dischi gestiti**, mantenere i valori predefiniti per le altre impostazioni e fare clic su **OK**.

7. Nella pagina del riepilogo fare clic su **OK** per avviare la distribuzione della macchina virtuale.

## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

Dopo aver completato la distribuzione, creare una connessione SSH con la macchina virtuale.

1. Fare clic sulla macchina virtuale. La VM è disponibile nella schermata iniziale del portale di Azure o selezionando **Macchine virtuali** nel menu a sinistra.

2. Scegliere il pulsante **Connetti** . Il pulsante Connetti mostra una stringa di connessione SSH che può essere usata per connettersi alla macchina virtuale.

    ![Portale 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

3. Usare il comando seguente per creare una sessione SSH. Sostituire la stringa di connessione con quella copiata dal portale di Azure.

```bash 
ssh <replace with IP address>
```
## <a name="delete-virtual-machine"></a>Eliminare una macchina virtuale

Quando non serve più, eliminare il gruppo di risorse e tutte le risorse correlate. A tale scopo selezionare il gruppo di risorse nel pannello della macchina virtuale e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

[Creare esercitazioni per macchine virtuali a disponibilità elevata](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Esplorare gli esempi dell'interfaccia della riga di comando per la distribuzione della VM](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

