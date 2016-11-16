---
title: Introduzione alla distribuzione e all&quot;aggiornamento di app nel cluster locale | Documentazione Microsoft
description: Configurare un cluster di Infrastruttura di servizi locale, distribuire un&quot;applicazione esistente al suo interno e quindi aggiornare l&quot;applicazione.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 60a1f6a5-5478-46c0-80a8-18fe62da17a8
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/09/2016
ms.author: ryanwi;mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 515daddf2c118f26721a557b0caf5d5415cb22c5


---
# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Introduzione alla distribuzione e all'aggiornamento di applicazioni nel cluster locale
Azure Service Fabric SDK include un ambiente di sviluppo locale completo che può essere usato per iniziare rapidamente a distribuire e gestire applicazioni in un cluster locale. Questo articolo descrive come creare un cluster locale, distribuire un'applicazione esistente nel cluster e quindi aggiornare l'applicazione a una nuova versione usando Windows PowerShell.

> [!NOTE]
> Questo articolo presuppone che l' [ambiente di sviluppo sia già stato configurato](service-fabric-get-started.md).
> 
> 

## <a name="create-a-local-cluster"></a>Creare un cluster locale
Un cluster di Service Fabric rappresenta un set di risorse hardware in cui è possibile distribuire le applicazioni. In genere un cluster è costituito da un minimo di 5 fino a diverse migliaia di macchine virtuali, tuttavia Service Fabric SDK include una configurazione cluster che può essere eseguita in una singola macchina virtuale.

È importante comprendere che il cluster locale di Service Fabric non è un emulatore o un simulatore. Esegue lo stesso codice della piattaforma eseguito nei cluster costituiti da più macchine virtuali. La differenza sta nel fatto che esegue in una sola macchina virtuale i processi della piattaforma che normalmente vengono eseguiti in cinque macchine virtuali.

L'SDK fornisce due modi per configurare un cluster locale: uno script di Windows PowerShell e l'app dell'area di notifica Local Cluster Manager. Per questa esercitazione si userà lo script di PowerShell.

> [!NOTE]
> Se è già stato creato un cluster locale con la distribuzione di un'applicazione da Visual Studio, è possibile ignorare questa sezione.
> 
> 

1. Avviare una nuova finestra di PowerShell come amministratore.
2. Eseguire lo script di installazione del cluster dalla cartella dell'SDK:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```
   
    La configurazione del cluster richiede alcuni istanti. Al termine, l'output visualizzato sarà simile al seguente:
   
    ![Output installazione del cluster][cluster-setup-success]
   
    A questo punto, è possibile provare a distribuire un'applicazione nel cluster.

## <a name="deploy-an-application"></a>Distribuire un'applicazione
Service Fabric SDK include un set avanzato di framework e strumenti di sviluppo per la creazione di applicazioni. Per informazioni su come creare applicazioni in Visual Studio, vedere [Creare la prima applicazione di Service Fabric in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

In questa esercitazione si userà un'applicazione di esempio esistente, denominata WordCount, per potersi concentrare sugli aspetti di gestione della piattaforma, ad esempio distribuzione, monitoraggio e aggiornamento.

1. Avviare una nuova finestra di PowerShell come amministratore.
2. Importare il modulo PowerShell di Service Fabric SDK.
   
    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```
3. Creare una directory per archiviare l'applicazione scaricata e distribuita, ad esempio C:\ServiceFabric.
   
    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```
4. [Scaricare l'applicazione WordCount](http://aka.ms/servicefabric-wordcountapp) nel percorso creato.  Nota: il browser Microsoft Edge salva il file con l'estensione *zip* .  Modificare l'estensione di file in *sfpkg*.
5. Connettersi al cluster locale:
   
    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```
6. Creare una nuova applicazione con comando di distribuzione dell'SDK specificando il nome e il percorso del pacchetto dell'applicazione.
   
    ```powershell  
   Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```
   
    Se l'operazione viene eseguita correttamente, verrà visualizzato un output simile al seguente:
   
    ![Distribuzione di un'applicazione nel cluster locale][deploy-app-to-local-cluster]
7. Per visualizzare l'applicazione in funzione, avviare il browser e passare a [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Dovrebbe essere visualizzato:
   
    ![Interfaccia utente dell'applicazione distribuita][deployed-app-ui]
   
    L'applicazione WordCount è molto semplice. Include il codice JavaScript lato client per generare "parole" casuali di cinque caratteri, che vengono quindi inoltrate all'applicazione tramite l'API Web ASP.NET. Un servizio con stato tiene traccia del numero di parole conteggiate, che vengono partizionate in base al primo carattere della parola. È possibile trovare il codice sorgente per l'app WordCount negli [esempi introduttivi](https://azure.microsoft.com/documentation/samples/service-fabric-dotnet-getting-started/).
   
    L'applicazione distribuita contiene quattro partizioni. Le parole che iniziano con le lettere dalla A alla G vengono archiviate nella prima partizione, quelle che iniziano con le lettere dalla H alla N nella seconda partizione e così via.

## <a name="view-application-details-and-status"></a>Visualizzare i dettagli e lo stato dell'applicazione
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
   
    Si noti che l'applicazione è costituita da due servizi: il front-end Web e il servizio con stato che gestisce le parole.
3. Osservare infine l'elenco di partizioni per WordCountService:
   
    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```
   
    ![Visualizzazione delle partizioni del servizio in PowerShell][ps-getsfpartitions]
   
    Il set di comandi usati, come tutti i comandi di PowerShell per Service Fabric, è disponibile per tutti i cluster a cui ci si connette, locali o remoti.
   
    Per un'interazione più visiva con il cluster, è possibile usare lo strumento basato sul Web Service Fabric Explorer, digitando l'indirizzo [http://localhost:19080/Explorer](http://localhost:19080/Explorer) nella barra degli indirizzi del browser.
   
    ![Visualizzazione dei dettagli dell'applicazione in Service Fabric Explorer][sfx-service-overview]
   
   > [!NOTE]
   > Per altre informazioni su Service Fabric Explorer, vedere [Visualizzazione del cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
   > 
   > 

## <a name="upgrade-an-application"></a>Aggiornare un'applicazione
Con l'infrastruttura di servizi è possibile eseguire aggiornamenti senza tempi di inattività, grazie al monitoraggio dell'integrità dell'applicazione durante il rollout nel cluster. Ora verrà eseguito un semplice aggiornamento dell'applicazione WordCount.

La nuova versione dell'applicazione ora conta solo le parole che iniziano con una vocale. Durante il rollout dell'aggiornamento si noteranno due variazioni nel comportamento dell'applicazione. In primo luogo, l'incremento del conteggio dovrebbe rallentare, perché viene conteggiato un minor numero di parole. Quindi, poiché la prima partizione contiene due vocali (A ed E) e tutte le altre ne contengono una, a un certo punto il conteggio della prima inizierà a superare quello delle altre.

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
4. Se si esegue di nuovo la query precedente per il set di servizi nell'applicazione fabric:/WordCount, si noterà che la versione di WordCountService è cambiata, mentre la versione di WordCountWebService è rimasta uguale:
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Query per individuare i servizi dell'applicazione dopo l'aggiornamento][ps-getsfsvc-postupgrade]
   
    Illustra il modo in cui Service Fabric gestisce gli aggiornamenti dell'applicazione. Agisce solo sul set di servizi, o sui pacchetti di codice o di configurazione all'interno di tali servizi, che sono stati modificati, accelerando il processo di aggiornamento e aumentandone l'affidabilità.
5. Infine, tornare di nuovo alla finestra del browser per osservare il comportamento della nuova versione dell'applicazione. Come previsto, il conteggio aumenta più lentamente e la prima partizione presenta un volume maggiore di quello delle altre.
   
    ![Visualizzazione della nuova versione dell'applicazione nel browser][deployed-app-ui-v2]

## <a name="cleaning-up"></a>+ Cleaning up
Prima di concludere, è importante ricordare che il cluster locale è reale. L'esecuzione delle applicazioni continua in background finché non vengono rimosse.  A seconda della natura delle app, un'app in esecuzione può richiedere risorse significative del computer. Sono disponibili diverse opzioni per gestire le applicazioni e il cluster:

1. Per rimuovere una singola applicazione con tutti i dati, eseguire quanto segue:
   
    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```
   
    In alternativa, eliminare l'applicazione dal menu **AZIONI** in Service Fabric Explorer o dal menu di scelta rapida nella vista elenco dell'applicazione del riquadro a sinistra.
   
    ![Eliminare un'applicazione in Service Fabric Explorer][sfe-delete-application]
2. Dopo avere eliminato l'applicazione dal cluster, è possibile annullare la registrazione delle versioni 1.0.0 e 2.0.0 del tipo di applicazione WordCount. L'operazione di eliminazione rimuove i pacchetti dell'applicazione, inclusi il codice e la configurazione, dall'archivio immagini del cluster.
   
    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```
   
    In alternativa, in Service Fabric Explorer scegliere **Unprovision Type** (Annulla provisioning del tipo) per l'applicazione.
3. Per arrestare il cluster mantenendo i dati applicazione e le tracce, fare clic su **Stop Local Cluster** (Arresta cluster locale) nell'app dell'area di notifica.
4. Per eliminare completamente il cluster, fare clic su **Remove Local Cluster** (Rimuovi cluster locale) nell'app dell'area di notifica. Questa opzione comporterà un'altra distribuzione lenta la prossima volta che si preme F5 in Visual Studio. Rimuovere il cluster locale solo se non si prevede di usarlo per un certo periodo o se è necessario recuperare risorse.

## <a name="1-node-and-5-node-cluster-mode"></a>Modalità cluster a 1 nodo e a 5 nodi
Quando si usa il cluster locale per sviluppare applicazioni, ci si ritroverà spesso a eseguire rapide iterazioni di scrittura di codice, debug, modifica del codice, debug e così via. Per ottimizzare questo processo, il cluster locale può essere eseguito in due modalità: a 1 nodo o a 5 nodi. Entrambe le modalità cluster presentano vantaggi.
La modalità cluster a 5 nodi consente di usare un vero cluster. È possibile testare gli scenari di failover e usare più istanze e repliche dei servizi.
La modalità cluster a 1 nodo è ottimizzata per eseguire rapidamente la distribuzione e la registrazione dei servizi e convalidare quindi velocemente il codice usando il runtime di Service Fabric.

Né la modalità cluster a 1 nodo né quella a 5 sono un emulatore o un simulatore. Esegue lo stesso codice della piattaforma eseguito nei cluster costituiti da più macchine virtuali.

> [!NOTE]
> Questa funzionalità è disponibile nell'SDK versione 5.2 e successiva.
> 
> 

Per cambiare la modalità cluster con un cluster a 1 nodo, usare Local Cluster Manager (Gestione cluster locale) di Service Fabric o usare PowerShell come illustrato di seguito:

1. Avviare una nuova finestra di PowerShell come amministratore.
2. Eseguire lo script di installazione del cluster dalla cartella dell'SDK:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```
   
    La configurazione del cluster richiede alcuni istanti. Al termine, l'output visualizzato sarà simile al seguente:
   
    ![Output installazione del cluster][cluster-setup-success-1-node]

Se si usa Local Cluster Manager (Gestione cluster locale) di Service Fabric:

![Cambiare la modalità cluster][switch-cluster-mode]

> [!WARNING]
> Quando si cambia la modalità cluster, il cluster corrente viene rimosso dal sistema e viene creato un nuovo cluster. I dati archiviati nel cluster, verranno eliminati quando si cambia la modalità cluster.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* Dopo aver distribuito e aggiornato alcune applicazioni precompilate, è possibile [provare a creare un'applicazione personalizzata in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
* Tutte le azioni eseguite nel cluster locale descritte in questo articolo possono essere eseguite anche in un [cluster di Azure](service-fabric-cluster-creation-via-portal.md) .
* L'aggiornamento eseguito in questo articolo è semplice. Per altre informazioni sulle potenzialità e sulla flessibilità degli aggiornamenti di Service Fabric, vedere la [documentazione relativa all'aggiornamento](service-fabric-application-upgrade.md) .

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
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png



<!--HONumber=Nov16_HO2-->


