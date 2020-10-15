---
title: Impostazioni del firewall per Azure Lab Services
description: Informazioni su come stabilire l'indirizzo IP pubblico e l'intervallo di numero di porte delle macchine virtuali di un lab in modo da poter aggiungere tali informazioni alle regole del firewall.
author: emaher
ms.author: enewman
ms.date: 06/26/2020
ms.topic: article
ms.openlocfilehash: 067a2c9672b87974557f650cef07b0394e7d5a63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445849"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Impostazioni del firewall per Azure Lab Services

Ogni organizzazione o istituto di istruzione può configurare la rete nel modo più adatto alle proprie esigenze.  Ad esempio può aggiungere regole firewall per bloccare le connessioni Remote Desktop Protocol (RDP) o Secure Shell (SSH) a computer che si trovano al di fuori della rete.  Poiché Azure Lab Services viene eseguito nel cloud pubblico, potrebbero essere necessarie alcune configurazioni aggiuntive per consentire agli studenti di accedere alla propria macchina virtuale quando si connettono dalla rete del campus.

Ogni lab usa un singolo indirizzo IP pubblico e più porte.  Tutte le macchine virtuali, sia le VM modello che quelle degli studenti, usano questo indirizzo IP pubblico.  L'indirizzo IP pubblico rimane lo stesso per tutta la durata del lab.  Tuttavia, ogni macchina virtuale ha un numero di porta diverso.  I numeri di porta sono compresi tra 49152 e 65535.  La combinazione di indirizzo IP pubblico e numero di porta viene usata per connettere docenti e studenti alla macchina virtuale corretta.  Questo articolo spiega come trovare l'indirizzo IP pubblico specifico usato da un lab.  Questa informazione può essere usata per aggiornare le regole del firewall in ingresso e in uscita, in modo che gli studenti possano accedere alle proprie macchine virtuali.

>[!IMPORTANT]
>Ogni Lab ha un indirizzo IP pubblico diverso.

## <a name="find-public-ip-for-a-lab"></a>Trovare l'indirizzo IP pubblico di un lab

Gli indirizzi IP pubblici di ogni lab sono elencati nella pagina **Tutti i Lab** dell'account lab di Lab Services.  Per istruzioni su come trovare la pagina **Tutti i lab**, vedere [visualizzare i lab di un account lab](manage-labs.md#view-labs-in-a-lab-account).  

> [!div class="mx-imgBorder"]
> ![Pagina Tutti i lab](./media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Se il computer modello del lab non è ancora stato pubblicato, l'indirizzo IP pubblico non viene visualizzato.

## <a name="conclusion"></a>Conclusioni

A questo punto si conosce l'indirizzo IP pubblico del lab.  È possibile creare regole in ingresso e in uscita per l'indirizzo IP pubblico nel firewall dell'organizzazione con l'intervallo di porte 49152-65535.  Una volta aggiornate le regole, gli studenti possono accedere alle macchine virtuali senza che il firewall di rete blocchi l'accesso.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Consentire all'autore del lab di selezionare la località](allow-lab-creator-pick-lab-location.md)
- [Connettere la rete di lab a una rete virtuale peer](how-to-connect-peer-virtual-network.md)
- [Collegare una raccolta immagini condivisa a un lab](how-to-attach-detach-shared-image-gallery.md)
- [Aggiungere un utente come proprietario del lab](how-to-add-user-lab-owner.md)
- [Visualizzare le impostazioni del firewall di un lab](how-to-configure-firewall-settings.md)
- [Configurare altre impostazioni di un lab](how-to-configure-lab-accounts.md)
