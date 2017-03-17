---
title: Aggiornare un cluster autonomo di Azure Service Fabric in Windows Server | Microsoft Docs
description: "Aggiornare il codice di Azure Service Fabric e/o della configurazione che esegue un cluster autonomo di Service Fabric e impostare la modalità di aggiornamento del cluster."
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
ms.date: 02/02/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: df15775f8e93c1dfad82c69ee4caa7838a39a545
ms.lasthandoff: 03/07/2017


---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Aggiornare il cluster autonomo di Azure Service Fabric in Windows Server
> [!div class="op_single_selector"]
> * [Cluster di Azure](service-fabric-cluster-upgrade.md)
> * [Cluster autonomo](service-fabric-cluster-upgrade-windows-server.md)
>
>

Per i sistemi attuali la progettazione a livello di aggiornamento è fondamentale per il successo a lungo termine di un prodotto. Un cluster di Azure Service Fabric è una risorsa di cui si è proprietari. Questo articolo descrive in che modo è possibile verificare che il cluster esegua sempre versioni supportate del codice e delle configurazioni di Service Fabric.

## <a name="control-the-fabric-version-that-runs-on-your-cluster"></a>Controllare la versione di Service Fabric eseguita nel cluster
È possibile impostare il cluster per scaricare gli aggiornamenti automatici di Service Fabric quando Microsoft rilascia una nuova versione. In alternativa, è possibile selezionare una versione supportata di Service Fabric in cui eseguire il cluster.

Per controllare la versione di Service Fabric, impostare la configurazione del cluster "fabricClusterAutoupgradeEnabled" su true o false.

> [!NOTE]
> Verificare che il cluster esegua sempre una versione di Service Fabric supportata. Quando viene annunciato il rilascio di una nuova versione di Service Fabric, viene segnalato che il periodo di supporto della versione precedente terminerà dopo un minimo di 60 giorni dalla data del rilascio. Le nuove versioni vengono annunciate nel [blog del team di Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).
>
>

È possibile aggiornare il cluster alla nuova versione solo se si usa una configurazione del nodo di tipo produzione, in cui ogni nodo di Service Fabric viene allocato in una macchina virtuale o fisica separata. Se si usa un cluster di sviluppo in cui sono presenti più nodi di Service Fabric in un'unica macchina virtuale o fisica, è necessario eliminare tale cluster e ricrearlo con la nuova versione.

Per aggiornare il cluster alla versione di Service Fabric più recente o comunque a una versione supportata, sono disponibili i due flussi di lavoro seguenti:


*   I cluster con connettività scaricano automaticamente la versione più recente
*   I cluster senza connettività scaricano la versione più recente di Service Fabric

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>Aggiornare i cluster con la connettività per scaricare il codice e la configurazione più recenti
Seguire questa procedura per aggiornare il cluster a una versione supportata, se i nodi del cluster dispongono di connettività Internet all'[Area download Microsoft](http://download.microsoft.com).

Per i cluster che dispongono della connettività all'[Area download Microsoft](http://download.microsoft.com), viene periodicamente verificata la disponibilità di nuove versioni di Service Fabric.

Quando è disponibile una nuova versione di Service Fabric, il pacchetto viene scaricato localmente nel cluster e ne viene eseguito il provisioning per l'aggiornamento. Inoltre, per informare il cliente di questa nuova versione, il sistema attiva un avviso esplicito di integrità del cluster simile al seguente:

"Il supporto per la versione corrente del cluster [versione] termina il [Data]."

Quando il cluster inizia a eseguire la versione più recente, l'avviso non viene più visualizzato.

#### <a name="cluster-upgrade-workflow"></a>Flusso di lavoro per l'aggiornamento del cluster
Quando viene visualizzato l'avviso di integrità del cluster, è necessario eseguire le operazioni seguenti:

1. Connettersi al cluster da qualsiasi macchina con accesso amministrativo a tutte le macchine elencate come nodi nel cluster. La macchina in cui viene eseguito lo script seguente non deve necessariamente far parte del cluster.

    ```powershell

    ###### Connect to the secure cluster using certs
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

2. Ottenere l'elenco delle versioni di Service Fabric a cui è possibile eseguire l'aggiornamento.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    L'output dovrebbe essere simile al seguente:

    ![Ottenere le versioni di Service Fabric][getfabversions]

3. Avviare un aggiornamento del cluster a una delle versioni disponibili usando il comando [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx) di Windows PowerShell.

    ```powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Cluster upgrade example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Per monitorare lo stato di avanzamento dell'aggiornamento, è possibile usare Service Fabric Explorer o eseguire il comando di Windows PowerShell seguente.

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. È possibile specificare criteri di integrità personalizzati al momento dell'esecuzione del comando Start-ServiceFabricClusterUpgrade. Per altre informazioni, vedere [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Dopo aver risolto i problemi che hanno determinato il rollback, è necessario avviare di nuovo l'aggiornamento ripetendo la procedura descritta in precedenza.

### <a name="upgrade-the-clusters-with-no-connectivity-to-download-the-latest-code-and-configuration"></a>Aggiornare i cluster privi di connettività per scaricare il codice e la configurazione più recenti
Se i nodi del cluster non dispongono di connettività Internet all'[Area download Microsoft](http://download.microsoft.com), seguire le procedure descritte in questa sezione per aggiornare il cluster a una versione supportata.

> [!NOTE]
> Se si esegue un cluster non connesso a Internet, è necessario monitorare il [blog del team di Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) per eventuali notifiche relative alla nuova versione. Il sistema *non* attiva alcun avviso di integrità del cluster per segnalare eventuali problemi relativi alla versione.  
>
>

1. Modificare la configurazione del cluster per impostare la proprietà seguente su false

        "fabricClusterAutoupgradeEnabled": false,

2.      Avviare un aggiornamento della configurazione. Per altre informazioni, vedere [Start-ServiceFabricClusterConfigurationUpgrade](https://msdn.microsoft.com/en-us/library/mt788302.aspx). Prima di avviare l'aggiornamento della configurazione, aggiornare il valore clusterConfigurationVersion nel file JSON (JavaScript Object Notation).

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>Flusso di lavoro per l'aggiornamento del cluster
1. Scaricare la versione più recente del pacchetto dal documento [Creare un cluster Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md).
2. Connettersi al cluster da qualsiasi macchina con accesso amministrativo a tutte le macchine elencate come nodi nel cluster. La macchina in cui viene eseguito lo script non deve necessariamente far parte del cluster.

    ```powershell

    ###### Connect to the cluster
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

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Code example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```

4. Registrare il pacchetto copiato.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Code example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
5. Avviare un aggiornamento del cluster a una delle versioni disponibili.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Code example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Per monitorare lo stato di avanzamento dell'aggiornamento, è possibile usare Service Fabric Explorer o eseguire il comando di Windows PowerShell seguente.

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. È possibile specificare criteri di integrità personalizzati al momento dell'esecuzione del comando start-serviceFabricClusterUpgrade. Per altre informazioni, vedere [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Dopo aver risolto i problemi che hanno determinato il rollback, è necessario avviare di nuovo l'aggiornamento ripetendo la procedura descritta in precedenza.


## <a name="upgrade-the-cluster-configuration"></a>Aggiornare la configurazione del cluster
Per aggiornare la configurazione del cluster, eseguire il comando Start-ServiceFabricClusterConfigurationUpgrade. L'aggiornamento della configurazione viene eseguito per dominio di aggiornamento.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```


## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come personalizzare alcune [impostazioni dei cluster di Service Fabric](service-fabric-cluster-fabric-settings.md).
* Informazioni su come [aumentare o ridurre le istanze del cluster](service-fabric-cluster-scale-up-down.md).
* Informazioni su come eseguire [aggiornamenti dell'applicazione](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG

