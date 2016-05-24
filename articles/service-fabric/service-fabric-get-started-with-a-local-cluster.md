<properties
   pageTitle="Introduzione alla distribuzione e all'aggiornamento di app nel cluster locale | Microsoft Azure"
   description="Configurare un cluster di Infrastruttura di servizi locale, distribuire un'applicazione esistente al suo interno e quindi aggiornare l'applicazione."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/12/2016"
   ms.author="ryanwi"/>

# Introduzione alla distribuzione e all'aggiornamento di applicazioni nel cluster locale
Azure Service Fabric SDK include un ambiente di sviluppo locale completo che può essere usato per iniziare rapidamente a distribuire e gestire applicazioni in un cluster locale. Questa esercitazione descrive come creare un cluster locale, come distribuire un'applicazione locale al suo interno e quindi come aggiornare l'applicazione a una nuova versione usando Windows PowerShell.

> [AZURE.NOTE] Questo articolo presuppone che l'[ambiente di sviluppo sia già stato configurato](service-fabric-get-started.md).

## Creare un cluster locale
Un cluster di Service Fabric rappresenta un set di risorse hardware in cui è possibile distribuire le applicazioni. In genere un cluster è costituito da un minimo di 5 fino a diverse migliaia di macchine virtuali, tuttavia Service Fabric SDK include una configurazione cluster che può essere eseguita in una singola macchina virtuale.

È importante comprendere che il cluster locale di Service Fabric non è un emulatore o un simulatore. Esegue lo stesso codice della piattaforma eseguito nei cluster costituiti da più macchine virtuali. La differenza sta nel fatto che esegue in una sola macchina virtuale i processi della piattaforma che normalmente vengono eseguiti in cinque macchine virtuali.

L'SDK fornisce due modi per configurare un cluster locale: uno script di Windows PowerShell e l'app dell'area di notifica Local Cluster Manager. Per questa esercitazione verrà usato lo script di PowerShell.

> [AZURE.NOTE] Se è già stato creato un cluster locale con la distribuzione di un'applicazione da Visual Studio, è possibile ignorare questa sezione.


1. Avviare una nuova finestra di PowerShell come amministratore.

2. Eseguire lo script di installazione del cluster dalla cartella dell'SDK:

	```powershell
	& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
	```

    La configurazione del cluster richiederà qualche istante. Al termine, l'output visualizzato avrà un aspetto simile al seguente:

    ![Output installazione del cluster][cluster-setup-success]

    A questo punto, è possibile provare a distribuire un'applicazione nel cluster.

## Distribuire un'applicazione
Service Fabric SDK include un set avanzato di framework e strumenti di sviluppo per la creazione di applicazioni. Per informazioni su come creare applicazioni in Visual Studio, vedere [Creare la prima applicazione di Service Fabric in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

In questa esercitazione si userà un'applicazione di esempio esistente, denominata WordCount, per potersi concentrare sugli aspetti di gestione della piattaforma, ad esempio distribuzione, monitoraggio e aggiornamento.


1. Avviare una nuova finestra di PowerShell come amministratore.

2. Importare il modulo PowerShell di Service Fabric SDK.

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. Creare una directory per archiviare l'applicazione che verrà scaricata e distribuita, ad esempio C:\\ServiceFabric.

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. [Scaricare l'applicazione WordCount](http://aka.ms/servicefabric-wordcountapp) nel percorso creato.

5. Connettersi al cluster locale:

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Richiamare il comando di distribuzione dell'SDK per creare una nuova applicazione specificando un nome e un percorso del pacchetto dell'applicazione.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    Se l'operazione viene eseguita correttamente, verrà visualizzato un output simile al seguente:

    ![Distribuzione di un'applicazione nel cluster locale][deploy-app-to-local-cluster]

7. Per visualizzare l'applicazione in funzione, avviare il browser e passare a [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Verrà visualizzata una schermata analoga alla seguente:

    ![Interfaccia utente dell'applicazione distribuita][deployed-app-ui]

    L'applicazione WordCount è molto semplice. Include il codice JavaScript lato client per generare "parole" casuali di cinque caratteri, che vengono quindi inoltrate all'applicazione tramite l'API Web ASP.NET. Un servizio con stato tiene traccia del numero di parole conteggiate, che vengono partizionate in base al primo carattere della parola.

    L'applicazione distribuita contiene quattro partizioni. Le parole che iniziano con le lettere dalla A alla G vengono archiviate nella prima partizione, quelle che iniziano con le lettere dalla H alla N nella seconda partizione e così via.

## Visualizzare i dettagli e lo stato dell'applicazione
Una volta distribuita l'applicazione, si osserveranno alcuni dettagli dell'app in PowerShell.

1. Eseguire una query per individuare tutte le applicazioni nel cluster:

    ```powershell
    Get-ServiceFabricApplication
    ```

    Partendo dal presupposto che è stata distribuita solo l'app WordCount, verrà visualizzato un output simile al seguente:

    ![Query per individuare tutte le applicazioni distribuite in PowerShell][ps-getsfapp]

2. Passare al livello successivo eseguendo una query per trovare il set di servizi inclusi nell'applicazione WordCount.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Elenco dei servizi per l'applicazione in PowerShell][ps-getsfsvc]

    Si noti che l'applicazione è costituita da due servizi, il front-end Web e il servizio con stato che gestisce le parole.

3. Osservare infine l'elenco di partizioni per WordCountService:

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![Visualizzazione delle partizioni del servizio in PowerShell][ps-getsfpartitions]

    Il set di comandi appena usati, come tutti i comandi di PowerShell per Service Fabric, sono disponibili per tutti i cluster a cui ci si connette, locali o remoti.

    Per un'interazione più visiva con il cluster, è possibile usare lo strumento basato sul Web Service Fabric Explorer, digitando l'indirizzo [http://localhost:19080/Explorer](http://localhost:19080/Explorer) nella barra degli indirizzi del browser.

    ![Visualizzazione dei dettagli dell'applicazione in Service Fabric Explorer][sfx-service-overview]

    > [AZURE.NOTE] Per altre informazioni su Service Fabric Explorer, vedere [Visualizzazione del cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

## Aggiornare un'applicazione
Con l'infrastruttura di servizi è possibile eseguire aggiornamenti senza tempi di inattività, grazie al monitoraggio dell'integrità dell'applicazione durante il rollout nel cluster. Ora verrà eseguito un semplice aggiornamento dell'applicazione WordCount.

La nuova versione dell'applicazione conterà solo le parole che iniziano con una vocale. Durante il rollout dell'aggiornamento, si noteranno due variazioni nel comportamento dell'applicazione. In primo luogo, l'incremento del conteggio dovrebbe rallentare, perché viene conteggiato un minor numero di parole. Quindi, poiché la prima partizione contiene due vocali (A ed E) e tutte le altre ne contengono una, a un certo punto il conteggio della prima inizierà a superare quello delle altre.

1. [Scaricare il pacchetto WordCount v2](http://aka.ms/servicefabric-wordcountappv2) e salvarlo nello stesso percorso in cui è stato scaricato il pacchetto v1.

2. Tornare alla finestra di PowerShell e usare il comando di aggiornamento dell'SDK per registrare la nuova versione nel cluster. Iniziare quindi ad aggiornare l'applicazione fabric:/WordCount.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    Quando inizia l'aggiornamento, in PowerShell verrà visualizzato un output simile al seguente.

    ![Stato dell'aggiornamento in PowerShell][ps-appupgradeprogress]

3. Sarà più facile monitorare lo stato dell'aggiornamento da Service Fabric Explorer. Avviare una finestra del browser e passare all'indirizzo [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Espandere **Applicazioni** nell'albero a sinistra, quindi scegliere **WordCount** e infine **fabric:/WordCount**. Nella scheda Informazioni di base verrà visualizzato lo stato dell'operazione di aggiornamento nei domini di aggiornamento del cluster.

    ![Stato dell'aggiornamento in Service Fabric Explorer][sfx-upgradeprogress]

    Durante l'operazione di aggiornamento in ogni dominio vengono eseguiti controlli di integrità, per garantire che il comportamento dell'applicazione risulti corretto.

4. Se si esegue di nuovo la query precedente per il set di servizi inclusi nell'applicazione fabric:/WordCount, si noterà che la versione di WordCountService è cambiata, mentre la versione di WordCountWebService è rimasta uguale:

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Query per individuare i servizi dell'applicazione dopo l'aggiornamento][ps-getsfsvc-postupgrade]

    Illustra il modo in cui Service Fabric gestisce gli aggiornamenti dell'applicazione. Agisce solo sul set di servizi, o sui pacchetti di codice o di configurazione all'interno di tali servizi, che sono stati modificati, accelerando il processo di aggiornamento e aumentandone l'affidabilità.

5. Infine, tornare di nuovo alla finestra del browser per osservare il comportamento della nuova versione dell'applicazione. Come previsto, il conteggio aumenta più lentamente e la prima partizione presenta un volume maggiore di quello delle altre.

    ![Visualizzazione della nuova versione dell'applicazione nel browser][deployed-app-ui-v2]

## Passaggi successivi
- Dopo aver distribuito e aggiornato alcune applicazioni precompilate, è possibile [provare a creare un'applicazione personalizzata in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
- Tutte le azioni eseguite nel cluster locale nel corso di questa esercitazione, possono anche essere eseguite in un [cluster di Azure](service-fabric-cluster-creation-via-portal.md).
- L'aggiornamento eseguito in questa esercitazione è molto semplice. Per altre informazioni sulle potenzialità e sulla flessibilità degli aggiornamenti di Service Fabric, vedere la [documentazione relativa all'aggiornamento](service-fabric-application-upgrade.md).

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png

<!---HONumber=AcomDC_0518_2016-->