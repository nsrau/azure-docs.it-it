<properties 
	pageTitle="SharePoint nei servizi infrastruttura di Azure" 
	description="Ottenere i principali argomenti che descrivono come impostare un sviluppo/test o produzione farm di SharePoint 2013 nei servizi dell&#39;infrastruttura di Azure." 
	documentationCenter="" 
	services="virtual-machines"
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="josephd"/>

# SharePoint nei servizi infrastruttura di Azure

Impostare la prima o successiva di sviluppo e test o produzione farm di SharePoint nei servizi infrastruttura di Microsoft Azure, dove è possibile sfruttare facilità di configurazione e la possibilità di espandere rapidamente la farm per includere la nuova capacità o l'ottimizzazione delle funzionalità chiave.

## Base farm di sviluppo e test di SharePoint 

È possibile utilizzare il [Server Farm di SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) modello nel portale di anteprima di Azure per creare una farm di sviluppo e test di base per un sito web di SharePoint con connessione Internet.

L'ambiente creato automaticamente è costituito da tre server per un controller di dominio, un server SQL e SharePoint server in una rete virtuale Azure solo cloud.

## Farm di sviluppo e test di SharePoint a disponibilità elevata

È inoltre possibile utilizzare il [Server Farm di SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) modello nel portale di anteprima di Azure per creare una farm di sviluppo e test di SharePoint a disponibilità elevata per un sito web di SharePoint con connessione Internet.

L'ambiente creato automaticamente costituito da nove server in una rete virtuale Azure solo cloud: due per i controller di dominio, tre per un cluster SQL server, due server di SharePoint del livello applicazione e due server di SharePoint del livello web.

## Farm di sviluppo e test cloud ibrida

Con la [farm di SharePoint intranet in un ambiente di sviluppo e test del cloud ibrido](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), si crea una configurazione cloud ibrida simulato che ospita una farm di SharePoint semplice e a due livelli, è possibile utilizzare per testare una farm di SharePoint intranet ospitata in Azure dal percorso su Internet.

## Farm di produzione SharePoint intranet a disponibilità elevata.

Con [distribuzione di SharePoint 2013 con gruppi di disponibilità di SQL Server AlwaysOn in Azure](https://msdn.microsoft.com/library/dn275959.aspx), creare un ambiente di produzione, a disponibilità elevata, farm di SharePoint Server 2013 intranet in Azure.

## Risorse aggiuntive

[SharePoint nei servizi infrastruttura di Azure](https://msdn.microsoft.com/library/dn275955.aspx)

[Pianificazione per SharePoint 2013 nei servizi infrastruttura di Azure](https://msdn.microsoft.com/library/dn275958.aspx)

[Architetture di Microsoft Azure per SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

<!--HONumber=52-->
 