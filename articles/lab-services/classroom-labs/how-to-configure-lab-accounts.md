---
title: Configurare gli account lab in Azure Lab Services - Documenti Microsoft
description: Questo articolo descrive come creare un account lab, visualizzare tutti gli account lab o eliminare un account lab in Azure Lab Services.This article describes how to create a lab account, view all lab accounts, or delete a lab account in Azure Lab Services.
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
ms.openlocfilehash: fa9dba62b3b58687ec6a2bfc29e8722f7016b679
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284304"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Configurare gli account lab in Azure Lab ServicesConfigure lab accounts in Azure Lab Services 
In Azure Lab Services un account lab è un contenitore per i tipi di lab gestiti, ad esempio i lab per aule. Un amministratore configura un account lab con Azure Lab Services e fornisce l'accesso ai proprietari del lab autorizzati a creare lab nell'account. 

In questo articolo viene illustrato come eseguire le attività seguenti:This article shows how to perform the following tasks: 

- Specificare un intervallo di indirizzi per le macchine virtuali nel labSpecify an address range for VMs in the lab
- Configurare l'arresto automatico delle macchine virtuali alla disconnessione

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Specificare un intervallo di indirizzi per le macchine virtuali nel labSpecify an address range for VMs in the lab
La procedura seguente include i passaggi per specificare un intervallo di indirizzi per le macchine virtuali nel lab. Se si aggiorna l'intervallo specificato in precedenza, l'intervallo di indirizzi modificati si applica solo alle macchine virtuali create dopo la modifica. 

Di seguito sono riportate alcune restrizioni quando si specifica l'intervallo di indirizzi da tenere presente. 

- Il prefisso deve essere minore o uguale a 23. 
- Se viene eseguito il peered di una rete virtuale nell'account lab, l'intervallo di indirizzi fornito non può sovrapporsi all'intervallo di indirizzi dalla rete virtuale con peered.

1. Nella pagina **Account lab** selezionare **Impostazioni lab** nel menu a sinistra.
2. Per il campo **Intervallo di indirizzi** specificare l'intervallo di indirizzi per le macchine virtuali che verranno create nel lab. L'intervallo di indirizzi deve essere nella notazione CIDR (Classless Inter-Domain Routing) (esempio: 10.20.0.0/23). Le macchine virtuali nel lab verranno create in questo intervallo di indirizzi.
3. Sulla barra degli strumenti selezionare **Salva**. 

    ![Configurare l'intervallo di indirizzi](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Arresto automatico delle macchine virtuali alla disconnessione
È possibile abilitare o disabilitare l'arresto automatico delle macchine virtuali lab di Windows (modello o studente) dopo la disconnessione di una connessione desktop remoto. È inoltre possibile specificare per quanto tempo le macchine virtuali devono attendere la riconnessione dell'utente prima dell'arresto automatico.

![Impostazione di arresto automatico nell'account lab](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Questa impostazione si applica a tutti i lab creati nell'account lab. Un proprietario del lab può ignorare questa impostazione a livello di lab. La modifica di questa impostazione nell'account lab avrà effetto solo sui lab creati dopo la modifica.

Per informazioni su come il proprietario di un lab può configurare questa impostazione a livello di lab, vedere [questo articolo](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Consentire all'autore del lab di selezionare la località](allow-lab-creator-pick-lab-location.md)
- [Connettere la rete del lab a una rete virtuale peerConnect your lab's network with a peer virtual network](how-to-connect-peer-virtual-network.md)
- [Allegare una raccolta di immagini condivise a un lab](how-to-attach-detach-shared-image-gallery.md)
- [Aggiungere un utente come proprietario del labAdd a user as a lab owner](how-to-add-user-lab-owner.md)
- [Visualizzare le impostazioni del firewall per un lab](how-to-configure-firewall-settings.md)
