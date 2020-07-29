---
title: Impostare l'ambito per l'individuazione di macchine virtuali VMware con Azure Migrate
description: Viene descritto come impostare l'ambito di individuazione per la valutazione e la migrazione di macchine virtuali VMware con Azure Migrate.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: e53eb0d01df2152aeced2901335f75879885fd22
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84770391"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Impostare l'ambito di individuazione per le macchine virtuali VMware

Questo articolo descrive come limitare l'ambito delle macchine virtuali VMware individuate quando si è:

- Quando si usa lo strumento Azure Migrate: server Assessment, è possibile individuare le macchine virtuali con l' [appliance Azure migrate](migrate-appliance-architecture.md) .
- Individuazione di macchine virtuali con l' [appliance Azure migrate](migrate-appliance-architecture.md) quando si usa lo strumento di migrazione Azure migrate: Server, per la migrazione senza agenti di macchine virtuali VMware in Azure.

Quando si configura il dispositivo, si connette a server vCenter e avvia l'individuazione. Prima di connettere l'appliance a server vCenter, è possibile limitare l'individuazione a data center server vCenter, cluster, una cartella di cluster, host, una cartella di host o singole macchine virtuali. Per impostare l'ambito, assegnare le autorizzazioni per l'account usato dall'appliance per accedere al server vCenter.

## <a name="before-you-start"></a>Prima di iniziare

Se non è stato configurato un account utente vCenter che Azure Migrate USA per l'individuazione, effettuare questa operazione ora per la [valutazione](tutorial-prepare-vmware.md#set-up-permissions-for-assessment) o la [migrazione senza agenti](tutorial-prepare-vmware.md#assign-permissions-to-an-account).


## <a name="assign-permissions-and-roles"></a>Assegnare autorizzazioni e ruoli

È possibile assegnare autorizzazioni per gli oggetti di inventario VMware usando uno dei due metodi seguenti:

- Nell'account usato dall'appliance assegnare un ruolo con le autorizzazioni necessarie per gli oggetti che si desidera definire come ambito.
- In alternativa, assegnare un ruolo all'account a livello di data center e propagarsi agli oggetti figlio. Assegnare quindi all'account un ruolo **Nessun accesso** per ogni oggetto che non si vuole includere nell'ambito. Questo approccio non è consigliato perché è complesso e potrebbe esporre i controlli di accesso, perché a ogni nuovo oggetto figlio viene automaticamente concesso l'accesso ereditato dall'elemento padre.

Non è possibile definire l'ambito dell'individuazione dell'inventario a livello di cartella della macchina virtuale vCenter. Se è necessario definire l'ambito di individuazione per le macchine virtuali in una cartella di macchine virtuali, creare un utente e concedere l'accesso singolarmente a ogni macchina virtuale richiesta. Sono supportate le cartelle host e cluster.


### <a name="assign-a-role-for-assessment"></a>Assegnazione di un ruolo per la valutazione

1. Nell'account vCenter dell'appliance usato per l'individuazione, applicare il ruolo di sola **lettura** per tutti gli oggetti padre che ospitano le macchine virtuali che si vuole individuare e valutare (host, cluster, cartella host, cartella cluster, fino al Data Center).
2. Propagare queste autorizzazioni agli oggetti figlio nella gerarchia.

    ![Assegnare le autorizzazioni](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Assegnazione di un ruolo per la migrazione senza agenti

1. Nell'account appliance vCenter usato per la migrazione, applicare un ruolo definito dall'utente che disponga delle [autorizzazioni necessarie](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless), a tutti gli oggetti padre che ospitano le macchine virtuali di cui si vuole eseguire l'individuazione e la migrazione.
2. È possibile denominare il ruolo con un elemento più semplice da identificare. Ad esempio, <em>Azure_Migrate</em>.

## <a name="work-around-vm-folder-restriction"></a>Aggirare la restrizione delle cartelle VM

Attualmente, lo strumento Server Assessment non riesce a individuare le VM se l'accesso viene concesso a livello di cartella della macchina virtuale vCenter. Per definire l'ambito di individuazione e valutazione da cartelle VM, usare questa soluzione alternativa.

1. Assegnare le autorizzazioni di sola lettura in tutte le macchine virtuali che si trovano nelle cartelle VM di cui si vuole definire l'ambito per l'individuazione e la valutazione.
2. Concedere l'accesso in sola lettura a tutti gli oggetti padre che ospitano l'host VM, il cluster, la cartella host, la cartella cluster, fino al Data Center. Non è necessario propagare le autorizzazioni a tutti gli oggetti figlio.
3. Per usare le credenziali per l'individuazione, selezionare il Data Center come **ambito della raccolta**.


Il programma di installazione del controllo degli accessi in base al ruolo garantisce che l'account utente vCenter corrispondente abbia accesso solo alle macchine virtuali specifiche del tenant.


## <a name="next-steps"></a>Passaggi successivi

[Configurare l'appliance](how-to-set-up-appliance-vmware.md)e avviare l' [individuazione continua](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential).
