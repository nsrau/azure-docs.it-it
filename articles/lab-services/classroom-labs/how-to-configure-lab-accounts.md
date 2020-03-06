---
title: Configurare gli account Lab in Azure Lab Services | Microsoft Docs
description: Questo articolo descrive come creare un account Lab, visualizzare tutti gli account Lab o eliminare un account Lab in Azure Lab Services.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361002"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Configurare gli account Lab in Azure Lab Services 
In Azure Lab Services, un account Lab è un contenitore per i tipi Lab gestiti, ad esempio Lab in aula. Un amministratore configura un account lab con Azure Lab Services e fornisce l'accesso ai proprietari del lab autorizzati a creare lab nell'account. 

Questo articolo illustra come eseguire le attività seguenti: 

- Specificare un intervallo di indirizzi per le macchine virtuali nel Lab
- Configurare l'arresto automatico delle macchine virtuali in disconnessione

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Specificare un intervallo di indirizzi per le macchine virtuali nel Lab
La procedura seguente include i passaggi per specificare un intervallo di indirizzi per le macchine virtuali nel Lab. Se si aggiorna l'intervallo specificato in precedenza, l'intervallo di indirizzi modificato si applica solo alle macchine virtuali create dopo che è stata apportata la modifica. 

Di seguito sono riportate alcune restrizioni per specificare l'intervallo di indirizzi da tenere in considerazione. 

- Il prefisso deve essere minore o uguale a 23. 
- Se una rete virtuale viene associata all'account Lab, l'intervallo di indirizzi specificato non può sovrapporsi all'intervallo di indirizzi dalla rete virtuale con peering.

1. Nella pagina **account Lab** selezionare **impostazioni Lab** nel menu a sinistra.
2. Per il campo **intervallo di indirizzi** specificare l'intervallo di indirizzi per le macchine virtuali che verranno create nel Lab. L'intervallo di indirizzi deve essere nella notazione CIDR (Inter-Domain Routing) di classe (ad esempio: 10.20.0.0/23). Le macchine virtuali nel lab verranno create in questo intervallo di indirizzi.
3. Sulla barra degli strumenti selezionare **Salva**. 

    ![Configura intervallo di indirizzi](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Arresto automatico delle macchine virtuali in disconnessione
È possibile abilitare o disabilitare l'arresto automatico delle VM di Windows Lab (modello o studente) dopo la disconnessione di una connessione Desktop remoto. È anche possibile specificare per quanto tempo le macchine virtuali devono attendere che l'utente si riconnetta prima di arrestarsi automaticamente.

![Impostazione arresto automatico nell'account Lab](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Questa impostazione si applica a tutti i Lab creati nell'account Lab. Un proprietario del Lab può eseguire l'override di questa impostazione a livello di Lab. La modifica apportata a questa impostazione nell'account Lab influirà solo sui Lab creati dopo la modifica.

Per informazioni su come un proprietario del Lab può configurare questa impostazione a livello di Lab, vedere [questo articolo](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Consenti a Lab Creator di selezionare la posizione del Lab](allow-lab-creator-pick-lab-location.md)
- [Connettere la rete del Lab a una rete virtuale peer](how-to-connect-peer-virtual-network.md)
- [Alleghi una raccolta di immagini condivise a un Lab](how-to-attach-detach-shared-image-gallery.md)
- [Aggiungere un utente come proprietario del Lab](how-to-add-user-lab-owner.md)
- [Visualizzare le impostazioni del firewall per un Lab](how-to-configure-firewall-settings.md)
