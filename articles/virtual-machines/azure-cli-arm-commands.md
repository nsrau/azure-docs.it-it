---
title: Comandi CLI di Azure classico
description: Comandi dell'interfaccia della riga di comando di Azure per gestire le risorse.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/18/2017
ms.author: cynthn
ms.openlocfilehash: 5b7d6f4efa4a910d3141638602c603d484fac6da
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86181863"
---
# <a name="azure-classic-cli-commands"></a>Comandi CLI di Azure classico 

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Questo argomento illustra come installare l'interfaccia della riga di comando classica di Azure. L'interfaccia della riga di comando classica è deprecata e deve essere usata solo con il modello di distribuzione classica. Per tutte le altre distribuzioni, usare l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/).

Questo articolo fornisce la sintassi e le opzioni per i comandi dell'interfaccia della riga di comando di Azure classico usati comunemente per creare e gestire le risorse di Azure. Non si tratta di un riferimento completo e la versione dell'interfaccia della riga di comando in uso potrebbe mostrare comandi o parametri leggermente diversi. 

Per iniziare, installare prima [l'interfaccia della](../cli-install-nodejs.md) riga di comando di Azure classico e [connettersi alla sottoscrizione di Azure](/cli/azure/authenticate-azure-cli).

Per la sintassi e le opzioni dei comandi correnti nella riga di comando in modalità Gestione risorse, digitare `azure help` o `azure help [command]` per visualizzare la Guida per un comando specifico. Sono inoltre disponibili esempi dell'interfaccia della riga di comando nella documentazione per la creazione e la gestione di servizi di Azure specifici.

I parametri facoltativi sono indicati tra parentesi quadre, ad esempio `[parameter]`. Tutti gli altri parametri sono obbligatori.

Oltre ai parametri facoltativi specifici del comando documentati qui, vi sono tre parametri opzionali che possono essere utilizzati per visualizzare output dettagliato come opzioni richiesta e codici di stato. Il parametro `-v` fornisce l'output dettagliato, mentre il parametro `-vv` fornisce un output con un dettaglio ancor maggiore. L'opzione `--json` consente di visualizzare il risultato in formato JSON non elaborato.

## <a name="setting-the-resource-manager-mode"></a>Impostazione della modalità di gestione risorse
Usare il comando seguente per abilitare i comandi della modalità Resource Manager dell'interfaccia della riga di comando di Azure.

```azurecli
azure config mode arm
```

> [!NOTE]
> La modalità Azure Resource Manager e la modalità Gestione servizi di Azure dell'interfaccia della riga di comando si escludono a vicenda, ossia le risorse create in una modalità non possono essere gestite dall'altra.
>


## <a name="account-information"></a>Informazioni sull'account
Le informazioni relative alla sottoscrizione di Azure vengono usate dallo strumento per connettersi all'account dell'utente.

**Elenca le sottoscrizioni importate**

```azurecli
account list [options]
```

**Visualizzare i dettagli relativi a una sottoscrizione**  

```azurecli
account show [options] [subscriptionNameOrId]
```

**Impostare la sottoscrizione corrente**
```azurecli
account set [options] <subscriptionNameOrId>
```

**Rimuovere una sottoscrizione o un ambiente oppure cancellare tutte le informazioni dell'account o dell'ambiente archiviate**  
```azurecli
account clear [options]
```

**Comandi per la gestione dell'ambiente dell'account**  

```azurecli
account env list [options]
account env show [options] [environment]
account env add [options] [environment]
account env set [options] [environment]
account env delete [options] [environment]
```

## <a name="active-directory-objects"></a>Oggetti Active Directory
**Comandi per visualizzare le applicazioni di Active Directory**

```azurecli
ad app create [options]
ad app delete [options] <object-id>
```

**Comandi per visualizzare i gruppi di Active Directory**

```azurecli
ad group list [options]
ad group show [options]
```

**Comandi per fornire le informazioni relative a un membro o a un sottogruppo di Active Directory**

```azurecli
ad group member list [options] [objectId]
```

**Comandi per visualizzare le entità servizio di Active Directory**

```azurecli
ad sp list [options]
ad sp show [options]
ad sp create [options] <application-id>
ad sp delete [options] <object-id>
```

**Comandi per visualizzare gli utenti di Active Directory**

```azurecli
ad user list [options]
ad user show [options]
```

## <a name="availability-sets"></a>Set di disponibilità
**Creare un set di disponibilità all'interno di un gruppo di risorse**

```azurecli
availset create [options] <resource-group> <name> <location> [tags]
```

**Elencare i set di disponibilità all'interno di un gruppo di risorse**

```azurecli
availset list [options] <resource-group>
```

**Ottenere un set di disponibilità all'interno di un gruppo di risorse**

```azurecli
availset show [options] <resource-group> <name>
```

**Eliminare un set di disponibilità all'interno di un gruppo di risorse**

```azurecli
availset delete [options] <resource-group> <name>
```

## <a name="local-settings"></a>Impostazioni locali
**Elencare le impostazioni di configurazione dell'interfaccia della riga di comando di Azure**

```azurecli
config list [options]
```

**Eliminare un'impostazione di configurazione**

```azurecli
config delete [options] <name>
```

**Aggiornare un'impostazione di configurazione**

```azurecli
config set <name> <value>
```

**Impostare la modalità operativa dell'interfaccia della riga di comando di Azure su `arm` o `asm`**

```azurecli
config mode [options] <modename>
```


## <a name="account-features"></a>Funzionalità dell'account
**Elencare tutte le funzionalità disponibili per la sottoscrizione**

```azurecli
feature list [options]
```

**Visualizzare una funzionalità**

```azurecli
feature show [options] <providerName> <featureName>
```

**Registrare una funzionalità in anteprima di un provider di risorse**

```azurecli
feature register [options] <providerName> <featureName>
```

## <a name="resource-groups"></a>Gruppi di risorse
**Crea un gruppo di risorse**

```azurecli
group create [options] <name> <location>
```

**Impostare i tag per un gruppo di risorse**

```azurecli
group set [options] <name> <tags>
```

**Eliminare un gruppo di risorse**

```azurecli
group delete [options] <name>
```

**Elencare i gruppi di risorse per la sottoscrizione**

```azurecli
group list [options]
```

**Visualizzare un gruppo di risorse per la sottoscrizione**

```azurecli
group show [options] <name>
```

**Comandi per gestire i log dei gruppi di risorse**

```azurecli
group log show [options] [name]
```

**Comandi per gestire la distribuzione in un gruppo di risorse**

```azurecli
group deployment create [options] [resource-group] [name]
group deployment list [options] <resource-group> [state]
group deployment show [options] <resource-group> [deployment-name]
group deployment stop [options] <resource-group> [deployment-name]
```

**Comandi per gestire il modello di gruppo di risorse locale o della raccolta**

```azurecli
group template list [options]
group template show [options] <name>
group template download [options] [name] [file]
group template validate [options] <resource-group>
```

## <a name="hdinsight-clusters"></a>Cluster HDInsight
**Comandi per creare o aggiungere a un file di configurazione del cluster**

```azurecli
hdinsight config create [options] <configFilePath> <overwrite>
hdinsight config add-config-values [options] <configFilePath>
hdinsight config add-script-action [options] <configFilePath>
```


Esempio: creare un file di configurazione che contiene un'azione script per l'esecuzione durante la creazione di un cluster.

```azurecli
hdinsight config create "C:\myFiles\configFile.config"
hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"
```

**Comando per creare un cluster in un gruppo di risorse**

```azurecli
hdinsight cluster create [options] <clusterName>
```

Esempio: creare uno Storm nel cluster Linux

```azurecli
azure hdinsight cluster create -g myarmgroup -l westus -y Linux --clusterType Storm --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 myNewCluster01

info:    Executing command hdinsight cluster create
+ Submitting the request to create cluster...
info:    hdinsight cluster create command OK
```

Esempio: creare un cluster con un'azione script

```azurecli
azure hdinsight cluster create -g myarmgroup -l westus -y Linux --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01

info:    Executing command hdinsight cluster create
+ Submitting the request to create cluster...
info:    hdinsight cluster create command OK
```

Opzioni dei parametri:

```txt
-h, --help                                                 output usage information
-v, --verbose                                              use verbose output
-vv                                                        more verbose with debug output
--json                                                     use json output
-g --resource-group <resource-group>                       The name of the resource group
-c, --clusterName <clusterName>                            HDInsight cluster name
-l, --location <location>                                  Data center location for the cluster
-y, --osType <osType>                                      HDInsight cluster operating system
'Windows' or 'Linux'
--version <version>                                        HDInsight cluster version
--clusterType <clusterType>                                HDInsight cluster type.
Hadoop | HBase | Spark | Storm
--defaultStorageAccountName <storageAccountName>           Storage account url to use for default HDInsight storage
--defaultStorageAccountKey <storageAccountKey>             Key to the storage account to use for default HDInsight storage
--defaultStorageContainer <storageContainer>               Container in the storage account to use for HDInsight default storage
--headNodeSize <headNodeSize>                              (Optional) Head node size for the cluster
--workerNodeCount <workerNodeCount>                        Number of worker nodes to use for the cluster
--workerNodeSize <workerNodeSize>                          (Optional) Worker node size for the cluster)
--zookeeperNodeSize <zookeeperNodeSize>                    (Optional) Zookeeper node size for the cluster
--userName <userName>                                      Cluster username
--password <password>                                      Cluster password
--sshUserName <sshUserName>                                SSH username (only for Linux clusters)
--sshPassword <sshPassword>                                SSH password (only for Linux clusters)
--sshPublicKey <sshPublicKey>                              SSH public key (only for Linux clusters)
--rdpUserName <rdpUserName>                                RDP username (only for Windows clusters)
--rdpPassword <rdpPassword>                                RDP password (only for Windows clusters)
--rdpAccessExpiry <rdpAccessExpiry>                        RDP access expiry.
For example 12/12/2015 (only for Windows clusters)
--virtualNetworkId <virtualNetworkId>                      (Optional) Virtual network ID for the cluster.
Value is a GUID for Windows cluster and ARM resource ID for Linux cluster)
--subnetName <subnetName>                                  (Optional) Subnet for the cluster
--additionalStorageAccounts <additionalStorageAccounts>    (Optional) Additional storage accounts.
Can be multiple.
In the format of 'accountName#accountKey'.
For example, --additionalStorageAccounts "acc1#key1;acc2#key2"
--hiveMetastoreServerName <hiveMetastoreServerName>        (Optional) SQL Server name for the external metastore for Hive
--hiveMetastoreDatabaseName <hiveMetastoreDatabaseName>    (Optional) Database name for the external metastore for Hive
--hiveMetastoreUserName <hiveMetastoreUserName>            (Optional) Database username for the external metastore for Hive
--hiveMetastorePassword <hiveMetastorePassword>            (Optional) Database password for the external metastore for Hive
--oozieMetastoreServerName <oozieMetastoreServerName>      (Optional) SQL Server name for the external metastore for Oozie
--oozieMetastoreDatabaseName <oozieMetastoreDatabaseName>  (Optional) Database name for the external metastore for Oozie
--oozieMetastoreUserName <oozieMetastoreUserName>          (Optional) Database username for the external metastore for Oozie
--oozieMetastorePassword <oozieMetastorePassword>          (Optional) Database password for the external metastore for Oozie
--configurationPath <configurationPath>                    (Optional) HDInsight cluster configuration file path
-s, --subscription <id>                                    The subscription id
--tags <tags>                                              Tags to set to the cluster.
Can be multiple.
In the format of 'name=value'.
Name is required and value is optional.
For example, --tags tag1=value1;tag2
```


**Comando per eliminare un cluster**

```azurecli
hdinsight cluster delete [options] <clusterName>
```

**Comando per mostrare i dettagli dei cluster**

```azurecli
hdinsight cluster show [options] <clusterName>
```

**Comando per elencare tutti i cluster (in un gruppo di risorse specifico, se specificato)**

```azurecli
hdinsight cluster list [options]
```

**Comando per ridimensionare un cluster**

```azurecli
hdinsight cluster resize [options] <clusterName> <targetInstanceCount>
```

**Comando per abilitare l'accesso HTTP per un cluster**

```azurecli
hdinsight cluster enable-http-access [options] <clusterName> <userName> <password>
```

**Comando per disabilitare l'accesso HTTP per un cluster**

```azurecli
hdinsight cluster disable-http-access [options] <clusterName>
```

**Comando per abilitare l'accesso RDP per un cluster**

```azurecli
hdinsight cluster enable-rdp-access [options] <clusterName> <rdpUserName> <rdpPassword> <rdpExpiryDate>
```

**Comando per disabilitare l'accesso HTTP per un cluster**

```azurecli
hdinsight cluster disable-rdp-access [options] <clusterName>
```

## <a name="insights-events-alert-rules-autoscale-settings-metrics"></a>Informazioni dettagliate (eventi, regole di avviso, impostazioni di scalabilità automatica, metriche)
**Recuperare i log delle operazioni per una sottoscrizione, un ID correlazione, un gruppo di risorse, una risorsa o un provider di risorse**

```azurecli
insights logs list [options]
```

## <a name="locations"></a>Posizioni 
**Elencare le posizioni disponibili**

```azurecli
location list [options]
```

## <a name="network-resources"></a>Risorse di rete
**Comandi per gestire le reti virtuali**

```azurecli
network vnet create [options] <resource-group> <name> <location>
```
Crea una rete virtuale. Nell'esempio seguente viene creata una rete virtuale denominata newvnet per il gruppo di risorse myresourcegroup nell'area westus.

```azurecli
azure network vnet create myresourcegroup newvnet "west us"
info:    Executing command network vnet create
+ Looking up virtual network "newvnet"
+ Creating virtual network "newvnet"
    Loading virtual network state
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
data:    Name:                 newvnet
data:    Type:                 Microsoft.Network/virtualNetworks
data:    Location:             westus
data:    Tags:
data:    Provisioning state:   Succeeded
data:    Address prefixes:
data:     10.0.0.0/8
data:    DNS servers:
data:    Subnets:
data:
info:    network vnet create command OK
```


Opzioni dei parametri:

```txt
-h, --help                                 output usage information
-v, --verbose                              use verbose output
--json                                     use json output
-g, --resource-group <resource-group>      the name of the resource group
-n, --name <name>                          the name of the virtual network
-l, --location <location>                  the location
-a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network
For example -a 10.0.0.0/24,10.0.1.0/24.
Default value is 10.0.0.0/8

-d, --dns-servers <dns-servers>            the comma separated list of DNS servers IP addresses
-t, --tags <tags>                          the tags set on this virtual network.
Can be multiple. In the format of "name=value".
Name is required and value is optional.
For example, -t tag1=value1;tag2
-s, --subscription <subscription>          the subscription identifier
```
<BR>

```azurecli
network vnet set [options] <resource-group> <name>
```

Aggiorna la configurazione di una rete virtuale all'interno di un gruppo di risorse.

```azurecli
azure network vnet set myresourcegroup newvnet

info:    Executing command network vnet set
+ Looking up virtual network "newvnet"
+ Updating virtual network "newvnet"
+ Loading virtual network state
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
data:    Name:                 newvnet
data:    Type:                 Microsoft.Network/virtualNetworks
data:    Location:             westus
data:    Tags:
data:    Provisioning state:   Succeeded
data:    Address prefixes:
data:     10.0.0.0/8
data:    DNS servers:
data:    Subnets:
data:
info:    network vnet set command OK
```

Opzioni dei parametri:

```txt
-h, --help                                 output usage information
-v, --verbose                              use verbose output
--json                                     use json output
-g, --resource-group <resource-group>      the name of the resource group
-n, --name <name>                          the name of the virtual network
-a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network.
For example -a 10.0.0.0/24,10.0.1.0/24.
This list will be appended to the current list of address prefixes.
The address prefixes in this list should not overlap between them.
The address prefixes in this list should not overlap with existing address prefixes in the vnet.

-d, --dns-servers [dns-servers]            the comma separated list of DNS servers IP addresses.
This list will be appended to the current list of DNS server IP addresses.

-t, --tags <tags>                          the tags set on this virtual network.
Can be multiple. In the format of "name=value".
Name is required and value is optional. For example, -t tag1=value1;tag2.
This list will be appended to the current list of tags

--no-tags                                  remove all existing tags
-s, --subscription <subscription>          the subscription identifier
```
<BR>

```azurecli
network vnet list [options] <resource-group>
```

Il comando elenca tutte le reti virtuali in un gruppo di risorse.

```azurecli
C:\>azure network vnet list myresourcegroup

info:    Executing command network vnet list
+ Listing virtual networks
    data:    ID
    Name      Location  Address prefixes  DNS servers
data:    -------------------------------------------------------------------
------  --------  --------  ----------------  -----------
data:    /subscriptions/###############################/resourceGroups/
wvnet   newvnet   westus    10.0.0.0/8
info:    network vnet list command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network vnet show [options] <resource-group> <name>
```
Il comando visualizza le proprietà della rete virtuale in un gruppo di risorse.

```azurecli
azure network vnet show -g myresourcegroup -n newvnet

info:    Executing command network vnet show
+ Looking up virtual network "newvnet"
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
data:    Name:                 newvnet
data:    Type:                 Microsoft.Network/virtualNetworks
data:    Location:             westus
data:    Tags:
data:    Provisioning state:   Succeeded
data:    Address prefixes:
data:     10.0.0.0/8
data:    DNS servers:
data:    Subnets:
data:
info:    network vnet show command OK
```
<BR>

```azurecli
network vnet delete [options] <resource-group> <name>
```
Il comando rimuove una rete virtuale.

```azurecli
azure network vnet delete myresourcegroup newvnetX

info:    Executing command network vnet delete
+ Looking up virtual network "newvnetX"
Delete virtual network newvnetX? [y/n] y
+ Deleting virtual network "newvnetX"
info:    network vnet delete command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the virtual network
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```


**Comandi per gestire le subnet delle reti virtuali**

```azurecli
network vnet subnet create [options] <resource-group> <vnet-name> <name>
```

Aggiunge un'altra subnet a una rete virtuale esistente.

```azurecli
azure network vnet subnet create -g myresourcegroup --vnet-name newvnet -n subnet --address-prefix 10.0.1.0/24

info:    Executing command network vnet subnet create
+ Looking up the subnet "subnet"
+ Creating subnet "subnet"
+ Looking up the subnet "subnet"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
data:    Name:                      subnet
data:    Type:                      Microsoft.Network/virtualNetworks/subnets
data:    Provisioning state:        Succeeded
data:    Address prefix:            10.0.1.0/24
info:    network vnet subnet create command OK
```

Opzioni dei parametri:

```txt
-h, --help                                                       output usage information
-v, --verbose                                                    use verbose output
    --json                                                           use json output
-g, --resource-group <resource-group>                            the name of the resource group
-e, --vnet-name <vnet-name>                                      the name of the virtual network
-n, --name <name>                                                the name of the subnet
-a, --address-prefix <address-prefix>                            the address prefix
-w, --network-security-group-id <network-security-group-id>      the network security group identifier.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
-o, --network-security-group-name <network-security-group-name>  the network security group name
-s, --subscription <subscription>                                the subscription identifier
```

<BR>

```azurecli
network vnet subnet set [options] <resource-group> <vnet-name> <name>
```

Imposta una subnet specifica della rete virtuale all'interno di un gruppo di risorse.

```azurecli
C:\>azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

info:    Executing command network vnet subnet set
+ Looking up the subnet "subnet1"
+ Setting subnet "subnet1"
+ Looking up the subnet "subnet1"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
data:    Name:                      subnet1
data:    Type:                      Microsoft.Network/virtualNetworks/subnets
data:    Provisioning state:        Succeeded
data:    Address prefix:            10.0.1.0/24
info:    network vnet subnet set command OK
```
<BR>

```azurecli
network vnet subnet list [options] <resource-group> <vnet-name>
```

Elenca tutte le subnet per una rete virtuale specifica all'interno di un gruppo di risorse.

```azurecli
azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

info:    Executing command network vnet subnet set
+ Looking up the subnet "subnet1"
+ Setting subnet "subnet1"
+ Looking up the subnet "subnet1"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
data:    Name:                      subnet1
data:    Type:                      Microsoft.Network/virtualNetworks/subnets
data:    Provisioning state:        Succeeded
data:    Address prefix:            10.0.1.0/24
info:    network vnet subnet set command OK
```
<BR>

```azurecli
network vnet subnet show [options] <resource-group> <vnet-name> <name>
```
Visualizza le proprietà della subnet della rete virtuale

```azurecli
azure network vnet subnet show -g myresourcegroup --vnet-name newvnet -n subnet1

info:    Executing command network vnet subnet show
+ Looking up the subnet "subnet1"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft
.Network/virtualNetworks/newvnet/subnets/subnet1
data:    Name:                      subnet1
data:    Type:                      Microsoft.Network/virtualNetworks/subnets
data:    Provisioning state:        Succeeded
data:    Address prefix:            10.0.1.0/24
info:    network vnet subnet show command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-e, --vnet-name <vnet-name>            the name of the virtual network
-n, --name <name>                      the name of the subnet
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network vnet subnet delete [options] <resource-group> <vnet-name> <subnet-name>
```
Rimuove una subnet da una rete virtuale esistente.

```azurecli
azure network vnet subnet delete -g myresourcegroup --vnet-name newvnet -n subnet1

info:    Executing command network vnet subnet delete
+ Looking up the subnet "subnet1"
Delete subnet "subnet1"? [y/n] y
+ Deleting subnet "subnet1"
info:    network vnet subnet delete command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-e, --vnet-name <vnet-name>            the name of the virtual network
-n, --name <name>                      the subnet name
-s, --subscription <subscription>      the subscription identifier
-q, --quiet                            quiet mode, do not ask for delete confirmation
```

**Comandi per gestire il bilanciamento del carico**

```azurecli
network lb create [options] <resource-group> <name> <location>
```
Crea un set di bilanciamento del carico.

```azurecli
azure network lb create -g myresourcegroup -n mylb -l westus

info:    Executing command network lb create
+ Looking up the load balancer "mylb"
+ Creating load balancer "mylb"
+ Looking up the load balancer "mylb"
data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
data:    Name:                         mylb
data:    Type:                         Microsoft.Network/loadBalancers
data:    Location:                     westus
data:    Provisioning state:           Succeeded
info:    network lb create command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the load balancer
-l, --location <location>              the location
-t, --tags <tags>                      the list of tags.
    Can be multiple. In the format of "name=value".
    Name is required and value is optional. For example, -t tag1=value1;tag2
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network lb list [options] <resource-group>
```
Elenca le risorse di bilanciamento del carico all'interno di un gruppo di risorse.

```azurecli
azure network lb list myresourcegroup

info:    Executing command network lb list
+ Getting the load balancers
data:    Name  Location
data:    ----  --------
data:    mylb  westus
info:    network lb list command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network lb show [options] <resource-group> <name>
```

Visualizza le informazioni di un servizio/dispositivo di bilanciamento del carico specifico all'interno di un gruppo di risorse.

```azurecli
azure network lb show myresourcegroup mylb -v

info:    Executing command network lb show
verbose: Looking up the load balancer "mylb"
data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
data:    Name:                         mylb
data:    Type:                         Microsoft.Network/loadBalancers
data:    Location:                     westus
data:    Provisioning state:           Succeeded
info:    network lb show command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network lb delete [options] <resource-group> <name>
```

Elimina le risorse di bilanciamento del carico.

```azurecli
azure network lb delete  myresourcegroup mylb

info:    Executing command network lb delete
+ Looking up the load balancer "mylb"
Delete load balancer "mylb"? [y/n] y
+ Deleting load balancer "mylb"
info:    network lb delete command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the load balancer
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```

**Comandi per gestire i probe di un servizio/dispositivo di bilanciamento del carico**

```azurecli
network lb probe create [options] <resource-group> <lb-name> <name>
```

Crea la configurazione di probe per lo stato di integrità nel servizio/dispositivo di bilanciamento del carico. Tenere presente che, per eseguire questo comando, il servizio/dispositivo di bilanciamento del carico richiede una risorsa IP front-end. Vedere il comando "azure network frontend-ip" per assegnare un indirizzo IP per il bilanciamento del carico.

```azurecli
azure network lb probe create -g myresourcegroup --lb-name mylb -n mylbprobe --protocol tcp --port 80 -i 300

info:    Executing command network lb probe create
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
info:    network lb probe create command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the probe
-p, --protocol <protocol>              the probe protocol
-o, --port <port>                      the probe port
-f, --path <path>                      the probe path
-i, --interval <interval>              the probe interval in seconds
-c, --count <count>                    the number of probes
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network lb probe set [options] <resource-group> <lb-name> <name>
```

Aggiorna un probe esistente del servizio/dispositivo di bilanciamento del carico con nuovi valori.

```azurecli
azure network lb probe set -g myresourcegroup -l mylb -n mylbprobe -p mylbprobe1 -p TCP -o 443 -i 300

info:    Executing command network lb probe set
    + Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
info:    network lb probe set command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the probe
-e, --new-probe-name <new-probe-name>  the new name of the probe
-p, --protocol <protocol>              the new value for probe protocol
-o, --port <port>                      the new value for probe port
-f, --path <path>                      the new value for probe path
-i, --interval <interval>              the new value for probe interval in seconds
-c, --count <count>                    the new value for number of probes
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network lb probe list [options] <resource-group> <lb-name>
```

Elenca le proprietà di probe per un set di bilanciamento del carico.

```azurecli
C:\>azure network lb probe list -g myresourcegroup -l mylb

info:    Executing command network lb probe list
+ Looking up the load balancer "mylb"
data:    Name       Protocol  Port  Path  Interval  Count
data:    ---------  --------  ----  ----  --------  -----
data:    mylbprobe  Tcp       443         300       2
info:    network lb probe list command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```

```azurecli
network lb probe delete [options] <resource-group> <lb-name> <name>
```
Rimuove il probe creato per il servizio/dispositivo di bilanciamento del carico.

```azurecli
azure network lb probe delete -g myresourcegroup -l mylb -n mylbprobe

info:    Executing command network lb probe delete
+ Looking up the load balancer "mylb"
Delete a probe "mylbprobe?" [y/n] y
+ Updating load balancer "mylb"
info:    network lb probe delete command OK
```

**Comandi per gestire le configurazioni IP front-end di un servizio/dispositivo di bilanciamento del carico**

```azurecli
network lb frontend-ip create [options] <resource-group> <lb-name> <name>
```
Crea una configurazione IP front-end per un set di bilanciamento del carico esistente.

```azurecli
azure network lb frontend-ip create -g myresourcegroup --lb-name mylb -n myfrontendip -o Dynamic -e subnet -m newvnet

info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "mylb"
+ Looking up the subnet "subnet"
+ Creating frontend IP configuration "myfrontendip"
+ Looking up the load balancer "mylb"
data:    Id:                           /subscriptions/###############################/resourceGroups/Myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
/frontendIPConfigurations/myfrontendip
data:    Name:                         myfrontendip
data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
data:    Provisioning state:           Succeeded
data:    Private IP allocation method: Dynamic
data:    Private IP address:           10.0.1.4
data:    Subnet:                       id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
data:    Public IP address:
data:    Inbound NAT rules
data:    Outbound NAT rules
data:    Load balancing rules
data:
info:    network lb frontend-ip create command OK
```

<BR>

```azurecli
network lb frontend-ip set [options] <resource-group> <lb-name> <name>
```

Aggiorna una configurazione esistente di un elemento IP front-end. Il comando seguente aggiunge un elemento IP pubblico denominato mypubip5 a un elemento IP front-end di bilanciamento del carico esistente denominato myfrontendip.

```azurecli
azure network lb frontend-ip set -g myresourcegroup --lb-name mylb -n myfrontendip -i mypubip5

info:    Executing command network lb frontend-ip set
+ Looking up the load balancer "mylb"
+ Looking up the public ip "mypubip5"
+ Updating load balancer "mylb"
+ Looking up the load balancer "mylb"
data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
data:    Name:                         myfrontendip
data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
data:    Provisioning state:           Succeeded
data:    Private IP allocation method: Dynamic
data:    Private IP address:
data:    Subnet:
data:    Public IP address:            id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypubip5
data:    Inbound NAT rules
data:    Outbound NAT rules
data:    Load balancing rules
data:
info:    network lb frontend-ip set command OK
```

Opzioni dei parametri:

```txt
-h, --help                                                         output usage information
-v, --verbose                                                      use verbose output
--json                                                             use json output
-g, --resource-group <resource-group>                              the name of the resource group
-l, --lb-name <lb-name>                                            the name of the load balancer
-n, --name <name>                                                  the name of the frontend ip configuration
-a, --private-ip-address <private-ip-address>                      the private ip address
-o, --private-ip-allocation-method <private-ip-allocation-method>  the private ip allocation method [Static, Dynamic]
-u, --public-ip-id <public-ip-id>                                  the public ip identifier.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
-i, --public-ip-name <public-ip-name>                              the public ip name.
This public ip must exist in the same resource group as the lb.
Please use public-ip-id if that is not the case.
-b, --subnet-id <subnet-id>                                        the subnet id.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/VirtualNetworks/<vnet-name>/subnets/<subnet-name>
-e, --subnet-name <subnet-name>                                    the subnet name
-m, --vnet-name <vnet-name>                                        the virtual network name.
This virtual network must exist in the same resource group as the lb.
Please use subnet-id if that is not the case.
-s, --subscription <subscription>                                  the subscription identifier
```

<BR>

```azurecli
network lb frontend-ip list [options] <resource-group> <lb-name>
```

Elenca tutte le risorse IP front-end configurate per il servizio/dispositivo di bilanciamento del carico.

```azurecli
azure network lb frontend-ip list -g myresourcegroup -l mylb

info:    Executing command network lb frontend-ip list
+ Looking up the load balancer "mylb"
data:    Name         Provisioning state  Private IP allocation method  Subnet
data:    -----------  ------------------  ----------------------------  ------
data:    myprivateip  Succeeded           Dynamic
info:    network lb frontend-ip list command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network lb frontend-ip delete [options] <resource-group> <lb-name> <name>
```
Elimina l'oggetto IP front-end associato al servizio/dispositivo di bilanciamento del carico

```azurecli
network lb frontend-ip delete -g myresourcegroup -l mylb -n myfrontendip
info:    Executing command network lb frontend-ip delete
+ Looking up the load balancer "mylb"
Delete frontend ip configuration "myfrontendip"? [y/n] y
+ Updating load balancer "mylb"
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the frontend ip configuration
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```

**Comandi per gestire i pool di indirizzi back-end di un servizio/dispositivo di bilanciamento del carico**

```azurecli
network lb address-pool create [options] <resource-group> <lb-name> <name>
```

Creare un pool di indirizzi back-end per un servizio/dispositivo di bilanciamento del carico.

```azurecli
azure network lb address-pool create -g myresourcegroup --lb-name mylb -n myaddresspool

info:    Executing command network lb address-pool create
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
+ Looking up the load balancer "mylb"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourgroup/providers/Microso.Network/loadBalancers/mylb/backendAddressPools/myaddresspool
data:    Name:                      myaddresspool
data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
data:    Provisioning state:        Succeeded
data:    Backend IP configurations:
data:    Load balancing rules:
data:
info:    network lb address-pool create command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the backend address pool
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network lb address-pool list [options] <resource-group> <lb-name>
```

Elenca l'intervallo pool di indirizzi IP back-end per un gruppo di risorse specifico.

```azurecli
azure network lb address-pool list -g myresourcegroup -l mylb

info:    Executing command network lb address-pool list
+ Looking up the load balancer "mylb"
data:    Name           Provisioning state
data:    -------------  ------------------
data:    mybackendpool  Succeeded
info:    network lb address-pool list command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network lb address-pool delete [options] <resource-group> <lb-name> <name>
```

Rimuove la risorsa dell'intervallo pool di indirizzi IP back-end dal servizio/dispositivo di bilanciamento del carico.

```azurecli
azure network lb address-pool delete -g myresourcegroup -l mylb -n mybackendpool

info:    Executing command network lb address-pool delete
+ Looking up the load balancer "mylb"
Delete backend address pool "mybackendpool"? [y/n] y
+ Updating load balancer "mylb"
info:    network lb address-pool delete command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the backend address pool
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```

**Comandi per gestire le regole di bilanciamento del carico**

```azurecli
network lb rule create [options] <resource-group> <lb-name> <name>
```
Crea regole di bilanciamento del carico.

È possibile creare una regola di bilanciamento del carico configurando l'endpoint front-end per il servizio/dispositivo di bilanciamento del carico e l'intervallo di pool di indirizzi back-end a cui giunge il traffico di rete in ingresso. Le impostazioni includono anche le porte per l'endpoint IP front-end e le porte per l'intervallo pool di indirizzi back-end.

L'esempio seguente illustra come creare una regola di bilanciamento del carico, con l'endpoint front-end in ascolto sulla porta TCP 80 e l'invio del traffico di rete con bilanciamento del carico alla porta 8080 per l'intervallo di pool di indirizzi back-end.

```azurecli
azure network lb rule create -g myresourcegroup -l mylb -n mylbrule -p tcp -f 80 -b 8080 -i 10


info:    Executing command network lb rule create
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
+ Loading rule state
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mylbrule
data:    Name:                      mylbrule
data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
data:    Provisioning state:        Succeeded
data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
data:    Protocol:                  Tcp
data:    Frontend port:             80
data:    Backend port:              8080
data:    Enable floating IP:        false
data:    Idle timeout in minutes:   10
data:    Probes
data:
info:    network lb rule create command OK
```

<BR>

```azurecli
network lb rule set [options] <resource-group> <lb-name> <name>
```

Aggiorna una regola di sistema di bilanciamento del carico esistente impostata in un gruppo di risorse specifico. Nell'esempio seguente il nome della regola è stato modificato da mylbrule a mynewlbrule.

```azurecli
azure network lb rule set -g myresourcegroup -l mylb -n mylbrule -r mynewlbrule -p tcp -f 80 -b 8080 -i 10 -t myfrontendip -o mybackendpool

info:    Executing command network lb rule set
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
+ Loading rule state
data:    Id:                        /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mynewlbrule
data:    Name:                      mynewlbrule
data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
data:    Provisioning state:        Succeeded
data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
data:    Protocol:                  Tcp
data:    Frontend port:             80
data:    Backend port:              8080
data:    Enable floating IP:        false
data:    Idle timeout in minutes:   10
data:    Probes
data:
info:    network lb rule set command OK
```

Opzioni dei parametri:

```txt
-h, --help                                         output usage information
-v, --verbose                                      use verbose output
--json                                             use json output
-g, --resource-group <resource-group>              the name of the resource group
-l, --lb-name <lb-name>                            the name of the load balancer
-n, --name <name>                                  the name of the rule
-r, --new-rule-name <new-rule-name>                new rule name
-p, --protocol <protocol>                          the rule protocol
-f, --frontend-port <frontend-port>                the frontend port
-b, --backend-port <backend-port>                  the backend port
-e, --enable-floating-ip <enable-floating-ip>      enable floating point ip
-i, --idle-timeout <idle-timeout>                  the idle timeout in minutes
-a, --probe-name [probe-name]                      the name of the probe defined in the same load balancer
-t, --frontend-ip-name <frontend-ip-name>          the name of the frontend ip configuration in the same load balancer
-o, --backend-address-pool <backend-address-pool>  name of the backend address pool defined in the same load balancer
-s, --subscription <subscription>                  the subscription identifier
```


```azurecli
network lb rule list [options] <resource-group> <lb-name>
```

Elenca tutte le regole di bilanciamento del carico configurate per un servizio/dispositivo di bilanciamento del carico in un gruppo di risorse specifico.

```azurecli
azure network lb rule list -g myresourcegroup -l mylb

info:    Executing command network lb rule list
+ Looking up the load balancer "mylb"
data:    Name         Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend address pool  Probe data

data:    mynewlbrule  Succeeded           Tcp       80             8080          false               10                       /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
info:    network lb rule list command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```

```azurecli
network lb rule delete [options] <resource-group> <lb-name> <name>
```

Elimina una regola di bilanciamento del carico.

```azurecli
azure network lb rule delete -g myresourcegroup -l mylb -n mynewlbrule

info:    Executing command network lb rule delete
+ Looking up the load balancer "mylb"
Delete load balancing rule mynewlbrule? [y/n] y
+ Updating load balancer "mylb"
info:    network lb rule delete command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the rule
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```

**Comandi per gestire le regole NAT in ingresso per il bilanciamento del carico**

```azurecli
network lb inbound-nat-rule create [options] <resource-group> <lb-name> <name>
```
Crea una regola NAT in ingresso per il bilanciamento del carico.

Nell'esempio seguente è stata creata una regola NAT dall'oggetto IP front-end (definito in precedenza tramite il comando "azure network frontend-ip") con una porta di ascolto in ingresso e una porta in uscita usata dal servizio/dispositivo di bilanciamento del carico per inviare il traffico di rete.

```azurecli
azure network lb inbound-nat-rule create -g myresourcegroup -l mylb -n myinboundnat -p tcp -f 80 -b 8080 -i myfrontendip

info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
+ Looking up the load balancer "mylb"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
data:    Name:                      myinboundnat
data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
data:    Provisioning state:        Succeeded
data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
data:    Backend IP configuration
data:    Protocol                   Tcp
data:    Frontend port              80
data:    Backend port               8080
data:    Enable floating IP         false
info:    network lb inbound-nat-rule create command OK
```

Opzioni dei parametri:

```txt
-h, --help                                     output usage information
-v, --verbose                                  use verbose output
--json                                         use json output
-g, --resource-group <resource-group>          the name of the resource group
-l, --lb-name <lb-name>                        the name of the load balancer
-n, --name <name>                              the name of the inbound NAT rule
-p, --protocol <protocol>                      the rule protocol [tcp,udp]
-f, --frontend-port <frontend-port>            the frontend port [0-65535]
-b, --backend-port <backend-port>              the backend port [0-65535]
-e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
-i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
-m, --vm-id <vm-id>                            the VM id.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
-a, --vm-name <vm-name>                        the VM name.This VM must exist in the same resource group as the lb.
Please use vm-id if that is not the case.
this parameter will be ignored if --vm-id is specified
-s, --subscription <subscription>              the subscription identifier
```
<BR>

```azurecli
network lb inbound-nat-rule set [options] <resource-group> <lb-name> <name>
```
Aggiorna una regola NAT in ingresso esistente. Nell'esempio seguente la porta di ascolto in ingresso è stata modificata da 80 a 81.

```azurecli
azure network lb inbound-nat-rule set -g group-1 -l mylb -n myinboundnat -p tcp -f 81 -b 8080 -i myfrontendip

info:    Executing command network lb inbound-nat-rule set
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
+ Looking up the load balancer "mylb"
data:    Id:                        /subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
data:    Name:                      myinboundnat
data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
data:    Provisioning state:        Succeeded
data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
data:    Backend IP configuration
data:    Protocol                   Tcp
data:    Frontend port              81
data:    Backend port               8080
data:    Enable floating IP         false
info:    network lb inbound-nat-rule set command OK
```

Opzioni dei parametri:

```txt
-h, --help                                     output usage information
-v, --verbose                                  use verbose output
--json                                         use json output
-g, --resource-group <resource-group>          the name of the resource group
-l, --lb-name <lb-name>                        the name of the load balancer
-n, --name <name>                              the name of the inbound NAT rule
-p, --protocol <protocol>                      the rule protocol [tcp,udp]
-f, --frontend-port <frontend-port>            the frontend port [0-65535]
-b, --backend-port <backend-port>              the backend port [0-65535]
-e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
-i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
-m, --vm-id [vm-id]                            the VM id.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
-a, --vm-name <vm-name>                        the VM name.
This virtual machine must exist in the same resource group as the lb.
Please use vm-id if that is not the case
-s, --subscription <subscription>              the subscription identifier
```
<BR>

```azurecli
network lb inbound-nat-rule list [options] <resource-group> <lb-name>
```

Elenca tutte le regole NAT in ingresso per il servizio/dispositivo di bilanciamento del carico.

```azurecli
azure network lb inbound-nat-rule list -g myresourcegroup -l mylb

info:    Executing command network lb inbound-nat-rule list
+ Looking up the load balancer "mylb"
data:    Name          Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend IP configuration
data:    ------------  ------------------  --------  -------------  ------------  ------------------  -----------------------  ---
---------------------
data:    myinboundnat  Succeeded           Tcp       81             8080          false               4

info:    network lb inbound-nat-rule list command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network lb inbound-nat-rule delete [options] <resource-group> <lb-name> <name>
```

Elimina una regola NAT per il servizio/dispositivo di bilanciamento del carico in un gruppo di risorse specifico.

```azurecli
azure network lb inbound-nat-rule delete -g myresourcegroup -l mylb -n myinboundnat

info:    Executing command network lb inbound-nat-rule delete
+ Looking up the load balancer "mylb"
Delete inbound NAT rule "myinboundnat?" [y/n] y
+ Updating load balancer "mylb"
info:    network lb inbound-nat-rule delete command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the inbound NAT rule
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```

**Comandi per gestire gli indirizzi IP pubblici**

```azurecli
network public-ip create [options] <resource-group> <name> <location>
```
Crea una risorsa IP pubblico. La risorsa verrà creata e associata a un nome di dominio.

```azurecli
azure network public-ip create -g myresourcegroup -n mytestpublicip1 -l eastus -d azureclitest -a "Dynamic"
info:    Executing command network public-ip create
+ Looking up the public ip "mytestpublicip1"
+ Creating public ip address "mytestpublicip1"
+ Looking up the public ip "mytestpublicip1"
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
data:    Name:                 mytestpublicip1
data:    Type:                 Microsoft.Network/publicIPAddresses
data:    Location:             eastus
data:    Provisioning state:   Succeeded
data:    Allocation method:    Dynamic
data:    Idle timeout:         4
data:    Domain name label:    azureclitest
data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
info:    network public-ip create command OK
```


Opzioni dei parametri:

```txt
-h, --help                                   output usage information
-v, --verbose                                use verbose output
--json                                       use json output
-g, --resource-group <resource-group>        the name of the resource group
-n, --name <name>                            the name of the public ip
-l, --location <location>                    the location
-d, --domain-name-label <domain-name-label>  the domain name label.
This set DNS to <domain-name-label>.<location>.cloudapp.azure.com
-a, --allocation-method <allocation-method>  the allocation method [Static][Dynamic]
-i, --idletimeout <idletimeout>              the idle timeout in minutes
-f, --reverse-fqdn <reverse-fqdn>            the reverse fqdn
-t, --tags <tags>                            the list of tags.
Can be multiple. In the format of "name=value".
Name is required and value is optional.
For example, -t tag1=value1;tag2
-s, --subscription <subscription>            the subscription identifier
```
<br>

```azurecli
network public-ip set [options] <resource-group> <name>
```
Aggiorna le proprietà di una risorsa IP pubblico esistente. Nell'esempio seguente l'impostazione dell'indirizzo IP pubblico è stata modificata da Dynamic a Static.

```azurecli
azure network public-ip set -g group-1 -n mytestpublicip1 -d azureclitest -a "Static"
info:    Executing command network public-ip set
+ Looking up the public ip "mytestpublicip1"
+ Updating public ip address "mytestpublicip1"
+ Looking up the public ip "mytestpublicip1"
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
data:    Name:                 mytestpublicip1
data:    Type:                 Microsoft.Network/publicIPAddresses
data:    Location:             eastus
data:    Provisioning state:   Succeeded
data:    Allocation method:    Static
data:    Idle timeout:         4
data:    IP Address:           (static IP address)
data:    Domain name label:    azureclitest
data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
info:    network public-ip set command OK
```

Opzioni dei parametri:

```txt
-h, --help                                   output usage information
-v, --verbose                                use verbose output
--json                                       use json output
-g, --resource-group <resource-group>        the name of the resource group
-n, --name <name>                            the name of the public ip
-d, --domain-name-label [domain-name-label]  the domain name label.
This set DNS to <domain-name-label>.<location>.cloudapp.azure.com
-a, --allocation-method <allocation-method>  the allocation method [Static][Dynamic]
-i, --idletimeout <idletimeout>              the idle timeout in minutes
-f, --reverse-fqdn [reverse-fqdn]            the reverse fqdn
-t, --tags <tags>                            the list of tags.
Can be multiple. In the format of "name=value".
Name is required and value is optional.
For example, -t tag1=value1;tag2
--no-tags                                    remove all existing tags
-s, --subscription <subscription>            the subscription identifier
```

<br>

```azurecli
network public-ip list [options] <resource-group>
```
Elenca tutte le risorse IP pubbliche all'interno di un gruppo di risorse.

```azurecli
azure network public-ip list -g myresourcegroup

info:    Executing command network public-ip list
+ Getting the public ip addresses
data:    Name             Location  Allocation  IP Address    Idle timeout  DNS Name
data:    ---------------  --------  ----------  ------------  ------------  -------------------------------------------
data:    mypubip5         westus    Dynamic                   4             "domain name".westus.cloudapp.azure.com
data:    myPublicIP       eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
data:    mytestpublicip   eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
data:    mytestpublicip1  eastus   Static (Static IP address) 4             azureclitest.eastus.cloudapp.azure.com
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network public-ip show [options] <resource-group> <name>
```

Visualizza le proprietà di una risorsa IP pubblico all'interno di un gruppo di risorse.

```azurecli
azure network public-ip show -g myresourcegroup -n mytestpublicip

info:    Executing command network public-ip show
+ Looking up the public ip "mytestpublicip1"
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip
data:    Name:                 mytestpublicip
data:    Type:                 Microsoft.Network/publicIPAddresses
data:    Location:             eastus
data:    Provisioning state:   Succeeded
data:    Allocation method:    Static
data:    Idle timeout:         4
data:    IP Address:           (static IP address)
data:    Domain name label:    azureclitest
data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
info:    network public-ip show command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the public IP
-s, --subscription <subscription>      the subscription identifier
```


```azurecli
network public-ip delete [options] <resource-group> <name>
```

Elimina una risorsa IP pubblico.

```azurecli
azure network public-ip delete -g group-1 -n mypublicipname
info:    Executing command network public-ip delete
+ Looking up the public ip "mypublicipname"
Delete public ip address "mypublicipname"? [y/n] y
+ Deleting public ip address "mypublicipname"
info:    network public-ip delete command OK
```

Opzioni dei parametri:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the public IP
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```


**Comandi per gestire le interfacce di rete**

```azurecli
network nic create [options] <resource-group> <name> <location>
```
Crea una risorsa denominata interfaccia di rete (NIC) che può essere usata per i servizi/dispositivi di bilanciamento del carico o per l'associazione a una macchina virtuale.

```azurecli
azure network nic create -g myresourcegroup -l eastus -n testnic1 --subnet-name subnet-1 --subnet-vnet-name myvnet

info:    Executing command network nic create
+ Looking up the network interface "testnic1"
+ Looking up the subnet "subnet-1"
+ Creating network interface "testnic1"
+ Looking up the network interface "testnic1"
data:    Id:                     /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/networkInterfaces/testnic1
data:    Name:                   testnic1
data:    Type:                   Microsoft.Network/networkInterfaces
data:    Location:               eastus
data:    Provisioning state:     Succeeded
data:    IP configurations:
data:       Name:                         NIC-config
data:       Provisioning state:           Succeeded
data:       Private IP address:           10.0.0.5
data:       Private IP Allocation Method: Dynamic
data:       Subnet:                       /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/virtualNetworks/myVNET/subnets/Subnet-1
```

Opzioni dei parametri:

```txt
-h, --help                                                       output usage information
-v, --verbose                                                    use verbose output
--json                                                           use json output
-g, --resource-group <resource-group>                            the name of the resource group
-n, --name <name>                                                the name of the network interface
-l, --location <location>                                        the location
-w, --network-security-group-id <network-security-group-id>      the network security group identifier.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
-o, --network-security-group-name <network-security-group-name>  the network security group name.
This network security group must exist in the same resource group as the nic.
Please use network-security-group-id if that is not the case.
-i, --public-ip-id <public-ip-id>                                the public IP identifier.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
-p, --public-ip-name <public-ip-name>                            the public IP name.
This public ip must exist in the same resource group as the nic.
Please use public-ip-id if that is not the case.
-a, --private-ip-address <private-ip-address>                    the private IP address
-u, --subnet-id <subnet-id>                                      the subnet identifier.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>/subnets/<subnet-name>
--subnet-name <subnet-name>                                      the subnet name
-m, --subnet-vnet-name <subnet-vnet-name>                        the vnet name under which subnet-name exists
-t, --tags <tags>                                                the comma separated list of tags.
Can be multiple. In the format of "name=value".
Name is required and value is optional.
For example, -t tag1=value1;tag2
-s, --subscription <subscription>                                the subscription identifier
data:
info:    network nic create command OK
```

<BR>

```azurecli
network nic set [options] <resource-group> <name>
network nic list [options] <resource-group>
network nic show [options] <resource-group> <name>
network nic delete [options] <resource-group> <name>
```

**Comandi per gestire i gruppi di sicurezza di rete**

```azurecli
network nsg create [options] <resource-group> <name> <location>
network nsg set [options] <resource-group> <name>
network nsg list [options] <resource-group>
network nsg show [options] <resource-group> <name>
network nsg delete [options] <resource-group> <name>
```

**Comandi per gestire le regole dei gruppi di sicurezza di rete**

```azurecli
network nsg rule create [options] <resource-group> <nsg-name> <name>
network nsg rule set [options] <resource-group> <nsg-name> <name>
network nsg rule list [options] <resource-group> <nsg-name>
network nsg rule show [options] <resource-group> <nsg-name> <name>
network nsg rule delete [options] <resource-group> <nsg-name> <name>
```

**Comandi per gestire il profilo di gestione traffico**

```azurecli
network traffic-manager profile create [options] <resource-group> <name>
network traffic-manager profile set [options] <resource-group> <name>
network traffic-manager profile list [options] <resource-group>
network traffic-manager profile show [options] <resource-group> <name>
network traffic-manager profile delete [options] <resource-group> <name>
network traffic-manager profile is-dns-available [options] <resource-group> <relative-dns-name>
```

**Comandi per gestire gli endpoint di gestione traffico**

```azurecli
network traffic-manager profile endpoint create [options] <resource-group> <profile-name> <name> <endpoint-location>
network traffic-manager profile endpoint set [options] <resource-group> <profile-name> <name>
network traffic-manager profile endpoint delete [options] <resource-group> <profile-name> <name>
```

**Comandi per gestire i gateway di rete virtuale**

```azurecli
network gateway list [options] <resource-group>
```

## <a name="resource-provider-registrations"></a>Registrazioni del provider di risorse
**Elencare i provider attualmente registrati in Resource Manager**

```azurecli
provider list [options]
```

**Visualizzare i dettagli relativi allo spazio dei nomi del provider richiesto**

```azurecli
provider show [options] <namespace>
```

**Registrare il provider nella sottoscrizione**

```azurecli
provider register [options] <namespace>
```

**Annullare la registrazione del provider nella sottoscrizione**

```azurecli
provider unregister [options] <namespace>
```

## <a name="resources"></a>Risorse
**Creare una risorsa in un gruppo di risorse**

```azurecli
resource create [options] <resource-group> <name> <resource-type> <location> <api-version>
```

**Aggiornare una risorsa in un gruppo di risorse senza modelli o parametri**

```azurecli
resource set [options] <resource-group> <name> <resource-type> <properties> <api-version>
```

**Elencare le risorse**

```azurecli
resource list [options] [resource-group]
```

**Ottenere una risorsa all'interno di un gruppo di risorse o una sottoscrizione**

```azurecli
resource show [options] <resource-group> <name> <resource-type> <api-version>
```

**Eliminare una risorsa in un gruppo di risorse**

```azurecli
resource delete [options] <resource-group> <name> <resource-type> <api-version>
```

## <a name="azure-roles"></a>Ruoli di Azure
**Ottenere tutte le definizioni di ruolo disponibili**

```azurecli
role list [options]
```

**Ottenere una definizione di ruolo disponibile**

```azurecli
role show [options] [name]
```

**Comandi per gestire l'assegnazione del ruolo**

```azurecli
role assignment create [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
role assignment list [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
role assignment delete [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
```

## <a name="storage-objects"></a>Oggetti di archiviazione
**Comandi per la gestione degli account di archiviazione**

```azurecli
storage account list [options]
storage account show [options] <name>
storage account create [options] <name>
storage account set [options] <name>
storage account delete [options] <name>
```

**Comandi per gestire le chiavi degli account di archiviazione**

```azurecli
storage account keys list [options] <name>
storage account keys renew [options] <name>
```

**Comandi per visualizzare la stringa di connessione archiviazione**

```azurecli
storage account connectionstring show [options] <name>
```

**Comandi per gestire i contenitori di archiviazione**

```azurecli
storage container list [options] [prefix]
storage container show [options] [container]
storage container create [options] [container]
storage container delete [options] [container]
storage container set [options] [container]
```

**Comandi per gestire le firme di accesso condiviso del contenitore di archiviazione**

```azurecli
storage container sas create [options] [container] [permissions] [expiry]
```

**Comandi per gestire i criteri di accesso archiviati del contenitore di archiviazione**

```azurecli
storage container policy create [options] [container] [name]
storage container policy show [options] [container] [name]
storage container policy list [options] [container]
storage container policy set [options] [container] [name]
storage container policy delete [options] [container] [name]
```

**Comandi per gestire i BLOB di archiviazione**

```azurecli
storage blob list [options] [container] [prefix]
storage blob show [options] [container] [blob]
storage blob delete [options] [container] [blob]
storage blob upload [options] [file] [container] [blob]
storage blob download [options] [container] [blob] [destination]
```

**Comandi per gestire le operazioni di copia BLOB**

```azurecli
storage blob copy start [options] [sourceUri] [destContainer]
storage blob copy show [options] [container] [blob]
storage blob copy stop [options] [container] [blob] [copyid]
```

**Comandi per gestire la firma di accesso condiviso del BLOB di archiviazione**

```azurecli
storage blob sas create [options] [container] [blob] [permissions] [expiry]
```

**Comandi per gestire le condivisioni file di archiviazione**

```azurecli
storage share create [options] [share]
storage share show [options] [share]
storage share delete [options] [share]
storage share list [options] [prefix]
```

**Comandi per gestire i file di archiviazione**

```azurecli
storage file list [options] [share] [path]
storage file delete [options] [share] [path]
storage file upload [options] [source] [share] [path]
storage file download [options] [share] [path] [destination]
```

**Comandi per gestire la directory dei file di archiviazione**

```azurecli
storage directory create [options] [share] [path]
storage directory delete [options] [share] [path]
```

**Comandi per gestire le code di archiviazione**

```azurecli
storage queue create [options] [queue]
storage queue list [options] [prefix]
storage queue show [options] [queue]
storage queue delete [options] [queue]
```

**Comandi per gestire le firme di accesso condiviso della coda di archiviazione**

```azurecli
storage queue sas create [options] [queue] [permissions] [expiry]
```

**Comandi per gestire i criteri di accesso archiviati della coda di archiviazione**

```azurecli
storage queue policy create [options] [queue] [name]
storage queue policy show [options] [queue] [name]
storage queue policy list [options] [queue]
storage queue policy set [options] [queue] [name]
storage queue policy delete [options] [queue] [name]
```

**Comandi per gestire le proprietà di registrazione dell'archiviazione**

```azurecli
storage logging show [options]
storage logging set [options]
```

**Comandi per gestire le proprietà delle metriche di archiviazione**

```azurecli
storage metrics show [options]
storage metrics set [options]
```

**Comandi per gestire le tabelle di archiviazione**

```azurecli
storage table create [options] [table]
storage table list [options] [prefix]
storage table show [options] [table]
storage table delete [options] [table]
```

**Comandi per gestire le firme di accesso condiviso della tabella di archiviazione**

```azurecli
storage table sas create [options] [table] [permissions] [expiry]
```

**Comandi per gestire i criteri di accesso condiviso della tabella di archiviazione**

```azurecli
storage table policy create [options] [table] [name]
storage table policy show [options] [table] [name]
storage table policy list [options] [table]
storage table policy set [options] [table] [name]
storage table policy delete [options] [table] [name]
```

## <a name="tags"></a>Tag
**Aggiungere un tag**

```azurecli
tag create [options] <name> <value>
```

**Rimuovere un intero tag o un valore di tag**

```azurecli
tag delete [options] <name> <value>
```

**Elencare le informazioni di tag**

```azurecli
tag list [options]
```

**Ottenere un tag**

```azurecli
tag show [options] [name]
```

## <a name="virtual-machines"></a>Macchine virtuali
**Creare una macchina virtuale**

```azurecli
vm create [options] <resource-group> <name> <location> <os-type>
```

**Creare una macchina virtuale con le risorse predefinite**

```azurecli
vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password
```

> [!TIP]
> A partire dalla versione 0.10 dell'interfaccia della riga di comando è possibile specificare un alias breve, ad esempio "UbuntuLTS" o "Win2012R2Datacenter" come `image-urn` per alcune immagini Marketplace più diffuse. Eseguire `azure help vm quick-create` per le opzioni. A partire dalla versione 0.10, `azure vm quick-create` usa inoltre l'archiviazione Premium per impostazione predefinita, se disponibile nell'area selezionata.
> 
> 

**Elencare le macchine virtuali all’interno di un account**

```azurecli
vm list [options]
```

**Ottenere una macchina virtuale all'interno di un gruppo di risorse**

```azurecli
vm show [options] <resource-group> <name>
```

**Eliminare una macchina virtuale all'interno di un gruppo di risorse**

```azurecli
vm delete [options] <resource-group> <name>
```

**Arrestare una macchina virtuale all'interno di un gruppo di risorse**

```azurecli
vm stop [options] <resource-group> <name>
```

**Riavviare una macchina virtuale all'interno di un gruppo di risorse**

```azurecli
vm restart [options] <resource-group> <name>
```

**Avviare una macchina virtuale all'interno di un gruppo di risorse**

```azurecli
vm start [options] <resource-group> <name>
```

**Arrestare una macchina virtuale all'interno di un gruppo di risorse e rilasciare le risorse di calcolo**

```azurecli
vm deallocate [options] <resource-group> <name>
```

**Elencare le dimensioni delle macchine virtuali disponibili**

```azurecli
vm sizes [options]
```

**Acquisire la macchina virtuale come immagine del sistema operativo o immagine di macchina virtuale**

```azurecli
vm capture [options] <resource-group> <name> <vhd-name-prefix>
```

**Impostare la macchina virtuale sullo stato generalizzato**

```azurecli
vm generalize [options] <resource-group> <name>
```

**Ottenere la visualizzazione dell'istanza della macchina virtuale**

```azurecli
vm get-instance-view [options] <resource-group> <name>
```

**Consente di ripristinare le impostazioni di Accesso desktop remoto o SSH in una macchina virtuale e di reimpostare la password per l'account con autorità di amministratore o sudo**

```azurecli
vm reset-access [options] <resource-group> <name>
```

**Aggiornare la macchina virtuale con nuovi dati**

```azurecli
vm set [options] <resource-group> <name>
```

**Comandi per gestire i dischi dati delle macchine virtuali**

```azurecli
vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]
vm disk detach [options] <resource-group> <vm-name> <lun>
vm disk attach [options] <resource-group> <vm-name> [vhd-url]
```

**Comandi per gestire le estensioni delle risorse delle macchine virtuali**

```azurecli
vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>
vm extension get [options] <resource-group> <vm-name>
```

**Comandi per gestire la macchina virtuale Docker**

```azurecli
vm docker create [options] <resource-group> <name> <location> <os-type>
```

**Comandi per gestire le immagini delle macchine virtuali**

```azurecli
vm image list-publishers [options] <location>
vm image list-offers [options] <location> <publisher>
vm image list-skus [options] <location> <publisher> <offer>
vm image list [options] <location> <publisher> [offer] [sku]
```
