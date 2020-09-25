---
title: Architettura dell'appliance di Azure Migrate
description: Offre una panoramica dell'appliance di Azure Migrate usata per la valutazione e la migrazione del server.
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 028e0e5b0bbf83c08b5f9cd6d24d7914513ae89a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322258"
---
# <a name="azure-migrate-appliance-architecture"></a>Architettura dell'appliance di Azure Migrate

Questo articolo descrive l'architettura e i processi di Azure Migrate Appliance. Il Azure Migrate Appliance è un'appliance semplice distribuita in locale per individuare le macchine virtuali e i server fisici per la migrazione ad Azure. 

## <a name="deployment-scenarios"></a>Scenari di distribuzione

L'appliance di Azure Migrate viene usata negli scenari seguenti.

**Scenario** | **Strumento** | **Usata per** 
--- | --- | ---
**Valutazione della macchina virtuale VMware** | Azure Migrate: Valutazione server | Individuare le macchine virtuali VMware.<br/><br/> Individuare le app e le dipendenze del computer.<br/><br/> Raccogliere i metadati del computer e le prestazioni e inviarli ad Azure.
**Migrazione di macchine virtuali VMware (senza agenti)** | Azure Migrate: Migrazione del server | Individuare le macchine virtuali VMware<br/><br/>  Eseguire la replica di macchine virtuali VMware con [migrazione senza agenti](server-migrate-overview.md).
**Valutazione della macchina virtuale Hyper-V** | Azure Migrate: Valutazione server | Individuare le VM Hyper-V.<br/><br/> Raccogliere i metadati del computer e le prestazioni e inviarli ad Azure.
**Computer fisico** |  Azure Migrate: Valutazione server |  Individuare i server fisici.<br/><br/> Raccogliere i metadati del computer e le prestazioni e inviarli ad Azure.

## <a name="appliance-components"></a>Componenti dell'appliance

Il dispositivo dispone di un numero di componenti.

- **App di gestione**: si tratta di un'app Web per l'input dell'utente durante la distribuzione dell'appliance. È usata durante la valutazione dei computer per la migrazione ad Azure.
- **Agente di individuazione**: l'agente raccoglie i dati di configurazione del computer. È usata durante la valutazione dei computer per la migrazione ad Azure. 
- **Agente di raccolta**: l'agente raccoglie i dati sulle prestazioni. È usata durante la valutazione dei computer per la migrazione ad Azure.
- **Agente DRA**: organizza la replica della macchina virtuale e coordina la comunicazione tra computer replicati e Azure. È usato solo quando si esegue la replica di macchine virtuali VMware in Azure con la migrazione senza agente.
- **Gateway**: invia i dati replicati ad Azure. È usato solo quando si esegue la replica di macchine virtuali VMware in Azure con la migrazione senza agente.
- **Servizio di aggiornamento automatico**: aggiorna i componenti dell'appliance, viene eseguito ogni 24 ore.



## <a name="appliance-deployment"></a>Distribuzione dell'appliance

- Il dispositivo Azure Migrate può essere configurato usando un modello per [Hyper-V](how-to-set-up-appliance-hyper-v.md) o [VMware](how-to-set-up-appliance-vmware.md) oppure un programma di installazione di script di PowerShell per [VMware/Hyper-v](deploy-appliance-script.md)e per i [server fisici](how-to-set-up-appliance-physical.md). 
- I requisiti di supporto e i prerequisiti per la distribuzione del dispositivo sono riepilogati nella [matrice di supporto dell'appliance](migrate-appliance.md).


## <a name="appliance-registration"></a>Registrazione dell'appliance

Durante l'installazione dell'appliance si registra l'appliance con Azure Migrate e vengono eseguite le azioni riepilogate nella tabella.

**Azione** | **Dettagli** | **Autorizzazioni**
--- | --- | ---
**Registra provider di origine** | Questi provider di risorse sono registrati nella sottoscrizione scelta durante l'installazione dell'appliance: Microsoft. OffAzure, Microsoft. migrate e Microsoft. insieme di credenziali.<br/><br/> La registrazione di un provider di risorse configura la sottoscrizione per l'utilizzo del provider di risorse. | Per registrare i provider di risorse, è necessario il ruolo di proprietario o collaboratore della sottoscrizione.
**Creare Azure AD app-Communication** | Azure Migrate crea un'app Azure Active Directory (Azure AD) per la comunicazione (autenticazione e autorizzazione) tra gli agenti in esecuzione nell'appliance e i rispettivi servizi in esecuzione in Azure.<br/><br/> Questa app non dispone dei privilegi necessari per effettuare chiamate Azure Resource Manager o l'accesso RBAC a qualsiasi risorsa. | È necessario disporre di [queste autorizzazioni](tutorial-prepare-vmware.md#assign-permissions-to-create-azure-ad-apps) per Azure migrate per creare l'app.
**Creare app Azure AD-Key Vault** | Questa app viene creata solo per la migrazione senza agenti di macchine virtuali VMware in Azure.<br/><br/> Viene usato esclusivamente per accedere all'insieme di credenziali delle chiavi creato nella sottoscrizione dell'utente per la migrazione senza agenti.<br/><br/> Ha accesso RBAC nell'insieme di credenziali delle chiavi di Azure, creato nel tenant del cliente, quando l'individuazione viene avviata dall'appliance. | È necessario disporre di [queste autorizzazioni](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) per Azure migrate per creare l'app.



## <a name="collected-data"></a>Dati raccolti

I dati raccolti dal client per tutti gli scenari di distribuzione sono riepilogati nella [matrice di supporto dell'appliance](migrate-appliance.md).

## <a name="discovery-and-collection-process"></a>Processo di individuazione e raccolta

![Architecture](./media/migrate-appliance-architecture/architecture1.png)

L'appliance comunica con i server vCenter e gli host/cluster Hyper-V usando il processo seguente.

1. **Avvia individuazione**:
    - Quando si avvia l'individuazione nell'appliance Hyper-V, la comunicazione con gli host Hyper-V sulla porta WinRM 5985 (HTTP) viene comunicata.
    - Quando si avvia l'individuazione nell'appliance VMware, per impostazione predefinita comunica con il server vCenter sulla porta TCP 443. Se il server vCenter è in ascolto su una porta diversa, è possibile configurarla nell'app Web Appliance.
2. **Raccogliere i metadati e i dati sulle prestazioni**:
    - L'appliance usa una sessione di Common Information Model (CIM) per raccogliere i dati delle macchine virtuali Hyper-V dall'host Hyper-V sulla porta 5985.
    - Per impostazione predefinita, l'appliance comunica con la porta 443 per raccogliere i dati delle macchine virtuali VMware dal server vCenter.
3. **Invia dati**: il dispositivo invia i dati raccolti a Azure migrate server Assessment e Azure migrate migrazione del server sulla porta SSL 443. Il dispositivo può connettersi ad Azure tramite Internet o tramite ExpressRoute (richiede il peering Microsoft).
    - Per i dati sulle prestazioni, l'appliance raccoglie i dati di utilizzo in tempo reale.
        - I dati sulle prestazioni vengono raccolti ogni 20 secondi per VMware e ogni 30 secondi per Hyper-V per ogni metrica delle prestazioni.
        - Viene eseguito il rollup dei dati raccolti per creare un singolo punto dati per 10 minuti.
        - Il valore di picco dell'utilizzo viene selezionato da tutti i punti dati da 20 a 30 secondi e inviato ad Azure per il calcolo della valutazione.
        - In base al valore percentile specificato nelle proprietà di valutazione (cinquantesimo/90/95/99 °), i punti di dieci minuti sono ordinati in ordine crescente e il valore percentile appropriato viene usato per calcolare la valutazione
    - Per la migrazione del server, l'appliance avvia la raccolta dei dati della macchina virtuale e la replica in Azure.
4. Valutazione **e migrazione**: è ora possibile creare valutazioni dei metadati raccolti dal dispositivo usando Azure migrate Assessment server. Inoltre, è possibile avviare la migrazione di macchine virtuali VMware con Azure Migrate migrazione del server per orchestrare la replica di VM senza agenti.

## <a name="appliance-upgrades"></a>Aggiornamenti dell'appliance

L'appliance viene aggiornata quando vengono aggiornati gli agenti di Azure Migrate in esecuzione nell'appliance. Questa operazione viene eseguita automaticamente perché per impostazione predefinita l'aggiornamento automatico è abilitato nell'appliance. È possibile modificare questa impostazione predefinita per aggiornare manualmente gli agenti.

Per disattivare l'aggiornamento automatico nel registro di sistema, impostare la chiave HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance "AutoUpdate" su 0 (DWORD).


## <a name="next-steps"></a>Passaggi successivi

[Esaminare](migrate-appliance.md) la matrice di supporto dell'appliance.

