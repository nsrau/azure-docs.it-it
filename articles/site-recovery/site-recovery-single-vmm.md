
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
	ms.date="03/30/2016"
	ms.author="raynew"/>

#  Replicare macchine virtuali Hyper-V su un singolo server VMM

In questo articolo viene descritto come replicare le macchine virtuali Hyper-V che si trovano su un server host Hyper-V in un cloud VMM se nella distribuzione è presente un singolo server VMM.

In caso di domande dopo la lettura di questo articolo, è possibile pubblicarle in fondo alla pagina o sul [forum relativo a Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Panoramica

È possibile replicare le macchine virtuali Hyper-V presenti sugli host Hyper-V in cloud VMM in due modi:

- Replicare in Azure. 
- Replicare in un sito VMM secondario

Cosa succede se si vuole replicare in un sito VMM secondario, ma nella distribuzione è disponibile un singolo server VMM?

In questo scenario sono disponibili due opzioni:

- Replicare le macchine virtuali Hyper-V nei cloud VMM in Azure. [Ulteriori informazioni](site-recovery-vmm-to-azure.md) su questo scenario.
- Replicare tra i cloud in un singolo server VMM.

È consigliabile la prima opzione, poiché il failover e il ripristino non sono automatici nella seconda opzione. È invece necessaria una serie di passaggi manuali. Se si vuole eseguire la replica tra siti anziché in Azure, sono disponibili due opzioni.


## Replicare tra siti con un singolo server VMM autonomo

In questo scenario distribuire un singolo server VMM autonomo come macchina virtuale in un sito primario e replicare questa macchina virtuale in un sito secondario con Site Recovery e Hyper-V Replica. A tale scopo, effettuare l'operazione seguente:

1. Configurare VMM in una VM Hyper-V. Considerare la possibilità di condividere il percorso per l'istanza di SQL Server usata da VMM nella stessa macchina virtuale. Ciò consente di risparmiare tempo, poiché deve essere creata un'istanza di una sola VM. Se si vuole usare un'istanza remota di SQL Server e si verifica un'interruzione, è necessario ripristinare l'istanza prima di ripristinare VMM.
2. Assicurarsi che il server VMM abbia almeno due cloud configurati. Un cloud conterrà le VM da replicare e l’altro cloud verrà usato come posizione secondaria. Il cloud che contiene le macchine virtuali da proteggere deve avere:

	- Uno o più gruppi host VMM contenenti uno o più server host Hyper-V in ogni gruppo host.
	- Almeno una macchina virtuale Hyper-V in ogni server host Hyper-V.
3. Creare un insieme di credenziali di Ripristino sito, generare e scaricare una chiave di registrazione dell'insieme di credenziali e registrare il server VMM nell'insieme di credenziali. Durante la registrazione verrà installato il provider di Azure Site Recovery nel server VMM.
4. Configurare uno o più cloud sulla VM VMM e aggiungere gli host Hyper-V contenenti le VM da proteggere a questi cloud.
3. Configurare le impostazioni di protezione per i cloud in Azure Site Recovery. Nel Percorso di origine e di destinazione specificare il nome del singolo server VMM. Se si configura il mapping di rete, viene eseguito il mapping della rete delle VM per il cloud contenente le VM da proteggere nella rete delle VM per il cloud in cui si desidera replicare.
4. Abilitare la replica per le VM da proteggere tramite **Attraverso la rete** come metodo di replica, poiché entrambi i cloud si trovano nello stesso server.
4. Nella console di gestione Hyper-V, abilitare la Replica Hyper-V nell'host Hyper-V che contiene le VM VMM e abilitare la replica sulla VM. Assicurarsi di non aggiungere la macchina virtuale VMM ai cloud protetti mediante Ripristino sito, per garantire che le impostazioni di Replica Hyper-V non siano sottoposte a override da Ripristino sito.
5. Se si desidera creare piani di ripristino, specificare lo stesso server VMM per origine e destinazione.

Seguire le istruzioni in [questo articolo](site-recovery-vmm-to-vmm.md) per creare un insieme di credenziali per ottenere una chiave, registrare il server e configurare la protezione.

### In caso d'interruzione

In caso d'interruzione, ripristinare i carichi di lavoro sulle macchine virtuali Hyper-V come indicato di seguito:

1. Effettuare manualmente il failover della macchina virtuale di VMM nel sito secondario usando la console di gestione di Hyper-V con un failover pianificato. 
2. Quando la macchina virtuale di VMM è stata ripristinata, è possibile accedere a Gestione del ripristino di Hyper-V dal sito secondario ed effettuare un failover non pianificato delle VM dal sito secondario al sito primario. Si noti che sarà necessario effettuare il failover manuale della macchina virtuale di VMM nel sito secondario, prima di poter eseguire il failover delle VM con i carichi di lavoro.
3. Al termine del failover non pianificato, è di nuovo possibile accedere a tutte le risorse dal sito primario.


![Server VMM virtuale autonomo](./media/site-recovery-single-vmm/single-vmm-standalone.png)

## Replicare tra siti con un singolo server VMM in un cluster esteso

Anziché distribuire un server VMM autonomo come macchina virtuale che viene replicata in un sito secondario, è possibile garantire la disponibilità elevata di VMM distribuendolo come macchina virtuale in un cluster di failover di Windows, assicurando così flessibilità al carico di lavoro e protezione da errori hardware. Per effettuare la distribuzione usando Site Recovery, la macchina virtuale di VMM deve essere distribuita in un cluster esteso in siti geograficamente separati. A tale scopo, effettuare l'operazione seguente:

1. Installare VMM su una macchina virtuale in un cluster di failover di Windows e selezionare l'opzione per eseguire il server come disponibilità elevata durante l'installazione.
2. L'istanza di SQL Server usata da VMM deve essere replicata con gruppi di disponibilità AlwaysOn di SQL Server in modo che sia disponibile una replica del database nel sito secondario.

Si noti che quando si installa Site Recovery è necessario registrare ogni server VMM nel cluster nell'insieme di credenziali di Site Recovery. Installare quindi il provider in un nodo attivo e completare l'installazione per registrare il server VMM nell'insieme di credenziali. Quindi installare il provider sugli altri nodi.
 
### In caso d'interruzione 

Se si verificano interruzioni, il server VMM e il database SQL Server corrispondente sono sottoposti a failover e accessibili dal sito secondario.

![Server VMM virtuale cluster](./media/site-recovery-single-vmm/single-vmm-cluster.png)

## Passaggi successivi

[Altre informazioni](site-recovery-vmm-to-vmm.md) dettagliate sulla distribuzione di Site Recovery per VMM per la replica di VMM.




 

<!---HONumber=AcomDC_0413_2016-->