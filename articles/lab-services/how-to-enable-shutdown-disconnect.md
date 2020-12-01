---
title: Configurare l'arresto automatico delle macchine virtuali per un Lab in Azure Lab Services
description: Informazioni su come abilitare o disabilitare l'arresto automatico delle macchine virtuali al termine di una connessione desktop remota.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 8f9080f3b7b762d3b9fa448a903a4167cd2cec4a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433931"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>Configurare l'arresto automatico delle macchine virtuali per un Lab

Questo articolo illustra come configurare l'arresto automatico delle macchine virtuali per un Lab.

È possibile abilitare diverse funzionalità di controllo dei costi di arresto automatico per prevenire in modo proattivo i costi aggiuntivi quando le macchine virtuali non vengono usate attivamente. La combinazione delle tre funzionalità di arresto e disconnessione automatiche rileva la maggior parte dei casi in cui gli utenti lasciano accidentalmente le macchine virtuali in esecuzione:
 
* Disconnettere automaticamente gli utenti dalle macchine virtuali ritenute inattive dal sistema operativo.
* Arresta automaticamente le macchine virtuali quando gli utenti si disconnettono.
* Arrestare automaticamente le macchine virtuali avviate, ma gli utenti non si connettono.

Per ulteriori informazioni sulle funzionalità di arresto automatico, vedere la sezione [ottimizzare il controllo dei costi con le impostazioni di arresto automatico](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) .

Un amministratore dell'account lab può configurare questa impostazione per l'account in cui vengono creati i lab. Per altre informazioni, vedere [configurare l'arresto automatico delle macchine virtuali per un account Lab](how-to-configure-lab-accounts.md). Il proprietario del lab può eseguire l'override dell'impostazione quando crea un lab o dopo la creazione. 

## <a name="configure-for-the-lab-level"></a>Configurare per il livello Lab

È possibile configurare l'impostazione di arresto automatico nell' [Azure Lab Services](https://labs.azure.com/).

* Quando si crea un Lab (nei **criteri del Lab**) o
* Dopo la creazione del Lab (in **Impostazioni**)

> [!div class="mx-imgBorder"]
> ![Configurare al momento della creazione del lab](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

Assicurarsi di esaminare i dettagli relativi all'arresto automatico nella sezione [ottimizzare il controllo dei costi con le impostazioni di arresto automatico](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) .

> [!WARNING]
> Se si arresta il sistema operativo Linux o Windows in una macchina virtuale prima di disconnettere una sessione RDP alla macchina virtuale, la funzionalità di arresto automatico non funzionerà correttamente.  
## <a name="next-steps"></a>Passaggi successivi

[Dashboard per Labs](use-dashboard.md)
