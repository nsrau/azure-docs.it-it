---
title: Impostare le password per le macchine virtuali in Azure Lab Services | Microsoft Docs
description: Informazioni su come impostare e reimpostare le password per le macchine virtuali (VM) nel lab per le classi di Azure Lab Services.
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 3701c69ff97d840f6cba175df8f02bc55ece498b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123193"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>Impostare o reimpostare la password per le macchine virtuali nel lab per le classi
Questo articolo offre diverse modalità di impostazione e resettings le password per l'accesso alle macchine virtuali nel lab per le classi. 

## <a name="lab-owners-teachers"></a>Proprietari del lab (insegnanti)
Un proprietario del lab (insegnante) può impostare una password per le macchine virtuali nel lab nel **impostare le credenziali** pagina della procedura guidata Creazione lab.

![Impostare le credenziali](../media/tutorial-setup-classroom-lab/set-credentials.png)

Quindi, il proprietario del lab può reimpostare la password (se necessario) nei **modello configura** pagina della procedura guidata Creazione lab. 

![Pagina di configurazione del modello al termine dell'operazione](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

Il proprietario del lab possa anche reimpostare la password dopo aver creato il lab, nel dashboard. 

![Menu di reimpostazione della password nella home page](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)

Quindi, immettere la nuova password nel **Set password** pagina, quindi selezionare **Set password**. 

![Menu di reimpostazione della password nella home page](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>Utenti del lab (studenti)
Al momento della creazione del lab. Allo stesso tempo, il proprietario del lab può abilitare o disabilitare che consente agli studenti per impostare le proprie password per le macchine virtuali. Se il proprietario del lab (insegnante) abilita questa opzione, l'utente del lab (studente) sarà disponibile un'opzione per impostare la password per la macchina virtuale se lo studente accede alla macchina virtuale per la prima volta. In un **macchina virtuale Windows**, premere **CTRL + ALT + fine**e selezionare **Cambia password**. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle altre opzioni di utilizzo per studenti è (come proprietario del lab) possono configurare, vedere l'articolo seguente: [Configurare l'utilizzo degli studenti](how-to-configure-student-usage.md).
