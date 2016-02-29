<properties
	pageTitle="Considerazioni sulla distribuzione per VMM e Site Recovery | Microsoft Azure"
	description="Questo articolo illustra alcune considerazioni pratiche sull'integrazione di VMM con Azure Site Recovery."
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
	ms.workload="storage-backup-recovery"
	ms.date="02/15/2016"
	ms.author="raynew"/>

#  Considerazioni sulla distribuzione per VMM e Site Recovery

Il servizio Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza (BCDR) orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici. È possibile replicare i computer in Azure o in un data center locale secondario. Per una panoramica rapida, vedere [Che cos'è Azure Site Recovery?](site-recovery-overview.md).


## Panoramica

Questo articolo consente di progettare e implementare una soluzione di continuità aziendale e ripristino di emergenza e l'infrastruttura che include System Center VMM e Azure Site Recovery.

Lo scopo della strategia di BCDR è mantenere le applicazioni aziendali in esecuzione e di ripristinare i carichi di lavoro e servizi in caso di errore, affinché l'organizzazione possa riprendere le normali operazioni in tempi brevi. Lo sviluppo di strategie di ripristino di emergenza è un'operazione complessa, a causa dell'imprevedibilità degli eventi avversi e del costo elevato dell'implementazione di un'adeguata protezione contro gli errori di vasta portata. Site Recovery consente di implementare la protezione e il failover dal data center principale in uno secondario (o in Azure) tramite la copia iniziale (replica) dei dati primari e il successivo aggiornamento periodico delle repliche.

Una parte fondamentale della pianificazione BCDR è la definizione dell'obiettivo tempo di ripristino (RTO, Recovery Time Objective) e dell'obiettivo punto di ripristino (RPO, Recovery Point Objective) in modo da consentire all'organizzazione di ripristinare i dati aziendali online nel minor tempo (con un RTO basso) e con la minor perdita di dati (RPO basso) possibile. La progettazione di rete dell'organizzazione è un potenziale collo di bottiglia per gli obiettivi RTO e RPO. Pianificando la progettazione in modo ottimale, è possibile evitare tale ostacolo.


Se si distribuisce Site Recovery per eseguire la replica delle VM Hyper-V in cloud VMM, è necessario considerare la modalità di integrazione di VMM nella strategia di replica e di failover di Site Recovery.

## Integrazione di un server VMM autonomo

In questa topologia, si distribuisce un server VMM autonomo in una macchina virtuale in un sito primario, replicando tale macchina virtuale in un sito secondario con Site Recovery e Replica Hyper-V. L'installazione del server VMM e del relativo server SQL di supporto nella stessa macchina virtuale consente di ridurre i tempi di inattività, in quanto è necessario creare un'istanza di una sola VM. Quando il servizio VMM utilizza un SQL Server remoto, occorre ripristinare l'istanza di SQL Server prima del ripristino del server VMM.

Per distribuire un singolo VMM in una macchina virtuale con la Replica Hyper-V:

1. Configurare il server VMM in una macchina virtuale in cui è installato SQL Server.
2. Aggiungere al server VMM gli host da gestire nei cloud.
3. Accedere al portale di Azure e configurare la protezione dei cloud.
4. Abilitare la replica per tutte le macchine virtuali che devono essere protette dal server VMM.
5. Passare alla console di gestione di Hyper-V, selezionare la Replica Hyper-V e quindi abilitare la replica nella macchina virtuale VMM.
6. Verificare che la macchina virtuale VMM non venga aggiunta ai cloud protetti dal servizio Azure Site Recovery, affinché le impostazioni di replica Hyper-V non vengano sovrascritte da ASR.

In caso di emergenza, è possibile ripristinare i carichi di lavoro come indicato di seguito:

1. Eseguire il failover della macchina virtuale VMM di replica nel sito di ripristino, mediante la console di gestione di Hyper-V.
2. Al termine del ripristino della macchina virtuale VMM, l'utente può accedere a Gestione ripristino di Hyper-V dal sito secondario.
3. Al termine del failover non pianificato, l'utente può accedere a tutte le risorse nel sito primario.
4. Si noti che sarà necessario eseguire il failover manuale della macchina virtuale VMM nel sito secondario, prima di poter eseguire il failover dei carichi di lavoro.


### Integrazione di un cluster VMM


La [distribuzione di VMM in un cluster](https://technet.microsoft.com/library/gg610675.aspx) garantisce disponibilità e protezione elevate contro il failover hardware. Se si distribuisce il cluster VMM con Site Recovery, si noti che:

- Il server VMM deve essere distribuito in un cluster esteso in tutti i siti geograficamente separati.
- Il database di SQL Server usato da VMM deve essere protetto con gruppi di disponibilità AlwaysOn di SQL Server con una replica nel sito secondario.
- In caso di emergenze, il server VMM e il SQL Server corrispondente eseguiranno automaticamente il failover nel sito di ripristino. È quindi possibile eseguire il failover dei carichi di lavoro mediante Site Recovery.

	![Cluster VMM esteso](./media/site-recovery-network-design/network-design1.png)


## Passaggi successivi

[Informazioni](site-recovery-network-mapping.md) sulla modalità di mapping delle reti di origine e destinazione di Site Recovery.

<!---HONumber=AcomDC_0218_2016-->