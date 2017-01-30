---
title: Configurare un ambiente di sviluppo per i microservizi di Azure | Documentazione Microsoft
description: "Installare il runtime, l&quot;SDK e gli strumenti e creare un cluster di sviluppo locale. Al termine della configurazione, sarà possibile iniziare a sviluppare applicazioni."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/15/2016
ms.author: ryanwi, mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: 122b672c784357d781ac55e11fb9f648fdbbac24


---
# <a name="prepare-your-development-environment"></a>Preparare l'ambiente di sviluppo
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 Per compilare ed eseguire [applicazioni di Service Fabric][1] nel computer di sviluppo, installare il runtime, l'SDK e gli strumenti. È anche necessario abilitare l'esecuzione di script Windows PowerShell inclusi nell'SDK.

## <a name="prerequisites"></a>Prerequisiti
### <a name="supported-operating-system-versions"></a>Versioni del sistema operativo supportate
Per lo sviluppo, sono supportati i sistemi operativi seguenti:

* Windows 7
* Windows 8 e Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Per impostazione predefinita, Windows 7 include solo Windows PowerShell 2.0. I cmdlet di PowerShell per Service Fabric richiedono PowerShell 3.0 o versione successiva. È possibile [scaricare Windows PowerShell 5.0][powershell5-download] dall'Area download Microsoft.
> 
> 

## <a name="install-the-sdk-and-tools"></a>Installare l'SDK e gli strumenti
### <a name="to-use-visual-studio-2017-rc"></a>Per usare Visual Studio 2017 RC
Gli strumenti di Service Fabric fanno parte del carico di lavoro di sviluppo e gestione di Azure in Visual Studio 2017 RC. È necessario abilitare questo carico di lavoro durante l'installazione di Visual Studio.
È anche necessario installare Microsoft Azure Service Fabric SDK, usando Installazione guidata piattaforma Web.

* [Installare Microsoft Azure Service Fabric SDK][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Per usare Visual Studio 2015 (è necessario Visual Studio 2015 Update 2 o versioni successive)
Per Visual Studio 2015, gli strumenti di Service Fabric vengono installati con l'SDK, usando Installazione guidata piattaforma Web:

* [Installare Microsoft Azure Service Fabric SDK e gli strumenti][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Installazione solo dell'SDK
Se è necessario solo l'SDK, è possibile installare questo pacchetto:
* [Installare Microsoft Azure Service Fabric SDK][core-sdk]

> [!WARNING]
> Alcuni clienti hanno segnalato errori durante l'installazione mediante questi collegamenti di avvio o quando hanno usato tali collegamenti nel browser Chrome. Sono problemi noti dell'Installazione guidata piattaforma Web attualmente in corso di risoluzione.  Come soluzione alternativa, provare quanto segue:
>- Avviare i collegamenti dal browser Internet Explorer o Edge. Oppure
>- Avviare l'Installazione guidata piattaforma Web dal menu Start, individuare "Service Fabric" e installare l'SDK.
> 
> Ci scusiamo per l'inconveniente. 

Le versioni correnti sono:
* Service Fabric SDK 2.4.145
* Service Fabric runtime 5.4.145
* Strumenti di Visual Studio 2015 1.4.41209

Per un elenco delle versioni supportate, vedere [Service Fabric support](service-fabric-support.md) (Supporto di Service Fabric)

## <a name="enable-powershell-script-execution"></a>Consentire l'esecuzione di script di PowerShell
Service Fabric usa script di Windows PowerShell per creare un cluster di sviluppo locale e per distribuire le applicazioni da Visual Studio. Per impostazione predefinita, Windows blocca l'esecuzione di questi script. Per abilitarli, è necessario modificare i criteri di esecuzione di PowerShell. A tale scopo, aprire PowerShell come amministratori e immettere il comando seguente:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Passaggi successivi
Dopo avere configurato l'ambiente di sviluppo, iniziare a compilare ed eseguire le app.

* [Creare la prima applicazione Infrastruttura di servizi in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
* [Introduzione alla distribuzione e all'aggiornamento di applicazioni nel cluster locale](service-fabric-get-started-with-a-local-cluster.md)
* [Informazioni sui modelli di programmazione Reliable Services e Reliable Actors](service-fabric-choose-framework.md)
* [Vedere gli esempi di codice di Service Fabric in GitHub](https://aka.ms/servicefabricsamples)
* [Visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Seguire il percorso di apprendimento di Service Fabric per un'ampia Introduzione alla piattaforma](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Pagina della campagna di Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Collegamento WebPI VS 2015"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Collegamento WebPI Dev15"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Collegamento WebPI Core SDK"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395



<!--HONumber=Jan17_HO4-->


