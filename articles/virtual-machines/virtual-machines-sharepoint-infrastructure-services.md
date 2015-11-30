<properties
	pageTitle="Farm di SharePoint Server 2013 in Azure | Microsoft Azure"
	description="Trovare i principali argomenti che descrivono come impostare un farm di sviluppo/test o produzione di SharePoint 2013 nei servizi di infrastruttura di Microsoft Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# SharePoint nei servizi infrastruttura di Azure

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../../includes/learn-about-deployment-models-both-include.md)]

Impostare la prima o successiva di sviluppo e test o produzione farm di SharePoint nei servizi infrastruttura di Microsoft Azure, dove è possibile sfruttare facilità di configurazione e la possibilità di espandere rapidamente la farm per includere la nuova capacità o l'ottimizzazione delle funzionalità chiave.

> [AZURE.NOTE]Microsoft ha rilasciato l'anteprima di SharePoint Server 2016 IT. Per rendere questa versione di anteprima semplice da installare e testare, è possibile utilizzare un'immagine della raccolta della macchina virtuale di Azure con l’anteprima di SharePoint Server 2016 IT e i relativi prerequisiti pre-installati. Per ulteriori informazioni, vedere [Testare l’anteprima di SharePoint Server 2016 IT in Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Base farm di sviluppo e test di SharePoint

L'ambiente creato automaticamente è costituito da tre server in una rete virtuale di Azure solo cloud: un controller di dominio, un server SQL e il server SharePoint.

Vedere l'elemento [SharePoint 2013 non-HA Farm](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/) nella sezione Marketplace del portale di anteprima di Azure. Questo crea una farm di sviluppo e test di base per un sito Web di SharePoint con connessione Internet. Vedere [Creare farm di SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md) per ulteriori dettagli.

È anche possibile usare un modello di Gestione risorse di Azure. Vedere [Distribuire una farm di SharePoint a tre server](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm).

> [AZURE.NOTE]L'elemento **SharePoint Farm** nella sezione Marketplace del portale di anteprima di Azure è stato rimosso.

## Farm di sviluppo e test di SharePoint a disponibilità elevata

Questo ambiente creato automaticamente è costituito da nove server in una rete virtuale Azure solo cloud: due per i controller di dominio, tre per un cluster SQL server, due server SharePoint di livello applicazione e due server di SharePoint di livello Web.

Vedere l'elemento [SharePoint 2013 HA Farm](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/) nella sezione Marketplace del portale di anteprima di Azure. Questo crea una farm di sviluppo e test ad alta disponibilità per un sito Web di SharePoint con connessione Internet. Vedere [Creare farm di SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md) per ulteriori dettagli.

È anche possibile usare un modello di Gestione risorse di Azure. Vedere [Distribuire una farm di SharePoint a nove server](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm).

> [AZURE.NOTE]L'elemento **SharePoint Farm** nella sezione Marketplace del portale di anteprima di Azure è stato rimosso.

## Farm di sviluppo e test cloud ibrida

Con la [farm di SharePoint intranet in un ambiente di sviluppo e test del cloud ibrido](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), si crea una configurazione cloud ibrida simulato che ospita una farm di SharePoint semplice e a due livelli, è possibile utilizzare per testare una farm di SharePoint intranet ospitata in Azure dal percorso su Internet.

Questa configurazione usa il modello di distribuzione classica.

## Farm di produzione SharePoint intranet a disponibilità elevata.

Con la distribuzione di [SharePoint 2013 con gruppi di disponibilità di SQL Server AlwaysOn in Azure](virtual-machines-workload-intranet-sharepoint-overview.md), è possibile creare una farm di SharePoint Server 2013 intranet per l'ambiente di produzione e a disponibilità elevata in Azure.

Questa configurazione usa il modello di distribuzione classica.

## Risorse aggiuntive

[Architetture di Microsoft Azure per SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Siti Internet in Microsoft Azure tramite SharePoint Server 2013](https://technet.microsoft.com/library/dn635307.aspx)

[Ripristino di emergenza di SharePoint Server 2013 in Microsoft Azure](https://technet.microsoft.com/library/dn635313.aspx)

[Utilizzo di Microsoft Azure Active Directory per l'autenticazione di SharePoint 2013](https://technet.microsoft.com/library/dn635311.aspx)

[Distribuire la sincronizzazione delle directory di Office 365 (DirSync) in Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

<!---HONumber=Nov15_HO4-->