<properties
   pageTitle="Configurare l'ambiente di sviluppo di Service Fabric"
   description="Installare il runtime, l'SDK e gli strumenti di Service Fabric e creare un cluster di sviluppo locale."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/04/2015"
   ms.author="seanmck"/>

# Configurare l'ambiente di sviluppo di Service Fabric
 Questo articolo fornisce tutte le informazioni necessarie per iniziare a creare applicazioni di [Service Fabric][1], incluse l'installazione del runtime, dell'SDK e degli strumenti e la creazione di un cluster locale.

## Prerequisiti
### Sistemi operativi supportati
Sono supportati i sistemi operativi seguenti:

- Windows 8/8.1
- Windows Server 2012 R2
- Windows 10 Technical Preview

### Visual Studio 2015

Gli strumenti per Service Fabric Preview 1 dipendono da Visual Studio 2015 RC, disponibile [qui][2].

> [AZURE.NOTE]Se non si esegue uno dei sistemi operativi supportati o si preferisce non installare Visual Studio 2015 RC nel proprio PC, è possibile [configurare una macchina virtuale di Azure][3] con Windows Server 2012 R2 e Visual Studio 2015 preinstallati usando un'immagine della raccolta di macchine virtuali.

## Installare il runtime, l'SDK e gli strumenti

L'installazione dei componenti di Service Fabric viene eseguita mediante l'Installazione guidata piattaforma Web. Per effettuare l'installazione, seguire queste istruzioni:

1. Fare clic [qui][4] per scaricare l'SDK mediante l'Installazione guidata piattaforma Web.

2. Fare clic su Installa per avviare il processo di installazione.

3. Leggere e accettare il contratto di licenza con l'utente finale.

L'installazione procederà automaticamente.

## Consentire l'esecuzione di script di PowerShell

Service Fabric usa script di Windows PowerShell per creare un cluster di sviluppo locale e per distribuire le applicazioni da Visual Studio. Per impostazione predefinita, Windows bloccherà l'esecuzione di questi script. Per abilitarli, è necessario modificare i criteri di esecuzione di PowerShell. A tale scopo, aprire PowerShell come amministratori e immettere il comando seguente:

    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser


## Installare e avviare un cluster locale
Un cluster locale rappresenta in un singolo computer di sviluppo la topologia con più computer da usare in produzione. Per installare il cluster locale, seguire questa procedura:


1. Chiudere tutte le altre finestre di PowerShell e avviarne una nuova come amministratori.

2. Passare alla directory di installazione del cluster con `cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"`

3. Eseguire `.\DevClusterSetup.ps1`

Dopo qualche secondo come output verranno visualizzate le informazioni relative ai nodi e la conferma dell'avvenuta creazione del cluster. In alcuni casi potrebbero venire visualizzati degli avvisi durante l'avvio dei servizi Naming e Host di Service Fabric. Questi avvisi sono normali e verranno seguiti momentaneamente da alcune informazioni di base relative al cluster.

> [AZURE.NOTE]Il cluster locale usa esattamente lo stesso runtime che verrà eseguito in Azure. Non viene simulato o emulato in alcun modo. L'unica differenza è rappresentata dal fatto che tutti i nodi vengono eseguiti in un singolo computer invece di essere distribuiti in più computer come saranno effettivamente in Azure.

## Verificare l'installazione del cluster

È possibile verificare che il cluster sia stato creato correttamente usando lo strumento Service Fabric Explorer incluso nell'SDK.

1. Per avviare Service Fabric Explorer, eseguire `. "$env:ProgramW6432\Microsoft SDKs\Service Fabric\Tools\ServiceFabricExplorer\ServiceFabricExplorer.exe"`

2. Espandere il nodo Onebox/Local Cluster nell'angolo superiore sinistro.

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
[3]: http://blogs.msdn.com/b/visualstudioalm/archive/2014/06/04/visual-studio-14-ctp-now-available-in-the-virtual-machine-azure-gallery.aspx "Macchina virtuale di Azure"
[4]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "Collegamento WebPI"
 

<!---HONumber=July15_HO4-->