---
title: Valutare un numero elevato di server fisici per la migrazione ad Azure con Azure Migrate . Documenti Microsoft
description: Descrive come valutare un numero elevato di server fisici per la migrazione ad Azure usando il servizio Azure Migrate.Describes how to assess large numbers of physical servers for migration to Azure using the Azure Migrate service.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: a19a1b6e7416667079ab07fc5440ee8828c26bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76294370"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Valutare un numero elevato di server fisici per la migrazione ad AzureAssess large numbers of physical servers for migration to Azure

Questo articolo descrive come valutare un numero elevato di server fisici locali per la migrazione ad Azure usando lo strumento di valutazione del server di Azure Migrate.This article describes how to assess large numbers of on-premises physical servers for migration to Azure, using the Azure Migrate Server Assessment tool.

[Azure Migrate](migrate-services-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti. 


In questo articolo vengono illustrate le operazioni seguenti:
> [!div class="checklist"]
> * Pianificare la valutazione su larga scala.
> * Configurare le autorizzazioni di Azure e preparare i server fisici per la valutazione.
> * Creare un progetto Azure Migrate e creare una valutazione.
> * Esaminare la valutazione durante la pianificazione della migrazione.


> [!NOTE]
> Se si desidera provare un proof-of-concept per valutare un paio di server prima di valutare su larga scala, seguire la nostra [serie di tutorial](tutorial-prepare-physical.md).

## <a name="plan-for-assessment"></a>Piano per la valutazione

Quando si pianifica la valutazione di un numero elevato di server fisici, è necessario considerare un paio di aspetti:

- **Pianificare**i progetti di Azure Migrate: informazioni su come distribuire progetti di Azure Migrate.Plan Azure Migrate projects : How out how to deploy Azure Migrate projects. Ad esempio, se i data center si trovano in aree geografiche diverse o è necessario archiviare metadati correlati all'individuazione, alla valutazione o alla migrazione in un'area geografica diversa, potrebbero essere necessari più progetti.
- **Pianificare le appliance:** Azure Migrate usa un'appliance di Azure Migrate locale, distribuita in un computer Windows, per individuare continuamente i server per la valutazione e la migrazione. L'appliance monitora le modifiche dell'ambiente, ad esempio l'aggiunta di macchine virtuali, dischi o schede di rete. Invia inoltre metadati e dati sulle prestazioni su di essi ad Azure.It also sends metadata and performance data about them to Azure. È necessario determinare il numero di appliance da distribuire.


## <a name="planning-limits"></a>Limiti di pianificazione
 
Utilizzare i limiti riepilogati in questa tabella per la pianificazione.

**Pianificazione** | **Limiti**
--- | --- 
**Progetti di azure migrateAzure Migrate projects** | Valutare fino a 35.000 server in un progetto.
**Appliance Azure Migrate** | Un apparecchio può scoprire fino a 250 server.<br/> Un'appliance può essere associata solo a un singolo progetto di Azure Migrate.An appliance can only be associated with a single Azure Migrate project.<br/> È possibile associare un numero qualsiasi di appliance a un singolo progetto di Azure Migrate.Any number of appliances can be associated with a single Azure Migrate project. <br/><br/> 
**Gruppo** | È possibile aggiungere fino a 35.000 server in un singolo gruppo.
**Valutazione di Azure MigrateAzure Migrate assessment** | È possibile valutare fino a 35.000 server in un'unica valutazione.


## <a name="other-planning-considerations"></a>Altre considerazioni sulla pianificazione

- Per avviare l'individuazione dall'appliance, è necessario selezionare ogni server fisico. 

## <a name="prepare-for-assessment"></a>Prepararsi per la valutazione

Preparare Azure e i server fisici per la valutazione del server. 

1. Verificare [i requisiti e le limitazioni](migrate-support-matrix-physical.md)del supporto del server fisico .
2. Configurare le autorizzazioni per l'account Azure per interagire con Azure Migrate.Set up permissions for your Azure account to interact with Azure Migrate.
3. Preparare i server fisici.

Seguire le istruzioni in [questa esercitazione](tutorial-prepare-physical.md) per configurare queste impostazioni.

## <a name="create-a-project"></a>Creare un progetto

In conformità con i requisiti di pianificazione, eseguire le operazioni seguenti:

1. Creare un progetto di Azure Migrate.
2. Aggiungere lo strumento di valutazione del server di Azure Migrate ai progetti.

[Scopri di più](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Creare e rivedere una valutazione

1. Creare valutazioni per i server fisici.
1. Esaminare le valutazioni in preparazione per la pianificazione della migrazione.

[Ulteriori informazioni](tutorial-assess-physical.md) sulla creazione e la revisione delle valutazioni.
    

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si apprenderà come:
 
> [!div class="checklist"] 
> * Pianificato di ridimensionare le valutazioni di Azure Migrate per i server fisici.
> * Azure preparato e server fisici per la valutazione.
> * Creazione di un progetto Azure Migrate ed è stata eseguita le valutazioni.
> * Valutazioni riviste in preparazione per la migrazione.

A [questo](concepts-assessment-calculation.md) punto, scopri come vengono calcolate le valutazioni e come [modificare le valutazioni.](how-to-modify-assessment.md)
