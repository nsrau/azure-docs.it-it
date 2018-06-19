---
title: Distribuire un'applicazione .NET in un contenitore in Azure Service Fabric | Microsoft Docs
description: Informazioni su come aggiungere un'applicazione .NET esistente a contenitori con Visual Studio ed eseguire il debug dei contenitori in Service Fabric in locale. L'applicazione aggiunta a contenitori viene inviata tramite push a un registro contenitori di Azure e distribuita in un cluster di Service Fabric. Quando viene distribuita in Azure, l'applicazione usa database SQL di Azure per salvare in modo permanente i dati.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/18/2018
ms.author: ryanwi
ms.openlocfilehash: 6fe314125440096d21a1276defd082c4e1997b8e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642683"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Esercitazione: Distribuire un'applicazione .NET in un contenitore Windows in Azure Service Fabric

Questa esercitazione mostra come aggiungere un'applicazione ASP.NET esistente a contenitori e assemblarla in un pacchetto come applicazione di Service Fabric.  Eseguire i contenitori in locale nel cluster di sviluppo di Service Fabric e quindi distribuire l'applicazione in Azure.  L'applicazione salva i dati in modo permanente nel [database SQL di Azure](/azure/sql-database/sql-database-technical-overview). 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere un'applicazione esistente in un contenitore con Visual Studio
> * Creare un database SQL di Azure
> * Creare un registro contenitori di Azure
> * Distribuire un'applicazione di Service Fabric in Azure

## <a name="prerequisites"></a>prerequisiti

1. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
2. Installare [Docker CE per Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) in modo da poter eseguire i contenitori in Windows 10.
3. Installare il [runtime di Service Fabric versione 6.2 o successiva](service-fabric-get-started.md) e [Service Fabric SDK versione 3.1](service-fabric-get-started.md) o versioni successive.
4. Installare [Visual Studio 2017 versione 15.7](https://www.visualstudio.com/) o successiva con i carichi di lavoro **Sviluppo di Azure** e **Sviluppo ASP.NET e Web**.
5. Installare [Azure PowerShell][link-azure-powershell-install]
 

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Scaricare ed eseguire Fabrikam Fiber CallCenter
Scaricare l'applicazione di esempio [Fabrikam Fiber CallCenter][link-fabrikam-github].  Fare clic sul collegamento **download archive** (Scarica archivio).  Dalla directory *sourceCode* nel file *fabrikam.zip* estrarre il file *sourceCode.zip* e quindi estrarre la directory *VS2015* nel computer in uso.

Verificare che l'applicazione di Fabrikam Fiber CallCenter venga compilata ed eseguita senza errori.  Avviare Visual Studio come **amministratore** e aprire il file [FabrikamFiber.CallCenter.sln][link-fabrikam-github].  Premere F5 per eseguire il debug dell'applicazione ed eseguirla.

![Esempio Web Fabrikam][fabrikam-web-page]

## <a name="containerize-the-application"></a>Distribuire l'applicazione in un contenitore
Fare clic con il pulsante destro del mouse sul progetto **FabrikamFiber.Web** > **Aggiungi** > **Supporto per l'agente di orchestrazione del contenitore**.  Selezionare **Service Fabric** come agente di orchestrazione del contenitore e fare clic su **OK**.

Fare clic su **Sì** per impostare adesso Docker sui contenitori Windows.

Nella soluzione viene creato un nuovo progetto di applicazione di Service Fabric **FabrikamFiber.CallCenterApplication**.  Viene aggiunto un Dockerfile al progetto **FabrikamFiber.Web** esistente.  Viene anche aggiunta una directory **PackageRoot** al progetto**FabrikamFiber.Web**, che contiene il manifesto del servizio e le impostazioni per il nuovo servizio FabrikamFiber.Web. 

Il contenitore è ora pronto per la compilazione e l'inserimento come pacchetto in un'applicazione di Service Fabric. Dopo aver creato l'immagine del contenitore sul computer, è possibile eseguirne il push in qualsiasi registro contenitori e quindi estrarla in qualsiasi host per l'esecuzione.

## <a name="create-an-azure-sql-db"></a>Creare un database SQL di Azure
Quando si esegue l'applicazione Fabrikam Fiber CallCenter nell'ambiente di produzione, i dati devono essere salvati in modo permanente in un database. Al momento non c'è modo di garantire la persistenza dei dati in un contenitore, pertanto non è possibile archiviare i dati di produzione in SQL Server in un contenitore.

È consigliabile usare [database SQL di Azure](/azure/sql-database/sql-database-get-started-powershell). Per configurare ed eseguire un database di SQL Server gestito in Azure, eseguire lo script seguente.  Modificare le variabili dello script in base alle necessità. *clientIP* è l'indirizzo IP del computer di sviluppo.  Se si è protetti da un firewall aziendale, l'indirizzo IP del computer di sviluppo potrebbe non essere l'indirizzo IP esposto a Internet.  È anche possibile impostare la regola del firewall del server per il database SQL tramite il [portale di Azure](https://portal.azure.com), che elenca l'indirizzo IP del computer in uso.

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The logical server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "24.18.117.76"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzureRmSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzureRmSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Creeate the database in the server.
New-AzureRmSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

## <a name="update-the-web-config"></a>Aggiornare il file di configurazione Web
Tornare al progetto **FabrikamFiber.Web** e aggiornare la stringa di connessione nel file **web.config** in modo che punti all'istanza di SQL Server nel contenitore.  Aggiornare la parte *Server* della stringa di connessione per il server creato dallo script precedente. 

```xml
<add name="FabrikamFiber-Express" connectionString="Server=tcp:fab-fiber-1300282665.database.windows.net,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=tcp:fab-fiber-1300282665.database.windows.net,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```
>[!NOTE]
>Per il debug locale è possibile usare un'istanza di SQL Server a scelta, a condizione che sia raggiungibile dall'host. **localdb**, tuttavia, non supporta la comunicazione `container -> host`. Se si vuole usare un database SQL diverso durante la creazione di una compilazione di rilascio dell'applicazione Web, aggiungere un'altra stringa di connessione al file *web.release.config*.

## <a name="run-the-containerized-application-locally"></a>Eseguire l'applicazione in un contenitore in locale
Premere **F5** per eseguire l'applicazione ed eseguirne il debug in un contenitore nel cluster di sviluppo locale di Service Fabric. Fare clic su **Sì** se viene visualizzata una finestra di messaggio che richiede di concedere al gruppo 'ServiceFabricAllowedUsers' le autorizzazioni di lettura ed esecuzione per la directory del progetto di Visual Studio.

## <a name="create-a-container-registry"></a>Creare un registro di contenitori
Ora che l'applicazione viene eseguita in locale, iniziare a preparare la distribuzione in Azure.  Le immagini dei contenitori devono essere archiviate in un registro contenitori.  Creare un [registro contenitori di Azure](/azure/container-registry/container-registry-intro) usando lo script seguente. Il nome del registro contenitori è visibile per le altre sottoscrizioni di Azure, pertanto deve essere univoco.
Prima di distribuire l'applicazione in Azure, eseguire il push dell'immagine del contenitore in questo registro.  Quando l'applicazione viene distribuita nel cluster in Azure, l'immagine del contenitore viene recuperata tramite pull da questo registro.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzureRmResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzureRMContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Creare un cluster di Service Fabric in Azure
Le applicazioni Service Fabric sono eseguite in un cluster, un set di macchine virtuali o fisiche connesse mediante una rete.  Prima di poter distribuire l'applicazione in Azure, creare un cluster di Service Fabric in Azure.

È possibile:
- Creare un cluster di test da Visual Studio. Questa opzione consente di creare un cluster sicuro direttamente da Visual Studio con le configurazioni preferite. 
- [Creare un cluster sicuro da un modello](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

Questa esercitazione consente di creare un cluster da Visual Studio, ideale per scenari di test. Se si crea un cluster in altri modi o si usa un cluster esistente, è possibile copiare e incollare l'endpoint della connessione o sceglierlo dalla sottoscrizione. 

Quando si crea il cluster, scegliere uno SKU che supporta l'esecuzione dei contenitori. Il sistema operativo Windows Server nei nodi del cluster deve essere compatibile con il sistema operativo Windows Server del contenitore. Per altre informazioni, vedere [Windows Server container OS and host OS compatibility](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility) (Compatibilità tra il sistema operativo del contenitore di Windows Server e il sistema operativo dell'host). Per impostazione predefinita, questa esercitazione consente di creare un'immagine Docker basata su Windows Server 2016 LTSC. I contenitori basati su questa immagine verranno eseguiti nei cluster creati con Windows Server 2016 Datacenter con Contenitori. Tuttavia, se si crea un cluster o si usa un cluster esistente basato su Windows Server Datacenter Core 1709 con Contenitori, è necessario modificare l'immagine del sistema operativo Windows Server su cui è basato il contenitore. Aprire **Dockerfile** nel progetto **FabrikamFiber.Web**, impostare come commento l'istruzione `FROM` esistente (basata su `windowsservercore-ltsc`) e rimuovere il commento dall'istruzione `FROM` basata su `windowsservercore-1709`. 

1. Fare clic con il pulsante destro del mouse sul progetto di applicazione **FabrikamFiber.CallCenterApplication** in Esplora soluzioni e scegliere **Pubblica**.

2. Accedere con l'account Azure per poter avere accesso alle sottoscrizioni. 

3. Selezionare l'elenco a discesa per **Endpoint connessione** e quindi l'opzione **Crea nuovo cluster**.    
        
4. Nella finestra di dialogo **Crea cluster** modificare le impostazioni seguenti:

    1. Specificare il nome del cluster nel campo **Nome del cluster**, nonché la sottoscrizione e la località da usare.
    2. Facoltativamente, è possibile modificare il numero di nodi. Per impostazione predefinita vengono usati tre nodi, il numero minimo necessario per testare gli scenari di Service Fabric.
    3. Selezionare la scheda **Certificato**. In questa scheda digitare una password che verrà usata per proteggere il certificato del cluster. Questo certificato consente di rendere sicuro il cluster. È anche possibile modificare il percorso in cui si vuole salvare il certificato. Visual Studio può anche importare automaticamente il certificato, perché questo passaggio è obbligatorio per la pubblicazione dell'applicazione nel cluster.
    4. Selezionare la scheda **Dettagli macchina virtuale**. Specificare la password che si vuole usare per le macchine virtuali (VM) che costituiscono il cluster. Il nome utente e la password possono essere usati per la connessione remota alle VM. Si deve anche selezionare una dimensione di macchina virtuale ed è possibile modificare l'immagine di VM, se necessario.
    5. Nella scheda **Avanzate** elencare la porta dell'applicazione per aprirla nel servizio di bilanciamento del carico quando si distribuisce il cluster. In Esplora soluzioni aprire FabrikamFiber.Web->PackageRoot->ServiceManifest.xml.  La porta per il front-end Web è elencata in **Endpoint**.  È anche possibile aggiungere una chiave di Application Insights esistente a cui indirizzare i file di log dell'applicazione.
    6. Al termine della modifica delle impostazioni, selezionare il pulsante **Crea**. 
5. La creazione richiede alcuni minuti. Il completamento dell'operazione verrà indicato nella finestra di output.
    

## <a name="allow-your-application-running-in-azure-to-access-the-sql-db"></a>Consentire all'applicazione in esecuzione in Azure di accedere al database SQL
In precedenza è stata creata una regola del firewall SQL per concedere l'accesso all'applicazione in esecuzione in locale.  A questo punto è necessario consentire all'applicazione in esecuzione in Azure di accedere al database SQL.  Creare un [endpoint di servizio di rete virtuale](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) per il cluster di Service Fabric e quindi creare una regola per consentire a tale endpoint di accedere al database SQL.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "fabrikamfiber.callcenterapplication_RG"
$resource = Get-AzureRmResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName 

# Get the virtual network by name.
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzureRmVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```
## <a name="deploy-the-application-to-azure"></a>Distribuzione dell'applicazione in Azure
Ora che l'applicazione è pronta, è possibile distribuirla nel cluster in Azure direttamente da Visual Studio.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di applicazione **FabrikamFiber.CallCenterApplication** e scegliere **Pubblica**.  In **Endpoint connessione** selezionare l'endpoint del cluster creato in precedenza.  In **Registro contenitori di Azure** selezionare il registro contenitori creato in precedenza.  Fare clic su **Pubblica** per distribuire l'applicazione nel cluster in Azure.

![Pubblicare l'applicazione][publish-app]

Seguire lo stato della distribuzione nella finestra di output.  Al termine della distribuzione dell'applicazione, aprire un browser e digitare l'indirizzo del cluster e la porta dell'applicazione. Ad esempio, http://http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/.

![Esempio Web Fabrikam][fabrikam-web-page-deployed]

## <a name="clean-up-resources"></a>Pulire le risorse
Se l'operazione è terminata, assicurarsi di rimuovere tutte le risorse create.  Il modo più semplice consiste nel rimuovere i gruppi di risorse che contengono il cluster di Service Fabric, il database SQL di Azure e Registro contenitori di Azure.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzureRmResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzureRmResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzureRmResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Aggiungere un'applicazione esistente in un contenitore con Visual Studio
> * Creare un database SQL di Azure
> * Creare un registro contenitori di Azure
> * Distribuire un'applicazione di Service Fabric in Azure

La parte successiva dell'esercitazione comprende informazioni su come configurare il [monitoraggio del contenitore](service-fabric-tutorial-monitoring-wincontainers.md).


[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
