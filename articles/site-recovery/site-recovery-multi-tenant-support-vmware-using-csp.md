---
title: Supporto multi-tenant in Azure Site Recovery per replicare le macchine virtuali VMware in Azure tramite il programma CSP | Microsoft Docs
description: Descrive come distribuire Azure Site Recovery i n un ambiente multi-tenant per orchestrare la replica, il failover e il ripristino di macchine virtuali VMware locali in Azure tramite il programma CSP con il portale di Azure
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: manayar
translationtype: Human Translation
ms.sourcegitcommit: 4727eeb92a7cd06c4775d7cbf5594ab752a1e8f2
ms.openlocfilehash: 80eb816f50d707e7605b9863c9cb99bce5c3d592


---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-the-csp-program"></a>Supporto multi-tenant in Azure Site Recovery per replicare le macchine virtuali VMware in Azure tramite il programma CSP

Azure Site Recovery supporta ambienti multi-tenant per sottoscrizioni tenant. La multi-tenancy è supportata anche per le sottoscrizioni tenant create e gestite tramite il programma CSP. In questo articolo sono illustrate le indicazioni per implementare e gestire scenari multi-tenant da VMware ad Azure. Sono illustrati anche i dettagli della creazione e della gestione di sottoscrizioni tenant tramite CSP.

Si noti che queste indicazioni traggono molte informazioni dalla documentazione esistente per la replica di macchine virtuali VMware in Azure. Le indicazioni devono essere usate congiuntamente con la [documentazione](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Ambienti multi-tenant
Esistono tre modelli multi-tenant principali:

1.  **Provider di servizi di hosting condiviso**: in questo caso il partner possiede l'infrastruttura fisica e usa risorse condivise (vCenter, data center, archiviazione fisica e così via) per ospitare le macchine virtuali di più tenant nella stessa infrastruttura. La gestione del ripristino di emergenza può essere offerta dal partner come servizio gestito oppure essere di proprietà del tenant come soluzione di ripristino di emergenza self-service.
2.  **Provider di servizi di hosting dedicato**: in questo caso il partner possiede l'infrastruttura fisica ma usa risorse dedicate (più vCenter, archivi dati fisici e così via) per ospitare le macchine virtuali di ogni tenant in un'infrastruttura separata. Il ripristino di emergenza può essere gestita dal partner oppure in autonomia dal tenant.
3.  **Provider di servizi gestiti**: in questo caso il cliente possiede l'infrastruttura fisica che ospita le macchine virtuali e il partner provvede ad abilitare e gestire il ripristino di emergenza.

## <a name="shared-hosting-multi-tenant-guidance"></a>Indicazioni per il multi-tenant di hosting condiviso
Queste indicazioni approfondiscono lo scenario di hosting condiviso. Gli altri due scenari sono derivati dello scenario di hosting condiviso e si basano sugli stessi principi. Alla fine delle indicazioni sull'hosting condiviso vengono descritte le differenze.

Il requisito di base in uno scenario multi-tenant è isolare i diversi tenant; ciò significa che un tenant non deve poter osservato ciò che viene ospitato da un altro tenant. In un ambiente completamente gestito da partner, questo requisito non è importante come in un ambiente self-service, dove invece può essere critico. Queste indicazioni presuppongono che sia necessario isolare i tenant.

L'architettura ha un aspetto simile al seguente:

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)

**Figura 1: Scenario di hosting condiviso con un vCenter**

Come illustrato dalla rappresentazione precedente, ogni cliente avrà un server di gestione separato. Questo ha lo scopo di limitare l'accesso ai tenant alle macchine virtuali specifiche del tenant per abilitare l'isolamento dei tenant. Lo scenario di replica delle macchine virtuali VMware usa il server di configurazione per gestire gli account in modo da individuare le macchine virtuali e installare gli agenti. Gli stessi principi valgono per gli ambienti multi-tenant, dove in aggiunta viene limitata l'individuazione delle macchine virtuali attraverso il controllo di accesso di vCenter. 

Il requisito di isolamento di dati richiede che tutte le informazioni riservate dell'infrastruttura (ad esempio le credenziali di accesso) rimangano divulgate da tenant. Per questo motivo, è consigliabile che tutti i componenti del server di gestione (Configuration Server o CS, Process Server o PS e Master Target Server o MT) rimangono sotto il controllo esclusivo del partner. È incluso l'aumento delle istanze di PS.

### <a name="every-cs-under-the-multi-tenant-scenario-uses-two-accounts"></a>Ogni CS nello scenario multi-tenant usa due account:

- **Account di accesso a vCenter**: viene usato per individuare le macchine virtuali tenant ed è l'account a cui sono assegnate le autorizzazioni di accesso a vCenter (come descritto nella sezione successiva). È consigliabile che sia il partner a immettere le credenziali nello strumento di configurazione, per evitare la perdita accidentale dell'accesso.
- **Account di accesso alla macchina virtuale**: viene usato per installare l'agente di mobilità nelle macchine virtuali tenant tramite push automatico. In genere si tratta di un account di dominio che un tenant potrebbe offrire al partner o in alternativa può essere gestito direttamente dal partner. Nel caso in cui il tenant non desideri condividere i dettagli per collaborare direttamente, al tenant può essere consentito immettere le credenziali tramite un accesso limitato nel tempo a CS oppure, in alternativa, il tenant può installare manualmente gli agenti di mobilità con il supporto del partner.

### <a name="requirements-for-vcenter-access-account"></a>Requisiti per l'account di accesso a vCenter

Come descritto in dettaglio nella sezione precedente, per CS è necessaria la configurazione con un account a cui sia assegnato un ruolo speciale. È importante notare che è necessario eseguire questa assegnazione di ruolo all'account di accesso a vCenter per ogni oggetto vCenter e che non deve essere propagata agli oggetti figlio. In questo modo viene garantito l'isolamento dei tenant, poiché la propagazione dell'accesso può causare l'accesso accidentale anche ad altri oggetti.

![permissions-without-propagation](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

L'alternativa consiste nell'assegnare l'account utente e il ruolo dell'oggetto data center e propagarlo agli oggetti figlio; successivamente all'account deve essere assegnato un ruolo "No access" per ogni oggetto (ad esempio macchine virtuali di altri tenant) che non deve essere accessibile a un particolare tenant. Questa procedura è complicata e al tempo stesso espone a controlli di accesso accidentali, poiché a ogni nuovo oggetto figlio creato viene automaticamente concesso l'accesso ereditato dall'oggetto padre. Di conseguenza è consigliabile adottare il primo approccio.

La procedura di accesso all'account vCenter è la seguente:

1.  Creare un nuovo ruolo clonando il ruolo predefinito "Read-only" e assegnargli un nome appropriato (ad esempio Azure_Site_Recovery usato qui). 
2.  Assegnare al ruolo le autorizzazioni seguenti:
 *  Datastore (Archivio dati) -> Allocate space (Alloca spazio), Browse datastore (Sfoglia archivio dati), Low level file operations (Operazioni file di livello basso), Remove file (Rimuovi file), Update virtual machine files (Aggiorna file macchina virtuale)
 *  Network (Rete) -> Network assign (Assegnazione rete)
 *  Risorsa -> Assegnare VM al pool di risorse, migrare la VM spenta, migrare la VM accesa
 *  Tasks (Attività) -> Create task (Crea attività), Update task (Aggiorna attività)
 *  Virtual machine (Macchina virtuale) -> Configuration (Configurazione)
 *  Macchina virtuale -> Interazione -> Answer question (Rispondi alla domanda), Connessione dispositivo, Configure CD media (Configura supporto CD), Configure floppy media (Configura supporto floppy), Spegni, Power on (Accendi), VMware tools install (Installazione strumenti VMware)
 *  Virtual machine (Macchina virtuale) -> Inventory (Inventario) -> Create (Crea), Register (Registra), Unregister (Annulla registrazione)
 *  Virtual machine (Macchina virtuale) -> Provisioning -> Allow virtual machine download (Consenti download macchina virtuale), Allow virtual machine files upload (Consenti upload file macchina virtuale)
 *  Macchina virtuale -> Snapshot -> Rimuovi snapshot.

    ![role-permissions](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3.  Assegnare il livello di accesso all'account vCenter (usato nel CS del tenant) per oggetti diversi, come segue:

| **Object** | **Ruolo** | **Osservazioni** |
| --- | --- | --- |
| vCenter | Read-Only | Serve solo per consentire l'accesso a vCenter per la gestione di oggetti diversi. Questa autorizzazione può essere rimossa se l'account non dovrà mai essere offerto a un tenant o usato per operazioni di gestione in vCenter. |
| Data center | Azure_Site_Recovery |  |
| Host e cluster host | Azure_Site_Recovery | Assicura nuovamente che l'accesso sia a livello di oggetto, quindi che siano accessibili solo gli host che avranno macchine virtuali tenant prima del failover e dopo il failback. |
| Archivio dati e cluster archivio dati | Azure_Site_Recovery | Vedere sopra. |
| Rete | Azure_Site_Recovery |  |
| Server di gestione | Azure_Site_Recovery | Include l'accesso a tutti i componenti (CS, PS e MT) se al di fuori della macchina CS. |
| Macchine virtuali tenant | Azure_Site_Recovery | Assicura che anche le eventuali macchine virtuali tenant di un particolare tenant abbiano accesso, altrimenti non potranno essere rilevate attraverso il Portale di Azure. |

L'accesso all'account vCenter è ora completato. In questo modo vengono soddisfatti i requisiti minimi di autorizzazioni per completare le operazioni di failback. Si noti che queste autorizzazioni di accesso possono essere usate anche con i criteri esistenti. È sufficiente modificare il set di autorizzazioni esistente in modo che includa le autorizzazioni del ruolo descritte nel punto 2 sopra.

Per limitare le operazioni di ripristino di emergenza fino allo stato di failover, ovvero senza le funzionalità di failback, seguire la procedura descritta sopra ma, anziché assegnare il ruolo "Azure_Site_Recovery" all'account di accesso a vCenter, assegnare solo il ruolo "Read-Only". Questo set di autorizzazioni consente il failover e la replica delle macchine virtuali e non consente il failback. Si noti che tutto il resto del processo precedente rimane invariato. Ogni autorizzazione continua a essere assegnata solo a livello di oggetto e non viene propagata agli oggetti figlio per assicurare l'isolamento del tenant e limitare il rilevamento di macchine virtuali.

## <a name="other-multi-tenant-environments"></a>Altri ambienti multi-tenant

Le indicazioni riportate sopra descrivono i dettagli per la configurazione di un ambiente multi-tenant per una soluzione di hosting condiviso. Le altre due soluzioni principali sono il servizio di hosting dedicato e il servizio gestito. La loro architettura è illustrata di seguito: 

### <a name="dedicated-hosting-solution"></a>Soluzione di hosting dedicato

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)

**Figura 2: Scenario di hosting dedicato con più vCenter**

Qui la differenza in termini di architettura è che il provisioning dell'infrastruttura di ogni tenant viene eseguito solo per il tenant. Il provider di hosting deve continuare a seguire la procedura CSP descritta nei dettagli per l'hosting condiviso, tuttavia non deve preoccuparsi dell'isolamento del tenant poiché i tenant sono isolati tramite vCenter separati. Il provisioning CSP resta invariato.

### <a name="managed-service-solution"></a>Soluzione di servizi gestiti

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)

**Figura 3: Scenario di servizi gestiti con più vCenter**

Qui la differenza in termini di architettura è che l'infrastruttura di ogni tenant è anche fisicamente separata dagli altri tenant. Generalmente questo scenario si verifica quando il tenant è proprietario dell'infrastruttura e desidera che solo un provider di soluzione gestisca il ripristino di emergenza. Il partner deve continuare a seguire la procedura CSP descritta nei dettagli per l'hosting condiviso, tuttavia non deve preoccuparsi dell'isolamento del tenant poiché sono fisicamente isolati tramite diverse infrastrutture. Il provisioning CSP resta invariato.


## <a name="csp-program-overview"></a>Panoramica del programma CPS
Il [programma](https://partner.microsoft.com/en-US/cloud-solution-provider) Cloud Solution Provider (CPS) di Microsoft promuove racconti di collaborazione con i partner per offrire tutti i servizi cloud Microsoft, tra cui Office 365 EMS e Microsoft Azure. Consente ai partner Microsoft di essere proprietari della relazione end-to-end con i clienti e di diventare il punto di contatto primario nella relazione. Tramite CSP, un partner può distribuire le sottoscrizioni di Azure per i clienti e combinarle con le proprie offerte personalizzate a valore aggiunto. 

Nel caso di Azure Site Recovery, i partner possono gestire la soluzione completa di ripristino di emergenza per i clienti direttamente tramite CSP oppure usare CSP per configurare gli ambienti di Azure Site Recovery e consentire ai clienti di gestire le proprie esigenze di ripristino di emergenza in modalità self-service. In entrambi i casi, il partner è il collegamento tra Azure Site Recovery e i clienti finali e il partner provvede alla relazione con il cliente ed emette le fatture relative all'uso di Azure Site Recovery.

## <a name="creating-and-managing-tenant-accounts"></a>Creazione e gestione degli account del tenant

### <a name="step-0-prerequisite-check"></a>Passaggio 0: Controllo dei prerequisiti

I prerequisiti per le macchine virtuali sono quelli descritti nella [documentazione](site-recovery-vmware-to-azure.md) su Azure Site Recovery. Oltre a questi prerequisiti, prima di procedere alla gestione del tenant tramite CSP è necessario aver eseguito anche i controlli di accesso descritti sopra. Creare un server di gestione separato per ogni tenant in grado di comunicare con le macchine virtuali tenant e il vCenter del partner. Solo il partner dispone dei diritti di accesso a questo server.

### <a name="step-1-create-tenant-account"></a>Passaggio 1: Creare l'account del tenant

1.  Accedere al proprio account CSP tramite il [centro per i partner](https://partnercenter.microsoft.com/). Dal menu Dashboard a sinistra selezionare l'opzione Clienti.

    ![csp-dashboard](./media/site-recovery-multi-tenant-support-vmware-using-csp/csp-dashboard-display.png)

2.  Nella pagina visualizzata fare clic sul pulsante Aggiungi cliente.

    ![add-customer](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-new-customer.png)

3.  Nella pagina Nuovo cliente compilare tutti i dettagli sull'account per il tenant e fare clic su Successivo: Sottoscrizioni.

    ![fill-details](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-add-filled.png)

4.  Nella pagina di selezione delle sottoscrizioni scorrere verso il basso per aggiungere la sottoscrizione Microsoft Azure. È possibile aggiungere altre sottoscrizioni subito o in qualsiasi momento nel futuro.

    ![add-subscription](./media/site-recovery-multi-tenant-support-vmware-using-csp/azure-subscription-selection.png)

5.  Proseguire e nella pagina successiva esaminare tutti i dettagli immessi per il tenant, quindi fare clic sul pulsante Invia.

    ![customer-summary](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-summary-page.png)

6.  Dopo aver creato il cliente, viene visualizzata una pagina di conferma con i dettagli dell'account predefinito e la password per la sottoscrizione. Salvare le informazioni e modificare la password in un secondo momento in base alla necessità mediante l'accesso al Portale di Azure. Queste informazioni possono essere condivisi così come sono con il tenant oppure, se necessario, è possibile creare e condividere un account separato.

### <a name="step-2-access-tenant-account"></a>Passaggio 2: Accedere all'account tenant

1.  È possibile accedere alla sottoscrizione del tenant dalla pagina Clienti tramite il dashboard, come descritto al passaggio 1. Accedere alla pagina e fare clic sul nome dell'account tenant appena creato.
2.  Verrà visualizzata la sezione Sottoscrizioni dell'account del tenant; da qui è possibile monitorare le sottoscrizioni esistenti per l'account e aggiungerne altre in base alle esigenze. Per gestire le operazioni di ripristino di emergenza del tenant, selezionare l'opzione All resources (Azure portal) (Tutte le sottoscrizioni (Portale di Azure)) a destra della pagina. 

    ![all-resources](./media/site-recovery-multi-tenant-support-vmware-using-csp/all-resources-select.png)

3.  Facendo clic sul pulsante All resources (Tutte le risorse) viene concesso l'accesso alle sottoscrizioni di Azure del tenant ed è possibile verificare lo stesso selezionando l'opzione AAD visualizzata in alto a destra nel Portale di Azure. 

    ![aad-admin](./media/site-recovery-multi-tenant-support-vmware-using-csp/aad-admin-display.png)

A questo punto è possibile eseguire tutte le operazioni di Site Recovery per il tenant tramite il Portale di Azure e gestire le operazioni di ripristino di emergenza. Il processo descritto sopra deve essere seguito ogni volta per accedere alla sottoscrizione tenant tramite CSP per il ripristino di emergenza gestito.

### <a name="step-3-deploy-resources-to-tenant-subscription"></a>Passaggio 3: Distribuire le risorse alla sottoscrizione tenant
1.  Nel portale di Azure creare un gruppo di risorse e distribuire un insieme di credenziali dei servizi di ripristino come da processo consueto. Scaricare la chiave di registrazione dell'insieme di credenziali,
2.  Registrare il CS per il tenant mediante la chiave di registrazione dell'insieme di credenziali.
3.  Immettere le credenziali per i due account di accesso, cioè l'account di accesso a vCenter e l'account di accesso della macchina virtuale.

    ![config-accounts](./media/site-recovery-multi-tenant-support-vmware-using-csp/config-server-account-display.png)

### <a name="step-4-register-site-recovery-infrastructure-to-recovery-services-vault"></a>Passaggio 4: Registrare l'infrastruttura di Site Recovery all'insieme di credenziali dei servizi di ripristino
1.  Aprire il Portale di Azure e nell'insieme di credenziali creato in precedenza registrare il server vCenter al CS registrato nel passaggio precedente. A questo scopo usare l'account di accesso a vCenter.
2.  Completare il processo di preparazione dell'infrastruttura per Site Recovery come da processo consueto.
3.  Le macchine virtuali sono ora pronte per essere replicate. Verificare che nel pannello di selezione delle macchine virtuali nell'opzione Replica siano visibili solo le macchine virtuali del tenant.

    ![tenant-vms](./media/site-recovery-multi-tenant-support-vmware-using-csp/tenant-vm-display.png)

### <a name="step-5-assign-tenant-access-to-subscription"></a>Passaggio 5: Assegnare l'accesso tenant alla sottoscrizione

Per il caso di ripristino di emergenza self-service è necessario specificare al tenant i dettagli dell'account come indicato nella parte 6 del passaggio 1. Questa operazione deve essere eseguita dopo che il partner ha configurato l'infrastruttura di ripristino di emergenza. A prescindere dal tipo di ripristino di emergenza (gestito o self-service), il partner deve accedere alle sottoscrizioni tenant solo tramite il portale CSP, quindi deve configurare l'insieme di credenziali e registrare l'infrastruttura di proprietà del partner alle sottoscrizioni tenant.

Un partner può anche aggiungere un nuovo utente alla sottoscrizione tenant tramite il portale CSP come indicato di seguito:

1.  Accedere alla pagina della sottoscrizione CSP specifica del tenant e selezionare l'opzione Users and licenses (Utenti e licenze).
    
    ![user-licenses](./media/site-recovery-multi-tenant-support-vmware-using-csp/users-and-licences.png)

    È ora possibile creare un nuovo utente immettendo i dettagli più importanti e selezionando le autorizzazioni oppure, in alternativa, caricando l'elenco di utenti tramite un file CSV. 
2.  Dopo aver creato gli utenti, tornare al Portale di Azure e selezionare la sottoscrizione pertinente nel pannello Sottoscrizione. 
3.  Nel nuovo pannello visualizzato selezionare Controllo di accesso (IAM) e fare clic su +Aggiungi per aggiungere un utente con il livello di accesso pertinente. Gli utenti creati tramite il portale CSP verranno automaticamente visualizzati nel pannello aperto dopo aver fatto clic su un livello di accesso.

    ![user-subscription](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-user-subscription.png)

    Per la maggior parte delle operazioni di gestione, è sufficiente il ruolo di collaboratore. Un utente con questo livello di accesso può eseguire qualsiasi operazione su una sottoscrizione, tranne modificare i livelli di accesso (per questa operazione è richiesto il livello di accesso Proprietario). È inoltre possibile ritoccare i livelli di accesso in base alle esigenze.





<!--HONumber=Dec16_HO2-->


