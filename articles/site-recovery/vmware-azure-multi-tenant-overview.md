---
title: Panoramica del supporto multi-tenant per la replica di macchine virtuali VMware in Azure (CSP) tramite Azure Site Recovery | Microsoft Docs
description: Offre una panoramica del supporto di Azure Site Recovery per le sottoscrizioni tenant in un ambiente multi-tenant tramite il programma CSP.
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: manayar
ms.openlocfilehash: 9b4fbb34686a12f992b344ac61420c9ba99ee405
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="overview-of-multi-tenant-support-for-vmware-replication-to-azure-with-csp"></a>Panoramica del supporto multi-tenant per la replica VMware in Azure con CSP

[Azure Site Recovery](site-recovery-overview.md) supporta ambienti multi-tenant per sottoscrizioni tenant. Supporta anche la multi-tenancy per le sottoscrizioni tenant create e gestite tramite il programma Microsoft Cloud Solution Provider (CSP). 

Questo articolo offre una panoramica dell'implementazione e della gestione della replica VMware multi-tenant in Azure. 

## <a name="multi-tenant-environments"></a>Ambienti multi-tenant

Esistono tre modelli multi-tenant principali:

* **Provider di servizi di hosting condiviso**: il partner possiede l'infrastruttura fisica e usa risorse condivise, ad esempio vCenter, data center, archiviazione fisica e così via, per ospitare le macchine virtuali di più tenant nella stessa infrastruttura. Il partner può fornire il ripristino di emergenza come servizio gestito oppure il tenant può essere proprietario del ripristino di emergenza tramite una soluzione self-service.

* **Provider di servizi di hosting dedicato**: il partner possiede l'infrastruttura fisica, ma usa risorse dedicate, ad esempio più vCenter, archivi dati fisici e così via, per ospitare le macchine virtuali di ogni tenant in un'infrastruttura separata. Il partner può fornire il ripristino di emergenza come servizio gestito oppure il tenant può essere proprietario di un'apposita soluzione self-service.

* **Provider di servizi gestiti**: il cliente possiede l'infrastruttura fisica che ospita le macchine virtuali e il partner provvede ad abilitare e gestire il ripristino di emergenza.

## <a name="shared-hosting-services-provider-hsp"></a>Provider di servizi di hosting condiviso

 Gli altri due scenari sono subset dello scenario di hosting condiviso e si basano sugli stessi principi. Le differenze vengono descritte alla fine delle indicazioni sull'hosting condiviso.

Il requisito di base in uno scenario multi-tenant è che i tenant devono essere isolati. Un tenant non deve poter vedere ciò che viene ospitato in un altro tenant. In un ambiente gestito dal partner, questo requisito non è importante come in un ambiente self-service, dove invece può essere fondamentale. Questo articolo presuppone che sia necessario isolare i tenant.

L'architettura è illustrata nel diagramma seguente.

![Provider di servizi di hosting condiviso con un vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Hosting condiviso con un server vCenter**

Nel diagramma ogni cliente ha un server di gestione separato. Questa configurazione limita l'accesso dei tenant alle sole macchine virtuali specifiche dei tenant e abilita l'isolamento dei tenant stessi. La replica delle macchine virtuali VMware usa il server di configurazione per individuare le macchine virtuali e installare gli agenti. Gli stessi principi valgono per gli ambienti multi-tenant, dove in aggiunta viene limitata l'individuazione delle macchine virtuali attraverso il controllo di accesso di vCenter.

Il requisito di isolamento dei dati implica che nessuna informazione sensibile dell'infrastruttura, ad esempio le credenziali di accesso, verrà resa nota ai tenant. Per questo motivo, è consigliabile che tutti i componenti del server di gestione rimangono sotto il controllo esclusivo del partner. I componenti del server di gestione sono i seguenti:

* Server di configurazione
* Server di elaborazione
* Server master di destinazione

Il partner gestisce anche un server di elaborazione separato con scalabilità orizzontale.

## <a name="configuration-server-accounts"></a>Account del server di configurazione

Ogni server di configurazione nello scenario multi-tenant usa due account:

- **Account di accesso vCenter**: questo account viene usato per individuare le macchine virtuali tenant. Ha autorizzazioni di accesso vCenter assegnate. Per evitare la divulgazione delle credenziali di accesso, è consigliabile che i partner immettano personalmente queste credenziali nello strumento di configurazione.

- **Account di accesso alla macchina virtuale**: questo account viene usato per installare l'agente del servizio Mobility nelle macchine virtuali tenant con un push automatico. Si tratta in genere di un account di dominio che un tenant può fornire a un partner o che può essere gestito direttamente dal partner. Se un tenant non intende condividere i dettagli direttamente con il partner, può immettere le credenziali tramite un accesso limitato nel tempo al server di configurazione. In alternativa, con l'assistenza del partner è possibile installare l'agente del servizio Mobility manualmente.

## <a name="vcenter-account-requirements"></a>Requisiti dell'account vCenter

È necessario configurare il server di configurazione con un account a cui sia assegnato un ruolo apposito. 

- L'assegnazione del ruolo deve essere applicata all'account di accesso a vCenter per ogni oggetto vCenter e non deve essere propagata agli oggetti figlio. Questa configurazione garantisce l'isolamento dei tenant, perché la propagazione dell'accesso può determinare l'accesso accidentale ad altri oggetti.

    ![Opzione Propagate to Child Objects (Propaga a oggetti figlio)](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- L'approccio alternativo consiste nell'assegnare l'account e il ruolo utente all'oggetto data center e quindi propagarli agli oggetti figlio. Assegnare quindi all'account un ruolo **Nessun accesso** per ogni oggetto che deve essere inaccessibile a un tenant specifico, ad esempio le macchine virtuali appartenenti ad altri tenant. Questa configurazione è complicata e al tempo stesso espone a controlli di accesso accidentali, perché a ogni nuovo oggetto figlio viene automaticamente concesso l'accesso ereditato dall'oggetto padre. È quindi consigliabile usare il primo approccio.

### <a name="create-a-vcenter-account"></a>Creare un account vCenter

1. Creare un nuovo ruolo clonando il ruolo *Read-only* predefinito e assegnargli un nome appropriato, ad esempio Azure_Site_Recovery come in questo esempio.
2. Assegnare al ruolo le autorizzazioni seguenti:

    * **Datastore** (Archivio dati): Allocate space (Alloca spazio), Browse datastore (Sfoglia archivio dati), Low level file operations (Operazioni file di livello basso), Remove file (Rimuovi file), Update virtual machine files (Aggiorna file macchina virtuale)
    * **Network** (Rete): Network assign (Assegnazione rete)
    * **Resource** (Risorsa): Assign VM to resource pool (Assegna macchina virtuale a pool di risorse), Migrate powered off VM (Esegui migrazione macchina virtuale spenta), Migrate powered on VM (Esegui migrazione macchina virtuale accesa)
    * **Tasks** (Attività): Create task (Crea attività), Update task (Aggiorna attività)
    * **VM - Configuration (Configurazione)**: All (Tutto)
    - **VM - Interaction (Interazione)** > Answer question (Rispondi alla domanda), Device connection (Connessione dispositivo), Configure CD media (Configura supporto CD), Configure floppy media (Configura supporto floppy), Power off (Spegni), Power on (Accendi), VMware tools install (Installazione strumenti VMware)
    - **VM - Inventory (Inventario)** > Create from existing (Crea da esistente), Create new (Crea nuovo), Register (Registra), Unregister (Annulla registrazione)
    - **VM - Provisioning** > Allow virtual machine download (Consenti download macchina virtuale), Allow virtual machine files upload (Consenti upload file macchina virtuale)
    - **VM - Snapshot management (Gestione snapshot)** > Remove snapshots (Rimuovi snapshot)

        ![Finestra di dialogo Edit Role (Modifica ruolo)](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Assegnare i livelli di accesso all'account vCenter (usato nel server di configurazione del tenant) per diversi oggetti, come segue:

>| Oggetto | Ruolo | Osservazioni |
>| --- | --- | --- |
>| vCenter | Read-Only | Necessario solo per consentire l'accesso a vCenter per la gestione di oggetti diversi. Questa autorizzazione può essere rimossa se l'account non dovrà mai essere offerto a un tenant o usato per operazioni di gestione in vCenter. |
>| Data center | Azure_Site_Recovery |  |
>| Host e cluster host | Azure_Site_Recovery | Assicura nuovamente che l'accesso sia a livello di oggetto, in modo che solo gli host accessibili abbiano VM tenant prima del failover e dopo il failback. |
>| Archivio dati e cluster archivio dati | Azure_Site_Recovery | Come sopra. |
>| Rete | Azure_Site_Recovery |  |
>| Server di gestione | Azure_Site_Recovery | Include l'accesso a tutti i componenti, CS, PS e MT, al di fuori della macchina CS. |
>| Macchine virtuali tenant | Azure_Site_Recovery | Assicura che anche le eventuali nuove macchine virtuali tenant di un particolare tenant abbiano questo accesso, altrimenti non potranno essere rilevate attraverso il Portale di Azure. |

L'accesso all'account vCenter è ora completato. Questo passaggio soddisfa i requisiti minimi di autorizzazione per completare le operazioni di failback. Queste autorizzazioni di accesso possono essere usate anche con i criteri esistenti. È sufficiente modificare il set di autorizzazioni esistente in modo che includa le autorizzazioni del ruolo descritte nel passaggio 2 precedente.

### <a name="failover-only"></a>Solo failover
Per limitare le operazioni di ripristino di emergenza fino al solo failover (vale a dire senza le funzionalità di failback), usare la procedura precedente con queste eccezioni:

- Invece di assegnare il ruolo *Azure_Site_Recovery* all'account di accesso a vCenter, assegnare solo il ruolo *Read-Only*. Questo set di autorizzazioni consente il failover e la replica delle macchine virtuali e non consente il failback.
- Tutte le altre fasi del processo precedente restano invariate. Ogni autorizzazione continua a essere assegnata solo a livello di oggetto e non viene propagata agli oggetti figlio per assicurare l'isolamento del tenant e limitare il rilevamento di macchine virtuali.


## <a name="dedicated-hosting-solution"></a>Soluzione di hosting dedicato

Come illustrato nel diagramma seguente, la differenza in termini di architettura in una soluzione di hosting dedicato è che l'infrastruttura di ogni tenant viene configurata solo per quel tenant. Poiché i tenant sono isolati tramite vCenter separati, il provider di hosting deve comunque seguire la procedura CSP descritta per l'hosting condiviso, senza tuttavia preoccuparsi dell'isolamento del tenant. La configurazione CSP resta invariata.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Scenario di hosting dedicato con più vCenter**

## <a name="managed-service-solution"></a>Soluzione di servizi gestiti

Come illustrato nel diagramma seguente, la differenza in termini di architettura in una soluzione di servizi gestiti è che l'infrastruttura di ogni tenant è separata anche fisicamente dall'infrastruttura degli altri tenant. Questo scenario si verifica in genere quando il tenant è proprietario dell'infrastruttura e vuole che un provider di soluzioni gestisca il ripristino di emergenza. Anche in questo caso, essendo i tenant isolati fisicamente tramite infrastrutture diverse, il partner deve seguire la procedura CSP descritta per l'hosting condiviso, senza tuttavia preoccuparsi dell'isolamento del tenant. Il provisioning CSP resta invariato.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Scenario di servizi gestiti con più vCenter**

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni](site-recovery-role-based-linked-access-control.md) sul controllo degli accessi in base al ruolo in Site Recovery.
Informazioni su come [configurare il ripristino di emergenza di macchine virtuali VMware in Azure](vmware-azure-tutorial.md)
[Configurare il ripristino di emergenza per macchine virtuali VMWare con multi-tenancy con CSP](vmware-azure-multi-tenant-csp-disaster-recovery.md)
