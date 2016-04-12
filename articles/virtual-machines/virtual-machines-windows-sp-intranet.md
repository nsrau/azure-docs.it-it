<properties
	pageTitle="Farm di SharePoint Server 2013 in Azure | Microsoft Azure"
	description="Informazioni sul valore di una farm di SharePoint Server 2013 in Azure, configurare un ambiente di test e distribuire una configurazione a disponibilità elevata."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="josephd"/>

# Carico di lavoro dei servizi di infrastruttura di Azure: farm di SharePoint Intranet

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

Impostando la prima o la prossima farm di SharePoint in Microsoft Azure, è possibile usufruire della facilità di configurazione e della possibilità di espandere rapidamente la farm per includere nuova capacità o ottimizzare la funzionalità principale. Molte farm di SharePoint partono con una configurazione a tre livelli a disponibilità elevata standard e diventano farm con dieci o più server ottimizzati per le prestazioni o per ruoli distinti, ad esempio la ricerca o la memorizzazione nella cache distribuita.

Con le funzionalità per macchine virtuali e reti virtuali dei servizi di infrastruttura di Azure, è possibile distribuire ed eseguire rapidamente una farm di SharePoint connessa in modo trasparente alla rete locale. Ad esempio, è possibile impostare la seguente:

![](./media/virtual-machines-windows-sp-intranet/workload-spsqlao.png)

Poiché la rete virtuale di Azure è un'estensione della rete locale con la denominazione e il routing del traffico già impostati correttamente, gli utenti potranno accedervi come se si trovasse in un data center locale.

Questa configurazione consente di espandere facilmente la farm di SharePoint aggiungendo nuove macchine virtuali di Azure in cui i costi operativi dell'hardware e della manutenzione sono inferiori a quelli relativi alla gestione di una farm equivalente nel data center.

L'hosting di una farm di SharePoint Intranet nei servizi di infrastruttura di Azure è un esempio di applicazione line-of-business. Per una panoramica, vedere il [diagramma dell'architettura delle applicazioni line-of-business](http://msdn.microsoft.com/dn630664).

Il passaggio successivo consiste nel configurare una farm di SharePoint Intranet di sviluppo/testing.

> [AZURE.NOTE] Microsoft ha rilasciato l'anteprima di SharePoint Server 2016 IT. Per rendere questa versione di anteprima semplice da installare e testare, è possibile utilizzare un'immagine della raccolta della macchina virtuale di Azure con l’anteprima di SharePoint Server 2016 IT e i relativi prerequisiti pre-installati. Per ulteriori informazioni, vedere [Testare l’anteprima di SharePoint Server 2016 IT in Azure](https://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Creare una farm di SharePoint Intranet di sviluppo/testing ospitata in Azure

Per creare un ambiente di sviluppo/testing per una farm di SharePoint ospitata in Azure, è possibile scegliere tra due opzioni:

- Rete virtuale solo cloud
- Rete virtuale cross-premise

È possibile creare questi ambienti di sviluppo/testing gratuitamente con il proprio [abbonamento a Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) o una [sottoscrizione di prova di Azure](https://azure.microsoft.com/pricing/free-trial/).

### Rete virtuale solo cloud

Una rete virtuale solo cloud non è connessa a una rete locale. Se si vuole solo creare rapidamente una farm di SharePoint di base o a disponibilità elevata, vedere l'articolo [Creare server farm di SharePoint](virtual-machines-windows-sharepoint-farm.md). L'esempio seguente illustra la configurazione di base di una farm di SharePoint.

![](./media/virtual-machines-windows-sp-intranet/Non-HAFarm.png)

### Rete virtuale cross-premise

Una rete virtuale cross-premise è connessa a una rete locale mediante una connessione ExpressRoute o VPN da sito a sito. Se si vuole creare un ambiente di sviluppo/testing che simuli la configurazione finale e provare ad accedere al server SharePoint e a eseguire le attività di amministrazione remota su una connessione VPN, vedere [Configurazione di una farm Intranet di SharePoint in un cloud ibrido per l'esecuzione di test](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

![](./media/virtual-machines-windows-sp-intranet/CreateSPFarmHybridCloud.png)

Il passaggio successivo consiste nel creare una farm di SharePoint Intranet a disponibilità elevata in Azure.

## Distribuire una farm di SharePoint Intranet ospitata in Azure

La configurazione rappresentativa di base relativa a una farm di SharePoint Intranet a disponibilità elevata funzionante in Azure è la seguente:

![](./media/virtual-machines-windows-sp-intranet/workload-spsqlao.png)

È costituita dagli elementi seguenti:

- Una farm di SharePoint Intranet con due server a livello di Web, applicazione e database
- Configurazione di gruppi di disponibilità SQL Server AlwaysOn con due server SQL e un computer del nodo di maggioranza in un cluster
- Due controller di dominio di replica di un dominio di Active Directory locale.

Per prendere visione di tale configurazione come infografica, vedere [SharePoint con SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788).

Per distribuire questa configurazione, usare il processo seguente:

- Fase 1: Configurare Azure.

	Usare Azure PowerShell per creare un account di archiviazione, i set di disponibilità e una rete virtuale cross-premise. Per i passaggi di configurazione dettagliati, vedere [Fase 1](virtual-machines-windows-ps-sp-intranet-ph1.md).

- Fase 2: Configurare i controller di dominio.

	Configurare due controller di dominio di replica di Active Directory e le impostazioni DNS per la rete virtuale. Per i passaggi di configurazione dettagliati, vedere [Fase 2](virtual-machines-windows-ps-sp-intranet-ph2.md).

- Fase 3: Configurare l'infrastruttura di SQL Server

	Preparare le macchine virtuali di SQL Server per l'uso con SharePoint e creare il cluster di SQL Server. Per i passaggi di configurazione dettagliati, vedere [Fase 3](virtual-machines-windows-ps-sp-intranet-ph3.md).

- Fase 4: Configurare i server SharePoint

	Configurare le quattro macchine virtuali di SharePoint per una nuova farm di SharePoint. Per la configurazione dettagliata, vedere [Fase 4](virtual-machines-windows-ps-sp-intranet-ph4.md).

- Fase 5: Creare un gruppo di disponibilità AlwaysOn

	Preparare i database di SharePoint, creare un gruppo di disponibilità AlwaysOn e quindi aggiungervi i database di SharePoint. Per i passaggi di configurazione dettagliati, vedere [Fase 5](virtual-machines-windows-ps-sp-intranet-ph5.md).

Dopo aver eseguito la configurazione, è possibile espandere questa farm di SharePoint seguendo le istruzioni contenute in [Architetture di Microsoft Azure per SharePoint 2013](http://technet.microsoft.com/library/dn635309.aspx).

## Passaggio successivo

- Ottenere una [panoramica](virtual-machines-windows-sp-intranet-overview.md) del carico di lavoro di produzione prima di approfondire la configurazione.

<!---HONumber=AcomDC_0323_2016-->