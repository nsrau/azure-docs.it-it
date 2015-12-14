
<properties
	pageTitle="Azure Site Recovery: Macchine virtuali replica Hyper-V (singolo server VMM)"
	description="Azure Site Recovery coordina la replica, il failover e il ripristino delle macchine virtuali ubicate nei cloud VMM locali in Azure o in un cloud VMM secondario."
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
	ms.date="12/01/2015"
	ms.author="raynew"/>

#  Azure Site Recovery: Macchine virtuali replica Hyper-V (singolo server VMM)

Il servizio Azure Site Recovery contribuisce a un’efficace soluzione di ripristino di emergenza e continuità aziendale (BCDR) orchestrando e automatizzando la replica e il failover di server fisici e macchine virtuali in Azure o in un centro dati locale secondario. In questo articolo viene descritto come distribuire Ripristino sito per proteggere le macchine virtuali Hyper-V che si trovano in un cloud VMM quando si dispone solo di un singolo server VMM nella distribuzione. In caso di domande dopo la lettura di questo articolo, è possibile pubblicarle nel [forum relativo a Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Panoramica

È possibile replicare le VM Hyper-V in due modi:

- Replicare le VM Hyper-V che si trovano negli host Hyper-V che non si trovano in un cloud VMM in Azure
- Replicare le VM Hyper-V che si trovano negli host Hyper-V in un cloud VMM in Azure
- Replicare le VM Hyper-V che si trovano negli host Hyper-V in un cloud VMM in Azure

Ma cosa succede se si desidera utilizzare VMM, ma avere solo un singolo server VMM nell'infrastruttura?

In questo caso sono disponibili due opzioni:

- Replicare le VM Hyper-V nei cloud VMM in Azure. [Ulteriori informazioni](site-recovery-vmm-to-azure.md) su questo scenario.
- Replicare tra cloud in un singolo server VMM

Si consiglia la prima opzione, poiché il failover e il ripristino non sono automatici nella seconda opzione ed è necessaria una serie di passaggi manuali.


### Replicare tra siti con un singolo server VMM autonomo

In questo scenario distribuire un singolo server VMM autonomo come macchina virtuale in un sito primario e replicare questa macchina virtuale in un sito secondario con Ripristino sito e Replica Hyper-V. A tale scopo, seguire questa procedura:

1. Configurare VMM in una VM Hyper-V. Prendere in considerazione la condivisione del percorso per l'istanza di SQL Server utilizzata da VMM nella VM stessa. Ciò consente di risparmiare tempo, poiché deve essere creata un'istanza di una sola VM. Se si desidera utilizzare un'istanza remota e si verifica un'interruzione, è necessario ripristinare l'istanza prima di ripristinare VMM.
2. Assicurarsi che il server VMM abbia almeno due cloud configurati. Un cloud conterrà le VM da replicare e l’altro cloud verrà usato come posizione secondaria. Il cloud che contiene le VM che si desidera proteggere deve avere uno o più gruppi host VMM con uno o più server host Hyper-V in ogni gruppo host e almeno una macchina virtuale Hyper-V in ogni server host.
2. Creare un insieme di credenziali di Ripristino sito, generare e scaricare una chiave di registrazione dell'insieme di credenziali e registrare il server VMM nell'insieme di credenziali.
2. Configurare uno o più cloud sulla VM VMM e aggiungere gli host Hyper-V contenenti le VM da proteggere a questi cloud.
3. Configurare le impostazioni di protezione per i cloud in Azure Site Recovery. Nel Percorso di origine e di destinazione specificare il nome del singolo server VMM. Se si configura il mapping di rete, viene eseguito il mapping della rete delle VM per il cloud contenente le VM da proteggere nella rete delle VM per il cloud in cui si desidera replicare.
4. Abilitare la replica per le VM da proteggere tramite **Attraverso la rete** come metodo di replica, poiché entrambi i cloud si trovano nello stesso server.
4. Nella console di gestione Hyper-V, abilitare la Replica Hyper-V nell'host Hyper-V che contiene le VM VMM e abilitare la replica sulla VM. Assicurarsi di non aggiungere la macchina virtuale VMM ai cloud protetti mediante Ripristino sito, per garantire che le impostazioni di Replica Hyper-V non siano sottoposte a override da Ripristino sito.
5. Se si desidera creare piani di ripristino, specificare lo stesso server VMM per origine e destinazione. 

Quando si verificano interruzioni, è possibile ripristinare i carichi di lavoro su VM Hyper-V come indicato di seguito:

1. Eseguire manualmente il failover della replica della VM VMM per il sito secondario utilizzando Gestione di Hyper-V con un failover pianificato.
2. Quando la VM VMM è stata ripristinata, è possibile accedere ad Hyper-V Recovery Manager dal sito secondario ed effettuare un failover non pianificato delle VM dal sito primario al sito secondario.
3. Al termine del failover non pianificato, l'utente può accedere a tutte le risorse nel sito primario.

Si noti che sarà necessario eseguire il failover manuale della macchina virtuale VMM nel sito secondario, prima di poter eseguire il failover dei carichi di lavoro.

![Server VMM virtuale autonomo](./media/site-recovery-single-vmm/single-vmm-standalone.png)

### Replicare tra siti con un singolo server VMM in un cluster esteso

Anziché distribuire un server VMM autonomo come macchina virtuale che viene replicata in un sito secondario, è possibile rendere VMM altamente disponibile mediante la distribuzione come VM in un cluster di failover di Windows, fornendo flessibilità al carico di lavoro e protezione da errori hardware. Per la distribuzione con Ripristino sito, il VMM deve essere distribuito in un cluster esteso in siti geograficamente separati. A tale scopo, seguire questa procedura:

1. Installare VMM su una macchina virtuale in un cluster di failover di Windows e selezionare l'opzione per eseguire il server come disponibilità elevata durante l'installazione.
2. L’istanza di SQL Server utilizzata da VMM deve essere replicata con gruppi di disponibilità AlwaysOn di SQL Server in modo che sia disponibile una replica del database nel sito secondario. 

Se si verificano interruzioni, il server VMM e il corrispondente SQL Server sono sottoposti a failover e accessibili dal sito secondario.

![Server VMM virtuale cluster](./media/site-recovery-single-vmm/single-vmm-cluster.png)




 

<!---HONumber=AcomDC_1203_2015-->