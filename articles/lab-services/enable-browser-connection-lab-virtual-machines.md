---
title: Abilitare la connessione del browser nelle macchine virtuali Azure DevTest Labs | Microsoft Docs
description: DevTest Labs ora si integra con Azure Bastion, in qualità di proprietario del Lab, è possibile abilitare l'accesso a tutte le macchine virtuali del Lab tramite un browser.
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
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 080dd91b2ab6792debfae3a3ccc97b0927015de4
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580141"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Abilitare la connessione del browser nelle macchine virtuali del Lab 

DevTest Labs si integra con [Azure Bastion](https://docs.microsoft.com/azure/bastion/), che consente di connettersi alle macchine virtuali tramite un browser. Per prima cosa è necessario abilitare la connessione del browser nelle macchine virtuali del Lab.

In qualità di proprietario di un Lab, è possibile abilitare l'accesso a tutte le macchine virtuali del Lab tramite un browser. Non è necessario un client, un agente o software aggiuntivo. Azure Bastion offre connettività RDP/SSH sicura e ininterrotta con le macchine virtuali direttamente nel portale di Azure tramite SSL. Quando ci si connette tramite Azure Bastion, le macchine virtuali non necessitano di un indirizzo IP pubblico. Per altre informazioni, vedere [che cos'è Azure Bastion?](../bastion/bastion-overview.md)


Questo articolo illustra come abilitare la connessione del browser nelle macchine virtuali del Lab.

## <a name="prerequisites"></a>Prerequisiti 
Distribuire un host Bastion nella rete virtuale del lab esistente **(o)** connettere il Lab con un Bastion configurato VNet. 

Per informazioni su come distribuire un host Bastion in una VNet, vedere [creare un host Bastion di Azure (anteprima)](../bastion/bastion-create-host-portal.md). Quando si crea l'host Bastion, selezionare la rete virtuale del Lab. 

Per informazioni su come connettere il Lab con un VNet configurato per Bastion, vedere [configurare una rete virtuale in Azure DevTest Labs](devtest-lab-configure-vnet.md). Selezionare il VNet con l'host Bastion distribuito e il **AzureBastionSubnet** . Ecco di seguito i passaggi dettagliati: 

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **tutti i servizi** nel menu di spostamento a sinistra. 
1. Selezionare **DevTest Labs** dall'elenco. 
1. Dall'elenco dei Lab selezionare *il Lab*. 

    > [!NOTE]
    > Azure Bastion è ora disponibile a livello generale nelle aree seguenti: Stati Uniti occidentali, Stati Uniti orientali, Europa occidentale, Stati Uniti centro-meridionali, Australia orientale e Giappone orientale. Quindi, creare un Lab in una di queste aree se il Lab non è in uno di essi. 
    
1. Selezionare **configurazione e criteri** nella sezione **Impostazioni** nel menu a sinistra. 
1. Selezionare **reti virtuali**.
1. Selezionare **Aggiungi** dalla barra degli strumenti. 
1. Selezionare il **VNet** con l'host Bastion distribuito. 
1. Selezionare la subnet: **AzureBastionSubnet**. 

    ![Subnet](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Selezionare **USA nell'opzione creazione macchina virtuale** . 
1. Sulla barra degli strumenti selezionare **Salva**. 
1. Se si dispone di una VNet precedente per il Lab, rimuoverla selezionando * *...*  e **Rimuovi**. 

## <a name="enable-browser-connection"></a>Abilita connessione browser 

Quando si dispone di un Bastion configurato VNet all'interno del Lab, come proprietario del Lab, è possibile abilitare la connessione del browser sulle macchine virtuali del Lab.

Per abilitare la connessione del browser nelle macchine virtuali del Lab, attenersi alla procedura seguente:

1. Nella portale di Azure passare al *Lab*.
1. Selezionare **Configuration and policies** (Configurazione e criteri).
1. In **Impostazioni**selezionare **browser Connetti (anteprima)** .

    ![Abilita connessione browser](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Fasi successive
Vedere l'articolo seguente per informazioni su come connettersi alle macchine virtuali usando un browser: [connettersi alle macchine virtuali tramite un browser](connect-virtual-machine-through-browser.md)
