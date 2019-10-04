---
title: Reimpostare le password per le macchine virtuali del Lab della classe in Azure Lab Services | Microsoft Docs
description: Informazioni su come reimpostare le password per le macchine virtuali (VM) in laboratori della classe Azure Lab Services.
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
ms.openlocfilehash: 26b0f710590496875521e0dd8577a35841fbd3dd
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657008"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Impostare o reimpostare la password per le macchine virtuali in laboratori della classe (studenti)
Questo articolo illustra in che modo gli studenti possono impostare o reimpostare la password per le proprie macchine virtuali. 

## <a name="enable-resetting-of-passwords"></a>Abilitare la reimpostazione delle password
Al momento della creazione del Lab, il proprietario del Lab può abilitare o disabilitare l' **uso della stessa password per tutte le macchine virtuali**. Se questa opzione è stata abilitata, gli studenti non possono reimpostare la password. Tutte le macchine virtuali nei lab avranno la stessa password impostata dall'insegnante. 

Se questa opzione è disabilitata, gli utenti dovranno impostare una password durante il tentativo di connessione alla macchina virtuale per la prima volta. Gli studenti possono anche reimpostare la password in un secondo momento, come illustrato nell'ultima sezione di questo articolo. 

## <a name="reset-password-for-the-first-time"></a>Reimposta la password per la prima volta
Se l'opzione **Usa la stessa password per tutte le macchine virtuali** è stata disabilitata, quando gli utenti (studenti) selezionano il pulsante **Connetti** nel riquadro Lab della pagina **macchine virtuali** , l'utente Visualizza la finestra di dialogo seguente per impostare la password per la macchina virtuale: 

![Reimposta la password per lo studente](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Reimposta la password in un secondo momento
Lo studente può anche impostare la password facendo clic sul menu di overflow (**tre puntini verticali**) nel riquadro Lab e selezionando **Reimposta password**. 

![Reimposta la password in un secondo momento](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle altre opzioni di utilizzo degli studenti che possono essere configurate da un proprietario del Lab, vedere l'articolo seguente: [Configurare l'utilizzo degli studenti](how-to-configure-student-usage.md).
