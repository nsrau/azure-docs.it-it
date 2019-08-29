---
title: Matrice di supporto Azure Migrate
description: Fornisce un riepilogo delle impostazioni e delle limitazioni del supporto per il servizio Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: raynew
ms.openlocfilehash: 99ef5c8a4a4291dd1b9d047da9006623dacd0b53
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147871"
---
# <a name="azure-migrate-support-matrix"></a>Matrice di supporto Azure Migrate

È possibile usare il [servizio Azure migrate](migrate-overview.md) per valutare ed eseguire la migrazione di computer nel cloud Microsoft Azure. Questo articolo riepiloga le impostazioni e le limitazioni generali del supporto per Azure Migrate scenari e distribuzioni.


## <a name="azure-migrate-versions"></a>Versioni di Azure Migrate

Sono disponibili due versioni del servizio Azure Migrate:

- **Versione corrente**: Con questa versione è possibile creare nuovi progetti di Azure Migrate, individuare le valutazioni locali e orchestrare valutazioni e migrazioni. [Altre informazioni](whats-new.md#azure-migrate-new-version)
- **Versione precedente**: ai clienti che usano la versione precedente di Azure Migrate (è supportata solo la valutazione di macchine virtuali VMware locali), si consiglia di usare ora la versione corrente. Nella versione precedente non è possibile creare nuovi progetti di Azure Migrate o eseguire nuove individuazioni.

## <a name="supported-migration-scenarios"></a>Scenari di migrazione supportati

Nella tabella sono riepilogati gli scenari di migrazione supportati.

**Distribuzione** | **Dettagli** 
--- | --- 
**Valutazione locale** | Valutare i carichi di lavoro e i dati locali in esecuzione su macchine virtuali VMware e Hyper-V. Valuta l'uso di Azure Migrate server Assessment and Microsoft Data Migration Assistant (DMA), nonché di strumenti di terze parti che includono Cloudamize, Corent Tech e Turbonomic server.
**Migrazione locale ad Azure** | Eseguire la migrazione di carichi di lavoro e dati in esecuzione su server fisici, macchine virtuali VMware, macchine virtuali Hyper-V, server fisici e macchine virtuali basate su cloud in Azure. Esegui la migrazione con Azure Migrate server assessment e il servizio migrazione del database di Azure (DMS), oltre che con strumenti di terze parti che includono Carbonite e CorentTech.

Il supporto dello strumento specifico viene riepilogato come indicato di seguito.

**Strumento** | **Valutazione/migrazione** | **Dettagli**
--- | --- | ---
Valutazione server di Azure Migrate | Valutazione | Provare valutazione server per [Hyper-V](tutorial-prepare-hyper-v.md) e [VMware](tutorial-prepare-vmware.md).
Cloudamize | Valutazione | [Altre informazioni](https://www.cloudamize.com/platform#tab-0)
CorentTech | Valutazione | [Altre informazioni](https://www.corenttech.com/)
Turbonomic | Valutazione | [Altre informazioni](https://turbonomic.com/solutions/technologies/azure-cloud/)
Migrazione server di Azure Migrate | Migrazione | Provare la migrazione del server per [Hyper-V](tutorial-migrate-hyper-v.md) e [VMware](tutorial-migrate-vmware.md).
Carbonite | Migrazione | [Altre informazioni](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure)
CorentTech | Migrazione | [Altre informazioni](https://www.corenttech.com/)


## <a name="azure-migrate-projects"></a>Progetti Azure Migrate

**Supporto** | **Dettagli**
--- | ---
Sottoscrizione | È possibile avere più progetti Azure Migrate in una sottoscrizione.
Autorizzazioni di Azure | Per creare un progetto di Azure Migrate, è necessario disporre delle autorizzazioni Collaboratore o proprietario nella sottoscrizione.
VM VMware  | Consente di valutare fino a 35.000 VM VMware in un singolo progetto.
VM Hyper-V | Valutazione di un massimo di 10.000 macchine virtuali Hyper-V in un singolo progetto.

Un progetto può includere sia macchine virtuali VMware che macchine virtuali Hyper-V, fino ai limiti di valutazione.


## <a name="vmware-assessment-and-migration"></a>Valutazione e migrazione VMware

[Esaminare](migrate-support-matrix-vmware.md) la matrice di supporto per la migrazione del server e la valutazione di Azure migrate server per le macchine virtuali VMware.

## <a name="hyper-v-assessment-and-migration"></a>Valutazione e migrazione di Hyper-V

[Esaminare](migrate-support-matrix-hyper-v.md) la matrice di supporto per la migrazione del server e la valutazione di Azure migrate server per le macchine virtuali Hyper-V.


## <a name="next-steps"></a>Passaggi successivi

- [Valutare le macchine virtuali VMware](tutorial-assess-vmware.md) per la migrazione.
- [Valutare le VM Hyper-V](tutorial-assess-hyper-v.md) per la migrazione.

