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
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/15/2015"
   ms.author="seanmck"/>

# Preparare l'ambiente di sviluppo
 Per compilare ed eseguire [applicazioni dell'infrastruttura di servizi][1] nel computer di sviluppo, è necessario installare il runtime, l'SDK e gli strumenti e configurare un cluster locale.

 >[AZURE.NOTE]Queste istruzioni devono essere usate per l'impostazione di nuovi computer. Se è installata una versione precedente dell'infrastruttura di servizi, seguire le [istruzioni per aggiornare l'ambiente di sviluppo](service-fabric-update-your-development-environment.md).

## Prerequisiti
### Versioni del sistema operativo supportate
Sono supportati i sistemi operativi seguenti:

- Windows 8/8.1
- Windows Server 2012 R2
- Windows 10

### Visual Studio 2015

Gli strumenti per Service Fabric dipendono da Visual Studio 2015, disponibile [qui][2].

> [AZURE.NOTE]Se non si esegue uno dei sistemi operativi supportati o si preferisce non installare Visual Studio 2015 nel proprio PC, è possibile configurare una macchina virtuale di Azure con Windows Server 2012 R2 e Visual Studio 2015 preinstallati usando un'immagine della raccolta di macchine virtuali di Azure.

## Installare il runtime, l'SDK e gli strumenti

L'installazione dei componenti di Service Fabric viene eseguita mediante l'Installazione guidata piattaforma Web. Per effettuare l'installazione, seguire queste istruzioni:

1. [Scaricare l'SDK][3] mediante l'Installazione guidata piattaforma Web.

2. Fare clic su **Installa** per avviare il processo di installazione.

3. Leggere e accettare il contratto di licenza con l'utente finale.

L'installazione procederà automaticamente.

## Consentire l'esecuzione di script di PowerShell

Service Fabric usa script di Windows PowerShell per creare un cluster di sviluppo locale e per distribuire le applicazioni da Visual Studio. Per impostazione predefinita, Windows bloccherà l'esecuzione di questi script. Per abilitarli, è necessario modificare i criteri di esecuzione di PowerShell. A tale scopo, aprire PowerShell come amministratori e immettere il comando seguente:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Installare e avviare un cluster locale
Un cluster locale rappresenta in un singolo computer di sviluppo la topologia con più computer da usare in produzione. Per configurare il cluster locale, seguire questa procedura:


1. Chiudere tutte le altre finestre di PowerShell e avviarne una nuova come amministratore.

2. Passare alla directory di installazione del cluster

    ```powershell
    cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"
    ```

3. Esegui

    ```powershell
    .\DevClusterSetup.ps1
    ```

Dopo qualche secondo come output verranno visualizzate le informazioni relative ai nodi e la conferma dell'avvenuta creazione del cluster. In alcuni casi potrebbero venire visualizzati degli avvisi durante l'avvio dei servizi Naming e Host di Service Fabric. Questi avvisi sono normali e verranno seguiti momentaneamente da alcune informazioni di base relative al cluster.

> [AZURE.NOTE]Il cluster locale usa esattamente lo stesso runtime che verrà eseguito in Azure. Non viene simulato o emulato in alcun modo. L'unica differenza è rappresentata dal fatto che tutti i nodi vengono eseguiti in un singolo computer invece di essere distribuiti in più computer come saranno effettivamente in Azure.

## Verificare l'installazione del cluster

È possibile verificare che il cluster sia stato creato correttamente usando lo strumento Service Fabric Explorer incluso nell'SDK.

1. Per avviare Service Fabric Explorer, eseguire

    ```powershell
    . "$env:ProgramW6432\Microsoft SDKs\Service Fabric\Tools\ServiceFabricExplorer\ServiceFabricExplorer.exe"
    ```

2. Espandere il nodo Onebox/Cluster locale nell'angolo superiore sinistro.

3. Assicurarsi che le visualizzazioni delle applicazioni e dei nodi siano verdi.

Se un elemento qualsiasi non è verde o viene visualizzato un errore, attendere alcuni istanti e fare clic sul pulsante di aggiornamento. Se il problema persiste, leggere la [procedura di risoluzione dei problemi di installazione del cluster](service-fabric-troubleshoot-local-cluster-setup.md).

## Passaggi successivi
Ora che l'ambiente di sviluppo è pronto, è possibile iniziare a compilare ed eseguire le applicazioni.

- [Informazioni sui modelli di programmazione Reliable Actors e Reliable Services](service-fabric-choose-framework.md)
- [Introduzione all'API Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Introduzione all'API Reliable Actors](service-fabric-reliable-actors-get-started.md)
- [Prendere visione degli esempi di Service Fabric in GitHub](https://github.com/azure/servicefabric-samples)
- [Visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Pagina della campagna di Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[3]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "Collegamento WebPI"

<!---HONumber=Nov15_HO1-->