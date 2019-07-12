---
title: Valutare un elevato numero di macchine virtuali Hyper-V per la migrazione ad Azure con Azure Migrate | Microsoft Docs
description: Viene descritto come valutare un elevato numero di macchine virtuali Hyper-V per la migrazione ad Azure tramite il servizio Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 95704f2694892b349d0967fca2160dabd990b472
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811544"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Valutare un elevato numero di macchine virtuali Hyper-V per la migrazione ad Azure

Questo articolo descrive come valutare un numero elevato (> 1000) delle macchine virtuali Hyper-V in locale per la migrazione ad Azure usando lo strumento di valutazione di Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) fornisce un hub di strumenti che consentono di individuare, valutare e la migrazione di App, infrastruttura e i carichi di lavoro in Microsoft Azure. L'hub include strumenti di Azure Migrate e le offerte di fornitori di software indipendenti di terze parti. 


In questo articolo viene spiegato come:
> [!div class="checklist"]
> * Piano per la valutazione su larga scala.
> * Configurare le autorizzazioni di Azure, quindi preparare Hyper-V per la valutazione.
> * Creare un progetto Azure Migrate e creare una valutazione.
> * Quando si pianifica la migrazione, rivedere la valutazione.


> [!NOTE]
> Se si vuole provare un proof-of-concept per valutare un paio di macchine virtuali di prima fase di valutazione su larga scala, seguire la [serie di esercitazioni](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Piano per la valutazione di

Durante la pianificazione per la valutazione del numero elevato di macchine virtuali Hyper-V, esistono un paio di aspetti da considerare:

- **Pianificare i progetti Azure Migrate**: Scoprire come distribuire i progetti Azure Migrate. Ad esempio, se è necessario archiviare il rilevamento, valutazione o i metadati correlati alla migrazione in una diversa area geografica dei data center sono in aree geografiche diverse oppure potrebbe essere più progetti.
- **Pianificare Appliance**: Azure Migrate Usa una Azure Migrate appliance locale, distribuita come macchina virtuale Hyper-V, per individuare continuamente le macchine virtuali per la valutazione e migrazione. L'appliance consente di monitorare le modifiche dell'ambiente, ad esempio l'aggiunta di macchine virtuali, dischi o schede di rete. Anche invia i dati dei metadati e le prestazioni riguardo ad Azure. È necessario stabilire quanti dispositivi per la distribuzione.


## <a name="planning-limits"></a>Pianificazione di limiti
 
Usare i limiti riepilogati nella tabella seguente per la pianificazione.

**Pianificazione** | **Limiti**
--- | --- 
**Progetti di Azure Migrate** | Consente di valutare fino a 10.000 macchine virtuali in un progetto.
**Appliance Azure Migrate** | Un'appliance può individuare le macchine virtuali fino a 5000.<br/> Un'appliance può connettersi agli host Hyper-V fino a 300.<br/> Può essere associata a un singolo progetto Azure Migrate solo un'appliance.<br/><br/> 
**Valutazione di Azure Migrate** | È possibile valutare fino a 10.000 macchine virtuali in una singola valutazione.



## <a name="other-planning-considerations"></a>Altre considerazioni sulla pianificazione

- Per avviare l'individuazione dall'appliance, è necessario selezionare ogni host Hyper-V. 
- Se si esegue un ambiente multi-tenant, è attualmente non è possibile individuare solo le macchine virtuali che appartengono a un tenant specifico. 

## <a name="prepare-for-assessment"></a>Preparare per la valutazione di

Preparare Hyper-V e Azure per la valutazione di server. 

1. Verificare [limitazioni e requisiti di supporto di Hyper-V](migrate-support-matrix-hyper-v.md).
2. Impostare le autorizzazioni per l'account di Azure interagire con Azure Migrate
3. Preparare gli host Hyper-V e macchine virtuali

Seguire le istruzioni in [in questa esercitazione](tutorial-prepare-hyper-v.md) configurare queste impostazioni.

## <a name="create-a-project"></a>Creare un progetto

In base alle necessità di pianificazione, eseguire le operazioni seguenti:

1. Creare progetti di Azure Migrate.
2. Aggiungere lo strumento di valutazione di Azure Migrate Server per i progetti.

[Altre informazioni](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Creare ed esaminare una valutazione

1. Per creare le valutazioni per le macchine virtuali Hyper-V.
1. Esaminare le valutazioni in preparazione per la pianificazione della migrazione.

[Altre informazioni](tutorial-assess-hyper-v.md) sulla creazione e verifica le valutazioni.
    

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si apprenderà come:
 
> [!div class="checklist"] 
> * Pianificato per la scalabilità di Azure Migrate valutazioni per le macchine virtuali Hyper-V
> * Preparazione di Azure e Hyper-V per la valutazione di
> * Creazione di un progetto Azure Migrate ed esecuzione delle valutazioni
> * Rivedere le valutazioni in preparazione per la migrazione.

A questo punto [informazioni su come](concepts-assessment-calculation.md) vengono calcolate le valutazioni e come [modificare valutazioni](how-to-modify-assessment.md)
