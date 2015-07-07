<properties 
	pageTitle="Scalabilità elastica del database SQL di Azure" 
	description="Mappa della documentazione, un diagramma ad albero per la funzionalità Elastic Scale del database SQL di Azure" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="sidneyh"/>

# Argomenti degli strumenti di database elastici di database SQL di Azure

Le funzionalità di database elastici di database SQL Azure sono progettate per semplificare lo sviluppo di livelli dati e, in particolare, la gestione per gli sviluppatori di software come servizio (SaaS), in cui si utilizza un numero elevato di database per supportare una base di clienti finali dinamica.

Gli **strumenti di database elastici** consentono di creare e gestire applicazioni che usano modelli di partizionamento orizzontale per la scalabilità orizzontale in molti database. Sono compresi la libreria client di database elastico che supporta la gestione delle partizioni, il routing dipendente dai dati e funzionalità di query tra le partizioni per ADO.Net. È incluso anche lo strumento di suddivisione/unione che semplifica la gestione di partizioni consentendo il trasferimento dei dati (suddivisione) da una partizione a un'altra (unione). Questi strumenti sono complementari ai pool di database elastici che consentono una determinazione dei prezzi stimabili con caratteristiche di prestazioni garantite per qualsiasi numero di database. La funzionalità di processo associato offre uno strumento semplice e affidabile per l'aggiornamento e la gestione di ogni database nel pool con gli script T-SQL.

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/ElasticScaleMapcoded.svg" width="100%" height="100%"> </object>
 

<!---HONumber=62-->