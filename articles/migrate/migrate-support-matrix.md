---
title: Matrice di supporto di Azure MigrateAzure Migrate support matrix
description: Viene fornito un riepilogo delle impostazioni di supporto e delle limitazioni per il servizio Azure Migrate.
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: raynew
ms.openlocfilehash: bf719f9179384ec3dca99d2429f569ef209b5daa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127710"
---
# <a name="azure-migrate-support-matrix"></a>Matrice di supporto di Azure MigrateAzure Migrate support matrix

È possibile usare il servizio Azure Migrate per valutare ed eseguire la migrazione delle macchine nel cloud di Microsoft Azure.You can use the [Azure Migrate service](migrate-overview.md) to assess and migrate machines to the Microsoft Azure cloud. Questo articolo riepiloga le impostazioni e le limitazioni generali del supporto per gli scenari e le distribuzioni di Azure Migrate.This article summarizes general support settings and limitations for Azure Migrate scenarios and deployments.

## <a name="supported-assessmentmigration-scenarios"></a>Scenari di valutazione/migrazione supportati

Nella tabella sono riepilogati gli scenari di individuazione, valutazione e migrazione supportati.

**Distribuzione** | **Dettagli** 
--- | --- 
**Individuazione specifica dell'app** | È possibile individuare app, ruoli e funzionalità in esecuzione nelle macchine virtuali VMware.You can discover apps, roles, and features running on VMware VMs. Attualmente questa funzionalità è limitata solo all'individuazione. La valutazione è attualmente a livello di macchina. Non offriamo ancora una valutazione specifica di app, ruoli o funzionalità. 
**Valutazione locale** | Valutare i carichi di lavoro e i dati locali in esecuzione nelle macchine virtuali VMware, nelle macchine virtuali Hyper-V e nei server fisici. Valutare l'utilizzo di Azure Migrate Server Assessment e Microsoft Data Migration Assistant (DMA), nonché di altri strumenti e offerte ISV.
**Migrazione locale in AzureOn-premises migration to Azure** | Eseguire la migrazione di carichi di lavoro e dati in esecuzione su server fisici, macchine virtuali VMware, macchine virtuali Hyper-V, server fisici e MACCHINE virtuali basate su cloud in Azure.Migrate workloads and data running on physical servers, VMware VMs, Hyper-V VMs, physical servers, and cloud-based VMS to Azure. Eseguire la migrazione usando la valutazione del server di azure migrate e il servizio DMS (Azure Database Migration Service) nonché altri strumenti e offerte ISV.


## <a name="supported-tools"></a>Strumenti supportati

Il supporto degli strumenti specifici è riepilogato nella tabella.

**Strumento** | **Valutare** | **Migrare** 
--- | --- | ---
Valutazione server di Azure Migrate | Valutare [le macchine virtuali VMware,](tutorial-prepare-vmware.md)le macchine virtuali [Hyper-V](tutorial-prepare-hyper-v.md)e i [server fisici.](tutorial-prepare-physical.md) |  Non disponibile (NA)
Migrazione server di Azure Migrate | ND | Eseguire la migrazione di [macchine virtuali VMware,](tutorial-migrate-vmware.md) [macchine virtuali Hyper-V](tutorial-migrate-hyper-v.md)e [server fisici.](tutorial-migrate-physical-virtual-machines.md)
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | ND | Eseguire la migrazione di macchine virtuali VMware, macchine virtuali Hyper-V, server fisici, carichi di lavoro cloud pubblico. 
[Cloudamizzare](https://www.cloudamize.com/platform#tab-0)| Valutare vMware, macchine virtuali Hyper-V, server fisici, carichi di lavoro cloud pubblico. | ND
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Valutare ed eseguire la migrazione di macchine virtuali VMware, macchine virtuali Hyper-V, server fisici, carichi di lavoro cloud pubblico. |  Eseguire la migrazione di macchine virtuali VMware, macchine virtuali Hyper-V, server fisici, carichi di lavoro cloud pubblico.
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Valutare vMware, macchine virtuali Hyper-V, server fisici, carichi di lavoro cloud pubblico.| ND
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Valutare i database di SQL Server locali. | ND
[Servizio Migrazione del database](https://docs.microsoft.com/azure/dms/dms-overview) | ND | Eseguire la migrazione di SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Valutare l'infrastruttura di desktop virtuale (VDI) | ND
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | Valutare le macchine virtuali VMWare, le macchine virtuali Hyper-V, le macchine virtuali Xen, le macchine fisiche, le workstation (inclusa l'infrastruttura VDI), i carichi di lavoro del cloud pubblico | ND
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | ND | Eseguire la migrazione di macchine virtuali VMWare, macchine virtuali Hyper-V, macchine virtuali Xen, macchine virtuali KVM, macchine fisiche, carichi di lavoro cloud pubblicoMigrate VMWare VMs, Hyper-VMs, Xen VMs, KVM VMs, physical machines, public cloud workloads 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Valutare vMware, macchine virtuali Hyper-V, server fisici, carichi di lavoro cloud pubblico. | ND
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Valutare vMware, macchine virtuali Hyper-V, server fisici, carichi di lavoro cloud pubblico e database di SQL Server. | ND
[Assistente migrazione Webapp](https://appmigration.microsoft.com/) | Valutare le app Web | Eseguire la migrazione di app Web.


## <a name="azure-migrate-projects"></a>Progetti di azure migrateAzure Migrate projects

**Supporto** | **Dettagli**
--- | ---
Subscription | È possibile avere più progetti di Azure Migrate in una sottoscrizione.
Autorizzazioni di Azure | Per creare un progetto di Azure Migrate, sono necessarie autorizzazioni di collaboratore o proprietario.
VM VMware  | Valutare fino a 35.000 macchine virtuali VMware in un singolo progetto.
VM Hyper-V    | Valutare fino a 35.000 macchine virtuali Hyper-V in un singolo progetto.

Un progetto può includere sia macchine virtuali VMware che macchine virtuali Hyper-V, fino ai limiti di valutazione.

## <a name="azure-permissions"></a>Autorizzazioni di Azure

Affinché Azure Migrate funzioni con Azure, queste autorizzazioni sono necessarie prima di iniziare a valutare e migrare le macchine.

**Attività** | **Autorizzazioni** | **Dettagli**
--- | --- | ---
Creare un progetto di Azure Migrate | L'account di Azure necessita di autorizzazioni per creare un progetto. | Configurazione per [server VMware,](tutorial-prepare-vmware.md#assign-permissions-to-create-project) [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)o [fisici.](tutorial-prepare-physical.md#assign-permissions-to-create-project)
Registrare l'appliance Azure Migrate| Azure Migrate usa [un'appliance di Azure Migrate](migrate-appliance.md) leggera per valutare le macchine con Azure Migrate Server Assessment ed eseguire la migrazione senza [agenti](server-migrate-overview.md) di macchine virtuali VMware con Azure Migrate Server Migration. Questa appliance individua i computer e invia metadati e dati sulle prestazioni ad Azure Migrate.This appliance discovers machines, and sends metadata and performance data to Azure Migrate.<br/><br/> Durante la registrazione, i provider di registrazione (Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault) vengono registrati con la sottoscrizione scelta nell'appliance, in modo che la sottoscrizione funzioni con il provider di risorse. Per effettuare la registrazione, è necessario l'accesso Collaboratore o Proprietario alla sottoscrizione.<br/><br/> **VMware**-Durante l'onboarding, Azure Migrate crea due app di Azure Active Directory (Azure AD). La prima app comunica tra gli agenti dell'appliance e il servizio Di schiene di Azure.The first app communicates between the appliance agents and the Azure Migrate service. L'app non dispone delle autorizzazioni per effettuare chiamate di gestione delle risorse di Azure o dispone dell'accesso RBAC per le risorse. La seconda app accede a un insieme di credenziali delle chiavi di Azure creato nella sottoscrizione utente solo per la migrazione di VMware senza agente. Nella migrazione senza agente, Azure Migrate crea un insieme di credenziali delle chiavi per gestire le chiavi di accesso all'account di archiviazione della replica nella sottoscrizione. Ha accesso RBAC all'insieme di chiavi di Azure (nel tenant del cliente) quando l'individuazione viene avviata dall'appliance.<br/><br/> **Hyper-V**-Durante l'onboarding. Azure Migrate crea un'app Azure AD. L'app comunica tra gli agenti dell'appliance e il servizio Di schiene di Azure.The app communicates between the appliance agents and the Azure Migrate service. L'app non dispone delle autorizzazioni per effettuare chiamate di gestione delle risorse di Azure o dispone dell'accesso RBAC per le risorse. | Configurazione per [server VMware,](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)o [fisici.](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance)
Creare un insieme di credenziali delle chiavi per la migrazione senza agente DiMwareCreate a key vault for VMware agentless migration | Per eseguire la migrazione delle macchine virtuali VMware con la migrazione del server di Azure Migrate senza agente, Azure Migrate crea un insieme di credenziali delle chiavi per gestire le chiavi di accesso all'account di archiviazione della replica nella sottoscrizione. Per creare l'insieme di credenziali, impostare le autorizzazioni (Proprietario o Collaboratore e Amministratore di accesso utente) per il gruppo di risorse in cui risiede il progetto Azure Migrate.To create the vault, you set permissions (Owner, or Contributor and User Access Administrator) on the resource group in which the Azure Migrate project resides. | [Impostare le](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) autorizzazioni.

## <a name="supported-geographies"></a>Geografie supportate

È possibile creare un progetto di Azure Migrate in diverse aree geografiche. Sebbene sia possibile creare progetti solo in queste aree geografiche, è possibile valutare o eseguire la migrazione dei computer per altre posizioni di destinazione. L'area geografica del progetto viene usata solo per archiviare i metadati individuati.

**Geografia** | **Posizione di archiviazione dei metadati**
--- | ---
Azure Government | US Gov Virginia
Asia Pacifico | Asia orientale o Asia sud-orientale
Australia | Australia Est o Australia Sud-Est
Brasile | Brasile meridionale
Canada | Canada Centrale o Canada Orientale
Europa | Europa settentrionale o Europa occidentale
Francia | Francia centrale
India | India centrale o India meridionale
Giappone |  Giappone Est o Giappone Ovest
Corea del Sud | Corea Centrale o Corea Del Sud
Regno Unito | Regno Unito meridionale o Regno Unito occidentale
Stati Uniti | Stati Uniti centrali o Stati Uniti occidentali 2


 > [!NOTE]
 > Il supporto per Azure per enti pubblici è attualmente disponibile solo per la versione precedente di Azure Migrate.Support for Azure Government is currently only available for the [older version](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) of Azure Migrate.



## <a name="vmware-assessment-and-migration"></a>Valutazione e migrazione di VMware

[Esaminare](migrate-support-matrix-vmware.md) la matrice di supporto di Azure Migrate Server Assessment and Server Migration per le macchine virtuali VMware.Review the Azure Migrate Server Assessment and Server Migration support matrix for VMware VMs.

## <a name="hyper-v-assessment-and-migration"></a>Valutazione e migrazione di Hyper-V

[Esaminare](migrate-support-matrix-hyper-v.md) la matrice di supporto per la valutazione del server di Azure Migrate e la migrazione del server per le macchine virtuali Hyper-V.Review the Azure Migrate Server Assessment and Server Migration support matrix for Hyper-V VMs.



## <a name="azure-migrate-versions"></a>Versioni di Azure Migrate

Sono disponibili due versioni del servizio Azure Migrate:

- **Versione corrente:** usando questa versione è possibile creare nuovi progetti di Azure Migrate, individuare valutazioni locali e orchestrare valutazioni e migrazioni. [Scopri di più](whats-new.md).
- **Versione precedente:** per i clienti che usano la versione precedente di Azure Migrate (era supportata solo la valutazione delle macchine virtuali VMware locali), ora dovresti usare la versione corrente. Nella versione precedente non è possibile creare nuovi progetti di Azure Migrate o eseguire nuove individuazioni.

## <a name="next-steps"></a>Passaggi successivi

- [Valutare le macchine virtuali VMware](tutorial-assess-vmware.md) per la migrazione.
- [Valutare le macchine virtuali Hyper-V](tutorial-assess-hyper-v.md) per la migrazione.

