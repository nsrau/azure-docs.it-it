---
title: Come reimpostare l'interfaccia di rete per la VM Windows Azure | Microsoft Docs
description: Illustra come reimpostare l'interfaccia di rete per la VM Windows Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: genlin
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/26/2017
ms.author: genli
ms.openlocfilehash: b8eecb9d546d5922844ede3744d80b951334b5d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Come reimpostare l'interfaccia di rete per la VM Windows di Azure 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Non è possibile connettersi alla macchina virtuale (VM) Windows di Microsoft Azure dopo avere disattivato l'interfaccia di rete (NIC) predefinita o si imposta manualmente un indirizzo IP statico per la scheda NIC. Questo articolo illustra come reimpostare l'interfaccia di rete per la macchina virtuale Windows di Azure che risolverà il problema della connessione remota.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]
## <a name="reset-network-interface"></a>Reimpostare l'interfaccia di rete

### <a name="for-classic-vms"></a>Per VM classiche

Per reimpostare l'interfaccia di rete, seguire questi passaggi:

1.  Accedere al [portale di Azure]( https://ms.portal.azure.com).
2.  Selezionare **Macchine virtuali (classiche)**.
3.  Selezionare la macchina virtuale.
4.  Selezionare **Indirizzi IP**.
5.  Se l'impostazione **Assegnazione IP privato** non è **Statica**, cambiarla in **Statica**.
6.  Cambiare l'**Indirizzo IP** in un altro indirizzo IP disponibile nella subnet.
7.  Selezionare Salva.
8.  La macchina virtuale verrà riavviata per inizializzare la nuova scheda NIC al sistema.
9.  Provare a eseguire RDP al computer in uso. Se ha esito positivo, è possibile modificare l'indirizzo IP privato originale se si vuole. In caso contrario è possibile mantenerlo. 

### <a name="for-vms-deployed-in-resource-group-model"></a>Per le VM distribuite nel modello di gruppo di risorse

1.  Accedere al [portale di Azure]( https://ms.portal.azure.com).
2.  Selezionare la macchina virtuale.
3.  Selezionare **Interfacce di rete**.
4.  Selezionare l'interfaccia di rete associata al computer
5.  Selezionare **Configurazioni IP**.
6.  Selezionare l'indirizzo IP. 
7.  Se l'impostazione **Assegnazione IP privato** non è **Statica**, cambiarla in **Statica**.
8.  Cambiare l'**Indirizzo IP** in un altro indirizzo IP disponibile nella subnet.
9. La macchina virtuale verrà riavviata per inizializzare la nuova scheda NIC al sistema.
10. Provare a eseguire RDP al computer in uso. Se ha esito positivo, è possibile modificare l'indirizzo IP privato originale se si vuole. In caso contrario è possibile mantenerlo. 

## <a name="delete-the-unavailable-nics"></a>Eliminare le schede di interfaccia non disponibili
Dopo essere riusciti a eseguire una connessione desktop remoto al computer, è necessario eliminare le schede NIC precedenti per evitare il problema potenziale:

1.  Aprire Gestione dispositivi.
2.  Selezionare **Vista** > **Mostra dispositivi nascosti**.
3.  Selezionare **Schede di rete**. 
4.  Controllare le schede di rete con un nome simile a "Scheda di rete Microsoft Hyper-V".
5.  Si potrebbe vedere una scheda non disponibile che appare in grigio. Fare clic con pulsante destro del mouse sulla scheda e selezionare Disinstalla.

    ![immagine della scheda NIC](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Disinstallare solo le schede non disponibili con il nome "Scheda di rete Microsoft Hyper-V". Se si disinstalla una delle altre schede nascoste, ciò potrebbe provocare altri problemi.
    >
    >

6.  Ora tutte le schede disponibili devono essere eliminate dal sistema.