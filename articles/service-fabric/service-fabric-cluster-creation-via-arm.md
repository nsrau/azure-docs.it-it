<properties
   pageTitle="Configurare un cluster di Service Fabric usando un modello di Gestione risorse di Azure | Microsoft Azure"
   description="Configurare un cluster di Service Fabric usando un modello di Gestione risorse di Azure."
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
   ms.date="02/12/2016"
   ms.author="chackdan"/>


# Configurare un cluster di Service Fabric usando un modello di Gestione risorse di Azure

Questo articolo descrive come configurare un cluster di Azure Service Fabric usando un modello di Gestione risorse di Azure. A questo scopo, è necessario che nella sottoscrizione sia incluso un numero di core sufficienti per distribuire le VM IaaS che costituiranno il cluster.

## Prerequisiti

- Per configurare un cluster sicuro, è necessario prima caricare un certificato X.509 nell'insieme di credenziali delle chiavi. A questo scopo, sarà necessario specificare l'URL dell'insieme di credenziali di origine, l'URL del certificato e l'identificazione personale del certificato.
- Per altre informazioni su come configurare un cluster sicuro, vedere l'articolo [Proteggere un cluster di Service Fabric](service-fabric-cluster-security.md).

## Acquisire un modello di Gestione risorse di esempio

In [Raccolta di modelli di avvio rapido di Azure su GitHub](https://github.com/Azure/azure-quickstart-templates) sono disponibili alcuni modelli di Gestione risorse di esempio. Tutti i nomi di modelli di Service Fabric iniziano con "service-fabric...". È possibile eseguire la ricerca di "fabric" nel repository oppure scorrere il set di modelli di esempio. Per rendere la ricerca più semplice e veloce, i modelli sono stati denominati come indicato di seguito:

- [service-fabric-unsecure-cluster-5-node-1-nodetype](http://go.microsoft.com/fwlink/?LinkId=716923) per indicare un modello di cluster non sicuro con nodo singolo a 5 nodi.

- [service-fabric-secure-cluster-5-node-1-nodetype-wad](http://go.microsoft.com/fwlink/?LinkID=716924) per indicare un modello di cluster sicuro con nodo singolo a 5 nodi abilitato per WAD.

- [service-fabric-secure-cluster-10-node-2-nodetype-wad](http://go.microsoft.com/fwlink/?LinkId=716925) per indicare un modello di cluster sicuro con nodo doppio a 10 nodi abilitato per WAD.

## Creare un modello di Gestione risorse personalizzato

Esistono due modi per creare un modello di Gestione risorse personalizzato:

1. È possibile acquisire un modello di esempio da [Raccolta di modelli di avvio rapido di Azure su GitHub](https://github.com/Azure/azure-quickstart-templates) e apportarvi modifiche.

2. Accedere al portale di Azure e usare le pagine del portale di Service Fabric per generare il modello da personalizzare. A tale scopo, seguire questi passaggi:

    a. Accedere al [portale di Azure](https://portal.azure.com/).

    b. Eseguire il processo di creazione del cluster come descritto in [Creare un cluster di Service Fabric dal portale di Azure](service-fabric-cluster-creation-via-portal.md), ma non fare clic su **Crea**. Passare invece al **Riepilogo** e scaricare il modello, come illustrato nell'immagine seguente.

 ![Schermata della pagina del cluster di Service Fabric in cui è disponibile il collegamento per scaricare un modello di Gestione risorse][DownloadTemplate]

## Distribuire il modello di Gestione risorse in Azure usando Azure PowerShell

Per istruzioni dettagliate su come distribuire il modello mediante PowerShell, vedere l'articolo relativo alla [distribuzione di modelli di Gestione risorse usando PowerShell](../resource-group-template-deploy.md).

>[AZURE.NOTE] I cluster di Service Fabric richiedono che un certo numero di nodi sia attivo in ogni momento allo scopo di mantenere la disponibilità e lo stato, ossia per "mantenere il quorum". Di conseguenza, in genere non è consigliabile spegnere tutti i computer nel cluster a meno che non sia stato prima eseguito un [backup completo dello stato](service-fabric-reliable-services-backup-restore.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
- [Sicurezza di un cluster di Service Fabric](service-fabric-cluster-security.md)
- [Gestione delle applicazioni di Infrastruttura di servizi in Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_0218_2016-->