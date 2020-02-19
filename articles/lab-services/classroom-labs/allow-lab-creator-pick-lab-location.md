---
title: Consenti al creatore del Lab di selezionare la posizione in Azure Lab Services
description: Questo articolo descrive come un amministratore dell'account Lab può consentire ai creatori di Lab di selezionare i percorsi per i propri laboratori.
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
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444367"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Consenti a Lab Creator di selezionare la posizione per il Lab in Azure Lab Services
In Azure Lab Services, un proprietario dell'account Lab può consentire ai creatori di Lab (educatori) di scegliere una località per il Lab creato. Questo percorso può essere diverso dalla posizione dell'account Lab. Un percorso è un gruppo di aree di Azure. Ad esempio, Stati Uniti percorso è un gruppo di aree, ad esempio Stati Uniti orientali, Stati Uniti occidentali e così via. 

Il proprietario di un account Lab può selezionare l'opzione **Consenti all'autore del Lab di scegliere la posizione del Lab** quando si crea un account Lab e dopo aver creato l'account Lab o un account lab esistente. 

## <a name="at-the-time-of-lab-account-creation"></a>Al momento della creazione dell'account Lab
Quando si crea un account Lab, questa opzione viene visualizzata nella prima schermata (scheda informazioni di**base** ). 

![Abilitare l'opzione al momento della creazione del Lab](../media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Questa opzione è disabilitata se si seleziona una rete virtuale peer per l'account Lab nella scheda **Avanzate** .  

![Quando è abilitata la rete virtuale peer](../media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>Dopo la creazione dell'account Lab
Dopo aver creato l'account Lab, è possibile abilitare o disabilitare questa opzione attenendosi alla procedura seguente: 

1. Nella pagina **account Lab** selezionare **impostazioni Lab** nel menu a sinistra.
2. Selezionare l'opzione **Allow Lab Creators to pick Lab location** se si desidera consentire all'autore del Lab di selezionare un percorso per il Lab. Se è disabilitato, i Lab vengono creati automaticamente nella stessa posizione in cui è presente l'account Lab. 
    
    Questo campo è disabilitato quando si seleziona una rete virtuale per il campo **rete virtuale peer** . Il motivo è che i Lab nell'account lab devono trovarsi nella stessa area dell'account Lab per accedere alle risorse nella rete virtuale peer. 
1. Sulla barra degli strumenti selezionare **Salva**. 

    ![Impostazioni Lab](../media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Non sono consentite selezioni di rete virtuale e località
In questo scenario non è stata abilitata l'opzione **Consenti all'autore del Lab di selezionare la posizione del Lab** . 

![Nessun percorso Lab](../media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Quindi, gli autori del Lab (docenti) non vedono un'opzione per scegliere una località per il Lab. Vedranno il prezzo per ogni ora per ogni opzione di dimensioni disponibili. Quando crea un Lab, viene creato in un'area di Azure che si trova nella stessa località dell'area di Azure in cui si trova l'account Lab. Ad esempio, se l'account Lab si trova negli **Stati Uniti occidentali**, il Lab potrebbe essere creato negli **Stati Uniti centro-meridionali** , ma non verrà creato in **Canada orientale**. Non è garantita alcuna garanzia sull'area a cui si è scelto di rientrare nella località. Se una dimensione è attualmente vincolata, l'autore del Lab visualizzerà una casella di controllo in cui è possibile visualizzare le dimensioni normalmente supportate, ma che attualmente non sono disponibili. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>La selezione della posizione e della rete virtuale non è consentita
In questo scenario, l'opzione **Allow Lab Creators to pick Lab location** è disabilitata perché è stata selezionata una rete virtuale peer per l'account Lab. Quindi, gli autori del Lab visualizzeranno la stessa schermata dell'opzione precedente. Poiché tutte le VM devono trovarsi nella stessa area di Azure della rete virtuale, il Lab verrà creato nella stessa area di Azure in cui si trova la rete virtuale. Se questa determinata area è vincolata per le dimensioni, le dimensioni verranno visualizzate come non disponibili. 

## <a name="location-selection-is-enabled"></a>Selezione località abilitata
Quando si seleziona **Consenti a Lab Creator di**selezionare la posizione del Lab, i creatori di Lab (docenti) vedono un'opzione per selezionare una località durante la creazione di un Lab. 

![Seleziona un percorso Lab](../media/allow-lab-creator-pick-lab-location/location-selection.png)

Gli autori del Lab visualizzano l'intervallo di prezzi per tutte le località in cui si trova la dimensione e possono scegliere una località. Il Lab verrà creato in qualsiasi area di Azure che esegue il mapping a tale località.

Se un percorso è vincolato, non viene visualizzato nell'elenco per impostazione predefinita. Espandere l'elenco a discesa e selezionare **Mostra percorsi non disponibili per questa dimensione**. 

![Mostra percorsi non disponibili](../media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Costo
In precedenza, i prezzi erano basati sulle dimensioni della macchina virtuale scelte per il Lab. Il prezzo è basato sulla combinazione del sistema operativo, le dimensioni e la posizione. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Connettere la rete del Lab a una rete virtuale peer](how-to-connect-peer-virtual-network.md)
- [Alleghi una raccolta di immagini condivise a un Lab](how-to-attach-detach-shared-image-gallery.md)
- [Aggiungere un utente come proprietario del Lab](how-to-add-user-lab-owner.md)
- [Visualizzare le impostazioni del firewall per un Lab](how-to-configure-firewall-settings.md)
- [Configurare altre impostazioni per un Lab](how-to-configure-lab-accounts.md)