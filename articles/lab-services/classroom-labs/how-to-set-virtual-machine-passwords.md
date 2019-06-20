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
ms.openlocfilehash: c1564fadef35a20d0d87db8439ae1cc3dc923318
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144087"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>Impostare o reimpostare la password per le macchine virtuali nel lab per le classi
Questo articolo offre diverse modalità di impostazione e resettings le password per l'accesso alle macchine virtuali nel lab per le classi. 

## <a name="lab-owners-teachers"></a>Proprietari del lab (insegnanti)
Un proprietario del lab (insegnante) possa impostare/reimpostare la password per le macchine virtuali al momento della creazione del lab (procedura guidata Creazione di lab) o dopo la creazione del lab (nel dashboard). 

### <a name="set-password-at-the-time-of-lab-creation"></a>Impostare la password al momento della creazione del lab
Un proprietario del lab (insegnante) può impostare una password per le macchine virtuali nel lab nel **impostare le credenziali** pagina della procedura guidata Creazione lab.

![Impostare le credenziali](../media/tutorial-setup-classroom-lab/set-credentials.png)

Per l'abilitazione/disabilitazione il **Usa la stessa password per tutte le macchine virtuali** opzione in questa pagina, un docente può scegliere di usare stessa password per tutte le macchine virtuali nel lab o consentire agli studenti di impostare le password per le proprie macchine virtuali. Per impostazione predefinita, questa impostazione è abilitata per tutti i Windows e Linux immagini del sistema operativo, ad eccezione di Ubuntu. Quando questa impostazione è disabilitata, gli studenti verranno richiesto di impostare una password quando provano a connettersi alla macchina virtuale per la prima volta. 

Il proprietario del lab può reimpostare la password (se necessario) nei **modello configura** pagina della procedura guidata Creazione lab. 

![Pagina di configurazione del modello al termine dell'operazione](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

Il proprietario del lab possa anche reimpostare la password dopo aver creato il lab, nel dashboard. 

### <a name="reset-password-on-the-dashboard"></a>Reimpostare la password nel dashboard

1. Selezionare il menu di overflow (tre punti verticali) nel riquadro del lab, quindi selezionare **la reimpostazione della password**. 

    ![Menu di reimpostazione della password nella home page](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. Nel **impostazione della password** nella finestra di dialogo immettere una password e selezionare **Set password**.
    
    ![Finestra di dialogo Imposta password](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>Utenti del lab (studenti)
Al momento della creazione del lab, il proprietario del lab può abilitare o disabilitare il **Usa la stessa password per tutte le macchine virtuali**. Se si seleziona questa opzione, studenti non possono reimpostare password. Tutte le macchine virtuali nel lab avrà la stessa password che è impostata per il docente. 

Se questa opzione è disabilitata, sarà necessario impostare una password quando provano a connettersi alla macchina virtuale per la prima volta. Quando gli utenti (studenti) selezionano il **Connect** pulsante sul riquadro lab la **macchine virtuali personali** pagina, l'utente visualizza la finestra di dialogo seguente per impostare la password per la macchina virtuale: 

![Reimpostare la password per gli studenti](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

Studente può anche impostare la password facendo clic sul menu di overflow (**sui tre punti verticali**) nel riquadro del lab e selezionando **Reimposta password**. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle altre opzioni di utilizzo per studenti è (come proprietario del lab) possono configurare, vedere l'articolo seguente: [Configurare l'utilizzo degli studenti](how-to-configure-student-usage.md).
