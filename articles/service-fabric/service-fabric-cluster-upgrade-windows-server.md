---
title: Aggiornare cluster autonomi di Service Fabric in Windows Server | Documentazione Microsoft
description: "Aggiornare il codice di Service Fabric e/o della configurazione che esegue un cluster autonomo di Service Fabric, con impostazione della modalità di aggiornamento del cluster"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2016
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 386102ad864d580ce280e3530bce428c532a751c


---
# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>Aggiornare il cluster autonomo di Service Fabric in Windows Server
> [!div class="op_single_selector"]
> * [Cluster di Azure](service-fabric-cluster-upgrade.md)
> * [Cluster autonomo](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

Per i sistemi attuali la progettazione a livello di aggiornamento è fondamentale per il successo a lungo termine di un prodotto. Un cluster di Service Fabric è una risorsa di cui si è proprietari. Questo articolo descrive la procedura per assicurarsi che il cluster esegua sempre le versioni supportate del codice e le configurazioni di Service Fabric.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controllo della versione di Fabric eseguita nel cluster
È possibile impostare il cluster per scaricare gli aggiornamenti automatici di Service Fabric quando Microsoft rilascia una nuova versione oppure scegliere di selezionare una versione supportata di Service Fabric in cui eseguire il cluster. 

A tale scopo, impostare la configurazione del cluster "fabricClusterAutoupgradeEnabled" su true o false.

> [!NOTE]
> Verificare che il cluster esegua sempre una versione di Service Fabric supportata. Quando viene annunciato il rilascio di una nuova versione di Service Fabric, viene segnalato il termine del periodo di supporto per la versione precedente dopo un minimo di 60 giorni da tale data. Le nuove versioni vengono annunciate nel [blog del team di Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). A questo punto è possibile scegliere la nuova versione. 
> 
> 

È possibile aggiornare il cluster alla nuova versione solo se si usa una configurazione del nodo di tipo produzione in cui ogni nodo di Service Fabric viene allocato in una macchina virtuale o fisica separata. Se si ha un cluster di sviluppo in cui sono presenti più nodi di Service Fabric in un'unica macchina virtuale o fisica, è necessario eliminare il cluster e ricrearlo con la nuova versione.

Esistono due flussi di lavoro distinti per l'aggiornamento del cluster alla versione di Service Fabric più recente o a una versione supportata. Uno per i cluster che hanno la connettività per scaricare automaticamente la versione più recente e il secondo per i cluster che non hanno la connettività per scaricare la versione più recente di Service Fabric.

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>Aggiornare i cluster con la connettività per scaricare il codice e la configurazione più recenti
Seguire questa procedura per aggiornare il cluster a una versione supportata, se i nodi del cluster hanno la connettività Internet a [http://download.microsoft.com](http://download.microsoft.com) 

Per i cluster che hanno la connettività a [http://download.microsoft.com](http://download.microsoft.com), viene periodicamente verificata la disponibilità di nuove versioni di Service Fabric.

Quando è disponibile una nuova versione di Service Fabric, il pacchetto viene scaricato localmente nel cluster e ne viene eseguito il provisioning per l'aggiornamento. Inoltre, per informare il cliente di questa nuova versione, il sistema attiva un avviso esplicito di integrità del cluster simile al seguente:

"Il supporto per la versione corrente del cluster [versione#] termina [Data].", Quando il cluster esegue la versione più recente, l'avviso non viene visualizzato.

#### <a name="cluster-upgrade-workflow"></a>Flusso di lavoro di aggiornamento del cluster.
Quando viene visualizzato l'avviso di integrità del cluster, è necessario eseguire le operazioni seguenti:

1. Connettersi al cluster da qualsiasi macchina con accesso amministrativo a tutte le macchine elencate come nodi nel cluster. La macchina in cui viene eseguito lo script non deve necessariamente far parte del cluster
   
    ```powershell
   
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```
2. Ottenere l'elenco delle versioni di Service Fabric a cui è possibile eseguire l'aggiornamento
   
    ```powershell
   
    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```
   
    L'output dovrebbe essere simile al seguente:
   
    ![ottenere versioni di Fabric][getfabversions]
3. Avviare un aggiornamento del cluster a una delle versioni disponibili tramite il [file CMD di PowerShell Start-ServiceFabricClusterUpgrade ](https://msdn.microsoft.com/library/mt125872.aspx)
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   È possibile monitorare lo stato di avanzamento dell'aggiornamento in Service Fabric Explorer oppure eseguendo il comando di PowerShell seguente
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. È possibile specificare criteri di integrità personalizzati al momento specificato. Per informazioni dettagliate sul comando Start-ServiceFabricClusterUpgrade, fare riferimento a [questo documento](https://msdn.microsoft.com/library/mt125872.aspx). 

Dopo aver risolto i problemi che hanno determinato il rollback, è necessario avviare di nuovo l'aggiornamento, seguendo la stessa procedura precedente.

### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>Aggiornare i cluster <U>privi di connettività</u> per scaricare il codice e la configurazione più recenti
Seguire questa procedura per aggiornare il cluster a una versione supportata, se i nodi del cluster ** non hanno** la connettività Internet a [http://download.microsoft.com](http://download.microsoft.com) 

> [!NOTE]
> Se si esegue un cluster non connesso a Internet, è necessario monitorare il blog del team di Service Fabric per ricevere le notifiche relative alla nuova versione. Il sistema **non** attiva alcun avviso di integrità del cluster per informare l'utente.  
> 
> 

1. Modificare la configurazione del cluster per impostare la proprietà seguente su false
   
        "fabricClusterAutoupgradeEnabled": false,

e avviare un aggiornamento della configurazione. Per informazioni dettagliate sull'utilizzo, fare riferimento a [Start-ServiceFabricClusterConfigurationUpgrade PS cmd (File CMD di PowerShell Start-ServiceFabricClusterConfigurationUpgrade)](https://msdn.microsoft.com/en-us/library/mt788302.aspx). Prima di avviare l'aggiornamento della configurazione, assicurarsi di aggiornare "clusterConfigurationVersion" nel file JSON.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File> 

```

#### <a name="cluster-upgrade-workflow"></a>Flusso di lavoro di aggiornamento del cluster.
1. Scaricare la versione più recente del pacchetto dal documento [Creare e gestire un cluster eseguito in Windows Server](service-fabric-cluster-creation-for-windows-server.md) 
2. Connettersi al cluster da qualsiasi macchina con accesso amministrativo a tutte le macchine elencate come nodi nel cluster. La macchina in cui viene eseguito lo script non deve necessariamente far parte del cluster 
   
    ```powershell
   
    ###### connect to the cluster
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```
3. Copiare il pacchetto scaricato nell'archivio immagini del cluster.
   
    ```powershell
   
   ###### Get the list of available service fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"
   
   ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```

1. Registrare il pacchetto copiato 
   
    ```powershell
   
    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 
   
    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
   
     ```
2. Avviare un aggiornamento del cluster a una delle versioni disponibili. 
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   È possibile monitorare lo stato di avanzamento dell'aggiornamento in Service Fabric Explorer oppure eseguendo il comando di PowerShell seguente
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. È possibile specificare criteri di integrità personalizzati al momento specificato. Per informazioni dettagliate sul comando Start-ServiceFabricClusterUpgrade, fare riferimento a [questo documento](https://msdn.microsoft.com/library/mt125872.aspx). 

Dopo aver risolto i problemi che hanno determinato il rollback, è necessario avviare di nuovo l'aggiornamento, seguendo la stessa procedura precedente.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come personalizzare alcune [impostazioni dei cluster di Service Fabric](service-fabric-cluster-fabric-settings.md)
* Informazioni su come [aumentare o ridurre le istanze del cluster](service-fabric-cluster-scale-up-down.md)
* Informazioni su come eseguire [aggiornamenti dell'applicazione](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG



<!--HONumber=Dec16_HO2-->


