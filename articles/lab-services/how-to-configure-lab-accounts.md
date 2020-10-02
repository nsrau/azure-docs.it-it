---
title: Configurare l'arresto automatico delle macchine virtuali in Azure Lab Services
description: Questo articolo descrive come configurare l'arresto automatico delle macchine virtuali nell'account lab.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: c0a147a81aaed88313a1b9aa4b0754d9a3badcb5
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91650035"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>Configurare l'arresto automatico delle macchine virtuali per un account Lab

È possibile abilitare diverse funzionalità di controllo dei costi di arresto automatico per prevenire in modo proattivo i costi aggiuntivi quando le macchine virtuali non vengono usate attivamente. La combinazione delle tre funzionalità di arresto e disconnessione automatiche rileva la maggior parte dei casi in cui gli utenti lasciano accidentalmente le macchine virtuali in esecuzione:
 
- Disconnettere automaticamente gli utenti dalle macchine virtuali ritenute inattive dal sistema operativo.
- Arresta automaticamente le macchine virtuali quando gli utenti si disconnettono.
- Arrestare automaticamente le macchine virtuali avviate, ma gli utenti non si connettono.

Per ulteriori informazioni sulle funzionalità di arresto automatico, vedere la sezione [ottimizzare il controllo dei costi con le impostazioni di arresto automatico](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) .

## <a name="enable-automatic-shutdown"></a>Abilitare l'arresto automatico

1. Nella [portale di Azure](https://portal.azure.com/) passare alla pagina dell' **account Lab** .
1. Selezionare **Labs Settings (impostazioni Lab** ) nel menu a sinistra.
1. Selezionare le impostazioni di arresto automatico appropriate per lo scenario in uso.  

    > [!div class="mx-imgBorder"]
    > ![Impostazione dell'arresto automatico per l'account lab](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    Le impostazioni si applicano a tutti i Lab creati nell'account Lab. Un autore di lab può eseguire l'override di questa impostazione a livello di lab. La modifica apportata a questa impostazione nell'account lab influirà solo sui lab creati dopo la modifica.

    Per disabilitare le impostazioni, deselezionare le caselle di controllo in questa pagina. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come un proprietario del Lab può configurare o eseguire l'override di questa impostazione a livello di Lab, vedere [configurare l'arresto automatico delle macchine virtuali per un Lab](how-to-enable-shutdown-disconnect.md)
