---
title: Eseguire la migrazione di server Windows Server 2008 in Azure con Azure Migrate/Site Recovery
description: Questo articolo illustra come eseguire la migrazione di computer Windows Server 2008 locali in Azure e consiglia l'uso di Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6cc0855d3a4540de780a566a4613b4dbc647cfc5
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369489"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>Eseguire la migrazione di server con Windows Server 2008 in Azure

Questa esercitazione illustra come eseguire la migrazione di server locali che eseguono Windows Server 2008 o 2008 R2 in Azure usando Azure Site Recovery. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Preparare l'ambiente locale per la migrazione.
> * Configurare l'ambiente di destinazione.
> * Configurare criteri di replica.
> * Abilitare la replica.
> * Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto.
> * Eseguire il failover in Azure e completare la migrazione.

## <a name="migrate-with-azure-migrate"></a>Eseguire la migrazione con Azure Migrate

È consigliabile eseguire la migrazione di computer ad Azure usando il servizio [Azure Migrate](../migrate/migrate-services-overview.md). 

- Azure Migrate è un servizio creato appositamente per la migrazione dei server.
- Azure Migrate fornisce un hub centralizzato per l'individuazione, la valutazione e la migrazione di computer locali in Azure. Azure Site Recovery deve essere usato solo per il ripristino di emergenza e non per la migrazione.
- Azure Migrate supporta la migrazione di server che eseguono Windows Server 2008.


## <a name="migrate-with-site-recovery"></a>Eseguire la migrazione con Site Recovery

### <a name="supported-operating-systems"></a>Sistemi operativi supportati


|Sistema operativo  | Environment  |
|---------|---------|
|Windows Server 2008 SP2 a 32 e 64 bit (IA-32 e x86-64)</br>- Standard</br>- Enterprise</br>- Datacenter   |     Macchine virtuali VMware, macchine virtuali Hyper-V e server fisici    |
|Windows Server 2008 R2 SP1 a 64 bit</br>- Standard</br>- Enterprise</br>- Datacenter     |     Macchine virtuali VMware, macchine virtuali Hyper-V e server fisici|

> [!WARNING]
> - La migrazione dei server che eseguono Server Core non è supportata.
> - Assicurarsi di usare il Service Pack più recente e che gli aggiornamenti di Windows siano installati prima di eseguire la migrazione.


### <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è utile esaminare l'architettura di Azure Site Recovery in relazione alla [migrazione di server fisici e VMware](vmware-azure-architecture.md) o alla [migrazione delle macchine virtuali Hyper-V](hyper-v-azure-architecture.md) 

Per eseguire la migrazione di macchine virtuali Hyper-V che eseguono Windows Server 2008 o Windows Server 2008 R2, seguire i passaggi dell'esercitazione sulla [migrazione dei computer locali in Azure](migrate-tutorial-on-premises-azure.md).

Il resto di questa esercitazione illustra come eseguire la migrazione di macchine virtuali VMware locali e server fisici che eseguono Windows Server 2008 o 2008 R2.
> [!TIP]
> Per informazioni sulla migrazione di macchine virtuali VMware in Azure senza agenti, [fare clic qui](../migrate/tutorial-migrate-vmware.md).


### <a name="limitations-and-known-issues"></a>Limitazioni e problemi noti

- Il server di configurazione, i server di elaborazione aggiuntivi e il servizio Mobility usato per la migrazione dei server Windows Server 2008 SP2 devono eseguire la versione 9.19.0.0 o versioni successive del software Azure Site Recovery.

- I punti di recupero coerenti con l'applicazione e la funzionalità di coerenza per più macchine virtuali non sono supportati per la replica dei server che eseguono Windows Server 2008 SP2. La migrazione dei server Windows Server 2008 SP2 deve essere eseguita in un punto di recupero coerente con l'arresto anomalo del sistema. I punti di recupero coerenti con l'arresto anomalo vengono generati ogni 5 minuti per impostazione predefinita. L'uso di criteri di replica con una frequenza snapshot coerenti con l'applicazione configurata causerà un peggioramento dell'integrità della replica dovuto alla mancanza di punti di recupero coerenti con l'applicazione. Per evitare falsi positivi, impostare su "Off" la frequenza snapshot coerenti con l'applicazione nei criteri di replica.

- I server in fase di migrazione devono avere .NET Framework 3.5 Service Pack 1 perché il servizio Mobility funzioni.

- Se il server usa dischi dinamici, in alcune configurazioni si può notare che questi dischi nel server di cui è stato effettuato failover sono contrassegnati come offline o indicati come dischi esterni. È anche possibile notare che la ridondanza risulta non riuscita nel set con mirroring per i volumi con mirroring tra i dischi dinamici. È possibile risolvere questo problema da diskmgmt.msc importando manualmente questi dischi e riattivandoli.

- I server in fase di migrazione devono avere il driver vmstorfl.sys. Il failover può non riuscire se il driver non è presente nel server di cui si esegue la migrazione. 
  > [!TIP]
  >Verificare se il driver è presente in "C:\Windows\system32\drivers\vmstorfl.sys". Se il driver non viene trovato, è possibile risolvere il problema creando al suo posto un file fittizio. 
  >
  > Aprire il prompt dei comandi (esegui > cmd) ed eseguire il comando seguente: "copy nul c:\Windows\system32\drivers\vmstorfl.sys"

- Può non essere possibile connettersi con RDP ai server Windows Server 2008 SP2 che eseguono il sistema operativo a 32 bit immediatamente dopo essere stati sottoposti al failover o al failover di test in Azure. Riavviare la macchina virtuale di cui è stato effettuato il failover dal portale di Azure e provare nuovamente a connettersi. Se non è ancora possibile connettersi, verificare se il server è configurato in modo da consentire le connessioni al desktop remoto e assicurarsi che non siano presenti regole di firewall o gruppi di sicurezza di rete che bloccano la connessione. 
  > [!TIP]
  > Si consiglia di eseguire un failover di test prima della migrazione dei server. Assicurarsi di aver eseguito almeno un failover di test riuscito in ogni server di cui si esegue la migrazione. Come parte del failover di test, connettersi al computer sottoposto al failover e verificare che tutto funzioni come previsto.
  >
  >L'operazione di failover di test non comporta interruzioni e consente di testare le migrazioni creando macchine virtuali in una rete isolata di propria scelta. A differenza dell'operazione di failover, durante l'operazione di failover di test la replica dei dati continua. È possibile eseguire qualsiasi numero di failover di test prima di essere pronti a eseguire la migrazione. 
  >
  


### <a name="get-started"></a>Introduzione

Eseguire le attività riportate di seguito per preparare la sottoscrizione di Azure e l'ambiente VMware o fisico locale:

1. [Preparare Azure](tutorial-prepare-azure.md)
2. Preparare istanze di [VMware](vmware-azure-tutorial-prepare-on-premises.md) locali


### <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

1. Accedere a [portale di Azure](https://portal.azure.com) > **Servizi di ripristino**.
2. Fare clic su **Crea una risorsa** > **Strumenti di gestione** > **Backup e Site Recovery (OMS)** .
3. In **Nome** specificare il nome descrittivo **W2K8-migration**. Se è disponibile più di una sottoscrizione, selezionare quella appropriata.
4. Creare un gruppo di risorse **w2k8migrate**.
5. Specificare un'area di Azure. Per verificare le aree supportate, vedere la sezione relativa alla disponibilità a livello geografico in [Prezzi di Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Per accedere rapidamente all'insieme di credenziali dal dashboard, fare clic su **Aggiungi al dashboard** e quindi su **Crea**.

   ![Screenshot che mostra le opzioni per la creazione dell'insieme di credenziali.](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

Il nuovo insieme di credenziali viene aggiunto al **Dashboard**, nella sezione **Tutte le risorse**, e nella pagina **Insiemi di credenziali dei servizi di ripristino** principale.


### <a name="prepare-your-on-premises-environment-for-migration"></a>Preparare l'ambiente locale per la migrazione

- Per eseguire la migrazione di macchine virtuali di Windows Server 2008 in esecuzione in VMware [configurare il Server di configurazione locale in VMware](vmware-azure-tutorial.md#set-up-the-source-environment).
- Se il Server di configurazione non può essere configurato come una macchina virtuale VMware, [configurare il Server di configurazione in un server fisico locale o in una macchina virtuale](physical-azure-disaster-recovery.md#set-up-the-source-environment).

### <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare e verificare le risorse di destinazione.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare la sottoscrizione di Azure da usare.
2. Specificare il modello di distribuzione di Gestione risorse.
3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.


### <a name="set-up-a-replication-policy"></a>Configurare criteri di replica

1. Per creare nuovi criteri di replica, fare clic su **Infrastruttura di Site Recovery** > **Criteri di replica** >  **+Criteri di replica**.
2. In **Creare i criteri di replica** specificare un nome per i criteri.
3. In **Soglia RPO**, specificare il limite dell'obiettivo del punto di ripristino (RPO). Se il valore RPO della replica supera questo limite, viene generato un avviso.
4. In **Conservazione del punto di recupero**, specificare la durata in ore dell'intervallo di conservazione per ogni punto di recupero. I server replicati possono essere recuperati in qualsiasi punto di questo intervallo. È supportata la conservazione fino a 24 ore per le macchine replicate in Archiviazione Premium e fino a 72 ore per Archiviazione Standard.
5. In **Frequenza snapshot coerenti con l'app** specificare **Disattivata**. Fare clic su **OK** per creare i criteri.

I criteri vengono automaticamente associati al server di configurazione.

> [!WARNING]
> Assicurarsi di specificare **Disattivata** nell'impostazione della frequenza snapshot coerenti con l'app dei criteri di replica. Sono supportati solo i punti di recupero coerenti con l'arresto anomalo del sistema quando si replicano i server con Windows Server 2008. Se si specificano altri valori per la frequenza snapshot coerenti con l'app, verranno generati falsi avvisi perché lo stato dell'integrità della replica del server diventa critico a causa della mancanza di punti di recupero coerenti con l'app.

   ![Screenshot che mostra le opzioni per la creazione di criteri di replica.](media/migrate-tutorial-windows-server-2008/create-policy.png)

### <a name="enable-replication"></a>Abilitare la replica

[Abilitare la replica](physical-azure-disaster-recovery.md#enable-replication) per il server Windows Server 2008 SP2 o Windows Server 2008 R2 SP1 da sottoporre a migrazione.
   
   ![Screenshot che mostra le opzioni per l'aggiunta di computer fisici.](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![Screenshot che mostra le opzioni per abilitare la replica.](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

### <a name="run-a-test-migration"></a>Eseguire una migrazione di test

È possibile eseguire un failover di test dei server sottoposti a replica al termine della replica iniziale e lo stato del server diventa **Protetto**.

Eseguire un [failover di test](tutorial-dr-drill-azure.md) in Azure per verificare che tutto funzioni correttamente.

   ![Screenshot che mostra il comando di failover di test.](media/migrate-tutorial-windows-server-2008/testfailover.png)


### <a name="migrate-to-azure"></a>Eseguire la migrazione ad Azure

Eseguire un failover per i computer di cui si vuole eseguire la migrazione.

1. In **Impostazioni** > **Elementi replicati** fare clic su computer > **Failover**.
2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. Selezionare l'ultimo punto di ripristino.
3. Selezionare **Arrestare la macchina prima di iniziare il failover**. Site Recovery tenta di arrestare il server prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.
4. Assicurarsi che la macchina virtuale di Azure sia visualizzata in Azure come previsto.
5. In **Elementi replicati** fare clic con il pulsante destro del mouse sul server > **Completa la migrazione**. Vengono eseguite le operazioni seguenti:

    - Il processo di migrazione viene completato, la replica del server viene arrestata e la fatturazione di Site Recovery per il server viene interrotta.
    - In questo passaggio vengono eliminati i dati di replica, ma non le macchine virtuali di cui è stata eseguita la migrazione.

   ![Screenshot che mostra il comando per completare la migrazione.](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **Non annullare un failover in corso**: La replica del server viene arrestata prima dell'avvio del failover. Se si annulla un failover in corso, il failover viene arrestato ma la replica del server non continuerà.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Esaminare le domande comuni](../migrate/resources-faq.md) su Azure Migrate.