---
title: Connettersi alle macchine virtuali tramite un browser-Azure | Microsoft Docs
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
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 22d7afa889994068c3f906d0b9adbc1a74bc4839
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642591"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Connettersi alle macchine virtuali tramite un browser 

DevTest Labs si integra con [Azure Bastion](https://docs.microsoft.com/azure/bastion/), che consente di connettersi alle macchine virtuali tramite un browser. Per informazioni sull'abilitazione di questa funzionalità in DevTest Labs, vedere [Enable browser connection on Lab Virtual Machines](enable-browser-connection-lab-virtual-machines.md).

Una volta abilitata la *connessione del browser* , gli utenti del Lab possono accedere alle macchine virtuali tramite un browser.  

> [!NOTE]
> L'abilitazione della connessione del browser nelle macchine virtuali del Lab è in anteprima.

## <a name="create-a-lab-virtual-machine"></a>Creare una macchina virtuale Lab

Per prima cosa è necessario creare la macchina virtuale Lab all'interno di una VNet con Bastion configurato. È possibile selezionare un VNet durante la creazione della macchina virtuale passando alla scheda **Impostazioni avanzate** .

![Crea macchina virtuale](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Avvia la macchina virtuale in un browser

Una volta creata la macchina virtuale, è possibile avviarla in un browser facendo clic sul pulsante di *connessione del browser* e immettendo il nome utente e la password per il computer.  

![Avvia in un browser](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Fasi successive

[Aggiungere una macchina virtuale in un lab in Azure DevTest Labs](devtest-lab-add-vm.md)