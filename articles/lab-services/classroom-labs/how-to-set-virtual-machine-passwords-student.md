---
title: Reimpostare le password per le macchine virtuali del laboratorio in classe in Azure Lab Services . Documenti Microsoft
description: Informazioni su come reimpostare le password per le macchine virtuali nei laboratori in classe di Azure Lab Services.Learn how to reset passwords for virtual machines (VMs) in classroom labs of Azure Lab Services.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 7c757ef8508f9364a46116e6ddf19207f23a4b6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73583673"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Impostare o reimpostare la password per le macchine virtuali nei laboratori di classe (studenti)Set or reset password for virtual machines in classroom labs (students)
Questo articolo illustra come gli studenti possono impostare/reimpostare la password per le macchine virtuali. 

## <a name="enable-resetting-of-passwords"></a>Abilitare la reimpostazione delle password
Al momento della creazione del lab, il proprietario del lab può abilitare o disabilitare la **stessa password per tutte le macchine virtuali.** Se questa opzione è stata abilitata, gli studenti non possono reimpostare la password. Tutte le macchine virtuali nei laboratori avranno la stessa password impostata dall'istruttore. 

Se questa opzione è disabilitata, gli utenti dovranno impostare una password quando tentano di connettersi alla macchina virtuale per la prima volta. Gli studenti possono anche reimpostare la password in un secondo momento, come mostrato nell'ultima sezione di questo articolo. 

## <a name="reset-password-for-the-first-time"></a>Reimpostare la password per la prima volta
Se l'opzione **Usa la stessa password per tutte le macchine virtuali** è stata disabilitata, quando gli utenti (studenti) selezionano il pulsante Connetti nel riquadro lab nella pagina Macchine virtuali personali, l'utente visualizza la finestra di dialogo seguente per impostare la password per la macchina virtuale:If the Use same password for all virtual machines option was disabled, when users (students) select the **connect** button on the lab tile on the My **virtual machines** page, the user see the following dialog box to set the password for the VM: 

![Reimpostare la password per lo studente](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Reimposta password in un secondo momento
Lo studente può anche impostare la password facendo clic sul menu di overflow **(tre puntini verticali)** nel riquadro lab e selezionando **Reimposta password**. 

![Reimposta password in un secondo momento](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle altre opzioni di utilizzo degli studenti che il proprietario di un lab può configurare, vedere il seguente articolo: [Configure student usage](how-to-configure-student-usage.md).
