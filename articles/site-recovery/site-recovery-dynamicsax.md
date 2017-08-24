---
title: Eseguire la replica di una distribuzione di Dynamics AX multilivello con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come eseguire la replica e proteggere Dynamics AX usando Azure Site Recovery
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: asgang
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: cf568d20f60709dbb64774bcbcc1b4aa6c43d8d3
ms.contentlocale: it-it
ms.lasthandoff: 06/16/2017


---
# <a name="replicate-a-multi-tier-dynamics-ax-application-using-azure-site-recovery"></a>Eseguire la replica di un'applicazione Dynamics AX multilivello usando Azure Site Recovery

## <a name="overview"></a>Panoramica


Microsoft Dynamics AX è una delle soluzioni ERP più diffuse tra le aziende per standardizzare i processi tra località, gestire le risorse e semplificare la conformità. Poiché si tratta di un'applicazione aziendale critica per un'organizzazione, è molto importante fare in modo che l'applicazione sia operativa in tempi minimi nel caso di un evento di emergenza.

Attualmente Microsoft Dynamics AX non include funzionalità di ripristino di emergenza predefinite. Microsoft Dynamics AX include molti componenti server come Application Object Server (AOS), Active Directory, il server di database SQL, SharePoint Server, Reporting Server e così via. La gestione manuale del ripristino di emergenza di ognuno di questi componenti non solo è costosa, ma è anche soggetta a errori.

Questo articolo descrive dettagliatamente come creare una soluzione di ripristino di emergenza per l'applicazione Dynamics AX usando [Azure Site Recovery](site-recovery-overview.md). Vengono anche descritti i failover pianificati/non pianificati/di test tramite piano di ripristino con un solo clic, le configurazioni supportate e i prerequisiti.
La soluzione di ripristino di emergenza basata su Azure Site Recovery è completamente testata, certificata e consigliata da Microsoft Dynamics AX.



## <a name="prerequisites"></a>Prerequisiti

Per l'implementazione del ripristino di emergenza per l'applicazione Dynamics AX con Azure Site Recovery, è necessario soddisfare i prerequisiti seguenti.

•   Configurare una distribuzione di Dynamics AX locale

•   Creare un insieme di credenziali dei servizi di Azure Site Recovery nella sottoscrizione di Microsoft Azure

•   Se Azure è il sito di ripristino, eseguire lo strumento di valutazione della conformità delle macchine virtuali di Azure nelle VM per garantire che siano compatibili con le VM di Azure e i servizi di Azure Site Recovery


## <a name="site-recovery-support"></a>Supporto di Site Recovery

Ai fini di questo articolo sono state usate macchine virtuali VMware con Dynamics AX 2012 R3 su Windows Server 2012 R2 Enterprise. Poiché la replica di Site Recovery è indipendente dall'applicazione, i consigli inclusi in questo articolo saranno validi anche per gli scenari seguenti.

### <a name="source-and-target"></a>Origine e destinazione

**Scenario** | **In un sito secondario** | **In Azure**
--- | --- | ---
**Hyper-V** | Sì | Sì
**VMware** | Sì | Sì
**Server fisico** | Sì | Sì

## <a name="enable-dr-of-dynamics-ax-application-using-asr"></a>Abilitare il ripristino di emergenza dell'applicazione Dynamics AX con Azure Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Proteggere l'applicazione Dynamics AX
Ogni componente di Dynamics AX deve essere protetto per consentire la replica e il ripristino completi dell'applicazione. Questa sezione descrive queste operazioni:

**1. Protezione di Active Directory**

**2. Protezione del livello SQL**

**3. Protezione dei livelli app e Web**

**4. Configurazione delle impostazioni di rete**

**5. Piano di ripristino**

### <a name="1-setup-ad-and-dns-replication"></a>1. Configurare la replica di Active Directory e DNS

Ai fini del funzionamento dell'applicazione Dynamics AX, nel sito di ripristino di emergenza deve essere presente Active Directory. Le scelte consigliate sono due, a seconda della complessità dell'ambiente locale del cliente.

**Opzione 1**

Se il cliente ha un numero limitato di applicazioni e un singolo controller di dominio per l'intero sito locale ed eseguirà il failover dell'intero sito, è consigliabile usare ASR-Replication per la replica del computer del controller di dominio nel sito secondario (applicabile sia per l'operazione da sito a sito sia per l'operazione da sito ad Azure).

**Opzione 2**

Se il cliente ha un numero elevato di applicazioni, esegue una foresta Active Directory ed eseguirà il failover di poche applicazioni per volta, è consigliabile configurare un controller di dominio aggiuntivo nel sito di ripristino di emergenza (sito secondario in Azure).

Fare riferimento alla [guida complementare su come rendere disponibile un controller di dominio nel sito di ripristino di emergenza](site-recovery-active-directory.md). Nella parte restante di questo documento si presuppone che nel sito di ripristino di emergenza sia disponibile un controller di dominio.

### <a name="2-setup-sql-server-replication"></a>2. Configurare la replica di SQL Server
Per indicazioni tecniche dettagliate sulle opzioni consigliate per la protezione del [livello SQL](site-recovery-sql.md), fare riferimento alla guida complementare.

### <a name="3-enable-protection-for-dynamics-ax-client-and-aos-vms"></a>3. Abilitare la protezione per le VM AOS e client Dynamics AX
Eseguire la configurazione di Azure Site Recovery pertinente a seconda che le VM vengano distribuite in [Hyper-V](site-recovery-hyper-v-site-to-azure.md) o [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> La frequenza consigliata per la coerenza in caso di arresto anomalo è 15 minuti.
>

Lo snapshot seguente mostra lo stato di protezione delle VM del componente Dynamics in uno scenario di protezione "Da sito VMware ad Azure".
![Elementi protetti ](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Configurare le impostazioni di rete
Configurare le impostazioni di calcolo e di rete delle VM

Per le VM AOS e client AX, configurare le impostazioni di rete in Azure Site Recovery in modo che le reti delle VM siano collegate alla rete di ripristino di emergenza corretta dopo il failover. Assicurarsi che la rete di ripristino di emergenza per questi livelli possa essere instradata al livello SQL.

È possibile selezionare la VM negli elementi replicati per configurare le impostazioni di rete come mostrato nello snapshot seguente.

* Per i server AOS, selezionare il set di disponibilità corretto.

* Se si usa un indirizzo IP statico, specificare l'indirizzo IP che dovrà essere usato dalla macchina virtuale nel campo **IP di destinazione** ![Impostazioni di rete ](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)



### <a name="5-creating-a-recovery-plan"></a>5. Creazione di un piano di ripristino

È possibile creare un piano di ripristino in Azure Site Recovery per automatizzare il processo di failover. Aggiungere il livello app e il livello Web nel piano di ripristino. Ordinare i livelli in gruppi diversi in modo che il front-end si arresti prima del livello app.

1)  Selezionare l'insieme di credenziali di Azure Site Recovery nella sottoscrizione e fare clic sul riquadro "Piani di ripristino".

2)  Fare clic su "Crea piano di ripristino" e specificare un nome.

3)  Selezionare i valori desiderati per "Origine" e "Destinazione". La destinazione può essere Azure o il sito secondario. Se si sceglie Azure, è necessario specificare il modello di distribuzione

![Crea piano di ripristino](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4)  Selezionare le VM AOS e client nel piano di ripristino e fare clic su ✓.
![Crea piano di ripristino](./media/site-recovery-dynamics-ax/selectvms.png)


![Piano di ripristino](./media/site-recovery-dynamics-ax/recoveryplan.png)

È possibile personalizzare il piano di ripristino per l'applicazione Dynamics AX aggiungendo diversi passaggi, come descritto di seguito. Lo snapshot precedente mostra il piano di ripristino completo dopo aver aggiunto tutti i passaggi.

*Passaggi:*

*1. Passaggi di failover di SQL Server*

Fare riferimento alla guida complementare [Soluzione di ripristino di emergenza di SQL Server](site-recovery-sql.md) per informazioni sui passaggi di ripristino specifici per SQL server.

*2. Gruppo di failover 1: eseguire il failover delle VM AOS*

Assicurarsi che il punto di recupero selezionato sia quanto più vicino al ripristino temporizzato del database, ma che non sia in anticipo.

*3. Script: aggiungere il bilanciamento del carico (solo E-A)*. Aggiungere uno script (tramite l'automazione di Azure) dopo la visualizzazione del gruppo di VM AOS per aggiungere il bilanciamento del carico. A questo scopo, è possibile usare uno script. Fare riferimento all'articolo [How to add load balancer for multi-tier application DR](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/) (Come aggiungere il bilanciamento del carico per il ripristino di emergenza di applicazioni multilivello)

*4. Gruppo di failover 2: eseguire il failover delle VM client AX.*
Eseguire il failover delle VM di livello Web come parte del piano di ripristino.


### <a name="doing-a-test-failover"></a>Esecuzione di un failover di test

Fare riferimento alle guide complementari "Soluzione di ripristino di emergenza di Active Directory" e "Soluzione di ripristino di emergenza di SQL Server" per le considerazioni specifiche rispettivamente di Active Directory e SQL Server durante il failover di test.

1.  Accedere al portale di Azure e selezionare l'insieme di credenziali di Site Recovery.
2.  Fare clic sul piano di ripristino creato per Dynamics AX.
3.  Fare clic su "Failover di test".
4.  Selezionare la rete virtuale per avviare il processo di failover di test.
5.  Quando l'ambiente secondario è disponibile, è possibile eseguire le convalide.
6.  Al termine delle convalide è possibile selezionare 'Convalide complete' per pulire l'ambiente di failover di test.

Seguire [queste linee guida](site-recovery-test-failover-to-azure.md) per eseguire un failover di test.

### <a name="doing-a-failover"></a>Esecuzione di un failover

1.  Accedere al portale di Azure e selezionare l'insieme di credenziali di Site Recovery.
2.  Fare clic sul piano di ripristino creato per Dynamics AX.
3.  Fare clic su "Failover" e selezionare "Failover".
4.  Selezionare la rete di destinazione e quindi fare clic su ✓ per avviare il processo di failover.

Seguire [queste linee guida](site-recovery-failover.md) quando si esegue un failover.

### <a name="perform-a-failback"></a>Eseguire un failback

Fare riferimento alla guida complementare "Soluzione di ripristino di emergenza di SQL Server" per le considerazioni specifiche di SQL Server durante il failback.

1.  Accedere al portale di Azure e selezionare l'insieme di credenziali di Site Recovery.
2.  Fare clic sul piano di ripristino creato per Dynamics AX.
3.  Fare clic su "Failover" e selezionare il failover.
4.  Fare clic su "Cambia direzione".
5.  Selezionare le opzioni di sincronizzazione dei dati e di creazione delle VM appropriate
6.  Fare clic su ✓ per avviare il processo di failback.


Seguire [queste linee guida](site-recovery-failback-azure-to-vmware.md) quando si esegue un failback.

##<a name="summary"></a>Riepilogo
Con Azure Site Recovery è possibile creare un piano di ripristino di emergenza completamente automatico per l'applicazione Dynamics AX. È possibile avviare il failover in pochi secondi da qualsiasi luogo in caso di un'interruzione, perché l'applicazione sia operativa in pochi minuti.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla protezione dei carichi di lavoro aziendali con Azure Site Recovery, leggere [Quali carichi di lavoro è possibile proteggere?](site-recovery-workload.md).

