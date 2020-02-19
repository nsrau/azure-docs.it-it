---
title: Impostazioni del firewall per Azure Lab Services
description: Informazioni su come determinare l'indirizzo IP pubblico e l'intervallo di numeri di porta delle macchine virtuali in un Lab in modo che le informazioni possano essere aggiunte alle regole del firewall.
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443457"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Impostazioni del firewall per Azure Lab Services

Ogni organizzazione o Istituto di istruzione configurerà la propria rete in modo più adatto alle proprie esigenze.  In alcuni casi è inclusa l'impostazione di regole firewall che bloccano le connessioni Remote Desktop Protocol (RDP) o Secure Shell (SSH) a computer esterni alla propria rete.  Poiché Azure Lab Services viene eseguito nel cloud pubblico, alcune configurazioni aggiuntive potrebbero essere necessarie per consentire agli studenti di accedere alla propria VM durante la connessione dalla rete del campus.

Ogni Lab usa un singolo indirizzo IP pubblico e più porte.  Tutte le macchine virtuali, sia le VM del modello che quelle degli studenti, useranno questo indirizzo IP pubblico.  L'indirizzo IP pubblico non cambierà per la durata del Lab.  Tuttavia, ogni macchina virtuale avrà un numero di porta diverso.  I numeri di porta sono compresi tra 49152 e 65535.  La combinazione di indirizzo IP pubblico e numero di porta viene usata per connettere docenti e studenti alla macchina virtuale corretta.  Questo articolo illustra come trovare l'indirizzo IP pubblico specifico usato da un Lab.  Queste informazioni possono essere usate per aggiornare le regole del firewall in ingresso e in uscita, in modo che gli studenti possano accedere alle macchine virtuali.

>[!IMPORTANT]
>Ogni Lab avrà un indirizzo IP pubblico diverso.

## <a name="find-public-ip-for-a-lab"></a>Trovare un indirizzo IP pubblico per un Lab

Gli indirizzi IP pubblici per ogni Lab sono elencati nella pagina **tutti** i Lab dell'account Lab di Lab Services.  Per istruzioni su come trovare la pagina **tutti i Lab** , vedere come gestire i Lab [in un account Lab](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account).  

> [!div class="mx-imgBorder"]
> ![pagina tutti i Lab](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Se il computer modello per il Lab non è ancora pubblicato, l'indirizzo IP pubblico non verrà visualizzato.

## <a name="conclusion"></a>Conclusione

A questo punto si conosce l'indirizzo IP pubblico per il Lab.  È possibile creare regole in ingresso e in uscita per il firewall dell'organizzazione per l'indirizzo IP pubblico e l'intervallo di porte 49152-65535.  Una volta aggiornate le regole, gli studenti possono accedere alle VM senza il firewall di rete che blocca l'accesso.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Consenti a Lab Creator di selezionare la posizione del Lab](allow-lab-creator-pick-lab-location.md)
- [Connettere la rete del Lab a una rete virtuale peer](how-to-connect-peer-virtual-network.md)
- [Alleghi una raccolta di immagini condivise a un Lab](how-to-attach-detach-shared-image-gallery.md)
- [Aggiungere un utente come proprietario del Lab](how-to-add-user-lab-owner.md)
- [Visualizzare le impostazioni del firewall per un Lab](how-to-configure-firewall-settings.md)
- [Configurare altre impostazioni per un Lab](how-to-configure-lab-accounts.md)
