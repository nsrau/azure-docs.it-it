<properties 
	pageTitle="Applicazione line-of-business in Azure | Microsoft Azure" 
	description="Informazioni sul valore di un'applicazione line-of-business in Azure, configurare un ambiente di test e distribuire una configurazione a disponibilità elevata." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2016" 
	ms.author="josephd"/>

# Carico di lavoro dei servizi di infrastruttura di Azure: applicazione line-of-business a disponibilità elevata

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

Impostando la prima o la successiva applicazione line-of-business solo Intranet basata sul Web in Microsoft Azure, è possibile usufruire della facilità di configurazione e della possibilità di espandere rapidamente l'applicazione per includere nuova capacità.
 
Con le funzionalità per macchine virtuali e reti virtuali dei servizi di infrastruttura di Azure, è possibile distribuire ed eseguire rapidamente un'applicazione line-of-business accessibile agli utenti dell'organizzazione. Ad esempio, è possibile definire la configurazione seguente.

![](./media/virtual-machines-windows-lob/workload-lobapp-phase4.png)
 
Dal momento che la rete virtuale di Azure è un'estensione della rete locale con la denominazione e il routing del traffico già impostati correttamente, gli utenti potranno accedere ai server dell'applicazione line-of-business come se si trovassero in un data center locale.

Questa configurazione consente di espandere facilmente la capacità dell'applicazione aggiungendo nuove macchine virtuali di Azure in cui i costi operativi dell'hardware e della manutenzione sono inferiori a quelli relativi alla gestione di una soluzione equivalente nel data center.

Il passaggio successivo consiste nel configurare un'applicazione line-of-business di sviluppo/testing ospitata in Azure.

## Creare un'applicazione line-of-business di sviluppo/testing ospitata in Azure

Una rete virtuale cross-premise è connessa a una rete locale mediante una connessione ExpressRoute o VPN da sito a sito. Se si vuole creare un ambiente di sviluppo/testing che simuli la configurazione finale e provare ad accedere all'applicazione e a eseguire le attività di amministrazione remota su una connessione VPN, vedere [Configurare un'applicazione LOB basata sul Web in un cloud ibrido per l'esecuzione di test](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md).

![](./media/virtual-machines-windows-lob/CreateLOBAppHybridCloud_3.png)
 
È possibile creare questo ambiente di sviluppo/test gratuitamente con il proprio [abbonamento a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) o una sottoscrizione di Azure.

Il passaggio successivo consiste nel creare un'applicazione line-of-business a disponibilità elevata in Azure.

## Distribuire un'applicazione line-of-business ospitata in Azure

La configurazione rappresentativa di base per un'applicazione line-of-business in Azure è simile alla seguente.

![](./media/virtual-machines-windows-lob/workload-lobapp-phase4.png)
 
È costituita dagli elementi seguenti:

- Un'applicazione line-of-business solo Intranet con due server a livello Web e di database
- Una configurazione SQL Server AlwaysOn con due macchine virtuali che eseguono SQL Server e un computer nodo di maggioranza in un cluster.
- Servizi di dominio Active Directory nella rete virtuale con due controller di dominio di replica

Per una panoramica delle applicazioni line-of-business, vedere [Progetti dell'architettura per applicazioni line-of-business](http://msdn.microsoft.com/dn630664).

Per distribuire questa configurazione, usare il processo seguente:

- Fase 1: Configurare Azure 

	Usare Azure PowerShell per creare gli account di archiviazione, i set di disponibilità e una rete virtuale cross-premise. Per i passaggi di configurazione dettagliati, vedere [Fase 1](virtual-machines-windows-ps-lob-ph1.md).

- Fase 2: Configurare i controller di dominio

	Configurare due controller di dominio di replica di Active Directory e le impostazioni DNS per la rete virtuale. Per i passaggi di configurazione dettagliati, vedere [Fase 2](virtual-machines-windows-ps-lob-ph2.md).

- Fase 3: Configurare l'infrastruttura di SQL Server

	Creare le macchine virtuali che eseguono SQL Server e il cluster. Per i passaggi di configurazione dettagliati, vedere [Fase 3](virtual-machines-windows-ps-lob-ph3.md).

- Fase 4: Configurare i server Web.

	Creare le macchine virtuali del server Web e aggiungervi l'applicazione line-of-business. Per la configurazione dettagliata, vedere [Fase 4](virtual-machines-windows-ps-lob-ph4.md).

- Fase 5: Configurare un gruppo di disponibilità SQL Server AlwaysOn.

	Preparare i database dell'applicazione, creare un gruppo di disponibilità SQL Server AlwaysOn e quindi aggiungervi i database dell'applicazione. Per i passaggi di configurazione dettagliati, vedere [Fase 5](virtual-machines-windows-ps-lob-ph5.md).

Dopo aver completato la configurazione, è possibile espandere facilmente questa applicazione line-of-business aggiungendo al cluster altri server Web o macchine virtuali che eseguono SQL Server.

## Passaggio successivo

- Ottenere una [panoramica](virtual-machines-windows-lob-overview.md) del carico di lavoro di produzione prima di approfondire la configurazione.

<!---HONumber=AcomDC_0518_2016-->