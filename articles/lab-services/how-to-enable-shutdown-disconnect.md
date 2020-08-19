---
title: Configurare l'arresto automatico delle macchine virtuali per un Lab in Azure Lab Services
description: Informazioni su come abilitare o disabilitare l'arresto automatico delle macchine virtuali al termine di una connessione desktop remota.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 7941a3eed502f2329f5d2acf244eec7cef322615
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589767"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>Configurare l'arresto automatico delle macchine virtuali per un Lab

Questo articolo illustra come configurare l'arresto automatico delle macchine virtuali per un Lab.

È possibile abilitare diverse funzionalità di controllo dei costi di arresto automatico per prevenire in modo proattivo i costi aggiuntivi quando le macchine virtuali non vengono usate attivamente. La combinazione delle tre funzionalità di arresto e disconnessione automatiche rileva la maggior parte dei casi in cui gli utenti lasciano accidentalmente le macchine virtuali in esecuzione:
 
* Disconnettere automaticamente gli utenti dalle macchine virtuali ritenute inattive dal sistema operativo (solo Windows).
* Arresta automaticamente le macchine virtuali quando gli utenti si disconnettono (Windows & Linux).
* Arrestare automaticamente le macchine virtuali avviate, ma gli utenti non si connettono.

Per ulteriori informazioni sulle funzionalità di arresto automatico, vedere la sezione [ottimizzare il controllo dei costi con le impostazioni di arresto automatico](cost-management-guide.md#maximize-cost-control-with-auto-shutdown-settings) .

Un amministratore dell'account lab può configurare questa impostazione per l'account in cui vengono creati i lab. Per altre informazioni, vedere [Configurare l'arresto automatico delle macchine virtuali tramite l'impostazione di disconnessione per un account lab](how-to-configure-lab-accounts.md). Il proprietario del lab può eseguire l'override dell'impostazione quando crea un lab o dopo la creazione. 

## <a name="configure-for-the-lab-level"></a>Configurare per il livello Lab

È possibile configurare l'impostazione di arresto automatico nell' [Azure Lab Services](https://labs.azure.com/).

* Quando si crea un Lab (nei **criteri del Lab**) o
* Dopo la creazione del Lab (in **Impostazioni**)

> [!div class="mx-imgBorder"]
> ![Configurare al momento della creazione del lab](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

Assicurarsi di esaminare i dettagli relativi all'arresto automatico nella sezione [ottimizzare il controllo dei costi con le impostazioni di arresto automatico](cost-management-guide.md#maximize-cost-control-with-auto-shutdown-settings) .

> [!WARNING]
> Se si arresta il sistema operativo Windows in una macchina virtuale prima di disconnettere una sessione RDP con la macchina virtuale, la funzionalità di arresto automatico non funzionerà correttamente.  

## <a name="next-steps"></a>Passaggi successivi

[Dashboard per lab per le classi](use-dashboard.md)
