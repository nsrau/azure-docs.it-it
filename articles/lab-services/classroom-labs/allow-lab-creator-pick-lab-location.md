---
title: Consentire al creatore del lab di selezionare il percorso in Azure Lab ServicesAllow lab creator to pick location in Azure Lab Services
description: In questo articolo viene descritto come un amministratore dell'account lab può consentire ai creatori di lab di selezionare le posizioni per i propri laboratori.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 52d5628698d1f945a7f672595ee7ce4739b6d13c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444367"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Consentire al creatore del lab di selezionare il percorso per il lab in Azure Lab ServicesAllow lab creator to pick location for the lab in Azure Lab Services
In Azure Lab Services il proprietario di un account lab può consentire ai creatori di lab (educatori) di scegliere un percorso per il lab che creano. Questo percorso può essere diverso da quello dell'account lab. Una posizione è un gruppo di aree di Azure.A location is a group of Azure regions. Ad esempio, la posizione degli Stati Uniti è un gruppo di aree, ad esempio Stati Uniti orientali, Stati Uniti occidentali e così via. 

L'utente, in qualità di proprietario di un account lab, può selezionare l'opzione **Consenti all'autore del lab** di selezionare il percorso lab quando si crea un account lab e dopo aver creato l'account lab (o un account lab esistente). 

## <a name="at-the-time-of-lab-account-creation"></a>Al momento della creazione dell'account lab
Quando si crea un account lab, questa opzione viene visualizzata nella prima schermata (scheda**Nozioni di base).** 

![Abilitare l'opzione al momento della creazione del labEnable the option at the time of lab creation](../media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Questa opzione è disabilitata se si seleziona una rete virtuale peer per l'account lab nella scheda **Avanzate.This** option is disabled if you select a peer virtual network for your lab account in the Advanced tab.  

![Quando la rete virtuale peer è abilitataWhen peer virtual network is enabled](../media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>Dopo aver creato l'account lab
Dopo aver creato l'account lab, è possibile abilitare o disabilitare questa opzione attenendosi alla seguente procedura: 

1. Nella pagina **Account lab** selezionare **Impostazioni lab** nel menu a sinistra.
2. Selezionare l'opzione **Consenti al creatore** lab di selezionare il percorso lab se si desidera consentire al creatore del lab di selezionare un percorso per il lab. Se è disabilitato, i lab vengono creati automaticamente nello stesso percorso in cui è presente l'account lab. 
    
    Questo campo è disabilitato quando si seleziona una rete virtuale per il campo **Rete virtuale peer.** È perché i lab nell'account lab devono trovarsi nella stessa area dell'account lab per poter accedere alle risorse nella rete virtuale peer. 
1. Sulla barra degli strumenti selezionare **Salva**. 

    ![Impostazioni lab](../media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Non è consentita la selezione di rete virtuale e posizione
In questo scenario non è stata abilitata l'opzione **Consenti al creatore** del lab di selezionare il percorso lab. 

![Nessuna posizione di laboratorio](../media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Quindi, i creatori di laboratorio (educatori) non vedono un'opzione per scegliere una posizione per il lab. Vedranno il prezzo all'ora per ogni opzione di dimensione a loro disposizione. Quando creano un lab, questo verrà creato in un'area di Azure che si trova nella stessa posizione dell'area di Azure in cui si trova l'account lab. Ad esempio, se l'account lab si trova **negli Stati Uniti occidentali**, è possibile che il lab venga creato negli Stati Uniti **centro-meridionali** ma non in **Canada orientale.** Non garantiamo nulla circa la regione che scegliamo a parte che è nella posizione. Se una dimensione è attualmente vincolata, l'autore del lab vedrà una casella di controllo in cui può vedere le dimensioni normalmente supportate ma che attualmente non sono disponibili. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>Nella rete virtuale e la selezione della posizione non è consentita
In questo scenario, l'opzione **Consenti al creatore lab** di selezionare il percorso lab è disabilitata perché è stata selezionata una rete virtuale peer per l'account lab. Quindi, i creatori di laboratorio vedranno la stessa schermata dell'opzione precedente. Poiché tutte le macchine virtuali devono trovarsi nella stessa area di Azure della rete virtuale, il lab verrà creato nella stessa area di Azure in cui si trova la rete virtuale. Se la particolare area è vincolata per una dimensione, la dimensione verrà visualizzata come non disponibile. 

## <a name="location-selection-is-enabled"></a>La selezione della posizione è abilitata
Quando si seleziona **Consenti al creatore**del lab di selezionare il percorso del lab, i creatori di lab (educatori) visualizzano un'opzione per selezionare un percorso durante la creazione di un lab. 

![Scegliere una posizione di laboratorio](../media/allow-lab-creator-pick-lab-location/location-selection.png)

I creatori del laboratorio vedono l'intervallo di prezzi per tutte le località in cui si trova la dimensione e possono scegliere una località. Il lab verrà creato in qualsiasi area di Azure mappata a tale posizione.

Se una posizione è vincolata, non viene visualizzata nell'elenco per impostazione predefinita. Espandere l'elenco a discesa e selezionare **Mostra percorsi non disponibili per questa dimensione.** 

![Mostra posizioni non disponibili](../media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Costi
In precedenza, il prezzo era basato sulle dimensioni della macchina virtuale scelta per il lab. Ora, il prezzo si basa sulla combinazione di sistema operativo (OS), dimensioni e posizione. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Connettere la rete del lab a una rete virtuale peerConnect your lab's network with a peer virtual network](how-to-connect-peer-virtual-network.md)
- [Allegare una raccolta di immagini condivise a un lab](how-to-attach-detach-shared-image-gallery.md)
- [Aggiungere un utente come proprietario del labAdd a user as a lab owner](how-to-add-user-lab-owner.md)
- [Visualizzare le impostazioni del firewall per un lab](how-to-configure-firewall-settings.md)
- [Configurare altre impostazioni per un lab](how-to-configure-lab-accounts.md)