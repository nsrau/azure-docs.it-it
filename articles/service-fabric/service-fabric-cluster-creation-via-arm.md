<properties
   pageTitle="Configurazione di un cluster dell'infrastruttura di servizi mediante un modello ARM | Microsoft Azure"
   description="Configurazione di un Cluster di infrastruttura di servizi mediante un modello ARM"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/19/2015"
   ms.author="chackdan"/>

# Configurare un Cluster di infrastruttura di servizi mediante un modello ARM

Questa pagina illustra come configurare un cluster di infrastruttura di servizi mediante un modello ARM. Si presuppone che la sottoscrizione preveda un numero di core sufficienti per distribuire le VM IaaS che costituiranno questo cluster.

## Prerequisiti

- Se si desidera configurare un cluster protetto, assicurarsi di aver caricato un certificato X509 per l'insieme di credenziali di chiave. Sarà necessario avere l'URL dell'insieme delle credenziali di origine, l’URL del certificato e l'identificazione personale del certificato.
-  Per altri dettagli, vedere [Sicurezza del cluster dell'infrastruttura di servizi](service-fabric-cluster-security.md).

## Acquisizione di un modello di esempio ARM

1. Sono disponibili dei modelli di esempio ARM in [Raccolta di modelli di avvio rapido di Azure su github](https://github.com/Azure/azure-quickstart-templates). Tutti i modelli dell'infrastruttura di servizi iniziano con il nome "service-fabric..". È possibile eseguire la ricerca del repository per "infrastruttura" o semplicemente scorrere il set di modelli di esempio.
2. Per consentire di trovare rapidamente ciò che si sta cercando, i modelli sono stati denominati come indicato di seguito
	1. [service-fabric-unsecure-cluster-5-node-1-nodetype](http://go.microsoft.com/fwlink/?LinkId=716923) per indicare un modello di cluster non protetto con nodo singolo a 5 nodi. 
	3. [service-fabric-secure-cluster-5-node-1-nodetype-wad](http://go.microsoft.com/fwlink/?LinkID=716924) per indicare un modello di cluster protetto con nodo singolo a 5 nodi abilitato per WAD. 
	4. [service-fabric-secure-cluster-10-node-2-nodetype-wad](http://go.microsoft.com/fwlink/?LinkId=716925) per indicare un modello di cluster protetto con nodo doppio a 10 nodi abilitato per WAD. 
	

## Creazione di un modello ARM personalizzato

2. Qui sono disponibili due opzioni 
	1. È possibile acquisire un modello di esempio da [Raccolta di modelli di avvio rapido di Azure su GitHub](https://github.com/Azure/azure-quickstart-templates) e apportarvi modifiche.
	2. Accedere al portale di azure e utilizzare le pagine del portale di infrastruttura di servizi per generare il modello per la personalizzazione. Tale procedura viene descritta di seguito.
3. Accedere al portale di Azure [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).
2. Eseguire il processo di creazione del cluster come descritto in [Creazione di un cluster di Service Fabric tramite il portale](service-fabric-cluster-creation-via-portal.md), ma non fare clic su ****crea**, passare invece al Riepilogo e scaricare il modello.

 ![DownloadTemplate][DownloadTemplate]

## Distribuire il modello ARM in Azure mediante Azure Powershell

Per istruzioni dettagliate su come distribuire il modello mediante PowerShell, fare riferimento a [Distribuzione dei modelli ARM mediante PS](resource-group-template-deploy.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
- [Protezione del Cluster di infrastruttura di servizi](service-fabric-cluster-security.md) 
- [Gestione delle applicazioni di Infrastruttura di servizi in Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introduzione al modello di integrità di Infrastruttura di servizi](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_1210_2015-->