---
title: Configurare un cluster di Azure Service Fabric | Microsoft Docs
description: Questa guida introduttiva illustra come creare un cluster Windows o Linux di Service Fabric in Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: ryanwi
ms.openlocfilehash: caf76bb739fa92982c511c8e3e6c6aaf2bf9d6c1
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Creare il primo cluster di Service Fabric di Azure
Un [cluster di Azure Service Fabric](service-fabric-deploy-anywhere.md) è un set connesso in rete di macchine virtuali e computer fisici in cui vengono distribuiti e gestiti i microservizi. Questa guida introduttiva illustra come creare in pochi minuti un cluster con cinque nodi, in esecuzione in Windows o Linux, con [Azure PowerShell](https://msdn.microsoft.com/library/dn135248) o il [portale di Azure](http://portal.azure.com). Per questa attività è anche possibile usare l'interfaccia della riga di comando di Azure.  

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="use-the-azure-portal"></a>Usare il portale di Azure

Accedere al portale di Azure all'indirizzo [http://portal.azure.com](http://portal.azure.com).

### <a name="create-the-cluster"></a>Creare il cluster

1. Nell'angolo superiore sinistro del portale di Azure selezionare **Nuovo**.
2. Cercare **Service Fabric** e selezionare **Cluster di Service Fabric** nei risultati restituiti. Selezionare quindi **Crea**.
3. Compilare il modulo **Informazioni di base** di Service Fabric. Per **Sistema operativo** selezionare la versione di Windows o Linux per l'esecuzione dei nodi del cluster. Il nome utente e la password immessi in questo modulo verranno usati per accedere alla macchina virtuale. Per **Gruppo di risorse** creare un nuovo gruppo. Un gruppo di risorse è un contenitore logico in cui vengono create e gestite collettivamente le risorse di Azure. Al termine, selezionare **OK**.

    ![Screenshot dell'output della configurazione del cluster][cluster-setup-basics]

4. Compilare il modulo **Configurazione cluster**. In **Numero di tipi di nodo** immettere **1**.

5. Selezionare **Tipo di nodo 1 (primario)** e compilare il modulo **Configurazione del tipo di nodo**. Immettere un nome per il tipo di nodo e impostare [Livello di durabilità](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) su **Bronzo**. Selezionare quindi una dimensione di VM.

    I tipi di nodo definiscono le dimensioni della VM, il numero di VM, gli endpoint personalizzati e altre impostazioni per le VM di quel tipo. Ogni tipo di nodo definito viene configurato come set di scalabilità di macchine virtuali separato, usato per distribuire e gestire le macchine virtuali come set. Per ogni tipo di nodo è possibile aumentare o ridurre le prestazioni in modo indipendente e avere diversi set di porte aperte e metriche per la capacità diverse. Il primo tipo di nodo (o tipo di nodo primario) ospita i servizi di sistema di Service Fabric. Questo tipo di nodo deve avere almeno cinque VM.

    La [pianificazione della capacità](service-fabric-cluster-capacity.md) è un passaggio importante per qualsiasi distribuzione di produzione. Per questa guida introduttiva, tuttavia, non si eseguono applicazioni, quindi selezionare la dimensione di VM *DS1_v2 Standard*. Selezionare **Argento** come [livello di affidabilità](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) e specificare una capacità iniziale del set di scalabilità di macchine virtuali pari a 5.  

    Gli endpoint personalizzati aprono porte in Azure Load Balancer, in modo da consentire la connessione alle applicazioni in esecuzione nel cluster.  Immettere **80, 8172** per aprire le porte 80 e 8172.

    Non selezionare la casella **Configura impostazioni avanzate**, che consente di personalizzare gli endpoint di gestione TCP/HTTP, gli intervalli di porte delle applicazioni, i [vincoli di posizionamento](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints) e le [proprietà di capacità](service-fabric-cluster-resource-manager-metrics.md).    
    
    ![Screenshot della configurazione del tipo di nodo][node-type-config]

    Selezionare **OK**.

6. Nel modulo **Configurazione cluster** impostare **Diagnostica** su **On**. Per questa Guida introduttiva non è necessario immettere alcuna proprietà per le [impostazioni dell'infrastruttura](service-fabric-cluster-fabric-settings.md).  In **Versione dell'infrastruttura** selezionare la modalità di aggiornamento **Automatico**, in modo che Microsoft aggiorni automaticamente la versione del codice dell'infrastruttura che esegue il cluster.  Impostare la modalità su **Manuale** se si vuole [scegliere una versione supportata](service-fabric-cluster-upgrade.md) a cui eseguire l'aggiornamento.     

    Selezionare **OK**.

7. Compilare il modulo **Sicurezza**. Per questa guida introduttiva selezionare **Senza protezione**. Si noti che in generale è consigliabile creare un cluster sicuro per i carichi di lavoro di produzione. Chiunque può connettersi in modo anonimo a un cluster senza protezione ed eseguire operazioni di gestione.  

   Service Fabric usa i certificati per offrire l'autenticazione e la crittografia e proteggere i vari aspetti di un cluster e le relative applicazioni. Per altre informazioni, vedere [Scenari di sicurezza di un cluster di Service Fabric](service-fabric-cluster-security.md). Per abilitare l'autenticazione utente con Azure Active Directory o configurare certificati per la sicurezza delle applicazioni, [creare un cluster da un modello di Resource Manager](service-fabric-cluster-creation-via-arm.md).

    Selezionare **OK**.

8. Esaminare il riepilogo. Se si vuole scaricare un modello di Azure Resource Manager basato sulle impostazioni immesse, selezionare **Scarica modello e parametri**. Selezionare **Crea** per creare il cluster.

    È possibile visualizzare lo stato di avanzamento del processo di creazione nell'area delle notifiche. Selezionare l'icona a forma di campana accanto alla barra di stato nell'angolo superiore destro della schermata. Se durante la creazione del cluster è stata selezionata l'opzione **Aggiungi alla Schermata iniziale**, nella **schermata iniziale** verrà visualizzata la voce **Distribuzione di Cluster di Service Fabric**.

### <a name="connect-to-the-cluster-by-using-powershell"></a>Connettersi al cluster con PowerShell
Verificare che il cluster sia in esecuzione connettendosi tramite PowerShell. Il modulo PowerShell per Service Fabric viene installato con [Service Fabric SDK](service-fabric-get-started.md). Il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) stabilisce una connessione al cluster.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint quickstartcluster.westus2.cloudapp.azure.com:19000
```
Per altri esempi di connessione a un cluster, vedere [Connettersi a un cluster sicuro](service-fabric-connect-to-secure-cluster.md). Dopo la connessione al cluster, usare il cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) per visualizzare un elenco dei nodi presenti nel cluster e informazioni sullo stato per ogni nodo. **HealthState** deve essere *OK* per ogni nodo.

```powershell
PS C:\Users\sfuser> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
```

### <a name="remove-the-cluster"></a>Rimuovere il cluster
Un cluster di Service Fabric è costituito da altre risorse di Azure oltre alla risorsa cluster stessa. Per eliminare completamente un cluster di Service Fabric, è necessario eliminare anche tutte le risorse da cui è costituito. Il modo più semplice per eliminare il cluster e tutte le risorse che utilizza consiste nell'eliminare il gruppo di risorse. Per altri modi per eliminare un cluster o solo alcune risorse di un gruppo di risorse, vedere [Eliminare un cluster](service-fabric-cluster-delete.md).

Eliminare un gruppo di risorse nel portale di Azure:
1. Passare al cluster di Service Fabric da eliminare.
2. Nella pagina delle informazioni di base del cluster selezionare il nome del **gruppo di risorse**.
3. Nella pagina **Informazioni di base** del gruppo di risorse selezionare **Elimina gruppo di risorse**. Seguire quindi le istruzioni visualizzate nella pagina per completare l'eliminazione del gruppo di risorse.
    ![Screenshot della pagina Informazioni di base del gruppo di risorse, con l'opzione Elimina gruppo di risorse evidenziata][cluster-delete]


## <a name="use-azure-powershell"></a>Uso di Azure PowerShell
In alternativa, è possibile creare il cluster usando PowerShell. Ecco come:

1. Scaricare il [modulo Azure PowerShell versione 4.0 o successiva](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) sul computer.

2. Eseguire il cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster) per creare un cluster di Service Fabric con cinque nodi protetto con un certificato X.509. Il comando crea un certificato autofirmato e lo carica in un nuovo insieme di credenziali delle chiavi. Il certificato viene copiato anche in una directory locale. Impostare il parametro *-OS* per scegliere la versione di Windows o Linux che viene eseguita sui nodi del cluster. Personalizzare i parametri in base alle esigenze. 

    ```powershell
    #Provide the subscription Id
    $subscriptionId = 'yourSubscriptionId'

    # Certificate variables.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
    $certfolder="c:\mycertificates\"

    # Variables for VM admin.
    $adminuser="vmadmin"
    $adminpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 

    # Variables for common values
    $clusterloc="SouthCentralUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=5 

    # Set the context to the subscription ID where the cluster will be created
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Create the Service Fabric cluster.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -VmUserName $adminuser -VmPassword $adminpwd -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
    ```

    Il completamento del comando può richiedere da 10 a 30 minuti. L'output conterrà informazioni sul certificato, sull'insieme di credenziali delle chiavi in cui è stato caricato e sulla cartella locale in cui viene copiato.     

3. Copiare l'intero output e salvarlo in un file di testo, per farvi riferimento in un secondo momento. Prendere nota delle informazioni seguenti dell'output: 

    - CertificateSavedLocalPath
    - CertificateThumbprint
    - ManagementEndpoint
    - ClientConnectionEndpointPort

### <a name="install-the-certificate-on-your-local-machine"></a>Installare il certificato nel computer locale
  
Per connettersi al cluster, installare il certificato nell'archivio personale (My) dell'utente corrente. 

Eseguire questo comando:

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\<certificatename>.pfx `
        -Password $certpwd
```

È ora possibile connettersi al cluster sicuro.

### <a name="connect-to-a-secure-cluster"></a>Connettersi a un cluster sicuro 

Eseguire il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) per connettersi a un cluster sicuro. I dettagli del certificato devono corrispondere a un certificato usato per configurare il cluster. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <ManagementEndpoint>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <CertificateThumbprint> `
          -FindType FindByThumbprint -FindValue <CertificateThumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

L'esempio seguente riporta i parametri di esempio: 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mycluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Eseguire questo comando per verificare di essere connessi e che il cluster sia integro.

```powershell

Get-ServiceFabricClusterHealth

```
### <a name="remove-the-cluster"></a>Rimuovere il cluster
Un cluster è costituito da altre risorse di Azure oltre alla risorsa cluster stessa. Il modo più semplice per eliminare il cluster e tutte le risorse che utilizza consiste nell'eliminare il gruppo di risorse. 

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```
## <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
In alternativa, è possibile creare il cluster usando l'interfaccia della riga di comando. Ecco come:

1. Installare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) nel computer.
2. Accedere ad Azure e selezionare la sottoscrizione in cui si vuole creare il cluster.
   ```azurecli
   az login
   az account set --subscription <GUID>
   ```
3. Eseguire il comando [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) per creare un cluster di Service Fabric con cinque nodi protetto con un certificato X.509. Il comando crea un certificato autofirmato e lo carica in un nuovo insieme di credenziali delle chiavi. Il certificato viene copiato anche in una directory locale. Impostare il parametro *-os* per scegliere la versione di Windows o Linux che viene eseguita nei nodi del cluster. Personalizzare i parametri in base alle esigenze.

    ```azurecli
    #!/bin/bash

    # Variables
    ResourceGroupName="aztestclustergroup" 
    ClusterName="aztestcluster" 
    Location="southcentralus" 
    Password="q6D7nN%6ck@6" 
    Subject="aztestcluster.southcentralus.cloudapp.azure.com" 
    VaultName="aztestkeyvault" 
    VaultGroupName="testvaultgroup"
    VmPassword="Mypa$$word!321"
    VmUserName="sfadminuser"

    # Create resource groups
    az group create --name $ResourceGroupName --location $Location 
    az group create --name $VaultGroupName --location $Location

    # Create secure five node Linux cluster. Creates a key vault in a resource group
    # and creates a certficate in the key vault. The certificate's subject name must match 
    # the domain that you use to access the Service Fabric cluster. The certificate is downloaded locally.
    az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . \
        --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName \
        --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $VaultGroupName \
        --vm-password $VmPassword --vm-user-name $VmUserName
    ```
    
### <a name="connect-to-the-cluster"></a>Connettersi al cluster
Eseguire il comando seguente dell'interfaccia della riga di comando per connettersi al cluster con il certificato.  Quando si usa un certificato client per l'autenticazione, assicurarsi che i dettagli del certificato corrispondano a un certificato distribuito nei nodi del cluster. Usare l'opzione `--no-verify` per un certificato autofirmato.

```azurecli
az sf cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 --pem ./linuxcluster201709161647.pem --no-verify
```

Eseguire questo comando per verificare di essere connessi e che il cluster sia integro.

```azurecli
az sf cluster health
```

### <a name="connect-to-the-nodes-directly"></a>Connettersi direttamente ai nodi 

Per connettersi ai nodi di un cluster Linux, è possibile usare il comando SSH (Secure Shell). Specificare un numero di porta a partire da 3389. Per il cluster a cinque nodi creato in precedenza, ad esempio, i comandi saranno i seguenti:
```bash
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3389
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3390
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3391
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3392
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3393
```

### <a name="remove-the-cluster"></a>Rimuovere il cluster
Un cluster è costituito da altre risorse di Azure oltre alla risorsa cluster stessa. Il modo più semplice per eliminare il cluster e tutte le risorse che utilizza consiste nell'eliminare il gruppo di risorse. 

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato configurato un cluster di sviluppo, provare a eseguire queste operazioni:
* [Visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Rimuovere un cluster](service-fabric-cluster-delete.md) 
* [Distribuire le app tramite PowerShell](service-fabric-deploy-remove-applications.md)
* [Distribuire le app tramite l'interfaccia della riga di comando](service-fabric-application-lifecycle-sfctl.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
