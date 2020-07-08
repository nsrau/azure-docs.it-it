---
title: Valutare un numero elevato di server fisici per la migrazione ad Azure con Azure Migrate | Microsoft Docs
description: Viene descritto come valutare un numero elevato di server fisici per la migrazione ad Azure usando il servizio Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: b84a7b88f700e2ba8e9fbe29099dfcbce8dbf3b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85081066"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Valutare un numero elevato di server fisici per la migrazione ad Azure

Questo articolo descrive come valutare un numero elevato di server fisici locali per la migrazione ad Azure, usando lo strumento di valutazione di Azure Migrate server.

[Azure Migrate](migrate-services-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti. 


In questo articolo vengono illustrate le operazioni seguenti:
> [!div class="checklist"]
> * Pianificare la valutazione su larga scala.
> * Configurare le autorizzazioni di Azure e preparare i server fisici per la valutazione.
> * Creare un progetto Azure Migrate e creare una valutazione.
> * Esaminare la valutazione durante la pianificazione della migrazione.


> [!NOTE]
> Se si vuole provare un modello di prova per valutare un paio di server prima di valutare la scalabilità, seguire la [serie di esercitazioni](tutorial-prepare-physical.md).

## <a name="plan-for-assessment"></a>Pianificare la valutazione

Quando si pianifica la valutazione di un numero elevato di server fisici, è necessario considerare alcuni aspetti:

- **Pianificare Azure migrate progetti**: scoprire come distribuire progetti di Azure migrate. Se, ad esempio, i Data Center si trovano in aree geografiche diverse oppure è necessario archiviare i metadati relativi a individuazione, valutazione o migrazione in un'altra area geografica, potrebbero essere necessari più progetti.
- **Dispositivi del piano**: Azure migrate usa un'appliance Azure migrate locale, distribuita in un computer Windows, per individuare continuamente i server per la valutazione e la migrazione. Il dispositivo monitora le modifiche apportate all'ambiente, ad esempio l'aggiunta di VM, dischi o schede di rete. Invia inoltre i metadati e i dati sulle prestazioni relativi ad Azure. È necessario determinare il numero di Appliance da distribuire.


## <a name="planning-limits"></a>Limiti di pianificazione
 
Usare i limiti riepilogati in questa tabella per la pianificazione.

**Pianificazione** | **Limiti**
--- | --- 
**Progetti Azure Migrate** | Valutazione di un massimo di 35.000 server in un progetto.
**Appliance Azure Migrate** | Un appliance può individuare fino a 1000 server.<br/> Un appliance può essere associato solo a un singolo progetto Azure Migrate.<br/> Un numero qualsiasi di Appliance può essere associato a un singolo progetto di Azure Migrate. <br/><br/> 
**Gruppo** | È possibile aggiungere fino a 35.000 server in un singolo gruppo.
**Valutazione Azure Migrate** | È possibile valutare fino a 35.000 server in una singola valutazione.


## <a name="other-planning-considerations"></a>Altre considerazioni sulla pianificazione

- Per avviare l'individuazione dal dispositivo, è necessario selezionare ciascun server fisico. 

## <a name="prepare-for-assessment"></a>Preparare la valutazione

Preparare i server fisici e di Azure per la valutazione del server. 

1. Verificare i [requisiti e le limitazioni del supporto per i server fisici](migrate-support-matrix-physical.md).
2. Configurare le autorizzazioni per l'account di Azure per interagire con Azure Migrate.
3. Preparare i server fisici.

Per configurare queste impostazioni, seguire le istruzioni riportate in [questa esercitazione](tutorial-prepare-physical.md) .

## <a name="create-a-project"></a>Creare un progetto

In conformità ai requisiti di pianificazione, eseguire le operazioni seguenti:

1. Creare un progetto di Azure Migrate.
2. Aggiungere lo strumento Azure Migrate server assessment ai progetti.

[Altre informazioni](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Creare ed esaminare una valutazione

1. Creare valutazioni per i server fisici.
1. Esaminare le valutazioni in preparazione per la pianificazione della migrazione.

[Altre](tutorial-assess-physical.md) informazioni sulla creazione e la revisione delle valutazioni.
    

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si apprenderà come:
 
> [!div class="checklist"] 
> * Pianificato per la scalabilità Azure Migrate valutazioni per i server fisici.
> * Server fisici e di Azure preparati per la valutazione.
> * Creazione di un progetto Azure Migrate ed esecuzione di valutazioni.
> * Revisione delle valutazioni in preparazione alla migrazione.

A questo punto, [informazioni su come](concepts-assessment-calculation.md) vengono calcolate le valutazioni e su come [modificare le valutazioni](how-to-modify-assessment.md).
