---
title: Creare una scala di macchine virtuali Linux impostata nel portale di Azure | Documenti Microsoft
description: "Imparare a creare rapidamente un scalabilità della macchina virtuale nel portale di Azure"
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
ms.openlocfilehash: fa6bf6b34d8b93ffa9aceaf7c6112c63d4cb9f1c
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Creare un Set di scalabilità della macchina virtuale nel portale di Azure
Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. È possibile ridimensionare manualmente il numero di VM nel set di scalabilità o definire regole di scalabilità automatica in base all'utilizzo delle risorse, ad esempio la CPU, alla richiesta di memoria o al traffico di rete. In questo articolo avviato recupero crei un scalabilità di macchine virtuali impostate nel portale di Azure. È inoltre possibile creare un set di scalabilità con la [CLI di Azure 2.0](virtual-machine-scale-sets-create-cli.md) o [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="log-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo http://portal.azure.com.


## <a name="create-virtual-machine-scale-set"></a>Creare set di scalabilità della macchina virtuale
È possibile distribuire un set di scalabilità con un'immagine di Linux, ad esempio RHEL, CentOS, Ubuntu o SLES o l'immagine di Windows Server.

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.
2. Cercare *set di scalabilità*, scegliere **set di scalabilità della macchina virtuale**, quindi selezionare **crea**.
3. Immettere un nome per il set di scalabilità, ad esempio *myScaleSet*.
4. Selezionare il tipo del sistema operativo desiderato, ad esempio *Data Center di Windows Server 2016*.
5. Immettere il nome del gruppo risorsa desiderata, ad esempio *myResourceGroup*e la posizione, ad esempio *Stati Uniti orientali*.
6. Immettere il nome utente desiderato e selezionare il tipo di autenticazione preferito.
    - Oggetto **Password** deve essere almeno 12 caratteri lungo e soddisfano i requisiti di complessità delle quattro seguenti tre: una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale. Per ulteriori informazioni, vedere [requisiti di nome utente e password](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
    - Se si seleziona un'immagine del disco del sistema operativo Linux, è possibile scegliere **chiave pubblica SSH**. Fornire solo la chiave pubblica, ad esempio *~/.ssh/id_rsa.pub*. È possibile utilizzare la Shell di Cloud di Azure dal portale per [creare e usare le chiavi SSH](../virtual-machines/linux/mac-create-ssh-keys.md).

7. Immettere un **nome indirizzo IP pubblico**, ad esempio *myPublicIP*.
8. Immettere un univoco **etichetta del nome di dominio**, ad esempio *myuniquedns*. Questa etichetta DNS costituisce la base del nome FQDN per il bilanciamento del carico davanti il set di scalabilità.
9. Per confermare la scala di imposta le opzioni, selezionare **crea**.

    ![Creare una scala di macchine virtuali impostata nel portale di Azure](./media/virtual-machine-scale-sets-create-portal/create-scale-set.png)


## <a name="connect-to-a-vm-in-the-scale-set"></a>Connettersi a una VM nel set di scalabilità
Quando si crea un set nel portale di scalabilità, viene creato un servizio di bilanciamento del carico. Le regole Network Address Translation (NAT) vengono utilizzate per distribuire il traffico alle istanze del set di scalabilità per la connettività remota, ad esempio RDP o SSH.

Per visualizzare questi NAT regole e le informazioni di connessione per la scala set di istanze:

1. Selezionare il gruppo di risorse creato nel passaggio precedente, ad esempio *myResourceGroup*.
2. Nell'elenco di risorse, selezionare il **bilanciamento del carico**, ad esempio *myScaleSetLab*.
3. Scegliere **regole NAT in ingresso** dal menu sul lato sinistro della finestra.

    ![Le regole NAT in ingresso che consentono di connettersi alle istanze di set di scalabilità di macchine virtuali](./media/virtual-machine-scale-sets-create-portal/inbound-nat-rules.png)

È possibile connettersi a ogni macchina virtuale nel set di scalabilità usando le regole NAT. Ogni istanza VM viene visualizzato un indirizzo IP di destinazione e il valore della porta TCP. Ad esempio, se l'indirizzo IP di destinazione è *104.42.1.19* e la porta TCP è *50001*, connettersi all'istanza di macchina virtuale come indicato di seguito:

- Per un set di scalabilità di Windows, connettersi all'istanza di macchina virtuale con RDP in`104.42.1.19:50001`
- Per un set di scalabilità di Linux, connettersi all'istanza di macchina virtuale con SSH in`ssh azureuser@104.42.1.19 -p 50001`

Quando richiesto, immettere le credenziali specificate nel passaggio precedente, una volta creato il set di scalabilità. Le istanze di set di scalabilità sono regolare le macchine virtuali che è possibile interagire con come di consueto. Per ulteriori informazioni su come distribuire ed eseguire applicazioni sulla scala set di istanze, vedere [distribuire l'applicazione nel set di scalabilità di macchine virtuali](virtual-machine-scale-sets-deploy-app.md)


## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessario, eliminare il gruppo di risorse, set di scalabilità e tutte risorse correlate. A tale scopo selezionare il gruppo di risorse per la macchina virtuale e fare clic su **Elimina**.


## <a name="next-steps"></a>Passaggi successivi
In questo articolo avviato recupero creata una scala di base impostata nel portale di Azure. Per maggiore scalabilità e l'automazione, espandere il set di scalabilità con i seguenti articoli:

- [Distribuire l'applicazione nel set di scalabilità di macchine virtuali](virtual-machine-scale-sets-deploy-app.md)
- Applicare la scalabilità automatica con [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md), [CLI di Azure](virtual-machine-scale-sets-autoscale-cli.md), o [portale di Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Utilizzare gli aggiornamenti automatici del sistema operativo per le istanze VM set di scalabilità](virtual-machine-scale-sets-automatic-upgrade.md)
