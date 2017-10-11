---
title: Eseguire la replica di una distribuzione Citrix XenDesktop e XenApp con Azure Site Recovery | Microsoft Docs
description: Questo articolo illustra come proteggere e ripristinare le distribuzioni Citrix XenDesktop e XenApp con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: ponatara
ms.openlocfilehash: dc064352b1841ff346b705dc63186b12d79350b3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-a-multi-tier-citrix-xenapp-and-xendesktop-deployment-using-azure-site-recovery"></a>Eseguire la replica di una distribuzione Citrix XenApp e XenDesktop multilivello con Azure Site Recovery

## <a name="overview"></a>Panoramica

Citrix XenDesktop è una soluzione di virtualizzazione del desktop che fornisce desktop e applicazioni come servizi su richiesta a qualsiasi utente, ovunque si trovi. Con la tecnologia di distribuzione FlexCast, XenDesktop può distribuire in modo rapido e sicuro applicazioni e desktop agli utenti.
Citrix XenApp non fornisce attualmente funzionalità di ripristino di emergenza.

Una soluzione di ripristino di emergenza valida deve consentire la modellazione dei piani di ripristino per le architetture di applicazioni complesse descritte in precedenza e consentire anche l'aggiunta di passaggi personalizzati per gestire i mapping delle applicazioni tra i vari livelli, fornendo una soluzione rapida e sicura in caso di emergenza, con un RTO inferiore.

Questo documento fornisce indicazioni dettagliate per la creazione di una soluzione di ripristino di emergenza per le distribuzioni Citrix XenApp locali in piattaforme Hyper-V e VMware vSphere. Questo documento illustra anche come eseguire un failover di test (esercitazione per il ripristino di emergenza) e un failover non pianificato in Azure usando piani di ripristino, configurazioni supportate e prerequisiti.


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario comprendere i concetti illustrati di seguito:

1. [Replica di una macchina virtuale in Azure](site-recovery-vmware-to-azure.md)
1. Come [progettare una rete di ripristino](site-recovery-network-design.md)
1. [Esecuzione di un failover di test in Azure](site-recovery-test-failover-to-azure.md)
1. [Esecuzione di un failover in Azure](site-recovery-failover.md)
1. Come [replicare un controller di dominio](site-recovery-active-directory.md)
1. Come [replicare SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Modelli di distribuzione

Una farm Citrix XenApp e XenDesktop presenta in genere questo modello di distribuzione:

**Modello di distribuzione**

Distribuzione Citrix XenApp e XenDesktop con server DNS di AD, server di database SQL, Citrix Delivery Controller, server StoreFront, XenApp Master (VDA), server licenze di Citrix XenApp

![Modello di distribuzione 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Supporto di Site Recovery

Per le finalità di questo articolo, sono state usate distribuzioni Citrix in macchine virtuali VMware gestite da vSphere 6.0 / System Center VMM 2012 R2 per configurare il ripristino di emergenza.

### <a name="source-and-target"></a>Origine e destinazione

**Scenario** | **In un sito secondario** | **In Azure**
--- | --- | ---
**Hyper-V** | Non nell'ambito | Sì
**VMware** | Non nell'ambito | Sì
**Server fisico** | Non nell'ambito | Sì

### <a name="versions"></a>Versioni
I clienti possono distribuire componenti di XenApp come macchine virtuali in esecuzione su Hyper-V o VMware oppure come server fisici. Azure Site Recovery può proteggere le distribuzioni fisiche e virtuali in Azure.
Poiché XenApp 7.7 è supportato in Azure, è possibile eseguire il failover in Azure solo delle distribuzioni con queste versioni per il ripristino di emergenza o la migrazione.

### <a name="things-to-keep-in-mind"></a>Aspetti da considerare

1. Sono supportati la protezione e il ripristino delle distribuzioni locali con computer con sistema operativo Server per fornire app XenApp pubblicate e desktop XenApp pubblicati.

2. Non sono supportati la protezione e il ripristino di distribuzioni locali con computer con sistema operativo Desktop per fornire VDI desktop per desktop virtuali client, incluso Windows 10. Azure Site Recovery non supporta infatti il ripristino di computer con sistemi operativi Desktop.  Alcuni tipi di desktop virtuale client, ad esempio Windows 7, non sono inoltre ancora supportati per le licenze in Azure. [Altre informazioni](https://azure.microsoft.com/pricing/licensing-faq/) sulle licenze per computer desktop client/server in Azure.

3.  Azure Site Recovery non può replicare e proteggere cloni MCS o PVS locali esistenti.
È necessario ricreare questi cloni usando il provisioning di Azure RM dal controller di distribuzione.

4. NetScaler non può essere protetto tramite Azure Site Recovery perché NetScaler è basato su FreeBSD e Azure Site Recovery non supporta la protezione del sistema operativo FreeBSD. Sarebbe necessario distribuire e configurare una nuova appliance NetScaler da Azure Market dopo il failover in Azure.


## <a name="replicating-virtual-machines"></a>Replica di macchine virtuali

I componenti seguenti della distribuzione Citrix XenApp devono essere protetti per consentire la replica e il ripristino.

* Protezione del server DNS di AD
* Protezione del server di database SQL
* Protezione di Citrix Delivery Controller
* Protezione del server StoreFront
* Protezione di XenApp Master (VDA)
* Protezione del server licenze di Citrix XenApp


**Replica del server DNS di AD**

Per indicazioni sulla replica e sulla configurazione di un controller di dominio in Azure, vedere [Proteggere Active Directory e DNS con Azure Site Recovery](site-recovery-active-directory.md).

**Replica del server di database SQL**

Per informazioni tecniche dettagliate sulle opzioni consigliate per la protezione dei server SQL, vedere [Proteggere SQL Server con il ripristino di emergenza di SQL Server e Azure Site Recovery](site-recovery-sql.md).

Per avviare la replica delle macchine virtuali degli altri componenti in Azure, vedere [queste indicazioni](site-recovery-vmware-to-azure.md).

![Protezione dei componenti di XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Impostazioni di Calcolo e rete**

Dopo avere completato la protezione dei computer (stato "Protetto" in Elementi replicati), è necessario configurare le impostazioni di Calcolo e rete.
In Calcolo e rete > Proprietà di calcolo è possibile specificare il nome e le dimensioni di destinazione della VM di Azure.
Se necessario, modificare il nome in modo che sia conforme ai requisiti di Azure. È anche possibile visualizzare e aggiungere le informazioni sulla rete di destinazione, la subnet e l'indirizzo IP che verranno assegnati alla macchina virtuale di Azure.

Tenere presente quanto segue:

* È possibile impostare l'indirizzo IP di destinazione. Se non si specifica un indirizzo, il computer di cui è stato eseguito il failover usa DHCP. Se si imposta un indirizzo che non è disponibile al momento del failover, il failover non riesce. Se l'indirizzo è disponibile nella rete di failover di test, è possibile usare lo stesso indirizzo IP di destinazione per il failover di test.

* Per il server di AD/DNS il mantenimento dell'indirizzo locale consente di specificare lo stesso indirizzo del server DNS per la rete virtuale di Azure.

Il numero di schede di rete dipende dalle dimensioni specificate per la macchina virtuale di destinazione, come illustrato di seguito:

*   Se il numero di schede di rete nella macchina di origine è minore o uguale al numero di schede consentite per la macchina di destinazione, la destinazione avrà lo stesso numero di schede dell’origine.
*   Se il numero di schede per la macchina virtuale di origine supera il numero consentito per le dimensioni di destinazione, verrà utilizzata la dimensione di destinazione massima.
* Ad esempio, se una macchina di origine ha due schede di rete e le dimensioni della macchina di destinazione ne supportano quattro, la macchina di destinazione avrà due schede. Se la macchina di origine dispone di due schede ma le dimensioni di destinazione supportate ne consentono solo una, la macchina di destinazione avrà una sola scheda.
*   Se la macchina virtuale ha più schede di rete, si connetteranno tutte alla stessa rete.
*   Se la macchina virtuale ha più schede di rete, la prima nell'elenco diventa la scheda di rete predefinita nella macchina virtuale di Azure.


## <a name="creating-a-recovery-plan"></a>Creazione di un piano di ripristino

Dopo l'abilitazione della replica per le VM del componente XenApp, il passaggio successivo consiste nella creazione di un piano di ripristino.
Un piano di ripristino raggruppa le macchine virtuali con requisiti simili per il failover e il ripristino.  

**Procedura per la creazione di un piano di ripristino**

1. Aggiungere le macchine virtuali del componente XenApp nel piano di ripristino.
2. Fare clic su Piani di ripristino -> + Piano di ripristino. Specificare un nome intuitivo per il piano di ripristino.
3. Per macchine virtuali VMware: selezionare il server di elaborazione VMware come origine, Microsoft Azure come destinazione e Resource Manager come modello di distribuzione, quindi fare clic su Seleziona elementi.
4. Per macchine virtuali Hyper-V: selezionare il server VMM come origine, Microsoft Azure come destinazione e Resource Manager come modello di distribuzione, quindi fare clic su Seleziona elementi e selezionare le VM della distribuzione XenApp.

### <a name="adding-virtual-machines-to-failover-groups"></a>Aggiunta di macchine virtuali a gruppi di failover

I piani di ripristino possono essere personalizzati per aggiungere gruppi di failover per specificare determinati ordini di avvio, script o azioni manuali. I gruppi seguenti devono essere aggiunti al piano di ripristino.

1. Gruppo di failover 1: DNS di AD
2. Gruppo di failover 2: VM SQL Server
2. Gruppo di failover 3: VM di immagine master VDA
3. Gruppo di failover 4: controller di distribuzione e VM del server StoreFront


### <a name="adding-scripts-to-the-recovery-plan"></a>Aggiunta di script al piano di ripristino

Gli script possono essere eseguiti prima o dopo un gruppo specifico in un piano di ripristino. È anche possibile includere azioni manuali ed eseguirle durante il failover.

Il piano di ripristino personalizzato ha un aspetto simile al seguente:

1. Gruppo di failover 1: DNS di AD
2. Gruppo di failover 2: VM SQL Server
3. Gruppo di failover 3: VM di immagine master VDA

   >[!NOTE]     
   >I passaggi 4, 6 e 7 contenenti le azioni manuali o di script sono applicabili solo a un ambiente XenApp locale con cataloghi MCS/PVS.

4. Gruppo 3 - Azione manuale o di script: arresto della VM VDA master. Quando viene eseguito il failover della VM VDA master in Azure, lo stato della VM sarà In esecuzione. Per creare nuovi cataloghi MCS usando l'hosting di Azure ARM, è necessario che lo stato della VM VDA master sia Arrestato (deallocato). Arrestare la VM dal portale di Azure.

5. Gruppo di failover 4: controller di distribuzione e VM del server StoreFront
6. Gruppo 3 - Azione manuale o di script 1:

    ***Aggiungere una connessione host di Azure RM***

    Creare una connessione host di Azure ARM nel computer del controller di distribuzione per effettuare il provisioning di nuovi cataloghi MCS in Azure. Seguire la procedura illustrata in questo [articolo](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Gruppo 3 - Azione manuale o di script 2:

    ***Ricreare i cataloghi MCS in Azure***

    I cloni MCS o PVS esistenti nel sito primario non verranno replicati in Azure. È necessario ricreare questi cloni usando VDA master sottoposto a replica e il provisioning di Azure ARM dal controller di distribuzione. Seguire la procedura illustrata in questo [articolo](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) per creare cataloghi MCS in Azure.

![Piano di ripristino per i componenti XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >È possibile usare gli script disponibili in questa [pagina](https://github.com/Azure/azure-quickstart-templates/blob/>master/asr-automation-recovery/scripts) per aggiornare DNS con i nuovi indirizzi IP delle macchine virtuali sottoposte a failover oppure per collegare un servizio di bilanciamento del carico alle macchine virtuali sottoposte a failover, se necessario.


## <a name="doing-a-test-failover"></a>Esecuzione di un failover di test

Seguire [queste linee guida](site-recovery-test-failover-to-azure.md) per eseguire un failover di test.

![Piano di ripristino](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Esecuzione di un failover

Seguire [queste linee guida](site-recovery-failover.md) quando si esegue un failover.

## <a name="next-steps"></a>Passaggi successivi

Per [altre informazioni](https://aka.ms/citrix-xenapp-xendesktop-with-asr) sulla replica di distribuzioni Citrix XenApp e XenDesktop, vedere questo white paper. Sono disponibili indicazioni sulla [replica di altre applicazioni](site-recovery-workload.md) con Site Recovery.
