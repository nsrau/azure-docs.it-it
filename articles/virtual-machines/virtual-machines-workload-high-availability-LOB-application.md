<properties 
	pageTitle="Applicazione line-of-business in Azure | Microsoft Azure" 
	description="Informazioni sul valore di un'applicazione line-of-business in Azure, configurare un ambiente di test e distribuire una configurazione a disponibilità elevata." 
	services="virtual-machines" 
	documentationCenter="" 
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
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# Carico di lavoro dei servizi di infrastruttura di Azure: applicazione line-of-business a disponibilità elevata

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrata la creazione delle risorse con il modello di distribuzione di gestione delle risorse.

Impostando la prima o la successiva applicazione line-of-business solo Intranet basata sul Web in Microsoft Azure, è possibile usufruire della facilità di configurazione e della possibilità di espandere rapidamente l'applicazione per includere nuova capacità.
 
Con le funzionalità per macchine virtuali e reti virtuali dei servizi di infrastruttura di Azure, è possibile distribuire ed eseguire rapidamente un'applicazione line-of-business accessibile agli utenti dell'organizzazione. Ad esempio, è possibile definire la configurazione seguente.

![](./media/virtual-machines-workload-high-availability-LOB-application/workload-lobapp-phase4.png)
 
Dal momento che la rete virtuale di Azure è un'estensione della rete locale con la denominazione e il routing del traffico già impostati correttamente, gli utenti potranno accedere ai server dell'applicazione line-of-business come se si trovassero in un data center locale.

Questa configurazione consente di espandere facilmente la capacità dell'applicazione aggiungendo nuove macchine virtuali di Azure in cui i costi operativi dell'hardware e della manutenzione sono inferiori a quelli relativi alla gestione di una soluzione equivalente nel data center.

Il passaggio successivo consiste nel configurare un'applicazione line-of-business di sviluppo/testing ospitata in Azure.

## Creare un'applicazione line-of-business di sviluppo/testing ospitata in Azure

Una rete virtuale cross-premise è connessa a una rete locale mediante una connessione ExpressRoute o VPN da sito a sito. Se si vuole creare un ambiente di sviluppo/testing che simuli la configurazione finale e provare ad accedere all'applicazione e a eseguire le attività di amministrazione remota su una connessione VPN, vedere [Configurare un'applicazione LOB basata sul Web in un cloud ibrido per l'esecuzione di test](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md).

![](./media/virtual-machines-workload-high-availability-LOB-application/CreateLOBAppHybridCloud_3.png)
 
È possibile creare questo ambiente di sviluppo/testing gratuitamente con il proprio [abbonamento a MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/) o una [sottoscrizione di valutazione di Azure](http://azure.microsoft.com/pricing/free-trial/).

Il passaggio successivo consiste nel creare un'applicazione line-of-business a disponibilità elevata in Azure.

## Distribuire un'applicazione line-of-business ospitata in Azure

La configurazione rappresentativa di base per un'applicazione line-of-business in Azure è simile alla seguente.

![](./media/virtual-machines-workload-high-availability-LOB-application/workload-lobapp-phase4.png)
 
È costituita dagli elementi seguenti:

- Un'applicazione line-of-business solo Intranet con due server a livello Web e di database
- Una configurazione SQL Server AlwaysOn con due macchine virtuali che eseguono SQL Server e un computer del nodo di maggioranza in un cluster.
- Servizi di dominio Active Directory nella rete virtuale con due controller di dominio di replica

Per una panoramica delle applicazioni line-of-business, vedere [Progetti dell'architettura per applicazioni line-of-business](http://msdn.microsoft.com/dn630664).

### Distinta base

Questa configurazione di base richiede il set seguente di componenti e servizi di Azure:

- Sette macchine virtuali
- Quattro dischi dati aggiuntivi per i controller di dominio e le macchine virtuali che eseguono SQL Server
- Tre set di disponibilità
- Una rete virtuale cross-premise
- Due account di archiviazione

### Fasi di distribuzione

Per distribuire questa configurazione, usare il processo seguente:

- Fase 1: Configurare Azure 

	Usare Azure PowerShell per creare gli account di archiviazione, i set di disponibilità e una rete virtuale cross-premise. Per i passaggi di configurazione dettagliati, vedere [Fase 1](virtual-machines-workload-high-availability-LOB-application-phase1.md).

- Fase 2: Configurare i controller di dominio

	Configurare due controller di dominio di replica di Active Directory e le impostazioni DNS per la rete virtuale. Per i passaggi di configurazione dettagliati, vedere [Fase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md).

- Fase 3: Configurare l'infrastruttura di SQL Server

	Creare le macchine virtuali che eseguono SQL Server e il cluster. Per i passaggi di configurazione dettagliati, vedere [Fase 3](virtual-machines-workload-high-availability-LOB-application-phase3.md).

- Fase 4: Configurare i server Web.

	Creare le macchine virtuali del server Web e aggiungervi l'applicazione line-of-business. Per la configurazione dettagliata, vedere [Fase 4](virtual-machines-workload-high-availability-LOB-application-phase4.md).

- Fase 5: Configurare un gruppo di disponibilità SQL Server AlwaysOn.

	Preparare i database dell'applicazione, creare un gruppo di disponibilità SQL Server AlwaysOn e quindi aggiungervi i database dell'applicazione. Per i passaggi di configurazione dettagliati, vedere [Fase 5](virtual-machines-workload-high-availability-LOB-application-phase5.md).

Dopo aver completato la configurazione, è possibile espandere facilmente questa applicazione line-of-business aggiungendo al cluster altri server Web o macchine virtuali che eseguono SQL Server.

## Risorse aggiuntive

[Distribuire un'applicazione line-of-business a disponibilità elevata in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Progetti dell'architettura per applicazioni line-of-business](http://msdn.microsoft.com/dn630664)

[Configurare un'applicazione LOB basata sul Web in un cloud ibrido per l'esecuzione di test](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Linee guida sull'implementazione dei servizi di infrastruttura di Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carico di lavoro dei servizi di infrastruttura di Azure: farm di SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Sept15_HO3-->