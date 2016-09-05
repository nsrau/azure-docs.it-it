
<properties
	pageTitle="Azure Site Recovery: Replicare macchine virtuali Hyper-V su un singolo server VMM | Microsoft Azure"
	description="In questo articolo viene descritto come replicare le macchine virtuali Hyper-V se si ha un singolo server VMM."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="08/24/2016"
	ms.author="raynew"/>  

#  Replicare macchine virtuali Hyper-V su un singolo server VMM

In questo articolo viene descritto come replicare le macchine virtuali Hyper-V che si trovano su un server host Hyper-V in un cloud VMM se nella distribuzione è presente un singolo server VMM.

Azure offre due diversi [modelli di distribuzione](../resource-manager-deployment-model.md) per creare e usare le risorse: Azure Resource Manager e la distribuzione classica. Sono anche disponibili il portale di Azure classico, che supporta il modello di distribuzione classica, e il portale di Azure, che supporta entrambi i modelli di distribuzione. Questo articolo contiene le istruzioni per la configurazione della replica nel portale di Azure.


In caso di domande dopo la lettura di questo articolo, pubblicarle nei commenti Desquis in fondo alla pagina o sul [forum relativo a Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Overview

Se si vuole replicare le VM Hyper-V presenti negli host Hyper-V in VMM e si dispone di un unico server VMM, è possibile [eseguire la replica in Azure](site-recovery-vmm-to-azure.md) o tra cloud nell'unico server VMM.

Si consiglia di eseguire la replica in Azure perché il failover e il ripristino non sono trasparenti durante la replica tra cloud e sono necessari alcuni passaggi manuali. Se si vuole replicare usando solo il server VMM, è possibile eseguire le operazioni seguenti:

- Replicare con un singolo server VMM autonomo
- Replicare con un singolo server VMM distribuito in un cluster Windows esteso


## Replicare tra siti con un singolo server VMM autonomo

![Server VMM virtuale autonomo](./media/site-recovery-single-vmm/single-vmm-standalone.png)  

In questo scenario distribuire il singolo server VMM come macchina virtuale nel sito primario e replicare questa VM in un sito secondario con Site Recovery e Hyper-V Replica.

1. Configurare VMM in una VM Hyper-V. È consigliabile installare l'istanza di SQL Server usata da VMM nella stessa VM per risparmiare tempo. Se si vuole usare un'istanza remota di SQL Server e si verifica un'interruzione, è necessario ripristinare l'istanza prima di ripristinare VMM.
2. Assicurarsi che il server VMM abbia almeno due cloud configurati. Un cloud contiene le VM da replicare e l'altro cloud verrà usato come posizione secondaria. Il cloud che contiene le macchine virtuali da proteggere deve avere:

	- Uno o più gruppi host VMM contenenti uno o più server host Hyper-V in ogni gruppo host.
	- Almeno una macchina virtuale Hyper-V in ogni server host Hyper-V.

3. Creare un insieme di credenziali di Servizi di ripristino, generare e scaricare una chiave di registrazione dell'insieme di credenziali e registrare il server VMM nell'insieme di credenziali. Durante la registrazione viene installato il provider di Azure Site Recovery nel server VMM.
4. Configurare uno o più cloud sulla VM VMM e aggiungere gli host Hyper-V a questi cloud.
3. Configurare le impostazioni di protezione per i cloud. Specificare il nome del singolo server VMM come percorsi di origine e di destinazione. Per configurare il mapping di rete, viene eseguito il mapping della rete delle VM per il cloud con le VM da proteggere nella rete delle VM per il cloud di replica.
4. Abilitare la replica iniziale per le VM da proteggere tramite la rete, poiché entrambi i cloud si trovano nello stesso server.
4. Nella console di gestione Hyper-V, abilitare la Replica Hyper-V nell'host Hyper-V che contiene le VM VMM e abilitare la replica sulla VM. Assicurarsi di non aggiungere la VM VMM ai cloud protetti da Site Recovery. In questo modo Site Recovery non esegue l'override delle impostazioni di Replica Hyper-V.
5. Per creare piani di ripristino, specificare lo stesso server VMM come origine e destinazione.

Seguire le istruzioni in [questo articolo](site-recovery-vmm-to-vmm.md) per creare un insieme di credenziali, registrare il server e configurare la protezione.

### Operazioni da eseguire in caso di interruzione del servizio

Se si verifica un'interruzione completa ed è necessario operare dal sito secondario, eseguire le operazioni seguenti:

1.	Nella console di gestione Hyper-V nel sito secondario, eseguire un failover non pianificato per eseguire il failover della VM VMM dal sito primario a quello secondario.
2.	Verificare che la VM VMM sia in esecuzione nel sito secondario.
3.	Nell'insieme di credenziali di Servizi di ripristino eseguire un failover non pianificato per eseguire il failover delle VM con il carico di lavoro dal cloud primario a quello secondario. Per completare il failover non pianificato delle VM, eseguire il commit del failover o selezionare un punto di ripristino diverso in base alle esigenze.
4.	Al completamento del failover non pianificato gli utenti possono accedere alle risorse del carico di lavoro nel sito secondario.

Quando il sito primario torna a funzionare, eseguire le operazioni seguenti:

1.	Nella console di gestione Hyper-V abilitare la replica inversa per la VM VMM in modo da avviare la replica dal sito secondario a quello primario.
2.	Nella console di gestione Hyper-V eseguire un failover pianificato per eseguire il failback della VM VMM nel sito primario. Eseguire il commit del failover per completare le operazioni. Abilitare la replica inversa per avviare la replica di VMM dal sito primario a quello secondario.
3.	Nell'insieme di credenziali dei Servizi di ripristino abilitare la replica inversa per le VM del carico di lavoro per avviarne la replica dal sito secondario a quello primario.
4.	Nell'insieme di credenziali dei Servizi di ripristino eseguire un failover pianificato per eseguire il failback delle VM del carico di lavoro nel sito primario. Eseguire il commit del failover per completare le operazioni. Abilitare la replica inversa per avviare la replica delle VM del carico di lavoro dal sito primario a quello secondario.



## Replicare tra siti con un singolo server VMM in un cluster esteso

![Server VMM virtuale cluster](./media/site-recovery-single-vmm/single-vmm-cluster.png)  

Anziché distribuire un server VMM autonomo come VM che replica in un sito secondario, è possibile garantire la disponibilità elevata di VMM distribuendolo come VM in un cluster di failover di Windows, assicurando così flessibilità al carico di lavoro e protezione da errori hardware. Per effettuare la distribuzione usando Site Recovery, la macchina virtuale di VMM deve essere distribuita in un cluster esteso in siti geograficamente separati. A tale scopo, seguire questa procedura:

1. Installare VMM su una macchina virtuale in un cluster di failover di Windows e selezionare l'opzione per eseguire il server come disponibilità elevata durante l'installazione.
2. L'istanza di SQL Server usata da VMM deve essere replicata con gruppi di disponibilità AlwaysOn di SQL Server in modo che sia disponibile una replica del database nel sito secondario.
3. Seguire le istruzioni in [questo articolo](site-recovery-vmm-to-vmm.md) per creare un insieme di credenziali, registrare il server e configurare la protezione. È necessario registrare ogni server VMM nel cluster nell'insieme di credenziali di Servizi di ripristino. A tale scopo, installare il Provider in un nodo attivo e registrare il server VMM. Quindi installare il provider sugli altri nodi.

### Operazioni da eseguire in caso di interruzione del servizio

Se si verifica un'interruzione, il server VMM e il database SQL Server corrispondente sono sottoposti a failover e l'accesso viene eseguito dal sito secondario.


## Passaggi successivi

[Altre informazioni](site-recovery-vmm-to-vmm.md) dettagliate sulla distribuzione di Site Recovery per VMM per la replica di VMM.

<!---HONumber=AcomDC_0824_2016-->