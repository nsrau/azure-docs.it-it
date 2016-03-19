<properties 
	pageTitle="Distribuire un'applicazione line-of-business | Microsoft Azure" 
	description="Distribuire un'applicazione line-of-business a disponibilità elevata basata sul Web con i gruppi di disponibilità SQL Server AlwaysOn in Azure in cinque fasi." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="josephd"/>

# Distribuire un'applicazione line-of-business a disponibilità elevata in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

Questo argomento fornisce collegamenti alle istruzioni dettagliate per la distribuzione di un'applicazione line-of-business a disponibilità elevata, solo intranet basata sul Web con i gruppi di disponibilità SQL Server AlwaysOn nei servizi di infrastruttura di Azure. L'applicazione è ospitata nei computer seguenti:

- Due server Web
- Due server di database
- Un server del nodo di maggioranza cluster
- Due controller di dominio

Questa è la configurazione, con nomi di segnaposto per ogni server.

![](./media/virtual-machines-workload-high-availability-LOB-application-overview/workload-lobapp-phase4.png)
 
Almeno due macchine per ogni ruolo garantiscono una disponibilità elevata. Tutte le macchine virtuali si trovano in un'unica posizione Azure (nota anche come area). Ogni gruppo di macchine virtuali per un ruolo specifico si trova nel relativo set di disponibilità.

## Distinta base

Questa configurazione di base richiede il set seguente di componenti e servizi di Azure:

- Sette macchine virtuali
- Quattro dischi dati aggiuntivi per i controller di dominio e le macchine virtuali che eseguono SQL Server
- Tre set di disponibilità
- Una rete virtuale cross-premise
- Due account di archiviazione

Ecco le macchine virtuali e le relative dimensioni predefinite per questa configurazione.

Elemento | Descrizione della macchina virtuale | Immagine della raccolta | Dimensioni predefinite 
--- | --- | --- | --- 
1\. | Primo controller di dominio | Windows Server 2012 R2 Datacenter | D1
2\. | Secondo controller di dominio | Windows Server 2012 R2 Datacenter | D1
3\. | Server di database primario | Microsoft SQL Server 2014 Enterprise - Windows Server 2012 R2 | D4
4\. | Server di database secondario | Microsoft SQL Server 2014 Enterprise - Windows Server 2012 R2 | D4
5\. | Nodo di maggioranza per il cluster | Windows Server 2012 R2 Datacenter | D1
6\. | Primo server web | Windows Server 2012 R2 Datacenter | D3
7\. | Secondo server web | Windows Server 2012 R2 Datacenter | D3

Per calcolare i costi stimati per questa configurazione, vedere il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/).

1. In **Moduli**, fare clic su **Calcolo**, quindi fare clic su **Macchine virtuali** fino a creare un elenco di sette macchine virtuali.
2. Per ogni macchina virtuale, selezionare:
	- L'area desiderata
	- **Windows** per il tipo
	- **Standard** per il piano tariffario
	- La dimensione predefinita nella tabella precedente o la dimensione desiderata per **Dimensioni istanze**

> [AZURE.NOTE]Il calcolatore dei prezzi di Azure non include i costi aggiuntivi per la licenza di SQL Server per le due macchine virtuali che eseguono SQL Server 2014 Enterprise. Per altre informazioni, vedere [Prezzi macchine virtuali - SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

## Fasi della distribuzione

La configurazione deve essere distribuita nelle seguenti fasi:

- [Fase 1: Configurare Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md). Creare un account di archiviazione, set di disponibilità e una rete virtuale cross-premise.
- [Fase 2: configurare i controller di dominio](virtual-machines-workload-high-availability-LOB-application-phase2.md). Creare e configurare i controller di dominio dei servizi di dominio Active Directory (AD DS) di replica.
- [Fase 3: configurare l'infrastruttura di SQL Server](virtual-machines-workload-high-availability-LOB-application-phase3.md). Creare e configurare le macchine virtuali che eseguono SQL Server, creare il cluster e abilitare i gruppi di disponibilità SQL Server AlwaysOn.
- [Fase 4: configurare i server Web](virtual-machines-workload-high-availability-LOB-application-phase4.md). Creare e configurare le due macchine virtuali del server Web.
- [Fase 5: aggiungere i database dell'applicazione a un gruppo di disponibilità SQL Server AlwaysOn](virtual-machines-workload-high-availability-LOB-application-phase5.md). Preparare i database dell'applicazione line-of-business e aggiungerli a un gruppo di disponibilità SQL Server AlwaysOn.

Questa distribuzione è progettata per integrare i [progetti di architettura per applicazioni line-of-business](http://msdn.microsoft.com/dn630664) e incorporare le raccomandazioni più recenti.

Questa è un'architettura indicativa predefinita. Tenere presente quanto segue:

- Gli utenti esperti nell'implementazione di applicazioni line-of-business basate sul Web possono adattare le istruzioni delle fasi da 3 a 5 e realizzare l'infrastruttura dell'applicazione che meglio si adatta alle proprie esigenze. 
- Se già c'è un'implementazione di cloud ibrido Azure esistente, è possibile adattare o ignorare le istruzioni delle fasi 1 e 2 per ospitare le macchine virtuali per la nuova applicazione nella subnet appropriata.
- Tutti i server si trovano in una singola subnet nella rete virtuale di Azure. Se si desidera garantire una maggiore protezione equivalente all'isolamento di subnet, è possibile utilizzare [Gruppi di sicurezza di rete](../virtual-networks/virtual-networks-nsg.md).

Per compilare un ambiente di sviluppo e test o un modello di prova di questa configurazione, vedere [Configurazione di un'applicazione LOB basata sul Web in un cloud ibrido per l'esecuzione di test](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md).

Per altre informazioni sulla progettazione di carichi di lavoro IT per Azure, vedere [Linee guida sull'implementazione dei servizi di infrastruttura di Azure](virtual-machines-infrastructure-services-implementation-guidelines.md).

## Passaggio successivo

Per avviare la configurazione di questo carico di lavoro, andare alla [Fase 1: Configurare Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md).

<!---HONumber=AcomDC_1223_2015-->