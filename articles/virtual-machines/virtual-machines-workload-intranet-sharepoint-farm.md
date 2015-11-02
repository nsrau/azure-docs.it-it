<properties
	pageTitle="Farm di SharePoint Server 2013 in Azure | Microsoft Azure"
	description="Informazioni sul valore di una farm di SharePoint Server 2013 in Azure, configurare un ambiente di test e distribuire una configurazione a disponibilità elevata."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# Carico di lavoro dei servizi di infrastruttura di Azure: farm di SharePoint Intranet

[AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)]Modello di distribuzione di Gestione risorse

Impostando la prima o la prossima farm di SharePoint in Microsoft Azure, è possibile usufruire della facilità di configurazione e della possibilità di espandere rapidamente la farm per includere nuova capacità o ottimizzare la funzionalità principale. Molte farm di SharePoint partono con una configurazione a tre livelli a disponibilità elevata standard e diventano farm con dieci o più server ottimizzati per le prestazioni o per ruoli distinti, ad esempio la ricerca o la memorizzazione nella cache distribuita.

Con le funzionalità per macchine virtuali e reti virtuali dei servizi di infrastruttura di Azure, è possibile distribuire ed eseguire rapidamente una farm di SharePoint connessa in modo trasparente alla rete locale. Ad esempio, è possibile impostare la rete seguente.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

Poiché la rete virtuale di Azure è un'estensione della rete locale con la denominazione e il routing del traffico già impostati correttamente, gli utenti potranno accedervi come se si trovasse in un data center in locale.

Questa configurazione consente di espandere facilmente la farm di SharePoint aggiungendo nuove macchine virtuali di Azure in cui i costi operativi dell'hardware e della manutenzione sono inferiori a quelli relativi alla gestione di una farm equivalente nel data center.

L'hosting di una farm di SharePoint Intranet nei servizi di infrastruttura di Azure è un esempio di applicazione line-of-business. Per una panoramica, vedere il [diagramma dell'architettura delle applicazioni line-of-business](http://msdn.microsoft.com/dn630664).

Il passaggio successivo consiste nel configurare una farm di SharePoint Intranet di sviluppo/testing.

> [AZURE.NOTE]Microsoft ha rilasciato l'anteprima di SharePoint Server 2016 IT. Per rendere questa versione di anteprima semplice da installare e testare, è possibile utilizzare un'immagine della raccolta della macchina virtuale di Azure con l’anteprima di SharePoint Server 2016 IT e i relativi prerequisiti pre-installati. Per ulteriori informazioni, vedere [Testare l’anteprima di SharePoint Server 2016 IT in Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Creare una farm di SharePoint Intranet di sviluppo/testing ospitata in Azure

Per creare un ambiente di sviluppo/testing per una farm di SharePoint ospitata in Azure, è possibile scegliere tra due opzioni:

- Rete virtuale solo cloud
- Rete virtuale cross-premise

È possibile creare questi ambienti di sviluppo/testing gratuitamente con il proprio [abbonamento a MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/) o una [sottoscrizione di valutazione di Azure](http://azure.microsoft.com/pricing/free-trial/).

### Rete virtuale solo cloud

Una rete virtuale solo cloud non è connessa a una rete locale. Se si vuole solo creare rapidamente una farm di SharePoint di base o a disponibilità elevata, vedere l'articolo relativo alla [server farm di SharePoint](virtual-machines-sharepoint-farm-azure-preview.md). L'esempio seguente illustra la configurazione di base di una farm di SharePoint.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/SPFarm_Basic.png)

### Rete virtuale cross-premise

Una rete virtuale cross-premise è connessa a una rete locale mediante una connessione ExpressRoute o VPN da sito a sito. Se si vuole creare un ambiente di sviluppo/testing che simuli la configurazione finale e provare ad accedere al server SharePoint e a eseguire le attività di amministrazione remota su una connessione VPN, vedere [Configurazione di una farm Intranet di SharePoint in un cloud ibrido per l'esecuzione di test](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

![](./media/virtual-machines-workload-intranet-sharepoint-farm/CreateSPFarmHybridCloud.png)

Il passaggio successivo consiste nel creare una farm di SharePoint Intranet a disponibilità elevata in Azure.

## Distribuire una farm di SharePoint Intranet ospitata in Azure

L'esempio seguente illustra la configurazione rappresentativa di base relativa a una farm di SharePoint Intranet a disponibilità elevata funzionante in Azure.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

È costituita dagli elementi seguenti:

- Una farm di SharePoint Intranet con due server a livello di Web, applicazione e database
- Configurazione di gruppi di disponibilità SQL Server AlwaysOn con due server SQL e un computer del nodo di maggioranza in un cluster
- Azure Active Directory nella rete virtuale con due controller di dominio di replica

Per prendere visione di tale configurazione come infografica, vedere [SharePoint con SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788).

### Distinta base

Questa configurazione di base richiede il set seguente di componenti e servizi di Azure:

- Nove macchine virtuali
- Quattro dischi dati aggiuntivi per i controller di dominio e le istanze di SQL Server
- Tre servizi cloud
- Quattro set di disponibilità
- Una rete virtuale cross-premise
- Un account di archiviazione
- Una sottoscrizione di Azure

### Fasi di distribuzione

Per distribuire questa configurazione, usare il processo seguente:

- Fase 1: Configurare Azure.

	Usare il portale di Azure e Azure PowerShell per creare un account di archiviazione, i servizi cloud e una rete virtuale cross-premise. Per i passaggi di configurazione dettagliati, vedere [Fase 1](virtual-machines-workload-intranet-sharepoint-phase1.md).

- Fase 2: Configurare i controller di dominio.

	Configurare due controller di dominio di replica di Azure Active Directory e le impostazioni DNS per la rete virtuale. Per i passaggi di configurazione dettagliati, vedere [Fase 2](virtual-machines-workload-intranet-sharepoint-phase2.md).

- Fase 3: Configurare l'infrastruttura di SQL Server

	Preparare le macchine virtuali di SQL Server per l'uso con SharePoint e creare il cluster di SQL Server. Per i passaggi di configurazione dettagliati, vedere [Fase 3](virtual-machines-workload-intranet-sharepoint-phase3.md).

- Fase 4: Configurare i server SharePoint

	Configurare le quattro macchine virtuali di SharePoint per una nuova farm di SharePoint. Per la configurazione dettagliata, vedere [Fase 4](virtual-machines-workload-intranet-sharepoint-phase4.md).

- Fase 5: Creare un gruppo di disponibilità AlwaysOn

	Preparare i database di SharePoint, creare un gruppo di disponibilità AlwaysOn e quindi aggiungervi i database di SharePoint. Per i passaggi di configurazione dettagliati, vedere [Fase 5](virtual-machines-workload-intranet-sharepoint-phase5.md).

Dopo aver eseguito la configurazione, è possibile espandere questa farm di SharePoint seguendo le istruzioni contenute nell'articolo relativo alle [architetture di Microsoft Azure per SharePoint 2013](http://technet.microsoft.com/library/dn635309.aspx).

## Risorse aggiuntive

[Distribuzione di SharePoint con gruppi di disponibilità di SQL Server AlwaysOn in Azure](virtual-machines-workload-deploy-spsqlao-overview.md)

[Configurare una farm Intranet di SharePoint in un cloud ibrido per l'esecuzione di test](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Architetture di Microsoft Azure per SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[SharePoint con infografica SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Farm di SharePoint ospitati nei servizi di infrastruttura di Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Linee guida sull'implementazione dei servizi di infrastruttura di Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carico di lavoro dei servizi di infrastruttura di Azure: applicazione line-of-business a disponibilità elevata](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=Oct15_HO4-->