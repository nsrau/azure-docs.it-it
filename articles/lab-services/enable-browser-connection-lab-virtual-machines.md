---
title: Abilitare la connessione del browser nelle macchine virtuali di Azure DevTest Labs . Documenti Microsoft
description: DevTest Labs ora si integra con Azure Bastion, come proprietario del lab è possibile abilitare l'accesso a tutte le macchine virtuali lab tramite un browser.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: e2dd642139ae082cc0d0838e61399c549d2d812a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74970779"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Abilitare la connessione del browser nelle macchine virtuali labEnable browser connection on lab virtual machines 
DevTest Labs si integra con [Azure Bastion](https://docs.microsoft.com/azure/bastion/), che consente di connettersi alle macchine virtuali tramite un browser. È innanzitutto necessario abilitare la connessione del browser nelle macchine virtuali lab.

In qualità di proprietario di un lab è possibile abilitare l'accesso a tutte le macchine virtuali lab tramite un browser. Non è necessario un client, un agente o software aggiuntivo. Azure Bastion fornisce connettività RDP/SSH sicura e trasparente alle macchine virtuali direttamente nel portale di Azure tramite SSL. Quando ci si connette tramite Il Bastione di Azure, le macchine virtuali non richiedono un indirizzo IP pubblico. Per altre informazioni, vedere [Che cos'è il Bastione](../bastion/bastion-overview.md) di Azure.


In questo articolo viene illustrato come abilitare la connessione del browser nelle macchine virtuali lab.

## <a name="prerequisites"></a>Prerequisiti 
Distribuire un host Bastion nella rete virtuale **(OR)** del lab esistente connettere il lab a una rete virtuale configurata da Bastion. 

Per informazioni su come distribuire un host Bastion in una rete virtuale, vedere [Creare un host di Azure Bastion.](../bastion/bastion-create-host-portal.md) Quando si crea l'host Bastion, selezionare la rete virtuale del lab. 

In primo luogo, è necessario creare una seconda subnet nella rete virtuale Bastion perché AzureBastionSubnet non consente la creazione di risorse non Bastion in esso. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Creare una seconda rete secondaria nella rete virtuale BastionCreate a second sub net in the Bastion virtual network
Non è possibile creare macchine virtuali lab in una subnet di Azure Bastion.You can't create lab VMs in an Azure Bastion subnet. Creare un'altra subnet all'interno della rete virtuale Bastion, come illustrato nell'immagine seguente:Create another subnet within the Bastion virtual network as shown in the following image:

![Seconda subnet nella rete virtuale di Azure BastionSecond subnet in Azure Bastion virtual network](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>Abilitare la creazione di macchine virtuali nella subnetEnable VM creation in the subnet
A questo punto, abilitare la creazione di macchine virtuali in questa subnet seguendo questi passaggi:Now, enable the creation of VMs in this subnet by following these steps: 

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare **Tutti i servizi** nel menu di navigazione a sinistra. 
1. Selezionare **DevTest Labs** dall'elenco. 
1. Nell'elenco dei laboratori selezionare *il lab*. 

    > [!NOTE]
    > Il bastione di Azure è ora disponibile nelle aree seguenti: Stati Uniti occidentali, Stati Uniti orientali, Europa occidentale, Stati Uniti centro-meridionali, Australia orientale e Giappone est. Quindi, creare un lab in una di queste aree se il lab non è in una di esse. 
    
1. Selezionare **Configurazione e criteri** nella sezione **Impostazioni** del menu a sinistra. 
1. Selezionare **Reti virtuali**.
1. Selezionare **Aggiungi** dalla barra degli strumenti. 
1. Selezionare la **rete virtuale** in cui è distribuito l'host Bastion. 
1. Selezionare la subnet per le macchine virtuali, non **AzureBastionSubnet**, l'altra creata in precedenza. Chiudere la pagina e riaprirla se la subnet non è presente nell'elenco nella parte inferiore. 

    ![Abilitare la creazione di macchine virtuali nella subnetEnable VM creation in the subnet](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. Selezionare l'opzione **Usa nella creazione della macchina virtuale.** 
1. Sulla barra degli strumenti selezionare **Salva**. 
1. Se si dispone di una vecchia rete virtuale per il lab, rimuoverla selezionando *...*  e **Rimuovi**. 

## <a name="enable-browser-connection"></a>Abilitare la connessione del browser 

Dopo aver configurato una rete virtuale Bastion all'interno del lab, come proprietario del lab, è possibile abilitare la connessione tramite browser nelle macchine virtuali lab.

Per abilitare la connessione tramite browser nelle macchine virtuali lab, attenersi alla seguente procedura:

1. Nel portale di Azure passare al *lab.*
1. Selezionare **Configuration and policies** (Configurazione e criteri).
1. In **Impostazioni**selezionare **Connessione browser**. Se questa opzione non è disponibile, chiudere la pagina Criteri di **configurazione** e riaprirla. 

    ![Abilitare la connessione del browser](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente per informazioni su come connettersi alle macchine virtuali usando un browser: [Connettersi alle macchine virtuali tramite un browserSee](connect-virtual-machine-through-browser.md) the following article to learn how to connect to your VMs using a browser: Connect to your virtual machines through a browser
