---
title: Supporto multi-tenant per la replica di macchine virtuali VMware in Azure (programma CSP) | Documentazione Microsoft
description: Descrive come distribuire Azure Site Recovery in un ambiente multi-tenant per orchestrare la replica, il failover e il ripristino di macchine virtuali VMware locali in Azure tramite il programma CSP usando il portale di Azure
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: manayar
ms.openlocfilehash: 97edbe67c25036dc1156f0f0ca5431a617d7a004
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>Supporto multi-tenant in Azure Site Recovery per replicare le macchine virtuali VMware in Azure tramite CSP

Azure Site Recovery supporta ambienti multi-tenant per sottoscrizioni tenant. Supporta anche la multi-tenancy per le sottoscrizioni tenant create e gestite tramite il programma Microsoft Cloud Solution Provider (CSP). In questo articolo sono illustrate le indicazioni per implementare e gestire scenari multi-tenant da VMware ad Azure. Sono illustrate anche la creazione e la gestione di sottoscrizioni tenant tramite CSP.

Queste indicazioni traggono molte informazioni dalla documentazione esistente per la replica di macchine virtuali VMware in Azure. Per altre informazioni, vedere [Replicare VM VMware in Azure con Site Recovery](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Ambienti multi-tenant
Esistono tre modelli multi-tenant principali:

* **Provider di servizi di hosting condiviso**: il partner possiede l'infrastruttura fisica e usa risorse condivise (vCenter, data center, archiviazione fisica e così via) per ospitare le macchine virtuali di più tenant nella stessa infrastruttura. Il partner può fornire il ripristino di emergenza come servizio gestito oppure il tenant può essere proprietario del ripristino di emergenza tramite una soluzione self-service.

* **Provider di servizi di hosting dedicato**: il partner possiede l'infrastruttura fisica, ma usa risorse dedicate (più vCenter, archivi dati fisici e così via) per ospitare le macchine virtuali di ogni tenant in un'infrastruttura separata. Il partner può fornire il ripristino di emergenza come servizio gestito oppure il tenant può essere proprietario di un'apposita soluzione self-service.

* **Provider di servizi gestiti**: il cliente possiede l'infrastruttura fisica che ospita le macchine virtuali e il partner provvede ad abilitare e gestire il ripristino di emergenza.

## <a name="shared-hosting-multi-tenant-guidance"></a>Indicazioni per il multi-tenant di hosting condiviso
Questa sezione approfondisce lo scenario di hosting condiviso. Gli altri due scenari sono subset dello scenario di hosting condiviso e si basano sugli stessi principi. Le differenze vengono descritte alla fine delle indicazioni sull'hosting condiviso.

Il requisito di base in uno scenario multi-tenant consiste nell'isolare i diversi tenant. Un tenant non deve poter vedere ciò che viene ospitato in un altro tenant. In un ambiente gestito dal partner, questo requisito non è importante come in un ambiente self-service, dove invece può essere fondamentale. Queste indicazioni presuppongono che sia necessario isolare i tenant.

L'architettura è illustrata nella figura seguente:

![Provider di servizi di hosting condiviso con un vCenter](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)  
**Scenario di hosting condiviso con un vCenter**

Come illustrato nel diagramma precedente, ogni cliente ha un server di gestione separato. Questa configurazione limita l'accesso dei tenant alle sole macchine virtuali specifiche dei tenant e implementa l'isolamento dei tenant stessi. Lo scenario di replica delle macchine virtuali VMware usa il server di configurazione per gestire gli account in modo da individuare le macchine virtuali e installare gli agenti. Gli stessi principi valgono per gli ambienti multi-tenant, dove in aggiunta viene limitata l'individuazione delle macchine virtuali attraverso il controllo di accesso di vCenter.

Il requisito di isolamento dei dati richiede che tutte le informazioni riservate dell'infrastruttura, ad esempio le credenziali di accesso, non vengano rese note ai tenant. Per questo motivo, è consigliabile che tutti i componenti del server di gestione rimangono sotto il controllo esclusivo del partner. I componenti del server di gestione sono i seguenti:
* Server di configurazione
* Server di elaborazione
* Server di destinazione master 

Il partner gestisce anche un server di elaborazione con scalabilità orizzontale.

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>Ogni server di configurazione nello scenario multi-tenant usa due account

- **Account di accesso vCenter**: usare questo account per individuare le macchine virtuali tenant. Ha autorizzazioni di accesso vCenter assegnate, come descritto nella sezione successiva. Per evitare la divulgazione accidentale delle credenziali di accesso, è consigliabile che i partner immettano personalmente queste credenziali nello strumento di configurazione.

- **Account di accesso alla macchina virtuale**: usare questo account per installare l'agente di mobilità nelle macchine virtuali tenant tramite push automatico. Si tratta in genere di un account di dominio che un tenant può fornire a un partner o che può essere gestito direttamente dal partner. Se un tenant non intende condividere i dettagli direttamente con il partner, può essere autorizzato a immettere le credenziali tramite un accesso limitato nel tempo al server di configurazione oppure può installare manualmente gli agenti di mobilità con il supporto del partner.

### <a name="requirements-for-a-vcenter-access-account"></a>Requisiti per l'account di accesso a vCenter

Come descritto nella sezione precedente, è necessario configurare il server di configurazione con un account a cui sia assegnato un ruolo speciale. L'assegnazione del ruolo deve essere applicata all'account di accesso a vCenter per ogni oggetto vCenter e non deve essere propagata agli oggetti figlio. Questa configurazione garantisce l'isolamento dei tenant, perché la propagazione dell'accesso può determinare l'accesso accidentale ad altri oggetti.

![Opzione Propagate to Child Objects (Propaga a oggetti figlio)](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

L'alternativa consiste nell'assegnare l'account e il ruolo dell'utente all'oggetto data center e quindi propagarli agli oggetti figlio. Assegnare quindi all'account un ruolo *Nessun accesso* per ogni oggetto (ad esempio le macchine virtuali degli altri tenant) che deve essere inaccessibile a un tenant specifico. Questa configurazione è complicata e al tempo stesso espone a controlli di accesso accidentali, perché a ogni nuovo oggetto figlio viene automaticamente concesso l'accesso ereditato dall'oggetto padre. È quindi consigliabile usare il primo approccio.

La procedura di accesso all'account vCenter è la seguente:

1. Creare un nuovo ruolo clonando il ruolo *Read-only* predefinito e assegnargli un nome appropriato, ad esempio Azure_Site_Recovery come in questo esempio.

2. Assegnare al ruolo le autorizzazioni seguenti:

    * **Datastore** (Archivio dati): Allocate space (Alloca spazio), Browse datastore (Sfoglia archivio dati), Low level file operations (Operazioni file di livello basso), Remove file (Rimuovi file), Update virtual machine files (Aggiorna file macchina virtuale)

    * **Network** (Rete): Network assign (Assegnazione rete)

    * **Resource** (Risorsa): Assign VM to resource pool (Assegna macchina virtuale a pool di risorse), Migrate powered off VM (Esegui migrazione macchina virtuale spenta), Migrate powered on VM (Esegui migrazione macchina virtuale accesa)

    * **Tasks** (Attività): Create task (Crea attività), Update task (Aggiorna attività)

    * **Virtual machine** (Macchina virtuale): 
        * Configuration (Configurazione) > all (tutto)
        * Interaction (Interazione) > Answer question (Rispondi alla domanda), Device connection (Connessione dispositivo), Configure CD media (Configura supporto CD), Configure floppy media (Configura supporto floppy), Power off (Spegni), Power on (Accendi), VMware tools install (Installazione strumenti VMware)
        * Inventory (Inventario) > Create from existing (Crea da esistente), Create new (Crea nuovo), Register (Registra), Unregister (Annulla registrazione)
        * Provisioning > Allow virtual machine download (Consenti download macchina virtuale), Allow virtual machine files upload (Consenti upload file macchina virtuale)
        * Snapshot management (Gestione snapshot) > Remove snapshots (Rimuovi snapshot)

    ![Finestra di dialogo Edit Role (Modifica ruolo)](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3. Assegnare i livelli di accesso all'account vCenter (usato nel server di configurazione del tenant) per diversi oggetti, come segue:

>| Oggetto | Ruolo | Osservazioni |
>| --- | --- | --- |
>| vCenter | Read-Only | Necessario solo per consentire l'accesso a vCenter per la gestione di oggetti diversi. Questa autorizzazione può essere rimossa se l'account non dovrà mai essere offerto a un tenant o usato per operazioni di gestione in vCenter. |
>| Data center | Azure_Site_Recovery |  |
>| Host e cluster host | Azure_Site_Recovery | Assicura nuovamente che l'accesso sia a livello di oggetto, in modo che solo gli host accessibili abbiano VM tenant prima del failover e dopo il failback. |
>| Archivio dati e cluster archivio dati | Azure_Site_Recovery | Come sopra. |
>| Rete | Azure_Site_Recovery |  |
>| Server di gestione | Azure_Site_Recovery | Include l'accesso a tutti i componenti (server di configurazione, server di elaborazione e server di destinazione master) se al di fuori della macchina del server di configurazione. |
>| Macchine virtuali tenant | Azure_Site_Recovery | Assicura che anche le eventuali nuove macchine virtuali tenant di un particolare tenant abbiano questo accesso, altrimenti non potranno essere rilevate attraverso il Portale di Azure. |

L'accesso all'account vCenter è ora completato. Questo passaggio soddisfa i requisiti minimi di autorizzazione per completare le operazioni di failback. Queste autorizzazioni di accesso possono essere usate anche con i criteri esistenti. È sufficiente modificare il set di autorizzazioni esistente in modo che includa le autorizzazioni del ruolo descritte nel passaggio 2 precedente.

Per limitare le operazioni di ripristino di emergenza fino allo stato di failover, ovvero senza le funzionalità di failback, seguire la procedura descritta sopra con un'eccezione: invece di assegnare il ruolo *Azure_Site_Recovery* all'account di accesso a vCenter, assegnare solo il ruolo *Read-Only*. Questo set di autorizzazioni consente il failover e la replica delle macchine virtuali e non consente il failback. Tutte le altre fasi del processo precedente restano invariate. Ogni autorizzazione continua a essere assegnata solo a livello di oggetto e non viene propagata agli oggetti figlio per assicurare l'isolamento del tenant e limitare il rilevamento di macchine virtuali.

## <a name="other-multi-tenant-environments"></a>Altri ambienti multi-tenant

La sezione precedente descrive come configurare un ambiente multi-tenant per una soluzione di hosting condiviso. Le altre due soluzioni principali sono il servizio di hosting dedicato e il servizio gestito. L'architettura di queste soluzioni è illustrata nelle sezioni seguenti.

### <a name="dedicated-hosting-solution"></a>Soluzione di hosting dedicato

Come illustrato nel diagramma seguente, la differenza in termini di architettura in una soluzione di hosting dedicato è che l'infrastruttura di ogni tenant viene configurata solo per quel tenant. Poiché i tenant sono isolati tramite vCenter separati, il provider di hosting deve comunque seguire la procedura CSP descritta per l'hosting condiviso, senza tuttavia preoccuparsi dell'isolamento del tenant. La configurazione CSP resta invariata.

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)  
**Scenario di hosting dedicato con più vCenter**

### <a name="managed-service-solution"></a>Soluzione di servizi gestiti

Come illustrato nel diagramma seguente, la differenza in termini di architettura in una soluzione di servizi gestiti è che l'infrastruttura di ogni tenant è separata anche fisicamente dall'infrastruttura degli altri tenant. Questo scenario si verifica in genere quando il tenant è proprietario dell'infrastruttura e vuole che un provider di soluzioni gestisca il ripristino di emergenza. Anche in questo caso, essendo i tenant isolati fisicamente tramite infrastrutture diverse, il partner deve seguire la procedura CSP descritta per l'hosting condiviso, senza tuttavia preoccuparsi dell'isolamento del tenant. Il provisioning CSP resta invariato.

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)  
**Scenario di servizi gestiti con più vCenter**

## <a name="csp-program-overview"></a>Panoramica del programma CPS
Il [programma CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) promuove scenari di collaborazione che offrono ai partner tutti i servizi cloud Microsoft, tra cui Office 365, Enterprise Mobility Suite e Microsoft Azure. Con CSP, i partner Microsoft acquisiscono una relazione end-to-end con i clienti e diventano il punto di contatto primario della relazione. I partner possono distribuire le sottoscrizioni di Azure per i clienti e combinarle con le proprie offerte personalizzate a valore aggiunto.

Con Azure Site Recovery, i partner possono gestire la soluzione di ripristino di emergenza completa per i clienti direttamente tramite CSP. Possono in alternativa usare il programma CSP per configurare gli ambienti di Site Recovery e consentire ai clienti di gestire le proprie esigenze di ripristino di emergenza in modalità self-service. In entrambi gli scenari, i partner rappresentano l'anello di congiunzione tra Site Recovery e i clienti. I partner gestiscono la relazione con i clienti, addebitando loro l'uso di Site Recovery.

## <a name="create-and-manage-tenant-accounts"></a>Creare e gestire account tenant

### <a name="step-0-prerequisite-check"></a>Passaggio 0: Controllo dei prerequisiti

I prerequisiti per le macchine virtuali sono quelli descritti nella [documentazione di Azure Site Recovery](site-recovery-vmware-to-azure.md). Oltre a questi prerequisiti, prima di procedere alla gestione dei tenant tramite CSP è necessario avere a disposizione i controlli di accesso descritti in precedenza. Per ogni tenant, creare un server di gestione separato che possa comunicare con le macchine virtuali tenant e il vCenter del partner. Solo il partner dispone dei diritti di accesso a questo server.

### <a name="step-1-create-a-tenant-account"></a>Passaggio 1: Creare un account tenant

1. Accedere all'account CSP tramite il [Centro per i partner Microsoft](https://partnercenter.microsoft.com/). 
 
2. Nel menu **Dashboard** selezionare **Clienti**.

    ![Collegamento Clienti del Centro per i partner Microsoft](./media/site-recovery-multi-tenant-support-vmware-using-csp/csp-dashboard-display.png)

3. Nella pagina visualizzata fare clic sul pulsante **Aggiungi cliente**.

    ![Pulsante Aggiungi cliente](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-new-customer.png)

4. Nella pagina **Nuovo cliente** compilare tutti i dettagli dell'account per il tenant e fare clic su **Successivo: Sottoscrizioni**.

    ![Pagina Informazioni account](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-add-filled.png)

5. Nella pagina di selezione delle sottoscrizioni selezionare la casella di controllo **Microsoft Azure**. È possibile aggiungere altre sottoscrizioni subito o in qualsiasi momento nel futuro.

    ![Casella di controllo della sottoscrizione di Microsoft Azure](./media/site-recovery-multi-tenant-support-vmware-using-csp/azure-subscription-selection.png)

6. Nella pagina **Revisione** verificare i dettagli del tenant e quindi fare clic su **Invia**.

    ![Pagina Revisione](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-summary-page.png)  

    Dopo aver creato l'account tenant verrà visualizzata una pagina di conferma con i dettagli dell'account predefinito e la password per la sottoscrizione. 

7. Salvare le informazioni e modificare eventualmente la password in un secondo momento tramite la pagina di accesso del portale di Azure.  
 
    Queste informazioni possono essere condivise con il tenant così come sono oppure, se necessario, è possibile creare e condividere un account separato.

### <a name="step-2-access-the-tenant-account"></a>Passaggio 2: Accedere all'account tenant

È possibile accedere alla sottoscrizione del tenant tramite il dashboard del Centro per i partner Microsoft, come descritto in "Passaggio 1: Creare un account tenant". 

1. Passare alla pagina **Clienti** e quindi fare clic sul nome dell'account tenant.

2. Nella pagina **Sottoscrizioni** dell'account tenant è possibile monitorare le sottoscrizioni esistenti per l'account e aggiungerne altre, se necessario. Per gestire le operazioni di ripristino di emergenza del tenant, selezionare l'opzione **Tutte le risorse (portale di Azure)**.

    ![Collegamento Tutte le risorse](./media/site-recovery-multi-tenant-support-vmware-using-csp/all-resources-select.png)  
    
    Fare clic su **Tutte le risorse** per consentire l'accesso alle sottoscrizioni di Azure del tenant. È possibile verificare l'accesso facendo clic sul collegamento di Azure Active Directory nella parte superiore destra del portale di Azure.

    ![Collegamento di Azure Active Directory](./media/site-recovery-multi-tenant-support-vmware-using-csp/aad-admin-display.png)

A questo punto è possibile eseguire tutte le operazioni di ripristino sito per il tenant tramite il portale di Azure e gestire le operazioni di ripristino di emergenza. Per accedere alla sottoscrizione del tenant tramite CSP per il ripristino di emergenza gestito, seguire la procedura descritta in precedenza.

### <a name="step-3-deploy-resources-to-the-tenant-subscription"></a>Passaggio 3: Distribuire le risorse nella sottoscrizione del tenant
1. Nel portale di Azure creare un gruppo di risorse e distribuire un insieme di credenziali di Servizi di ripristino seguendo la procedura consueta. 
 
2. Scaricare la chiave di registrazione dell'insieme di credenziali,

3. Registrare il server di configurazione per il tenant usando la chiave di registrazione dell'insieme di credenziali.

4. Immettere le credenziali per i due account di accesso, ovvero l'account di accesso a vCenter e l'account di accesso alla macchina virtuale.

    ![Gestire gli account del server di configurazione](./media/site-recovery-multi-tenant-support-vmware-using-csp/config-server-account-display.png)

### <a name="step-4-register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>Passaggio 4: Registrare l'infrastruttura di Site Recovery nell'insieme di credenziali di Servizi di ripristino
1. Passare al portale di Azure e nell'insieme di credenziali creato in precedenza registrare il server vCenter nel server di configurazione registrato in "Passaggio 3: Distribuire le risorse nella sottoscrizione del tenant". A questo scopo usare l'account di accesso a vCenter.
2. Completare il processo di preparazione dell'infrastruttura per Site Recovery seguendo la procedura consueta.
3. Le macchine virtuali sono ora pronte per essere replicate. Verificare che nel pannello **Seleziona macchine virtuali** sotto l'opzione **Replica** siano visibili solo le macchine virtuali del tenant.

    ![Elenco di macchine virtuali tenant nel pannello Seleziona macchine virtuali](./media/site-recovery-multi-tenant-support-vmware-using-csp/tenant-vm-display.png)

### <a name="step-5-assign-tenant-access-to-the-subscription"></a>Passaggio 5: Assegnare l'accesso tenant alla sottoscrizione

Per il ripristino di emergenza self-service, fornire al tenant i dettagli dell'account, come indicato nel passaggio 6 della sezione "Passaggio 1: Creare un account tenant". Eseguire questa operazione dopo che il partner ha configurato l'infrastruttura di ripristino di emergenza. A prescindere che il ripristino di emergenza sia gestito o self-service, i partner devono accedere alle sottoscrizioni del tenant tramite il portale CSP. I partner configurano l'insieme di credenziali di loro proprietà e registrano l'infrastruttura nelle sottoscrizioni del tenant.

I partner possono anche aggiungere un nuovo utente alla sottoscrizione del tenant tramite il portale CSP seguendo questa procedura:

1. Accedere alla pagina delle sottoscrizioni CSP del tenant e selezionare l'opzione **Users and licenses** (Utenti e licenze).

    ![Pagina delle sottoscrizioni CSP del tenant](./media/site-recovery-multi-tenant-support-vmware-using-csp/users-and-licences.png)

    È ora possibile creare un nuovo utente immettendo i relativi dettagli e selezionando le autorizzazioni oppure caricando l'elenco di utenti in un file CSV.

2. Dopo aver creato un nuovo utente, tornare al portale di Azure e selezionare la sottoscrizione corrispondente nel pannello **Sottoscrizione**.

3. Nel pannello visualizzato selezionare **Controllo di accesso (IAM)** e fare clic su **Aggiungi** per aggiungere un utente con il livello di accesso corrispondente.      
    Gli utenti creati tramite il portale CSP verranno automaticamente visualizzati nel pannello aperto dopo aver fatto clic su un livello di accesso.

    ![Aggiungere un utente](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-user-subscription.png)

    Per la maggior parte delle operazioni di gestione è sufficiente il ruolo *Collaboratore*. Gli utenti con questo livello di accesso possono eseguire qualsiasi operazione su una sottoscrizione, tranne modificare i livelli di accesso. Per questa operazione è infatti richiesto il livello di accesso *Proprietario*. È inoltre possibile ritoccare i livelli di accesso in base alle esigenze.
