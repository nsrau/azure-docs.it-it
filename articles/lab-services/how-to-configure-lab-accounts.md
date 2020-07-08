---
title: Configurare l'arresto automatico delle macchine virtuali in Azure Lab Services
description: Questo articolo descrive come configurare l'arresto automatico delle macchine virtuali nell'account lab.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 783e3b310b3ad06f637453f0e1b11f6a78beec3a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445815"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>Configurare l'arresto automatico delle macchine virtuali tramite l'impostazione di disconnessione per un account lab
È possibile abilitare o disabilitare l'arresto automatico delle macchine virtuali dei servizi lab Windows (modello o studente) dopo la disconnessione di una connessione Desktop remoto. È inoltre possibile specificare per quanto tempo i servizi lab devono attendere che l'utente si riconnetta prima di arrestare automaticamente le macchine virtuali.

## <a name="enable-automatic-shutdown"></a>Abilitare l'arresto automatico

1. Nella pagina **Account lab** selezionare **Impostazioni lab** nel menu a sinistra.
2. Selezionare l'opzione **Arresta automaticamente le macchine virtuali alla disconnessione degli utenti**.
3. Specificare per quanto tempo i servizi lab devono attendere che l'utente si riconnetta prima di arrestare automaticamente le macchine virtuali.

    ![Impostazione dell'arresto automatico per l'account lab](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    Questa impostazione si applica a tutti i lab creati nell'account lab. Un autore di lab può eseguire l'override di questa impostazione a livello di lab. La modifica apportata a questa impostazione nell'account lab influirà solo sui lab creati dopo la modifica.

    Per disabilitare questa impostazione, deselezionare la casella di controllo relativa all'opzione **Arresta automaticamente le macchine virtuali alla disconnessione degli utenti** in questa pagina. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come un proprietario del lab può configurare questa impostazione o eseguirne l'override a livello di lab, vedere [questo articolo](how-to-enable-shutdown-disconnect.md)
