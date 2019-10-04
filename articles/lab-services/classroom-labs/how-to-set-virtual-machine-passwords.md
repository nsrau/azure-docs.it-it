---
title: Impostare le password per le macchine virtuali in Azure Lab Services | Microsoft Docs
description: Informazioni su come impostare e reimpostare le password per le macchine virtuali (VM) in laboratori della classe di Azure Lab Services.
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: a4cb2abec429a790f493f95d3d16b2ff7b3eb445
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69645020"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-instructor"></a>Impostare o reimpostare la password per le macchine virtuali in laboratori della classe (Instructor)
Un proprietario del Lab (insegnante) può impostare o reimpostare la password per le macchine virtuali al momento della creazione del Lab (creazione guidata Lab) o dopo la creazione del Lab (nel dashboard). 

## <a name="set-password-at-the-time-of-lab-creation"></a>Impostare la password al momento della creazione del Lab
Un proprietario del Lab (insegnante) può impostare una password per le macchine virtuali nel Lab nella pagina **Imposta credenziali** della creazione guidata Lab.

![Imposta credenziali](../media/tutorial-setup-classroom-lab/set-credentials.png)

Abilitando/disabilitando l'opzione **Usa la stessa password per tutte le macchine virtuali** in questa pagina, un insegnante può scegliere di usare la stessa password per tutte le macchine virtuali nel Lab o consentire agli studenti di impostare le password per le proprie macchine virtuali. Per impostazione predefinita, questa impostazione è abilitata per tutte le immagini del sistema operativo Windows e Linux, ad eccezione di Ubuntu. Quando questa impostazione è disabilitata, agli studenti verrà richiesto di impostare una password quando tenteranno di connettersi alla macchina virtuale per la prima volta. 

Il proprietario del Lab può reimpostare la password, se necessario, nella pagina **Configura modello** della procedura guidata di creazione Lab. 

![Pagina di configurazione del modello al termine dell'operazione](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

Il proprietario del Lab può anche reimpostare la password dopo la creazione del Lab, nel dashboard. 

## <a name="reset-password-on-the-dashboard"></a>Reimposta la password nel dashboard

1. Selezionare il menu di overflow (tre puntini verticali) nel riquadro Lab e selezionare **Reimposta password**. 

    ![Menu Reimposta password nella home page](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. Nella finestra di dialogo **Imposta password** immettere una password e selezionare **Imposta password**.
    
    ![Finestra di dialogo Imposta password](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle altre opzioni di utilizzo degli studenti che possono essere configurate dal proprietario del Lab, vedere l'articolo seguente: [Configurare l'utilizzo degli studenti](how-to-configure-student-usage.md).

Per informazioni sul modo in cui gli studenti possono reimpostare le password per le proprie macchine virtuali, vedere [impostare o reimpostare la password per le macchine virtuali in laboratori della classe (studenti)](how-to-set-virtual-machine-passwords-student.md).