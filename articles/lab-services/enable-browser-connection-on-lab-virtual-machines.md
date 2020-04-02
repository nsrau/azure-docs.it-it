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
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 86304fc7776f49c999924b8609f2d26120cee372
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549091"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Abilitare la connessione del browser nelle macchine virtuali labEnable browser connection on lab virtual machines 

DevTest Labs si integra con [Azure Bastion](https://docs.microsoft.com/azure/bastion/), che consente di connettersi alle macchine virtuali tramite un browser. È innanzitutto necessario abilitare la connessione del browser nelle macchine virtuali lab.

In qualità di proprietario di un lab è possibile abilitare l'accesso a tutte le macchine virtuali lab tramite un browser. Non è necessario un client, un agente o software aggiuntivo. Azure Bastion fornisce connettività RDP/SSH sicura e trasparente alle macchine virtuali direttamente nel portale di Azure tramite TLS. Quando ci si connette tramite Il Bastione di Azure, le macchine virtuali non richiedono un indirizzo IP pubblico. Per altre informazioni, vedere [Che cos'è il Bastione](../bastion/bastion-overview.md) di Azure.

> [!NOTE]
> L'abilitazione della connessione del browser nelle macchine virtuali lab è in anteprima.

In questo articolo viene illustrato come abilitare la connessione del browser nelle macchine virtuali lab.

## <a name="prerequisites"></a>Prerequisiti 
Distribuire un host Bastion nella rete virtuale **(OR)** del lab esistente connettere il lab con una rete virtuale configurata da Bastion. 

Per informazioni su come distribuire un host Bastion in una rete virtuale, vedere [Creare un host Di Azure Bastion (anteprima).](../bastion/bastion-create-host-portal.md) Quando si crea l'host Bastion, selezionare la rete virtuale del lab. 

Per informazioni su come connettere il lab a una rete virtuale configurata da Bastion, vedere [Configurare una rete virtuale in Azure DevTest Labs.](devtest-lab-configure-vnet.md) Selezionare la rete virtuale in cui è distribuito l'host Bastion e **AzureBastionSubnet.** Ecco di seguito i passaggi dettagliati: 

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Tutti i servizi** nel menu di navigazione a sinistra. 
1. Selezionare **DevTest Labs** dall'elenco. 
1. Nell'elenco dei laboratori selezionare *il lab*. 

    > [!NOTE]
    > Il Bastione di Azure è attualmente in anteprima. È limitato alle seguenti regioni: Stati Uniti occidentali, Stati Uniti orientali, Europa occidentale, Stati Uniti centro-meridionali, Australia orientale e Giappone est. Quindi, creare un lab in una di queste aree se il lab non è in una di esse. 
1. Selezionare **Configurazione e criteri** nella sezione **Impostazioni** del menu a sinistra. 
1. Selezionare **Reti virtuali**.
1. Selezionare **Aggiungi** dalla barra degli strumenti. 
1. Selezionare la **rete virtuale** in cui è distribuito l'host Bastion. 
1. Selezionare la subnet: **AzureBastionSubnet**. 

    ![Subnet](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Selezionare l'opzione **Usa nella creazione della macchina virtuale.** 
1. Sulla barra degli strumenti selezionare **Salva**. 
1. Se si dispone di una vecchia rete virtuale per il lab, rimuoverla selezionando*il segno* di scambio ...  e **Rimuovi**. 

## <a name="enable-browser-connection"></a>Abilitare la connessione del browser 

Dopo aver configurato una rete virtuale Bastion all'interno del lab, come proprietario del lab, è possibile abilitare la connessione del browser nelle macchine virtuali lab.

Per abilitare la connessione tramite browser nelle macchine virtuali lab, attenersi alla seguente procedura:

1. Nel portale di Azure passare al *lab.*
1. Selezionare **Configuration and policies** (Configurazione e criteri).
1. In **Impostazioni**selezionare **Connessione browser (anteprima)**.

![Abilitare la connessione del browser](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente per informazioni su come connettersi alle macchine virtuali usando un browser: [Connettersi alle macchine virtuali tramite un browserSee](connect-virtual-machine-through-browser.md) the following article to learn how to connect to your VMs using a browser: Connect to your virtual machines through a browser