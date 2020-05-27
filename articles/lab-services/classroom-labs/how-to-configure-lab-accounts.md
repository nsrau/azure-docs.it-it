---
title: Configurare l'arresto automatico delle macchine virtuali in Azure Lab Services
description: Questo articolo descrive come configurare l'arresto automatico delle macchine virtuali nell'account lab.
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: afe74e5c3aec2519ec22eee9573ae7b47c1c73f8
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588208"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>Configurare l'arresto automatico delle macchine virtuali tramite l'impostazione di disconnessione per un account lab
È possibile abilitare o disabilitare l'arresto automatico delle macchine virtuali dei servizi lab Windows (modello o studente) dopo la disconnessione di una connessione Desktop remoto. È inoltre possibile specificare per quanto tempo i servizi lab devono attendere che l'utente si riconnetta prima di arrestare automaticamente le macchine virtuali.

## <a name="enable-automatic-shutdown"></a>Abilitare l'arresto automatico

1. Nella pagina **Account lab** selezionare **Impostazioni lab** nel menu a sinistra.
2. Selezionare l'opzione **Arresta automaticamente le macchine virtuali alla disconnessione degli utenti**.
3. Specificare per quanto tempo i servizi lab devono attendere che l'utente si riconnetta prima di arrestare automaticamente le macchine virtuali.

    ![Impostazione dell'arresto automatico per l'account lab](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    Questa impostazione si applica a tutti i lab creati nell'account lab. Un autore di lab può eseguire l'override di questa impostazione a livello di lab. La modifica apportata a questa impostazione nell'account lab influirà solo sui lab creati dopo la modifica.

    Per disabilitare questa impostazione, deselezionare la casella di controllo relativa all'opzione **Arresta automaticamente le macchine virtuali alla disconnessione degli utenti** in questa pagina. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come un proprietario del lab può configurare questa impostazione o eseguirne l'override a livello di lab, vedere [questo articolo](how-to-enable-shutdown-disconnect.md)
