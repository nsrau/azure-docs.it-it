---
title: Configurare l'arresto automatico delle macchine virtuali in Azure Lab Services
description: Questo articolo descrive come configurare l'arresto automatico delle macchine virtuali nell'account lab.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 9fd93d383ba6a2d57057a3b45f8b517c84990043
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589801"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>Configurare l'arresto automatico delle macchine virtuali per un account Lab

È possibile abilitare diverse funzionalità di controllo dei costi di arresto automatico per prevenire in modo proattivo i costi aggiuntivi quando le macchine virtuali non vengono usate attivamente. La combinazione delle tre funzionalità di arresto e disconnessione automatiche rileva la maggior parte dei casi in cui gli utenti lasciano accidentalmente le macchine virtuali in esecuzione:
 
- Disconnettere automaticamente gli utenti dalle macchine virtuali ritenute inattive dal sistema operativo (solo Windows).
- Arresta automaticamente le macchine virtuali quando gli utenti si disconnettono (Windows & Linux).
- Arrestare automaticamente le macchine virtuali avviate, ma gli utenti non si connettono.

Per ulteriori informazioni sulle funzionalità di arresto automatico, vedere la sezione [ottimizzare il controllo dei costi con le impostazioni di arresto automatico](cost-management-guide.md#maximize-cost-control-with-auto-shutdown-settings) .

## <a name="enable-automatic-shutdown"></a>Abilitare l'arresto automatico

1. Nella [portale di Azure](https://portal.azure.com/) passare alla pagina dell' **account Lab** .
1. Selezionare **Labs Settings (impostazioni Lab** ) nel menu a sinistra.
1. Selezionare le impostazioni di arresto automatico appropriate per lo scenario in uso.  

    > [!div class="mx-imgBorder"]
    > ![Impostazione dell'arresto automatico per l'account lab](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    Le impostazioni si applicano a tutti i Lab creati nell'account Lab. Un autore di lab può eseguire l'override di questa impostazione a livello di lab. La modifica apportata a questa impostazione nell'account lab influirà solo sui lab creati dopo la modifica.

    Per disabilitare le impostazioni, deselezionare le caselle di controllo in questa pagina. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come un proprietario del Lab può configurare o eseguire l'override di questa impostazione a livello di Lab, vedere [Enable Automatic Shutdown of VM on Disconnect](how-to-enable-shutdown-disconnect.md)
