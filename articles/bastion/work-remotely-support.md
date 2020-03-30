---
title: 'Lavorare in remoto con Bastion: Azure Bastion'
description: Questa pagina descrive come è possibile sfruttare Azure Bastion per consentire di lavorare in remoto a causa della pandemia COVID-19.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: c0bba2560cccc208e2384421218ecebdfef65236
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333061"
---
# <a name="working-remotely-using-azure-bastion"></a>Lavorare in remoto con Azure BastionWorking remotely using Azure Bastion

Il Bastione di Azure svolge un ruolo fondamentale nel supporto di scenari di lavoro remoti, consentendo agli utenti con connettività Internet di accedere alle macchine virtuali di Azure.Azure Bastion plays a pivotrole in supporting remote work scenarios by allowing users with internet connectivity to access Azure virtual machines. In particolare, consente agli amministratori IT di gestire le applicazioni in esecuzione in Azure in qualsiasi momento e da qualsiasi punto del mondo.

>[!NOTE]
>Questo articolo descrive come è possibile sfruttare Azure Bastion, Azure, la rete Microsoft e l'ecosistema dei partner di Azure per lavorare in remoto e ridurre i problemi di rete che si trovano ad affrontare a causa di una crisi di COVID-19.
>

## <a name="securely-access-virtual-machines"></a>Accesso sicuro alle macchine virtualiSecurely access virtual machines

In particolare, Azure Bastion fornisce connettività RDP/SSH sicura e trasparente alle macchine virtuali all'interno della rete virtuale di Azure, direttamente nel portale di Azure, senza l'uso di un indirizzo IP pubblico. Per altre informazioni sull'architettura di Azure Bastion e sulle funzionalità principali, vedere [Che cos'è Azure Bastion](bastion-overview.md).

Il Bastione di Azure viene distribuito per ogni rete virtuale, il che significa che le aziende possono configurare e gestire un Bastion di Azure per supportare rapidamente l'accesso utente remoto alle macchine virtuali all'interno di una rete virtuale di Azure.Azure Bastion is deployed per virtual network, meaning as, as can configure and manage one Azure Bastion to quickly support remote user access to virtual machines within an Azure virtual network. Per istruzioni su come creare e gestire Azure Bastion, vedere [Creare un bastione host](bastion-create-host-portal.md).

## <a name="next-steps"></a>Passaggi successivi

* Configurare Azure Bastion usando il portale di [Azure,](bastion-create-host-portal.md)PowerShell o l'interfaccia della riga di comando di Azure.Configure Azure Bastion using the Azure portal , [PowerShell,](bastion-create-host-powershell.md)or Azure CLI.

* Leggi le domande frequenti sul [Bastion](bastion-faq.md) per ulteriori informazioni.
