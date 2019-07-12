---
title: Matrice di supporto di Azure Migrate
description: Fornisce un riepilogo delle impostazioni di supporto e le limitazioni per il servizio Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: raynew
ms.openlocfilehash: b2ca1b9118ecc3d112a49bb4c79b413c46fe67cb
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811557"
---
# <a name="azure-migrate-support-matrix"></a>Matrice di supporto di Azure Migrate

È possibile usare la [servizio Azure Migrate](migrate-overview.md) valutare ed eseguire la migrazione di macchine nel cloud di Microsoft Azure. Questo articolo riepiloga le impostazioni del supporto generali e limitazioni per le distribuzioni e gli scenari di Azure Migrate.


## <a name="azure-migrate-versions"></a>Versioni di Azure Migrate

Sono disponibili due versioni del servizio Azure Migrate:

- **Versione corrente**: Usando questa versione che è possibile creare nuovi progetti di Azure Migrate, individuare un'istanza locale consente di valutare e orchestrare le valutazioni e le migrazioni. [Altre informazioni](whats-new.md#azure-migrate-new-version)
- **Versione precedente**: Per i clienti che usano la versione precedente di Azure Migrate (era supportata solo valutazione delle macchine virtuali VMware on-premises), è ora consigliabile usare la versione corrente. Nella versione precedente, è possibile creare nuovi progetti di eseguire la migrazione di Azure o eseguire nuove scoperte.

## <a name="supported-migration-scenarios"></a>Scenari di migrazione supportati

La tabella riepiloga gli scenari di migrazione supportati.

**Distribuzione** | **Dettagli*** 
--- | --- 
**Valutazione di un'istanza locale** | Consente di valutare i carichi di lavoro in locale e i dati in esecuzione nelle macchine virtuali Hyper-V e macchine virtuali VMware. Valutare mediante Microsoft Data Migration Assistant (DMA) e valutazione di Azure Migrate Server, nonché gli strumenti di terze parti che includono Cloudamize Corent Tech e Turbonomic Server.
**Migrazione da sito locale ad Azure** | Eseguire la migrazione dei carichi di lavoro e i dati in esecuzione in server fisici, macchine virtuali VMware, VM Hyper-V e sulle istanze AWS/GCP, in Azure. Eseguire la migrazione usando Azure eseguire la migrazione di Server Assessment and Azure Database Migration Service (DMS) e nonché utilizzando gli strumenti di terze parti che includono Carbonite e CorentTech.

Supporto di strumenti specifico viene riepilogato come segue.

**Strumento** | **Valutazione/migrazione** | **Dettagli**
--- | --- | ---
Azure Migrate Server Assessment | Valutazione | Provare a valutazione dei server per [Hyper-V](tutorial-prepare-hyper-v.md) e [VMware](tutorial-prepare-vmware.md).
Cloudamize | Valutazione | [Altre informazioni](https://www.cloudamize.com/platform#tab-0)
CorentTech | Valutazione | [Altre informazioni](https://www.corenttech.com/)
Turbonomic | Valutazione | [Altre informazioni](https://turbonomic.com/solutions/technologies/azure-cloud/)
Azure Migrate migrazione del Server | Migrazione | Provare la migrazione di server per [Hyper-V](tutorial-migrate-hyper-v.md) e [VMware](tutorial-migrate-vmware.md).
Carbonite | Migrazione | [Altre informazioni](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure)
CorentTech | Migrazione | [Altre informazioni](https://www.corenttech.com/)


## <a name="azure-migrate-projects"></a>Progetti di Azure Migrate

**Supporto** | **Dettagli**
--- | ---
Sottoscrizione | È possibile avere un singolo progetto Azure Migrate in una sottoscrizione.
Autorizzazioni di Azure | Sono necessarie autorizzazioni di proprietario o collaboratore nella sottoscrizione per creare un progetto Azure Migrate.
VM VMware  | Consente di valutare fino a 35.000 macchine virtuali VMware in un singolo progetto.
VM Hyper-V | Consente di valutare fino a 10.000 host Hyper-v in un singolo progetto.

Un progetto può includere sia macchine virtuali VMware e le macchine virtuali Hyper-V, fino ai limiti di valutazione.


## <a name="vmware-assessment-and-migration"></a>Migrazione e la valutazione di VMware

[Revisione](migrate-support-matrix-vmware.md) la valutazione di Azure Migrate Server e la migrazione di Server matrice di supporto per le macchine virtuali VMware.

## <a name="hyper-v-assessment-and-migration"></a>Migrazione e la valutazione di Hyper-V

[Revisione](migrate-support-matrix-hyper-v.md) la valutazione di Azure Migrate Server e la migrazione di Server matrice di supporto per le macchine virtuali Hyper-V.


## <a name="next-steps"></a>Passaggi successivi

- [Valutare le macchine virtuali VMware](tutorial-assess-vmware.md) per la migrazione.
- [Valutare le macchine virtuali Hyper-V](tutorial-assess-hyper-v.md) per la migrazione.

