<properties
   pageTitle="Aggiornare l'ambiente di sviluppo di Service Fabric"
   description="Aggiornare l'ambiente di sviluppo di Service Fabric per utilizzare il runtime, l’SDK e gli strumenti più recenti."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2015"
   ms.author="seanmck"/>

# Aggiornare l'ambiente di sviluppo di Service Fabric

 Service Fabric fornisce periodicamente nuove versioni di runtime, SDK e strumenti da utilizzare nello sviluppo locale. Mantenendo aggiornato l'ambiente di sviluppo locale con tali versioni, l’utente avrà accesso sempre alle funzionalità più recenti, alle correzioni di bug e ai miglioramenti delle prestazioni durante la compilazione e i test delle applicazioni in locale.

## Eseguire la pulizia del cluster locale

 Service Fabric attualmente non supporta l'aggiornamento del runtime durante l'esecuzione di un cluster locale. Per garantire un aggiornamento corretto, è importante eseguire prima la pulizia del cluster locale.

 >[AZURE.NOTE]La pulizia del cluster locale consente di rimuovere tutte le applicazioni distribuite e i relativi dati.

 È possibile eseguire la pulizia del cluster locale come segue:


 1. Chiudere tutte le altre finestre di PowerShell e avviarne una nuova come amministratore.

 2. Passare alla directory di installazione del cluster con `cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"`

 3. Eseguire `.\CleanCluster.ps1`


## Installare il runtime, l'SDK e gli strumenti

 Una volta eseguita correttamente la pulizia del cluster esistente, è possibile procedere con l'aggiornamento come segue:


 1. Avviare l'installazione guidata piattaforma Web per [eseguire l’aggiornamento alla nuova versione][1].

 2. Al termine, avviare una nuova finestra di PowerShell come amministratore e passare alla directory di installazione del cluster con `cd "$ENV:ProgramFiles\Microsoft SDKs\ServiceFabric\ClusterSetup"`.

 3. Eseguire la pulizia di `.\DevClusterSetup.ps1` per configurare il cluster locale

L'operazione è terminata. È ora possibile avviare Visual Studio e continuare la compilazione delle applicazioni di Service Fabric.

>[AZURE.NOTE]La struttura predefinita del progetto è stata modificata in questa versione. Dovrebbe essere possibile aprire ed eseguire i progetti esistenti in Visual Studio. Tuttavia, se si verificano problemi con la compilazione, la distribuzione o il debug delle applicazioni, considerare la creazione di un nuovo progetto e la migrazione del codice.

 [1]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "Collegamento WebPI"

<!---HONumber=August15_HO6-->