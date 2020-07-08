---
title: Connettersi alle macchine virtuali tramite un browser-Azure | Microsoft Docs
description: Informazioni su come connettersi alle macchine virtuali tramite un browser.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b1e50a15673199a4bf95956ae66e6b58d6d41d0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483738"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Connettersi alle macchine virtuali tramite un browser 

DevTest Labs si integra con [Azure Bastion](https://docs.microsoft.com/azure/bastion/), che consente di connettersi alle macchine virtuali tramite un browser. Per informazioni sull'abilitazione di questa funzionalità in DevTest Labs, vedere [Enable browser connection on Lab Virtual Machines](enable-browser-connection-lab-virtual-machines.md).

Una volta abilitata la *connessione del browser* , gli utenti del Lab possono accedere alle macchine virtuali tramite un browser.  

## <a name="create-a-lab-virtual-machine"></a>Creare una macchina virtuale Lab

Per prima cosa è necessario creare la macchina virtuale Lab all'interno di una rete virtuale in cui è configurato Bastion. Selezionare la seconda **subnet** creata e non la AzureBastionSubnet. È possibile selezionare una rete virtuale durante la creazione della macchina virtuale passando alla scheda **Impostazioni avanzate** .

![Creare macchina virtuale](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Avvia la macchina virtuale in un browser

Una volta creata la macchina virtuale, è possibile avviarla in un browser facendo clic sul pulsante di *connessione del browser* e immettendo il nome utente e la password per il computer.  

![Avvia in un browser](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere una macchina virtuale in un lab in Azure DevTest Labs](devtest-lab-add-vm.md)
