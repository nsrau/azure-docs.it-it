<properties
	pageTitle="Farm di SharePoint Server 2013 in Azure | Microsoft Azure"
	description="Trovare i principali argomenti che descrivono come impostare un farm di sviluppo/test o produzione di SharePoint 2013 nei servizi di infrastruttura di Microsoft Azure."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="04/25/2016"
	ms.author="josephd"/>

# SharePoint nei servizi infrastruttura di Azure

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../../includes/learn-about-deployment-models-both-include.md)]

Impostare la prima o la successiva farm SharePoint Server 2013 di sviluppo/test o produzione nei servizi di infrastruttura di Microsoft Azure, dove è possibile sfruttare la facilità di configurazione e la possibilità di espandere rapidamente la farm per includere la nuova capacità o l'ottimizzazione delle funzionalità chiave.

## Base farm di sviluppo e test di SharePoint

L'ambiente creato automaticamente è costituito da tre server in una rete virtuale di Azure solo cloud: un controller di dominio, un server SQL e il server SharePoint.

Vedere l'elemento [SharePoint 2013 non-HA Farm](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/) nella sezione Marketplace del portale di Azure. Questo crea una farm di sviluppo e test di base per un sito Web di SharePoint con connessione Internet. Vedere [Creare farm di SharePoint Server](virtual-machines-windows-sharepoint-farm.md) per ulteriori dettagli.

È anche possibile usare un modello di Gestione risorse di Azure. Vedere [SharePoint](virtual-machines-linux-app-frameworks.md).

> [AZURE.NOTE] L'elemento **SharePoint Farm** nella sezione Marketplace del portale di Azure è stato rimosso.

## Farm di sviluppo e test di SharePoint a disponibilità elevata

Questo ambiente creato automaticamente è costituito da nove server in una rete virtuale Azure solo cloud: due per i controller di dominio, tre per un cluster SQL server, due server SharePoint di livello applicazione e due server di SharePoint di livello Web.

Vedere l'elemento [SharePoint 2013 HA Farm](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/) nella sezione Marketplace del portale di Azure. Questo crea una farm di sviluppo e test ad alta disponibilità per un sito Web di SharePoint con connessione Internet. Vedere [Creare farm di SharePoint Server](virtual-machines-windows-sharepoint-farm.md) per ulteriori dettagli.

È anche possibile usare un modello di Gestione risorse di Azure. Vedere [Distribuire una farm di SharePoint a nove server](virtual-machines-windows-app-frameworks.md#deploy-a-nine-server-sharepoint-farm).

> [AZURE.NOTE] L'elemento **SharePoint Farm** nella sezione Marketplace del portale di Azure è stato rimosso.

## Farm di sviluppo e test cloud ibrida

Con la [farm di SharePoint intranet in un ambiente di sviluppo e test del cloud ibrido](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md), si crea una configurazione cloud ibrida simulato che ospita una farm di SharePoint semplice e a due livelli, è possibile utilizzare per testare una farm di SharePoint intranet ospitata in Azure dal percorso su Internet.

## Farm di produzione SharePoint intranet a disponibilità elevata.

Con la distribuzione di [SharePoint 2013 con gruppi di disponibilità di SQL Server AlwaysOn in Azure](virtual-machines-windows-sp-intranet-overview.md), è possibile creare una farm di SharePoint Server 2013 intranet per l'ambiente di produzione e a disponibilità elevata in Azure.

## Passaggio successivo

- Altre configurazioni di [SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) nei servizi dell'infrastruttura di Azure.

<!---HONumber=AcomDC_0511_2016-->