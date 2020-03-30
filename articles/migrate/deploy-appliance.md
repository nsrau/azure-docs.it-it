---
title: Configurare un applianc di Azure MigrateSet up an Azure Migrate applianc
description: Informazioni su come configurare un'appliance Di Azure Migrate per valutare ed eseguire la migrazione delle macchine virtuali VMware.Learn how to set up an Azure Migrate appliance to assess and migrate VMware VMs.
ms.topic: article
ms.date: 11/18/2019
ms.openlocfilehash: 0447443bb6a642cac09566450ea2d9bb6f6453d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337662"
---
# <a name="set-up-an-azure-migrate-appliance"></a>È stata configurata un'appliance di Azure Migrate

Questo articolo riepiloga le opzioni per la configurazione dell'appliance Di schiene di Azure.This article summarizes options for setting up the Azure Migrate appliance. 

L'appliance Azure Migrate è una distribuzione di appliance leggera in locale e viene usata in diversi scenari.

**Scenario** | **Dettagli**
--- | ---
Valutare le macchine virtuali VMware con Azure Migrate:Server Assessment | Individuare le app e le dipendenze delle macchine virtualiDiscover VM apps and dependencies<br/><br/> Raccogliere i metadati del computer e i metadati delle prestazioni per le valutazioni.
Valutare le macchine virtuali Hyper-V con Azure Migrate:Server Assessment | Individuazione delle macchine virtuali Hyper-V<br/><br/> Raccogliere i metadati del computer e i metadati delle prestazioni per le valutazioni.
Valutare le macchine fisiche | Individuazione delle macchine come server fisici<br/><br/> Raccogliere i metadati del computer e i metadati delle prestazioni per le valutazioni.
Replicare le macchine virtuali VMware con la migrazione senza agente. | Replicare le macchine virtuali VMware senza installare alcun elemento nelle macchine virtuali da replicare.


## <a name="deployment-options"></a>Opzioni di distribuzione

È possibile distribuire l'appliance in due modi.

**Scenario** | **Modello** | **Script** 
--- | --- | --- | ---
**Valutare le macchine virtuali VMware** | Eseguire la distribuzione con un modello OVA scaricato.<br/><br/> Informazioni su come distribuire l'appliance [usando un modello](how-to-set-up-appliance-vmware.md)oppure avviare l'esercitazione di [valutazione](tutorial-prepare-vmware.md) e distribuire l'appliance con un modello durante l'esercitazione.  | Eseguire la distribuzione usando uno script del programma di installazione di PowerShell.Deploy using a PowerShell installer script.<br/><br/>  [Esaminare le](deploy-appliance-script.md) istruzioni di script dell'appliance.
**Valutare le VM Hyper-V** | Eseguire la distribuzione con un modello VHD scaricato. <br/><br/> Informazioni su come distribuire l'appliance [usando un modello](how-to-set-up-appliance-vmware.md)oppure avviare l'esercitazione di [valutazione](tutorial-prepare-vmware.md) e distribuire l'appliance con un modello durante l'esercitazione. | Eseguire la distribuzione usando uno script del programma di installazione di PowerShell.Deploy using a PowerShell installer script.<br/><br/> [Esaminare le](deploy-appliance-script.md) istruzioni di script dell'appliance. 
**Valutare i server fisici** | Nessun modello. | Eseguire la distribuzione usando uno script del programma di installazione di PowerShell.Deploy using a PowerShell installer script.<br/><br/> Esaminare le istruzioni dello [script dell'appliance](how-to-set-up-appliance-physical.md)oppure avviare l'esercitazione di [valutazione](tutorial-prepare-physical.md)e distribuire l'appliance durante l'esercitazione.
**Replicare le macchine virtuali VMware (senza agente)Replicate VMware VMs (agentless)** | Eseguire la distribuzione con un modello OVA scaricato.<br/><br/> Se sono già state valutate le macchine virtuali replicate, l'appliance è già stata configurata durante la valutazione.<br/><br/> Se si replicano le macchine virtuali VMware senza valutarle, vedere come distribuire l'appliance usando un modello oppure avviare [l'esercitazione](tutorial-migrate-vmware.md)sulla migrazione senza agenti e distribuire l'appliance con un modello durante l'esercitazione. | Eseguire la distribuzione usando uno script del programma di installazione di PowerShell.Deploy using a PowerShell installer script. <br/><br/> [Esaminare le](deploy-appliance-script.md) istruzioni di script dell'appliance. 




## <a name="next-steps"></a>Passaggi successivi

[Esaminare](migrate-appliance.md) i requisiti dell'appliance.