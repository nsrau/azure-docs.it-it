---
title: "Creare un set di scalabilità di macchine virtuali nel portale di Azure | Microsoft Docs"
description: "Informazioni su come creare rapidamente un set di scalabilità di macchine virtuali nel portale di Azure"
keywords: "set di scalabilità di macchine virtuali"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a501a852a317ec7d087904c3a675ebefce1bece0
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Creare un set di scalabilità di macchine virtuali nel portale di Azure
Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. È possibile ridimensionare manualmente il numero di VM nel set di scalabilità o definire regole di scalabilità automatica in base all'utilizzo delle risorse, ad esempio la CPU, alla richiesta di memoria o al traffico di rete. In questo articolo introduttivo si crea un set di scalabilità di macchine virtuali nel portale di Azure. È anche possibile creare un set di scalabilità con l'[interfaccia della riga di comando di Azure 2.0](virtual-machine-scale-sets-create-cli.md) o con [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="log-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo http://portal.azure.com.


## <a name="create-virtual-machine-scale-set"></a>Creare un set di scalabilità di macchine virtuali
È possibile distribuire un set di scalabilità con un'immagine di Windows Server o di Linux, ad esempio RHEL, CentOS, Ubuntu o SLES.

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.
2. Cercare *set di scalabilità*, scegliere **Set di scalabilità di macchine virtuali** e quindi selezionare **Crea**.
3. Immettere un nome per il set di scalabilità, ad esempio *myScaleSet*.
4. Selezionare il tipo di sistema operativo desiderato, ad esempio *Data Center di Windows Server 2016*.
5. Immettere il nome del gruppo di risorse desiderato, ad esempio *myResourceGroup*e la posizione, ad esempio *East US*.
6. Immettere il nome utente desiderato e selezionare il tipo di autenticazione preferito.
    - Il valore del campo **Password** deve avere una lunghezza di almeno 12 caratteri e soddisfare tre dei quattro requisiti di complessità seguenti: una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale. Per altre informazioni, fare riferimento ai [requisiti relativi a nome utente e password](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
    - Se si seleziona un'immagine del disco del sistema operativo Linux, è invece possibile scegliere **Chiave pubblica SSH**. Specificare solo la chiave pubblica, ad esempio *~/.ssh/id_rsa.pub*. Per [creare e usare chiavi SSH](../virtual-machines/linux/mac-create-ssh-keys.md), è possibile usare Azure Cloud Shell nel portale.

7. Immettere un valore in **Nome indirizzo IP pubblico**, ad esempio *myPublicIP*.
8. Immettere un valore univoco in **Etichetta del nome di dominio**, ad esempio *myuniquedns*. Questa etichetta DNS costituisce la base del nome di dominio completo per il servizio di bilanciamento del carico davanti al set di scalabilità.
9. Per confermare le opzioni del set di scalabilità, selezionare **Crea**.

    ![Creare un set di scalabilità di macchine virtuali nel portale di Azure](./media/virtual-machine-scale-sets-create-portal/create-scale-set.png)


## <a name="connect-to-a-vm-in-the-scale-set"></a>Connettersi a una VM nel set di scalabilità
Quando si crea un set di scalabilità nel portale, viene creato un servizio di bilanciamento del carico. Le regole NAT (Network Address Translation) consentono di distribuire il traffico alle istanze del set di scalabilità per la connettività remota, ad esempio RDP o SSH.

Per visualizzare queste regole e le informazioni di connessione per le istanze del set di scalabilità:

1. Selezionare il gruppo di risorse creato nel passaggio precedente, ad esempio *myResourceGroup*.
2. Nell'elenco di risorse selezionare un valore per **Servizio di bilanciamento del carico**, ad esempio *myScaleSetLab*.
3. Scegliere **Regole NAT in ingresso** dal menu nella parte sinistra della finestra.

    ![Le regole NAT in ingresso consentono di connettersi alle istanze del set di scalabilità delle macchine virtuali](./media/virtual-machine-scale-sets-create-portal/inbound-nat-rules.png)

È possibile connettersi a ogni macchina virtuale nel set di scalabilità usando le regole NAT. In ogni istanza di macchina virtuale sono indicati un indirizzo IP di destinazione e un valore della porta TCP. Ad esempio, se l'indirizzo IP di destinazione è *104.42.1.19* e la porta TCP è *50001*, la connessione all'istanza di macchina virtuale viene stabilita come indicato di seguito:

- Per un set di scalabilità di Windows, connettersi all'istanza di macchina virtuale con RDP su `104.42.1.19:50001`
- Per un set di scalabilità di Linux, connettersi all'istanza di macchina virtuale con SSH su `ssh azureuser@104.42.1.19 -p 50001`

Quando richiesto, immettere le credenziali specificate nel passaggio precedente quando si è creato il set di scalabilità. Le istanze del set di scalabilità sono normali macchine virtuali con cui è possibile interagire nel modo consueto. Per altre informazioni su come distribuire ed eseguire applicazioni nelle istanze del set di scalabilità, vedere [Distribuire l'applicazione nei set di scalabilità delle macchine virtuali](virtual-machine-scale-sets-deploy-app.md).


## <a name="clean-up-resources"></a>Pulire le risorse
Quando il gruppo di risorse, il set di scalabilità e tutte le risorse correlate non sono più necessari, è possibile eliminarli. A tale scopo selezionare il gruppo di risorse per la macchina virtuale e fare clic su **Elimina**.


## <a name="next-steps"></a>Passaggi successivi
In questo articolo introduttivo si è creato un set di scalabilità di base nel portale di Azure. Per ottenere scalabilità e automazione maggiori, espandere il set di scalabilità con le procedure seguenti:

- [Distribuire l'applicazione nei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-deploy-app.md)
- È possibile applicare la scalabilità automatica con [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md), l'[interfaccia della riga di comando di Azure](virtual-machine-scale-sets-autoscale-cli.md) o il [portale di Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Usare gli aggiornamenti automatici del sistema operativo per le istanze di macchina virtuale del set di scalabilità](virtual-machine-scale-sets-automatic-upgrade.md)
