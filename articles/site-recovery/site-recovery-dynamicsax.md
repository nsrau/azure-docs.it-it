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
ms.date: 12/11/2017
ms.author: asgang
ms.openlocfilehash: 8ffc4a5a573b1c5951fab98fb766aed36f626fe4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="replicate-a-multitier-dynamics-ax-application-by-using-azure-site-recovery"></a>Eseguire la replica di un'applicazione Dynamics AX multilivello usando Azure Site Recovery

## <a name="overview"></a>Panoramica


 Dynamics AX è una delle soluzioni ERP più diffuse tra le aziende per standardizzare i processi tra località, gestire le risorse e semplificare la conformità. Trattandosi di un'applicazione critica per un'organizzazione, in caso di emergenza l'applicazione deve essere operativa in tempi minimi.

Dynamics AX non include attualmente funzionalità di ripristino di emergenza predefinite. Dynamics AX include molti componenti server come Windows Application Object Server, Azure Active Directory, database SQL di Azure, SharePoint Server, Reporting Services. La gestione manuale del ripristino di emergenza di ognuno di questi componenti non solo è costosa, ma è anche soggetta a errori.

Questo articolo spiega come creare una soluzione di ripristino di emergenza per l'applicazione Dynamics AX usando [Azure Site Recovery](site-recovery-overview.md). Vengono anche descritti i failover di test pianificati/non pianificati tramite un piano di ripristino con un solo clic, le configurazioni supportate e i prerequisiti.



## <a name="prerequisites"></a>Prerequisiti

Per l'implementazione del ripristino di emergenza per l'applicazione Dynamics AX con Site Recovery è necessario soddisfare i prerequisiti seguenti:

• Configurare una distribuzione di Dynamics AX locale.

• Creare un insieme di credenziali di Site Recovery in una sottoscrizione di Azure.

• Se Azure è il sito di ripristino, eseguire lo strumento Azure Virtual Machine Readiness Assessment nelle macchine virtuali. Le macchine virtuali devono essere compatibili con i servizi Macchine virtuali di Azure e Site Recovery.

## <a name="site-recovery-support"></a>Supporto di Site Recovery

Ai fini di questo articolo sono state usate macchine virtuali VMware con Dynamics AX 2012 R3 in Windows Server 2012 R2 Enterprise. Dato che la replica di Site Recovery è indipendente dall'applicazione, si prevede che i consigli inclusi in questo articolo siano validi per gli scenari seguenti.

### <a name="source-and-target"></a>Origine e destinazione

**Scenario** | **In un sito secondario** | **In Azure**
--- | --- | ---
**Hyper-V** | Sì | Sì
**VMware** | Sì | Sì
**Server fisico** | Sì | Sì

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Abilitare il ripristino di emergenza dell'applicazione Dynamics AX tramite Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Proteggere l'applicazione Dynamics AX
Per abilitare la replica e il ripristino completi dell'applicazione, ogni componente di Dynamics AX deve essere protetto. 

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Configurare la replica di Active Directory e DNS

Ai fini del funzionamento dell'applicazione Dynamics AX, nel sito di ripristino di emergenza deve essere presente Active Directory. Le scelte consigliate sono due, a seconda della complessità dell'ambiente locale del cliente.

**Opzione 1**

Il cliente ha un numero ridotto di applicazioni e un solo controller di dominio per l'intero sito locale e prevede di eseguire il failover dell'intero sito. È consigliabile usare la replica di Site Recovery per replicare il controller di dominio in un sito secondario. Questa soluzione è applicabile sia alla replica da sito a sito che da sito ad Azure.

**Opzione 2**

Il cliente ha un numero elevato di applicazioni, esegue una foresta Active Directory e prevede di eseguire il failover di poche applicazioni per volta. È consigliabile configurare un controller di dominio aggiuntivo nel sito di ripristino di emergenza (sito secondario o Azure).

 Per altre informazioni, vedere come [rendere disponibile un controller di dominio in un sito di ripristino di emergenza](site-recovery-active-directory.md). Nella parte restante di questo documento, si presuppone che sia disponibile un controller di dominio nel sito di ripristino di emergenza.

### <a name="2-set-up-sql-server-replication"></a>2. Configurare la replica di SQL Server
Per informazioni tecniche sull'opzione consigliata per la protezione del livello SQL, vedere come [eseguire la replica delle applicazioni con SQL Server e Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Abilitare la protezione per il client Dynamics AX e le VM AOS (Application Object Server)
Eseguire la configurazione di Site Recovery pertinente a seconda che le VM vengano distribuite in [Hyper-V](site-recovery-hyper-v-site-to-azure.md) o [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> La frequenza consigliata per la coerenza in caso di arresto anomalo è 15 minuti.
>

Lo snapshot seguente illustra lo stato di protezione delle VM del componente Dynamics in uno scenario di protezione da sito VMware ad Azure.

![Elementi protetti](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Configurare le impostazioni di rete
**Configurare le impostazioni di calcolo e di rete delle VM**

Per le macchine virtuali AOS (Application Object Server) e il client AX, configurare le impostazioni di rete in Site Recovery in modo che le reti delle macchine virtuali siano collegate alla rete di ripristino di emergenza corretta dopo il failover. Assicurarsi che la rete di ripristino di emergenza per questi livelli possa essere instradata al livello SQL.

È possibile selezionare la VM negli elementi replicati per configurare le impostazioni di rete, come illustrato nello snapshot seguente:

* Per i server AOS (Application Object Server), selezionare il set di disponibilità corretto.

* Se si usa un indirizzo IP statico, specificare l'indirizzo IP che dovrà essere usato dalla macchina virtuale nella casella di testo **IP di destinazione**.

    ![Impostazioni di rete ](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png).


### <a name="5-create-a-recovery-plan"></a>5. Creare un piano di ripristino

È possibile creare un piano di ripristino in Site Recovery per automatizzare il processo di failover. Aggiungere un livello app e il livello Web nel piano di ripristino. Ordinare i livelli in gruppi diversi in modo che il front-end si arresti prima del livello app.

1. Selezionare l'insieme di credenziali di Site Recovery nella sottoscrizione e selezionare il riquadro **Piani di ripristino**.

2. Selezionare **+ Piano di ripristino** e specificare un nome.

3. Selezionare i valori desiderati per **Origine** e **Destinazione**. La destinazione può essere Azure o un sito secondario. Se si sceglie Azure, è necessario specificare il modello di distribuzione.

    ![Crea piano di ripristino](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Selezionare il server AOS (Application Object Server) e le VM client per il piano di ripristino e quindi selezionare ✓.

    ![Selezionare gli elementi](./media/site-recovery-dynamics-ax/selectvms.png)

    Esempio di piano di ripristino:

    ![Dettagli del piano di ripristino](./media/site-recovery-dynamics-ax/recoveryplan.png)

È possibile personalizzare il piano di ripristino per l'applicazione Dynamics AX aggiungendo i passaggi seguenti. Lo snapshot precedente illustra il piano di ripristino completo dopo aver aggiunto tutti i passaggi.


* **Passaggi di failover di SQL Server**: per informazioni sulla procedura di ripristino specifica per SQL server, vedere come [eseguire la replica di applicazioni con SQL Server e Azure Site Recovery](site-recovery-sql.md).

* **Gruppo di failover 1**: eseguire il failover delle macchine virtuali AOS (Application Object Server).
Assicurarsi che il punto di ripristino selezionato sia il più vicino possibile al ripristino temporizzato del database, senza tuttavia precederlo.

* **Script**: aggiungere il bilanciamento del carico (solo E-A).
Aggiungere uno script tramite Automazione di Azure dopo la visualizzazione del gruppo di VM AOS (Application Object Server) per aggiungere il bilanciamento del carico. A questo scopo, è possibile usare uno script. Per altre informazioni, vedere [come aggiungere un bilanciamento del carico per il ripristino di emergenza di applicazioni multilivello](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Gruppo di failover 2**: eseguire il failover delle macchine virtuali client Dynamics AX. Eseguire il failover delle macchine virtuali di livello Web come parte del piano di ripristino.


### <a name="perform-a-test-failover"></a>Eseguire un failover di test

Per altre informazioni relative ad Active Directory durante il failover di test, vedere la guida complementare relativa alla soluzione di ripristino di emergenza di Active Directory. 

Per altre informazioni relative a SQL Server durante il failover di test, vedere come [eseguire la replica di applicazioni con SQL Server e Azure Site Recovery](site-recovery-sql.md).

1. Accedere al portale di Azure e selezionare l'insieme di credenziali di Site Recovery.

2. Selezionare il piano di ripristino creato per Dynamics AX.

3. Selezionare **Failover di test**.

4. Selezionare la rete virtuale per avviare il processo di failover di test.

5. Quando l'ambiente secondario è disponibile, è possibile eseguire le convalide.

6. Al termine delle convalide è possibile selezionare **Convalide complete** per pulire l'ambiente di failover di test.

Per altre informazioni sull'esecuzione di un failover di test, vedere [Failover di test in Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Eseguire un failover

1. Accedere al portale di Azure e selezionare l'insieme di credenziali di Site Recovery.

2. Selezionare il piano di ripristino creato per Dynamics AX.

3. Selezionare **Failover** e quindi **Failover**.

4. Selezionare la rete di destinazione e quindi **✓** per avviare il processo di failover.

Per altre informazioni sull'esecuzione di un failover, vedere [Failover in Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Eseguire il failback

Per altre informazioni relative a SQL Server durante il failback, vedere come [eseguire la replica di applicazioni con SQL Server e Azure Site Recovery](site-recovery-sql.md).

1. Accedere al portale di Azure e selezionare l'insieme di credenziali di Site Recovery.

2. Selezionare il piano di ripristino creato per Dynamics AX.

3. Selezionare **Failover** e quindi **Failover**.

4. Selezionare **Cambia direzione**.

5. Selezionare le opzioni appropriate: sincronizzazione dei dati e creazione di VM.

6. Selezionare **✓** per avviare il processo di failback.


Per altre informazioni sull'esecuzione di un failback, vedere [Eseguire il failback di una macchina virtuale VMware da Azure al sito locale](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Summary
Con Azure Site Recovery è possibile creare un piano di ripristino di emergenza completamente automatico per l'applicazione Dynamics AX. In caso di interruzione del servizio è possibile avviare il failover in pochi secondi da qualsiasi luogo per ripristinare l'operatività dell'applicazione in pochi minuti.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla protezione dei carichi di lavoro aziendali con Azure Site Recovery, vedere [Quali carichi di lavoro è possibile proteggere con Azure Site Recovery?](site-recovery-workload.md)
