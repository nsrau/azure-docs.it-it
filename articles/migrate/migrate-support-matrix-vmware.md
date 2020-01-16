---
title: Supporto per la valutazione VMware in Azure Migrate
description: Informazioni sul supporto per la valutazione di VMware in Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 2a9c5504d99f439723a250b619b9f9b660ea9c59
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029011"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matrice di supporto per VMware Assessment 

Questo articolo riepiloga le impostazioni di supporto e le limitazioni per la valutazione delle macchine virtuali VMware con [Azure migrate: server Assessment](migrate-services-overview.md#azure-migrate-server-migration-tool). Per informazioni sulla migrazione di macchine virtuali VMware in Azure, vedere la matrice di [supporto](migrate-support-matrix-vmware-migration.md)per la migrazione.

## <a name="overview"></a>Overview

Per valutare i computer locali per la migrazione ad Azure con questo articolo, è necessario aggiungere lo strumento Azure Migrate: server Assessment a un progetto Azure Migrate. Si distribuisce il [dispositivo Azure migrate](migrate-appliance.md). L'appliance individua continuamente i computer locali e invia i dati di configurazione e delle prestazioni in Azure. Dopo l'individuazione del computer, si raccolgono i computer individuati in gruppi ed è stata eseguita una valutazione per un gruppo.


## <a name="limitations"></a>Limitazioni

**Supporto** | **Dettagli**
--- | ---
**Limiti di valutazione**| Individuare e valutare fino a 35.000 macchine virtuali VMware in un singolo [progetto](migrate-support-matrix.md#azure-migrate-projects).
**Limiti del progetto** | È possibile creare più progetti in una sottoscrizione di Azure. Un progetto può includere VM VMware, macchine virtuali Hyper-V e server fisici, fino ai limiti di valutazione.
**Individuazione** | L'appliance Azure Migrate è in grado di rilevare fino a 10.000 VM VMware in una server vCenter.
**Valutazione** | È possibile aggiungere fino a 35.000 computer in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 VM in un'unica valutazione.

[Altre](concepts-assessment-calculation.md) informazioni sulle valutazioni.


## <a name="application-discovery"></a>Individuazione delle applicazioni

Oltre a individuare i computer, Azure Migrate: server assessment può individuare app, ruoli e funzionalità in esecuzione nei computer. L'individuazione dell'inventario delle app consente di identificare e pianificare un percorso di migrazione personalizzato per i carichi di lavoro locali. 

**Supporto** | **Dettagli**
--- | ---
Individuazione | L'individuazione è senza agente, usando le credenziali Guest del computer e accedendo in remoto ai computer usando le chiamate WMI e SSH.
Computer supportati | Macchine virtuali VMware locali.
Sistema operativo del computer | Tutte le versioni di Windows e Linux
Credenziali | Attualmente supporta l'utilizzo di una credenziale per tutti i server Windows e una credenziale per tutti i server Linux.<br/><br/> Si crea un account utente Guest per macchine virtuali Windows e un account utente normale/normale (accesso non sudo) per tutte le macchine virtuali Linux.
Limiti | Per l'individuazione di app è possibile individuare fino a 10000 per ogni appliance. 

## <a name="vmware-requirements"></a>Requisiti di VMware

**VMware** | **Dettagli**
--- | ---
**server vCenter** | I computer che si desidera individuare e valutare devono essere gestiti da server vCenter versione 5,5, 6,0, 6,5 o 6,7.
**Autorizzazioni (valutazione)** | server vCenter account di sola lettura.
**Autorizzazioni (app-Discovery)** | server vCenter account con accesso in sola lettura e privilegi abilitati per le macchine virtuali > operazioni Guest.
**Autorizzazioni (visualizzazione dipendenze)** | Center Server account con accesso in sola lettura e privilegi abilitati per le **macchine virtuali** > **operazioni Guest**.


## <a name="azure-migrate-appliance-requirements"></a>Requisiti del dispositivo Azure Migrate

Azure Migrate usa l' [appliance Azure migrate](migrate-appliance.md) per l'individuazione e la valutazione. L'appliance per VMware viene distribuita usando un modello OVA, importato in server vCenter. 

- Informazioni sui [requisiti degli appliance](migrate-appliance.md#appliance---vmware) per VMware.
- Informazioni sugli [URL](migrate-appliance.md#url-access) a cui l'appliance deve accedere.

## <a name="port-access"></a>Accesso alla porta

**Dispositivo** | **Connection**
--- | ---
Elettrodomestici | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni Desktop remoto al dispositivo.<br/><br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione Appliance usando l'URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Connessioni in uscita sulla porta 443, 5671 e 5672 per inviare i metadati di individuazione e prestazioni a Azure Migrate.
Server vCenter | Connessioni in ingresso sulla porta TCP 443 per consentire all'appliance di raccogliere i metadati di configurazione e prestazioni per le valutazioni. <br/><br/> Per impostazione predefinita, l'appliance si connette a vCenter sulla porta 443. Se il server vCenter è in ascolto su una porta diversa, è possibile modificare la porta quando si configura l'individuazione.

## <a name="dependency-visualization"></a>Visualizzazione delle dipendenze

La visualizzazione delle dipendenze consente di visualizzare le dipendenze tra i computer che si desidera valutare e migrare. In genere si usa il mapping delle dipendenze quando si desidera valutare i computer con livelli di confidenza più elevati. Per le macchine virtuali VMware, la visualizzazione delle dipendenze è supportata come segue:

- **Visualizzazione delle dipendenze senza agente**: questa opzione è attualmente in anteprima. Non richiede l'installazione di agenti nei computer.
    - L'operazione viene eseguita acquisendo i dati di connessione TCP da computer per i quali è abilitata. Dopo l'avvio dell'individuazione delle dipendenze, l'appliance raccoglie i dati dai computer a un intervallo di polling di cinque minuti.
    - Vengono raccolti i dati seguenti:
        - Connessioni TCP
        - Nomi dei processi con connessioni attive
        - Nomi delle applicazioni installate che eseguono i processi precedenti
        - No. delle connessioni rilevate a ogni intervallo di polling
- **Visualizzazione delle dipendenze basate su agente**: per usare la visualizzazione di dipendenza basata su agenti, è necessario scaricare e installare gli agenti seguenti in ogni computer locale che si vuole analizzare.
    - Installare Microsoft Monitoring Agent (MMA) in ogni computer. [Altre](how-to-create-group-machine-dependencies.md#install-the-mma) informazioni su come installare l'agente MMA.
    - Installare Dependency Agent in ogni computer. [Altre](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) informazioni su come installare Dependency Agent.
    - Se vi sono computer senza accesso a Internet, è necessario scaricare e installare il gateway di Log Analytics.


## <a name="next-steps"></a>Passaggi successivi

- [Esaminare](best-practices-assessment.md) le procedure consigliate per la creazione di valutazioni.
- [Prepararsi per VMware](tutorial-prepare-vmware.md) assessment.
