<properties
   pageTitle="Configurare l'ambiente di sviluppo | Microsoft Azure"
   description="Installare il runtime, l'SDK e gli strumenti e creare un cluster di sviluppo locale. Al termine della configurazione, sarà possibile iniziare a sviluppare applicazioni."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/26/2016"
   ms.author="seanmck"/>

# Preparare l'ambiente di sviluppo
 Per compilare ed eseguire [applicazioni di Service Fabric][1] nel computer di sviluppo, è necessario installare il runtime, l'SDK e gli strumenti, È anche necessario abilitare l'esecuzione di script Windows PowerShell inclusi nell'SDK.

## Prerequisiti
### Versioni del sistema operativo supportate
Per lo sviluppo, sono supportati i sistemi operativi seguenti:

- Windows 7
- Windows 8 e Windows 8.1
- Windows Server 2012 R2
- Windows 10

### Visual Studio 2015

Gli strumenti per Service Fabric richiedono almeno Visual Studio 2015, disponibile nel [sito Web Visual Studio][2].

> [AZURE.NOTE] Se non si esegue una delle versioni dei sistemi operativi supportati o si preferisce non installare Visual Studio nel computer locale, è possibile configurare una macchina virtuale di Azure con Windows Server 2012 R2 e Visual Studio preinstallati, usando un'immagine della raccolta di macchine virtuali di Azure.

## Installare il runtime, l'SDK e gli strumenti

L'Installazione guidata piattaforma Web esegue l'installazione dei componenti di Service Fabric. Sono disponibili tre opzioni per l'installazione:

- [Installare il runtime di Service Fabric, l'SDK e gli strumenti per Visual Studio 2015](full-bundle-vs2015)
- [Installare il runtime di Service Fabric, l'SDK e gli strumenti per Visual Studio "15" Preview](full-bundle-dev15)
- [Installare il runtime di Service Fabric e solo l'SDK, senza gli strumenti per Visual Studio](core-sdk)


## Consentire l'esecuzione di script di PowerShell

Service Fabric usa script di Windows PowerShell per creare un cluster di sviluppo locale e per distribuire le applicazioni da Visual Studio. Per impostazione predefinita, Windows bloccherà l'esecuzione di questi script. Per abilitarli, è necessario modificare i criteri di esecuzione di PowerShell. A tale scopo, aprire PowerShell come amministratori e immettere il comando seguente:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Passaggi successivi
Ora che l'ambiente di sviluppo è pronto, è possibile iniziare a compilare ed eseguire le applicazioni.

- [Creare la prima applicazione Infrastruttura di servizi in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Introduzione alla distribuzione e all'aggiornamento di applicazioni nel cluster locale](service-fabric-get-started-with-a-local-cluster.md)
- [Informazioni sui modelli di programmazione Reliable Services e Reliable Actors](service-fabric-choose-framework.md)
- [Vedere gli esempi di codice di Service Fabric in GitHub](https://aka.ms/servicefabricsamples)
- [Visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
- [Seguire il percorso di apprendimento di Service Fabric per un'ampia Introduzione alla piattaforma](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Pagina della campagna di Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Collegamento WebPI VS 2015"
[full-bundle-dev15]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Collegamento WebPI Dev15"
[core-sdk]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=ServiceFabricSDK "Collegamento WebPI Core SDK"

<!---HONumber=AcomDC_0330_2016-->