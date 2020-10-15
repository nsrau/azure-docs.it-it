---
title: Abilitare la connessione del browser nelle macchine virtuali Azure DevTest Labs
description: DevTest Labs ora si integra con Azure Bastion, in qualità di proprietario del Lab, è possibile abilitare l'accesso a tutte le macchine virtuali del Lab tramite un browser.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 28a3c11f3df578265f9746a173fcb3029a132b26
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88870494"
---
# <a name="enable-browser-connection-on-azure-devtest-labs-virtual-machines"></a>Abilitare la connessione del browser nelle macchine virtuali Azure DevTest Labs 
DevTest Labs si integra con [Azure Bastion](../bastion/index.yml), che consente di connettersi alle macchine virtuali tramite un browser. Per prima cosa è necessario abilitare la connessione del browser nelle macchine virtuali del Lab.

In qualità di proprietario di un Lab, è possibile abilitare l'accesso a tutte le macchine virtuali del Lab tramite un browser. Non è necessario un client, un agente o software aggiuntivo. Azure Bastion offre connettività RDP/SSH sicura e senza problemi alle macchine virtuali direttamente nel portale di Azure tramite TLS. Quando ci si connette tramite Azure Bastion, le macchine virtuali non necessitano di un indirizzo IP pubblico. Per altre informazioni, vedere [che cos'è Azure Bastion?](../bastion/bastion-overview.md)


Questo articolo illustra come abilitare la connessione del browser nelle macchine virtuali del Lab.

## <a name="prerequisites"></a>Prerequisiti 
- Distribuire un host Bastion nella rete virtuale del lab esistente **(o)** connettere il Lab con una rete virtuale configurata con Bastion.
Per informazioni su come distribuire un host Bastion in una rete virtuale, vedere  [creare un host Bastion di Azure](../bastion/bastion-create-host-portal.md). Quando si crea l'host Bastion, selezionare la rete virtuale del Lab. 
- L'utente del lab deve avere un ruolo **lettore** nell'host Bastion e nella rete virtuale in cui è configurato il Bastion. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Creare una seconda rete secondaria nella rete virtuale Bastion
In primo luogo, è necessario creare una seconda subnet nella rete virtuale Bastion poiché il AzureBastionSubnet non consente la creazione di risorse non bastione. Creare un'altra subnet all'interno della rete virtuale Bastion, come illustrato nella figura seguente:

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
1. Se si dispone di una rete virtuale precedente per il Lab, rimuoverla selezionando **...*  e **Rimuovi**. 

## <a name="enable-browser-connection"></a>Abilita connessione browser 

Quando si dispone di una rete virtuale configurata con Bastion nel Lab, in qualità di proprietario del Lab, è possibile abilitare la connessione del browser sulle macchine virtuali del Lab.

Per abilitare la connessione del browser nelle macchine virtuali del Lab, attenersi alla procedura seguente:

1. Nella portale di Azure passare al *Lab*.
1. Selezionare **Configuration and policies** (Configurazione e criteri).
1. In **Impostazioni**selezionare **browser Connetti**. Se questa opzione non viene visualizzata, chiudere la pagina **criteri di configurazione** e riaprirla. 

    ![Abilita connessione browser](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente per informazioni su come connettersi alle macchine virtuali usando un browser: [connettersi alle macchine virtuali tramite un browser](connect-virtual-machine-through-browser.md)
