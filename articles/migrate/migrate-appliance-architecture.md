---
title: Architettura dell'appliance di Azure MigrateAzure Migrate appliance architecture
description: Fornisce una panoramica dell'appliance di Azure Migrate usata nella valutazione e nella migrazione del server.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: d55d123bb056b46b5e78dd8ac836eeaf9b42fe70
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389019"
---
# <a name="azure-migrate-appliance-architecture"></a>Architettura dell'appliance di Azure MigrateAzure Migrate appliance architecture

Questo articolo descrive l'architettura e i processi dell'appliance di Azure Migrate.This article describes the Azure Migrate appliance architecture and processes. L'appliance Azure Migrate è un'appliance leggera distribuita in locale per individuare macchine virtuali e server fisici per la migrazione ad Azure.The Azure Migrate appliance is a lightweight appliance that's deployed on premises, to discover VMs and physical servers for migration to Azure. 

## <a name="deployment-scenarios"></a>Scenari di distribuzione

L'appliance di Azure Migrate viene usata negli scenari seguenti.

**Scenario** | **Strumento** | **Utilizzo** 
--- | --- | ---
**Valutazione delle macchine virtuali VMware** | Migrazione di Azure:valutazione del serverAzure Migrate:Server Assessment | Individuare le macchine virtuali VMware.<br/><br/> Scopri le app e le dipendenze del computer.<br/><br/> Raccogliere i metadati del computer e i metadati delle prestazioni e inviarli ad Azure.Collect machine metadata and performance metadata and send to Azure.
**Migrazione vm VMware (senza agente)** | Migrazione di Azure:migrazione serverAzure Migrate:Server Migration | Scopri le macchine virtuali VMware<br/><br/>  Replicare le macchine virtuali VMware con la [migrazione senza agente.](server-migrate-overview.md)
**Valutazione della macchina virtuale Hyper-V** | Migrazione di Azure:valutazione del serverAzure Migrate:Server Assessment | Individuare le macchine virtuali Hyper-V.<br/><br/> Raccogliere i metadati del computer e i metadati delle prestazioni e inviarli ad Azure.Collect machine metadata and performance metadata and send to Azure.
**Macchina fisica** |  Migrazione di Azure:valutazione del serverAzure Migrate:Server Assessment |  Scopri i server fisici.<br/><br/> Raccogliere i metadati del computer e i metadati delle prestazioni e inviarli ad Azure.Collect machine metadata and performance metadata and send to Azure.

## <a name="appliance-components"></a>Componenti dell'apparecchio

L'apparecchio ha una serie di componenti.

- **App di gestione:** si tratta di un'app Web per l'input dell'utente durante la distribuzione dell'appliance. Utilizzato durante la valutazione dei computer per la migrazione ad Azure.Used when assessing machines for migration to Azure.
- **Agente di individuazione:** l'agente raccoglie i dati di configurazione del computer. Utilizzato durante la valutazione dei computer per la migrazione ad Azure.Used when assessing machines for migration to Azure. 
- **Agente di valutazione:** l'agente raccoglie i dati sulle prestazioni. Utilizzato durante la valutazione dei computer per la migrazione ad Azure.Used when assessing machines for migration to Azure.
- **Agente DRA:** orchestra la replica delle macchine virtuali e coordina la comunicazione tra i computer replicati e Azure.DRA agent : Orchestrates VM replication, and coordinates communication between replicated machines and Azure. Utilizzato solo durante la replica di macchine virtuali VMware in Azure usando la migrazione senza agente.
- **Gateway:** invia i dati replicati in Azure.Gateway : Sends replicated data to Azure. Utilizzato solo durante la replica di macchine virtuali VMware in Azure usando la migrazione senza agente.
- **Servizio di aggiornamento automatico**: Aggiorna i componenti dell'appliance (eseguiti ogni 24 ore).



## <a name="appliance-deployment"></a>Distribuzione dell'appliance

- L'appliance Azure Migrate può essere configurata usando un modello per [Hyper-V](how-to-set-up-appliance-hyper-v.md) o [VMware](how-to-set-up-appliance-vmware.md) o un programma di installazione di script di PowerShell per [VMware/Hyper-V](deploy-appliance-script.md)e per [i server fisici.](how-to-set-up-appliance-physical.md) 
- I requisiti di supporto dell'appliance e i prerequisiti di distribuzione sono riepilogati nella matrice di [supporto dell'appliance.](migrate-appliance.md)


## <a name="appliance-registration"></a>Registrazione dell'apparecchio

Durante l'installazione dell'appliance, si registra l'appliance con Azure Migrate e si verificano le azioni riepilogate nella tabella.

**Azione** | **Dettagli** | **Autorizzazioni**
--- | --- | ---
**Registrare i provider di origineRegister source providers** | Questi provider di risorse vengono registrati nella sottoscrizione scelta durante l'installazione dell'appliance: Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault.These resources providers are registered in the subscription you choose during appliance setup: Microsoft.OffAzure, Microsoft.Migrate and Microsoft.KeyVault.<br/><br/> La registrazione di un provider di risorse configura la sottoscrizione per l'utilizzo del provider di risorse. | Per registrare i provider di risorse, è necessario il ruolo di proprietario o collaboratore della sottoscrizione.
**Creare la comunicazione delle app di Azure ADCreate Azure AD app-communication** | Azure Migrate creates an Azure Active Directory (Azure AD) app for communication (authentication and authorization) between the agents running on the appliance, and their respective services running on Azure.<br/><br/> Questa app non dispone dei privilegi per effettuare chiamate di Azure Resource Manager o l'accesso RBAC su qualsiasi risorsa. | Per creare l'app, sono necessarie queste autorizzazioni per Azure Migrate.You need [these permissions](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) for Azure Migrate to create the app.
**Creare l'insieme di credenziali delle chiavi delle app di Azure ADCreate Azure AD apps-Key vault** | Questa app viene creata solo per la migrazione senza agente di macchine virtuali VMware in Azure.This app is created only for agentless migration of VMware VMs to Azure.<br/><br/> Viene utilizzato esclusivamente per accedere all'insieme di credenziali delle chiavi creato nella sottoscrizione dell'utente per la migrazione senza agente.<br/><br/> Ha accesso RBAC all'insieme di credenziali delle chiavi di Azure (creato nel tenant del cliente) quando l'individuazione viene avviata dall'appliance. | Per creare l'app, sono necessarie queste autorizzazioni per Azure Migrate.You need [these permissions](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) for Azure Migrate to create the app.



## <a name="collected-data"></a>Dati raccolti

I dati raccolti dal client per tutti gli scenari di distribuzione sono riepilogati nella matrice di [supporto dell'appliance.](migrate-appliance.md)

## <a name="discovery-and-collection-process"></a>Processo di individuazione e raccolta

![Architecture](./media/migrate-appliance-architecture/architecture.png)

L'appliance comunica con i server vCenter e gli host/cluster Hyper-V utilizzando il processo seguente.

1. **Avvia individuazione**:
    - Quando si avvia l'individuazione nell'appliance Hyper-V, comunica con gli host Hyper-V sulle porte di Gestione remota Windows 5985 (HTTP) e 5986 (HTTPS).
    - Quando si avvia l'individuazione nell'appliance VMware, questa comunica con il server vCenter sulla porta TCP 443 per impostazione predefinita. Se il server vCenter è in ascolto su una porta diversa, è possibile configurarlo nell'app Web dell'appliance.
2. **Raccogliere metadati e dati sulle prestazioni**:
    - L'appliance utilizza una sessione CIM (Common Information Model) per raccogliere i dati della macchina virtuale Hyper-V dall'host Hyper-V sulle porte 5985 e 5986.
    - L'appliance comunica con la porta 443 per impostazione predefinita, per raccogliere i dati delle macchine virtuali VMware dal server vCenter.
3. **Invia dati:** l'appliance invia i dati raccolti a Valutazione server di migrazione di Azure e Migrazione del server di migrazione di Azure tramite la porta SSL 443.Send data : The appliance sends the collected data to Azure Migrate Server Assessment and Azure Migrate Server Migration over SSL port 443. L'appliance può connettersi ad Azure tramite Internet oppure è possibile usare ExpressRoute con il peering pubblico/Microsoft.The appliance can connect to Azure via the internet, or you can use ExpressRoute with public/Microsoft peering.
    - Per i dati sulle prestazioni, l'appliance raccoglie i dati di utilizzo in tempo reale.
        - I dati sulle prestazioni vengono raccolti ogni 20 secondi per VMware e ogni 30 secondi per Hyper-V, per ogni metrica delle prestazioni.
        - I dati raccolti vengono sottoposti a rollup per creare un singolo punto dati per 10 minuti.
        - Il valore di utilizzo massimo viene selezionato tra tutti i punti dati di 20/30 secondi e inviato ad Azure per il calcolo della valutazione.
        - In base al valore percentile specificato nelle proprietà di valutazione (50th/90th/95th/99th), i punti di dieci minuti vengono ordinati in ordine crescente e viene utilizzato il valore percentile appropriato per calcolare la valutazione
    - Per la migrazione del server, l'appliance inizia a raccogliere i dati della macchina virtuale e li replica in Azure.For Server Migration, the appliance starts collecting VM data, and replicates it to Azure.
4. **Valutazione ed migrazione:** è ora possibile creare valutazioni dai metadati raccolti dall'appliance usando valutazione del server di Azure Migrate.Assess and migrate : You can now create assessments from the metadata collected by the appliance using Azure Migrate Server Assessment. Inoltre, è anche possibile avviare la migrazione delle macchine virtuali VMware usando Migrazione server di Azure Migrate per orchestrare la replica delle macchine virtuali senza agente.





## <a name="appliance-upgrades"></a>Aggiornamenti dell'appliance

L'appliance viene aggiornata man mano che gli agenti di Azure Migrate in esecuzione nell'appliance vengono aggiornati. Ciò avviene automaticamente perché l'aggiornamento automatico è abilitato sull'appliance per impostazione predefinita. È possibile modificare questa impostazione predefinita per aggiornare manualmente gli agenti.

Disattivare l'aggiornamento automatico nel Registro di sistema impostando la chiave di HKEY_LOCAL_MACHINE SOFTWARE Microsoft AzureAppliance "AutoUpdate" su 0 (DWORD).

 

## <a name="next-steps"></a>Passaggi successivi

[Esaminare](migrate-appliance.md) la matrice di supporto dell'appliance.

