---
title: Impostare l'ambito per l'individuazione di macchine virtuali VMware con Azure Migrate
description: Viene descritto come impostare l'ambito di individuazione per la valutazione e la migrazione di macchine virtuali VMware con Azure Migrate.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337636"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Impostare l'ambito di individuazione per le macchine virtuali VMware

Questo articolo descrive come limitare l'ambito di individuazione per le macchine virtuali VMware individuate dal [dispositivo Azure migrate](migrate-appliance-architecture.md).

Il dispositivo Azure Migrate individua le VM VMware locali quando si: 

- Usare lo strumento [Azure migrate: server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) per valutare le macchine virtuali VMware per la migrazione ad Azure.
- Usare l' [Azure migrate: strumento di migrazione server](migrate-services-overview.md#azure-migrate-server-migration-tool) per la [migrazione senza agenti](server-migrate-overview.md) di macchine virtuali VMware in Azure.

## <a name="set-discovery-scope"></a>Imposta ambito di individuazione


Dopo aver configurato la Azure Migrate Appliance per la valutazione o la migrazione di macchine virtuali VMware, l'appliance inizia a individuare le VM e a inviare i metadati delle VM ad Azure. Prima di connettere l'appliance a server vCenter per l'individuazione, è possibile impostare l'ambito di individuazione per limitare l'individuazione ai data center server vCenter, ai cluster, a una cartella di cluster, a host, a una cartella di host o a singole macchine virtuali.

Per impostare l'ambito, assegnare le autorizzazioni per l'account utilizzato da Azure Migrate per accedere al server vCenter.

## <a name="create-a-vcenter-user-account"></a>Creare un account utente vCenter

Se non è già stato configurato un account utente vCenter usato dall'appliance Azure Migrate per individuare, valutare ed eseguire la migrazione di macchine virtuali VMware, eseguire prima questa operazione.

1.    Accedere al client Web vSphere come amministratore del server vCenter.
2.    Selezionare **Amministrazione** > **SSO utenti e gruppi**, quindi fare clic sulla scheda **utenti** .
3.    Selezionare l'icona **New User** (Nuovo utente).
4.    Immettere le informazioni sul nuovo utente > **OK**.

Le autorizzazioni dell'account dipendono da ciò che si sta distribuendo.

**Funzionalità** | **Autorizzazioni dell'account**
--- | ---
[Valutare](tutorial-assess-vmware.md)| Per l'account è necessario l'accesso in sola lettura.
[Individuare app, ruoli e funzionalità](how-to-discover-applications.md) | L'account richiede l'accesso in sola lettura, con privilegi abilitati per le macchine virtuali > operazioni Guest.
[Analizza dipendenze (senza agenti)](how-to-create-group-machine-dependencies-agentless.md) | L'account richiede l'accesso in sola lettura, con privilegi abilitati per le macchine virtuali > operazioni Guest.
[Esegui migrazione (senza agenti)](tutorial-migrate-vmware.md) | È necessario un ruolo a cui vengono assegnate le autorizzazioni appropriate.<br/><br/> Per creare un ruolo, accedere a vSphere Web client come amministratore server vCenter. Selezionare l'istanza di server vCenter > **Crea ruolo**. Specificare un nome di ruolo, ad esempio <em>Azure_Migrate</em>, e assegnare le [autorizzazioni necessarie](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) al ruolo.


## <a name="assign-permissions-on-vcenter-objects"></a>Assegnare le autorizzazioni per gli oggetti vCenter

È possibile assegnare autorizzazioni per gli oggetti di inventario usando uno dei due metodi seguenti:

- Assegnare un ruolo con le autorizzazioni necessarie all'account in uso, per tutti gli oggetti padre che ospitano le VM di cui si vuole eseguire l'individuazione o la migrazione.
- In alternativa, è possibile assegnare il ruolo e l'account utente a livello di data center e propagarli agli oggetti figlio. Assegnare quindi all'account un ruolo **Nessun accesso** per ogni oggetto che non si desidera individuare/migrare. Questo approccio non è consigliato perché è complesso e potrebbe esporre i controlli di accesso, perché a ogni nuovo oggetto figlio viene automaticamente concesso l'accesso ereditato dall'elemento padre.

Per assegnare un ruolo all'account che si sta usando per tutti gli oggetti rilevanti, procedere come segue:

- **Per la valutazione**: per la valutazione della macchina virtuale, applicare il ruolo di sola **lettura** all'account utente vCenter per tutti gli oggetti padre che ospitano le macchine virtuali che si desidera individuare. Oggetti padre inclusi: host, cartella di host, cluster e cartella dei cluster nella gerarchia, fino al Data Center. Propagare queste autorizzazioni agli oggetti figlio nella gerarchia.

    ![Assegnare le autorizzazioni](./media/tutorial-assess-vmware/assign-perms.png)

- **Per la migrazione**senza agenti: per la migrazione senza agenti, applicare un ruolo definito dall'utente con le [autorizzazioni necessarie](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) all'account utente, per tutti gli oggetti padre che ospitano le macchine virtuali che si desidera individuare. È possibile assegnare un nome al ruolo <em>Azure_Migrate</em>.

### <a name="scope-support"></a>Supporto ambito

Attualmente, lo strumento Server Assessment non riesce a individuare le VM se l'account vCenter ha accesso concesso a livello di cartella della macchina virtuale vCenter. Sono supportate le cartelle di host e cluster.

Per definire l'ambito di individuazione in base alle cartelle VM, completare la procedura descritta di seguito per assicurarsi che l'account vCenter disponga di accesso in sola lettura assegnato a livello di macchina virtuale.

1. Assegnare le autorizzazioni di sola lettura in tutte le VM nelle cartelle VM di cui si vuole definire l'ambito per l'individuazione.
2. Concedere l'accesso in sola lettura a tutti gli oggetti padre che ospitano macchine virtuali.
    - Sono inclusi tutti gli oggetti padre (host, cartella di host, cluster, cartella dei cluster) nella gerarchia fino al Data Center.
    - Non è necessario propagare le autorizzazioni a tutti gli oggetti figlio.
3. Usare le credenziali per l'individuazione selezionando il data center come **Ambito raccolta**. Il programma di installazione del controllo degli accessi in base al ruolo garantisce che l'account utente vCenter corrispondente abbia accesso solo alle macchine virtuali specifiche del tenant.


## <a name="next-steps"></a>Passaggi successivi

[Configurare l'appliance](how-to-set-up-appliance-vmware.md) e [avviare l'individuazione continua](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential).
