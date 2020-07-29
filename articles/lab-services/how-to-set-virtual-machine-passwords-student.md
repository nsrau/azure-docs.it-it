---
title: Reimpostare le password per le macchine virtuali del Lab della classe in Azure Lab Services | Microsoft Docs
description: Informazioni su come reimpostare le password per le macchine virtuali (VM) in laboratori della classe Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d63d14da8859c12fa797a04fa9a3c3230219adab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445339"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Impostare o reimpostare la password per le macchine virtuali in laboratori della classe (studenti)
Questo articolo illustra in che modo gli studenti possono impostare o reimpostare la password per le proprie macchine virtuali. 

## <a name="enable-resetting-of-passwords"></a>Abilitare la reimpostazione delle password
Al momento della creazione del Lab, il proprietario del Lab può abilitare o disabilitare l' **uso della stessa password per tutte le macchine virtuali**. Se questa opzione è stata abilitata, gli studenti non possono reimpostare la password. Tutte le macchine virtuali nei lab avranno la stessa password impostata dall'insegnante. 

Se questa opzione è disabilitata, gli utenti dovranno impostare una password durante il tentativo di connessione alla macchina virtuale per la prima volta. Gli studenti possono anche reimpostare la password in un secondo momento, come illustrato nell'ultima sezione di questo articolo. 

## <a name="reset-password-for-the-first-time"></a>Reimposta la password per la prima volta
Se l'opzione **Usa la stessa password per tutte le macchine virtuali** è stata disabilitata, quando gli utenti (studenti) selezionano il pulsante **Connetti** nel riquadro Lab della pagina **macchine virtuali** , l'utente Visualizza la finestra di dialogo seguente per impostare la password per la macchina virtuale: 

![Reimposta la password per lo studente](./media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Reimpostar la password in un secondo momento
Lo studente può anche impostare la password facendo clic sul menu di overflow (**tre puntini verticali**) nel riquadro Lab e selezionando **Reimposta password**. 

![Reimpostar la password in un secondo momento](./media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle altre opzioni di utilizzo degli studenti che un proprietario del Lab può configurare, vedere l'articolo seguente: [configurare l'utilizzo degli studenti](how-to-configure-student-usage.md).
