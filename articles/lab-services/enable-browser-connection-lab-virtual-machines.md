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
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: e2dd642139ae082cc0d0838e61399c549d2d812a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970779"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Abilitare la connessione del browser nelle macchine virtuali del Lab 
DevTest Labs si integra con [Azure Bastion](https://docs.microsoft.com/azure/bastion/), che consente di connettersi alle macchine virtuali tramite un browser. Per prima cosa è necessario abilitare la connessione del browser nelle macchine virtuali del Lab.

In qualità di proprietario di un Lab, è possibile abilitare l'accesso a tutte le macchine virtuali del Lab tramite un browser. Non è necessario un client, un agente o software aggiuntivo. Azure Bastion offre connettività RDP/SSH sicura e ininterrotta con le macchine virtuali direttamente nel portale di Azure tramite SSL. Quando ci si connette tramite Azure Bastion, le macchine virtuali non necessitano di un indirizzo IP pubblico. Per altre informazioni, vedere [che cos'è Azure Bastion?](../bastion/bastion-overview.md)


Questo articolo illustra come abilitare la connessione del browser nelle macchine virtuali del Lab.

## <a name="prerequisites"></a>Prerequisiti 
Distribuire un host Bastion nella rete virtuale del lab esistente **(o)** connettere il Lab con una rete virtuale configurata con Bastion. 

Per informazioni su come distribuire un host Bastion in una rete virtuale, vedere [creare un host Bastion di Azure](../bastion/bastion-create-host-portal.md). Quando si crea l'host Bastion, selezionare la rete virtuale del Lab. 

In primo luogo, è necessario creare una seconda subnet nella rete virtuale Bastion poiché il AzureBastionSubnet non consente la creazione di risorse non bastione. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Creare una seconda rete secondaria nella rete virtuale Bastion
Non è possibile creare macchine virtuali Lab in una subnet di Azure Bastion. Creare un'altra subnet all'interno della rete virtuale Bastion, come illustrato nella figura seguente:

![Seconda subnet nella rete virtuale di Azure Bastion](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>Abilitare la creazione della macchina virtuale nella subnet
A questo punto, abilitare la creazione di macchine virtuali in questa subnet attenendosi alla procedura seguente: 

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **tutti i servizi** nel menu di spostamento a sinistra. 
1. Selezionare **DevTest Labs** dall'elenco. 
1. Dall'elenco dei Lab selezionare *il Lab*. 

    > [!NOTE]
    > Azure Bastion è ora disponibile a livello generale nelle aree seguenti: Stati Uniti occidentali, Stati Uniti orientali, Europa occidentale, Stati Uniti centro-meridionali, Australia orientale e Giappone orientale. Quindi, creare un Lab in una di queste aree se il Lab non è in uno di essi. 
    
1. Selezionare **configurazione e criteri** nella sezione **Impostazioni** nel menu a sinistra. 
1. Selezionare **reti virtuali**.
1. Selezionare **Aggiungi** dalla barra degli strumenti. 
1. Selezionare la **rete virtuale** in cui è distribuito l'host Bastion. 
1. Selezionare la subnet per le macchine virtuali, non il **AzureBastionSubnet**, l'altra creata in precedenza. Chiudere la pagina e riaprirla se la subnet non è visibile nell'elenco in basso. 

    ![Abilitare la creazione della macchina virtuale nella subnet](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. Selezionare **USA nell'opzione creazione macchina virtuale** . 
1. Sulla barra degli strumenti selezionare **Salva**. 
1. Se si dispone di una rete virtuale precedente per il Lab, rimuoverla selezionando * *...*  e **Rimuovi**. 

## <a name="enable-browser-connection"></a>Abilita connessione browser 

Quando si dispone di una rete virtuale configurata con Bastion nel Lab, in qualità di proprietario del Lab, è possibile abilitare la connessione del browser sulle macchine virtuali del Lab.

Per abilitare la connessione del browser nelle macchine virtuali del Lab, attenersi alla procedura seguente:

1. Nella portale di Azure passare al *Lab*.
1. Selezionare **Configuration and policies** (Configurazione e criteri).
1. In **Impostazioni**selezionare **browser Connetti**. Se questa opzione non viene visualizzata, chiudere la pagina **criteri di configurazione** e riaprirla. 

    ![Abilita connessione browser](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Fasi successive
Vedere l'articolo seguente per informazioni su come connettersi alle macchine virtuali usando un browser: [connettersi alle macchine virtuali tramite un browser](connect-virtual-machine-through-browser.md)
