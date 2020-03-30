---
title: Impostare l'ambito per l'individuazione delle macchine virtuali VMware con Azure MigrateSet the scope for VMware VM discovery with Azure Migrate
description: Descrive come impostare l'ambito di individuazione per la valutazione e la migrazione delle macchine virtuali VMware con Azure Migrate.Describes how to set the discovery scope for VMware VM assessment and migration with Azure Migrate.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337636"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Impostare l'ambito di individuazione per le macchine virtuali VMwareSet discovery scope for VMware VMs

In questo articolo viene descritto come limitare l'ambito di individuazione per le macchine virtuali VMware individuate [dall'appliance Azure Migrate.](migrate-appliance-architecture.md)

L'appliance Di azure Migrate individua le macchine virtuali VMware locali quando:The Azure Migrate appliance discovers on-premises VMware VMs when you: 

- Usare lo strumento Azure Migrate:Server Assessment per valutare le macchine virtuali VMware per la migrazione ad Azure.Use the [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) tool to assess VMware VMs for migration to Azure.
- Usare lo strumento Migrazione server di Azure per la migrazione senza agente di macchine virtuali VMware in Azure.Use the [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) tool for [agentless](server-migrate-overview.md) Migration of VMware VMs to Azure.

## <a name="set-discovery-scope"></a>Impostare l'ambito di individuazione


Dopo aver configurato la valutazione o la migrazione dell'appliance Azure Migrate per le macchine virtuali VMware, l'appliance avvia l'individuazione delle macchine virtuali e l'invio dei metadati della macchina virtuale in Azure.After you set up the Azure Migrate appliance for VMware VMs assessment or migration, the appliance starts discovering VMs, and sending VM metadata to Azure. Prima di connettere l'appliance al server vCenter per l'individuazione, è possibile impostare l'ambito di individuazione per limitare l'individuazione ai data center di vCenter Server, ai cluster, a una cartella di cluster, agli host, a una cartella di host o a singole macchine virtuali.

Per impostare l'ambito, assegnare le autorizzazioni per l'account usato da Azure Migrate per accedere al server vCenter.

## <a name="create-a-vcenter-user-account"></a>Creare un account utente vCenter

Se non è già stato configurato un account utente vCenter utilizzato dall'appliance Azure Migrate per individuare, valutare ed eseguire la migrazione delle macchine virtuali VMware, eseguire prima questa operazione.

1.    Accedere al client Web vSphere come amministratore del server vCenter.
2.    Selezionare **Amministrazione** > **utenti e gruppi SSO**e fare clic sulla scheda **Utenti.**
3.    Selezionare l'icona **New User** (Nuovo utente).
4.    Immettere le nuove informazioni utente > **OK**.

Le autorizzazioni dell'account dipendono da ciò che si sta distribuendo.

**Funzionalità** | **Autorizzazioni dell'account**
--- | ---
[Valutare](tutorial-assess-vmware.md)| L'account deve disporre dell'accesso in sola lettura.
[Individuare app, ruoli e funzionalità](how-to-discover-applications.md) | L'account deve disporre dell'accesso in sola lettura, con privilegi abilitati per le macchine virtuali > Le operazioni guest.
[Analizzare le dipendenze (senza agente)Analyze dependencies (agentless)](how-to-create-group-machine-dependencies-agentless.md) | L'account deve disporre dell'accesso in sola lettura, con privilegi abilitati per le macchine virtuali > Le operazioni guest.
[Eseguire la migrazione (senza agente)](tutorial-migrate-vmware.md) | È necessario un ruolo a cui sono assegnate le autorizzazioni appropriate.<br/><br/> Per creare un ruolo, accedere a vSphere Web Client come amministratore del server vCenter. Selezionare l'istanza di vCenter Server > **Crea ruolo**. Specificare un nome di ruolo, ad esempio <em>Azure_Migrate</em>, e assegnare le [autorizzazioni necessarie](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) al ruolo.


## <a name="assign-permissions-on-vcenter-objects"></a>Assegnare le autorizzazioni per gli oggetti vCenter

È possibile assegnare autorizzazioni agli oggetti di magazzino utilizzando uno dei due metodi seguenti:

- Assegnare un ruolo con le autorizzazioni necessarie all'account in uso per tutti gli oggetti padre che ospitano le macchine virtuali che si desidera individuare/migrare.
- In alternativa, è possibile assegnare il ruolo e l'account utente a livello di data center e propagarli agli oggetti figlio. Assegnare quindi all'account un ruolo **No access,** per ogni oggetto che non si desidera individuare/migrare. Non è consigliabile questo approccio poiché è ingombrante e potrebbe esporre i controlli di accesso, perché a ogni nuovo oggetto figlio viene concesso automaticamente l'accesso ereditato dall'elemento padre.

Per assegnare un ruolo all'account utilizzato per tutti gli oggetti rilevanti, eseguire le operazioni seguenti:

- **Per la valutazione:** per la valutazione delle macchine virtuali, applicare il ruolo **di sola lettura** all'account utente vCenter per tutti gli oggetti padre che ospitano le macchine virtuali che ospitano le macchine virtuali che si desidera individuare. Oggetti padre inclusi: host, cartella di host, cluster e cartella di cluster nella gerarchia, fino al data center. Propagano queste autorizzazioni agli oggetti figlio nella gerarchia.

    ![Assegnare le autorizzazioni](./media/tutorial-assess-vmware/assign-perms.png)

- Per la **migrazione senza agente:** per la migrazione senza agente, applicare un ruolo definito dall'utente con [le autorizzazioni necessarie](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) all'account utente, per tutti gli oggetti padre che ospitano le macchine virtuali che si desidera individuare. È possibile assegnare un nome al ruolo <em>Azure_Migrate</em>.

### <a name="scope-support"></a>Supporto dell'ambito

Attualmente, lo strumento di valutazione del server non è in grado di individuare le macchine virtuali se l'account vCenter dispone dell'accesso concesso a livello di cartella VM vCenter. Sono supportate le cartelle di host e cluster.

Se si vuole definire l'ambito dell'individuazione in base alle cartelle delle macchine virtuali, completare la procedura successiva per assicurarsi che all'account vCenter sia assegnato l'accesso in sola lettura a livello di macchina virtuale.

1. Assegnare autorizzazioni di sola lettura in tutte le macchine virtuali nelle cartelle delle macchine virtuali che si desidera definire nell'ambito per l'individuazione.
2. Concedere l'accesso in sola lettura a tutti gli oggetti padre che ospitano le macchine virtuali.
    - Sono inclusi tutti gli oggetti padre (host, cartella di host, cluster, cartella di cluster) nella gerarchia fino al data center.
    - Non è necessario propagare le autorizzazioni a tutti gli oggetti figlio.
3. Usare le credenziali per l'individuazione selezionando il data center come **Ambito raccolta**. La configurazione del controllo degli accessi in base al ruolo garantisce che l'account utente vCenter corrispondente abbia accesso solo alle macchine virtuali specifiche del tenant.


## <a name="next-steps"></a>Passaggi successivi

[Impostare l'appliance](how-to-set-up-appliance-vmware.md) e [avviare l'individuazione continua.](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential)
