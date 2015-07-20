<properties 
	pageTitle="Farm di SharePoint ospitate nei servizi di infrastruttura di Azure" 
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
	ms.tgt_pltfrm="vm-windows-sharepoint" 
	ms.devlang="na" 
	ms.topic="index-page" 
	ms.date="07/07/2015" 
	ms.author="josephd"/>

# Farm di SharePoint ospitate nei servizi di infrastruttura di Azure

Impostare la prima o successiva di sviluppo e test o produzione farm di SharePoint nei servizi infrastruttura di Microsoft Azure, dove è possibile sfruttare facilità di configurazione e la possibilità di espandere rapidamente la farm per includere la nuova capacità o l'ottimizzazione delle funzionalità chiave.

## Base farm di sviluppo e test di SharePoint 

Per macchine virtuali create nella gestione dei servizi, utilizzare la funzionalità [Farm di SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md) del Portale di anteprima di Azure per creare una farm di sviluppo e test di base per un sito web di SharePoint con connessione Internet.

L'ambiente creato automaticamente è costituito da tre server per un controller di dominio, un server SQL e SharePoint server in una rete virtuale Azure solo cloud.

Per creare una configurazione simile con macchine virtuali create in Gestione risorse, utilizzare un modello. Vedere [Distribuire una farm di SharePoint da tre server](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm).

## Farm di sviluppo e test di SharePoint a disponibilità elevata

Per macchine virtuali create nella gestione dei servizi, utilizzare la funzionalità [Farm di SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md) del Portale di anteprima di Azure per creare una farm di sviluppo e test di base a disponibilità elevata per un sito web di SharePoint con connessione Internet.

L'ambiente creato automaticamente costituito da nove server in una rete virtuale Azure solo cloud: due per i controller di dominio, tre per un cluster SQL server, due server di SharePoint del livello applicazione e due server di SharePoint del livello web.

Per creare una configurazione simile con macchine virtuali create in Gestione risorse, utilizzare un modello. Vedere [Distribuire una farm di SharePoint da nove server](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm)

## Farm di sviluppo e test cloud ibrida

Con la [farm di SharePoint intranet in un ambiente di sviluppo e test del cloud ibrido](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), si crea una configurazione cloud ibrida simulato che ospita una farm di SharePoint semplice e a due livelli, è possibile utilizzare per testare una farm di SharePoint intranet ospitata in Azure dal percorso su Internet.

In questa configurazione vengono utilizzate le macchine virtuali create nella gestione dei servizi.

## Farm di produzione SharePoint intranet a disponibilità elevata.

Con [Distribuzione di SharePoint 2013 con gruppi di disponibilità di SQL Server AlwaysOn in Azure](virtual-machines-workload-intranet-sharepoint-overview.md), creare una farm di SharePoint Server 2013 intranet per l’ambiente di produzione e a disponibilità elevata in Azure.

In questa configurazione vengono utilizzate le macchine virtuali create nella gestione dei servizi.

## Risorse aggiuntive

[SharePoint nei servizi infrastruttura di Azure](https://msdn.microsoft.com/library/dn275955.aspx)

[Pianificazione per SharePoint 2013 nei servizi infrastruttura di Azure](https://msdn.microsoft.com/library/dn275958.aspx)

[Architetture di Microsoft Azure per SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)
 

<!---HONumber=July15_HO2-->