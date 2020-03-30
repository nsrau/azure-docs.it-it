---
title: Impostazioni del firewall per Azure Lab Services
description: Informazioni su come determinare l'indirizzo IP pubblico e l'intervallo di numero di porta delle macchine virtuali in un lab in modo che sia possibile aggiungere informazioni alle regole del firewall.
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443457"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Impostazioni del firewall per Azure Lab Services

Ogni organizzazione o scuola creerà la propria rete in modo che meglio si adatta alle proprie esigenze.  A volte ciò include l'impostazione di regole del firewall che bloccano le connessioni Remote Desktop Protocol (rdp) o Secure Shell (ssh) a computer esterni alla propria rete.  Poiché Azure Lab Services viene eseguito nel cloud pubblico, potrebbe essere necessaria una configurazione aggiuntiva per consentire agli studenti di accedere alla macchina virtuale quando si connettono dalla rete del campus.

Ogni lab utilizza un singolo indirizzo IP pubblico e più porte.  Tutte le macchine virtuali, sia la macchina virtuale modello che le macchine virtuali degli studenti, utilizzeranno questo indirizzo IP pubblico.  L'indirizzo IP pubblico non cambierà per la vita del laboratorio.  Tuttavia, ogni macchina virtuale avrà un numero di porta diverso.  I numeri di porta vanno da 49152 a 65535.  La combinazione di indirizzo IP pubblico e numero di porta viene utilizzata per connettere istruttore e studenti alla macchina virtuale corretta.  In questo articolo verrà illustrato come trovare l'indirizzo IP pubblico specifico utilizzato da un laboratorio.  Tali informazioni possono essere usate per aggiornare le regole del firewall in ingresso e in uscita in modo che gli studenti possano accedere alle macchine virtuali.

>[!IMPORTANT]
>Ogni lab avrà un indirizzo IP pubblico diverso.

## <a name="find-public-ip-for-a-lab"></a>Trovare IP pubblico per un labFind public IP for a lab

Gli indirizzi IP pubblici per ogni lab sono elencati nella pagina **Tutti i lab** dell'account lab di Lab Services.  Per istruzioni su come trovare la pagina **Tutti i lab,** vedere [Come gestire i lab in un account lab.](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account)  

> [!div class="mx-imgBorder"]
> ![Pagina Di tutti i laboratori](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Non verrà visualizzato l'indirizzo IP pubblico se il computer modello per il lab non è ancora pubblicato.

## <a name="conclusion"></a>Conclusioni

Ora conosciamo l'indirizzo IP pubblico per il laboratorio.  È possibile creare regole in ingresso e in uscita per il firewall dell'organizzazione per l'indirizzo IP pubblico e l'intervallo di porte 49152-65535.  Una volta aggiornate le regole, gli studenti possono accedere alle macchine virtuali senza che il firewall di rete blocchi l'accesso.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Consentire all'autore del lab di selezionare la località](allow-lab-creator-pick-lab-location.md)
- [Connettere la rete del lab a una rete virtuale peerConnect your lab's network with a peer virtual network](how-to-connect-peer-virtual-network.md)
- [Allegare una raccolta di immagini condivise a un lab](how-to-attach-detach-shared-image-gallery.md)
- [Aggiungere un utente come proprietario del labAdd a user as a lab owner](how-to-add-user-lab-owner.md)
- [Visualizzare le impostazioni del firewall per un lab](how-to-configure-firewall-settings.md)
- [Configurare altre impostazioni per un lab](how-to-configure-lab-accounts.md)
