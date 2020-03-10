---
title: Matrice di supporto Azure Migrate
description: Fornisce un riepilogo delle impostazioni e delle limitazioni del supporto per il servizio Azure Migrate.
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: raynew
ms.openlocfilehash: 5c3adecf62f9bd15c820baf116dbc01e5d3542fa
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361933"
---
# <a name="azure-migrate-support-matrix"></a>Matrice di supporto Azure Migrate

È possibile usare il [servizio Azure migrate](migrate-overview.md) per valutare ed eseguire la migrazione di computer nel cloud Microsoft Azure. Questo articolo riepiloga le impostazioni e le limitazioni generali del supporto per Azure Migrate scenari e distribuzioni.

## <a name="supported-assessmentmigration-scenarios"></a>Scenari di valutazione/migrazione supportati

Nella tabella sono riepilogati gli scenari di individuazione, valutazione e migrazione supportati.

**Distribuzione** | **Dettagli** 
--- | --- 
**Individuazione specifica dell'app** | È possibile individuare app, ruoli e funzionalità in esecuzione in macchine virtuali VMware. Questa funzionalità è attualmente limitata solo all'individuazione. La valutazione è attualmente a livello di computer. Non è ancora stata offerta una valutazione specifica dell'app, del ruolo o della funzionalità. 
**Valutazione locale** | Valutare i carichi di lavoro e i dati locali in esecuzione su macchine virtuali VMware, macchine virtuali Hyper-V e server fisici. Valutare l'utilizzo di Azure Migrate server Assessment and Microsoft Data Migration Assistant (DMA), nonché altri strumenti e offerte ISV.
**Migrazione locale ad Azure** | Eseguire la migrazione di carichi di lavoro e dati in esecuzione su server fisici, macchine virtuali VMware, macchine virtuali Hyper-V, server fisici e macchine virtuali basate su cloud in Azure. Eseguire la migrazione con Azure Migrate server assessment e il servizio migrazione del database di Azure (DMS), nonché altri strumenti e offerte ISV.


## <a name="supported-tools"></a>Strumenti supportati

Il supporto dello strumento specifico è riepilogato nella tabella.

**Strumento** | **Valutare** | **Migrazione** 
--- | --- | ---
Valutazione server di Azure Migrate | Valutare le [macchine virtuali VMware](tutorial-prepare-vmware.md), le [VM Hyper-V](tutorial-prepare-hyper-v.md)e i [server fisici](tutorial-prepare-physical.md). |  Non disponibile (NA)
Migrazione server di Azure Migrate | ND | Eseguire la migrazione di [VM VMware](tutorial-migrate-vmware.md), [macchine virtuali Hyper-V](tutorial-migrate-hyper-v.md)e [server fisici](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | ND | Eseguire la migrazione di macchine virtuali VMware, macchine virtuali Hyper-V, server fisici e carichi di lavoro di cloud pubblico. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Valutare le macchine virtuali VMware, le VM Hyper-V, i server fisici, i carichi di lavoro del cloud pubblico. | ND
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Valutare ed eseguire la migrazione di macchine virtuali VMware, macchine virtuali Hyper-V, server fisici e carichi di lavoro del cloud pubblico. |  Eseguire la migrazione di macchine virtuali VMware, macchine virtuali Hyper-V, server fisici e carichi di lavoro di cloud pubblico.
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Valutare le macchine virtuali VMware, le VM Hyper-V, i server fisici, i carichi di lavoro del cloud pubblico.| ND
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Valutare i database SQL Server locali. | ND
[Servizio Migrazione del database](https://docs.microsoft.com/azure/dms/dms-overview) | ND | Eseguire la migrazione di SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Valutazione di Virtual Desktop Infrastructure (VDI) | ND
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | Valutare le macchine virtuali VMWare, le VM Hyper-V, le VM Xen, i computer fisici, le workstation (incluso VDI), i carichi di lavoro del cloud pubblico | ND
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | ND | Eseguire la migrazione di macchine virtuali VMWare, VM Hyper-V, VM Xen, VM KVM, computer fisici, carichi di lavoro del cloud pubblico 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Valutare le macchine virtuali VMware, le VM Hyper-V, i server fisici, i carichi di lavoro del cloud pubblico. | ND
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Valutare le macchine virtuali VMware, le VM Hyper-V, i server fisici, i carichi di lavoro del cloud pubblico e i database SQL Server. | ND
[Migration Assistant webapp](https://appmigration.microsoft.com/) | Valutazione delle app Web | Eseguire la migrazione di app Web.


## <a name="azure-migrate-projects"></a>Progetti Azure Migrate

**Supporto** | **Dettagli**
--- | ---
Subscription | È possibile avere più progetti Azure Migrate in una sottoscrizione.
Autorizzazioni di Azure | Per creare un progetto di Azure Migrate, è necessario disporre delle autorizzazioni Collaboratore o proprietario nella sottoscrizione.
VM VMware  | Consente di valutare fino a 35.000 VM VMware in un singolo progetto.
VM Hyper-V | Valutazione di un massimo di 35.000 macchine virtuali Hyper-V in un singolo progetto.

Un progetto può includere sia macchine virtuali VMware che macchine virtuali Hyper-V, fino ai limiti di valutazione.

## <a name="azure-permissions"></a>Autorizzazioni di Azure

Per Azure Migrate usare Azure, è necessario disporre di queste autorizzazioni prima di iniziare la valutazione e la migrazione dei computer.

**Attività** | **Autorizzazioni** | **Dettagli**
--- | --- | ---
Creare un progetto di Azure Migrate | L'account di Azure necessita di autorizzazioni per creare un progetto. | Configurare per [VMware](tutorial-prepare-vmware.md#assign-permissions-to-create-project), [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)o [server fisici](tutorial-prepare-physical.md#assign-permissions-to-create-project).
Registrare il dispositivo Azure Migrate| Azure Migrate usa un' [appliance di Azure migrate](migrate-appliance.md) Lightweight per valutare i computer con Azure migrate Assessment server e per eseguire la [migrazione senza agenti](server-migrate-overview.md) di macchine virtuali VMware con Azure migrate migrazione del server. Questa appliance individua i computer e invia i metadati e i dati sulle prestazioni a Azure Migrate.<br/><br/> Durante la registrazione, i provider di registrazione (Microsoft. OffAzure, Microsoft. migrate e Microsoft. Vault) sono registrati con la sottoscrizione selezionata nell'appliance, in modo che la sottoscrizione funzioni con il provider di risorse. Per eseguire la registrazione, è necessario l'accesso come collaboratore o proprietario per la sottoscrizione.<br/><br/> **VMware**: durante il caricamento, Azure migrate crea due app Azure Active Directory (Azure ad). La prima app comunica tra gli agenti Appliance e il servizio Azure Migrate. L'app non ha le autorizzazioni necessarie per effettuare chiamate di gestione risorse di Azure o avere l'accesso RBAC per le risorse. La seconda app accede a un Azure Key Vault creato nella sottoscrizione utente per la migrazione VMware senza agente. Nella migrazione senza agenti Azure Migrate crea una Key Vault per gestire le chiavi di accesso all'account di archiviazione della replica nella sottoscrizione. Ha accesso RBAC sul Azure Key Vault (nel tenant del cliente) quando l'individuazione viene avviata dall'appliance.<br/><br/> **Hyper-V**-durante l'onboarding. Azure Migrate crea un'app Azure AD. L'app comunica tra gli agenti Appliance e il servizio Azure Migrate. L'app non ha le autorizzazioni necessarie per effettuare chiamate di gestione risorse di Azure o avere l'accesso RBAC per le risorse. | Configurare per [VMware](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance), [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)o [server fisici](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance).
Creare un insieme di credenziali delle chiavi per la migrazione senza agenti VMware | Per eseguire la migrazione di macchine virtuali VMware con la migrazione senza agente Azure Migrate server, Azure Migrate crea un Key Vault per gestire le chiavi di accesso all'account di archiviazione della replica nella sottoscrizione. Per creare l'insieme di credenziali, è necessario impostare le autorizzazioni (proprietario, collaboratore e amministratore accesso utenti) nel gruppo di risorse in cui risiede il progetto Azure Migrate. | [Impostare](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) le autorizzazioni.

## <a name="supported-geographies"></a>Aree geografiche supportate

È possibile creare un progetto Azure Migrate in diverse aree geografiche. Sebbene sia possibile creare progetti solo in queste aree geografiche, è possibile valutare o migrare i computer per altri percorsi di destinazione. L'area geografica del progetto viene usata solo per archiviare i metadati individuati.

**Area geografica** | **Posizione di archiviazione dei metadati**
--- | ---
Azure Government | US Gov Virginia
Asia Pacifico | Asia orientale o Asia sud-orientale
Australia | Australia orientale o Australia sudorientale
Brasile | Brasile meridionale
Canada | Canada centrale o Canada orientale
Europa | Europa settentrionale o Europa occidentale
Francia | Francia centrale
India | India centrale o India meridionale
Giappone |  Giappone orientale o Giappone occidentale
Corea del Sud | Corea centrale o Corea del sud
Regno Unito | Regno Unito meridionale o Regno Unito occidentale
Stati Uniti | Stati Uniti centrali o Stati Uniti occidentali 2


 > [!NOTE]
 > Il supporto per Azure per enti pubblici è attualmente disponibile solo per la [versione precedente](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) di Azure migrate.



## <a name="vmware-assessment-and-migration"></a>Valutazione e migrazione VMware

[Esaminare](migrate-support-matrix-vmware.md) la matrice di supporto per la migrazione del server e la valutazione di Azure migrate server per le macchine virtuali VMware.

## <a name="hyper-v-assessment-and-migration"></a>Valutazione e migrazione di Hyper-V

[Esaminare](migrate-support-matrix-hyper-v.md) la matrice di supporto per la migrazione del server e la valutazione di Azure migrate server per le macchine virtuali Hyper-V.



## <a name="azure-migrate-versions"></a>Versioni di Azure Migrate

Sono disponibili due versioni del servizio Azure Migrate:

- **Versione corrente**: con questa versione è possibile creare nuovi progetti di Azure migrate, individuare le valutazioni locali e orchestrare valutazioni e migrazioni. [Altre informazioni](whats-new.md#release-version-july-2019).
- **Versione precedente**: per i clienti che usano la versione precedente di Azure migrate (è supportata solo la valutazione delle macchine virtuali VMware locali), è ora necessario usare la versione corrente. Nella versione precedente non è possibile creare nuovi progetti di Azure Migrate o eseguire nuove individuazioni.

## <a name="next-steps"></a>Passaggi successivi

- [Valutare le macchine virtuali VMware](tutorial-assess-vmware.md) per la migrazione.
- [Valutare le VM Hyper-V](tutorial-assess-hyper-v.md) per la migrazione.

