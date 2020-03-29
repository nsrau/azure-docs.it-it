---
title: Connettersi alle macchine virtuali tramite un browser - Azure Documenti Microsoft
description: Informazioni su come connettersi alle macchine virtuali tramite un browser.
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
ms.openlocfilehash: 4549ee0f50c06040b70307c96b5ca96cd4162214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974292"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Connettersi alle macchine virtuali tramite un browserConnect to your virtual machines through a browser 

DevTest Labs si integra con [Azure Bastion](https://docs.microsoft.com/azure/bastion/), che consente di connettersi alle macchine virtuali tramite un browser. Per informazioni su come abilitare questa funzionalità in DevTest Labs, vedere [Abilitare la connessione del browser nelle macchine virtuali lab.](enable-browser-connection-lab-virtual-machines.md)

Una volta abilitata la connessione del *browser,* gli utenti del lab possono accedere alle macchine virtuali tramite un browser.  

## <a name="create-a-lab-virtual-machine"></a>Creare una macchina virtuale labCreate a lab virtual machine

È innanzitutto necessario creare la macchina virtuale lab all'interno di una rete virtuale con Bastion configurato su di essa. Selezionare la seconda subnet creata, non AzureBastionSubnet.Select the second **subnet** you created, not the AzureBastionSubnet. È possibile selezionare una rete virtuale durante la creazione della macchina virtuale accedendo alla scheda **Impostazioni avanzate.**

![Creare macchina virtuale](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Avviare una macchina virtuale in un browser

Una volta creata la macchina virtuale, è possibile avviarla in un browser facendo clic sul pulsante *Connessione browser* e immettendo il nome utente e la password per la macchina.  

![Avvio in un browser](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere una macchina virtuale in un lab in Azure DevTest Labs](devtest-lab-add-vm.md)
