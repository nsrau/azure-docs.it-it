---
title: Installare MongoDB in una macchina virtuale Linux con l&quot;interfaccia della riga di comando di Azure 2.0 | Documentazione Microsoft
description: Informazioni su come installare e configurare MongoDB in una macchina virtuale Linux usando l&quot;interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/14/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 4a584a677df140f4923515527214dffd03846a74
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Come installare e configurare MongoDB in una macchina virtuale Linux
[MongoDB](http://www.mongodb.org) è un diffuso database NoSQL open source a prestazioni elevate. Questo articolo illustra come installare e configurare MongoDB in una VM Linux usando l'interfaccia della riga di comando di Azure 2.0. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](install-mongodb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Alcuni esempi illustrano in dettaglio come fare a:

* [Installare e configurare manualmente un'istanza di MongoDB di base](#manually-install-and-configure-mongodb-on-a-vm)
* [Creare un'istanza di MongoDB di base usando un modello di Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Creare un cluster complesso di MongoDB partizionato con set di repliche usando un modello di Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Installare e configurare manualmente MongoDB su una VM
MongoDB [fornisce le istruzioni di installazione](https://docs.mongodb.com/manual/administration/install-on-linux/) per i sistemi operativi Linux Red Hat/CentOS, SUSE, Ubuntu e Debian. L'esempio seguente crea una VM `CentOS` usando la chiave SSH archiviata in `~/.ssh/id_rsa.pub`. Per creare questo ambiente, è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure tramite [az login](/cli/azure/#login).

Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#create). Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `West US`:

```azurecli
 az group create --name myResourceGroup --location westus
```

Creare una VM con il comando [az vm create](/cli/azure/vm#create). L'esempio seguente crea una VM denominata `myVM` con un utente denominato `azureuser` usando l'autenticazione con chiave pubblica SSH e la voce DNS pubblica `mypublicdns`:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --public-ip-address-dns-name mypublicdns
```

Eseguire l'accesso alla VM usando l'indirizzo DNS pubblico della VM. È possibile visualizzare l'indirizzo DNS pubblico con [az vm show](/cli/azure/vm#show):

```azurecli
az vm show -g myResourceGroup -n myVM -d --query [fqdns] -o tsv
```

Eseguire SSH sulla VM usando il proprio nome utente e l'indirizzo DNS pubblico:

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Per aggiungere le origini di installazione di MongoDB, creare un file di archivio `yum` come di seguito:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.2.repo
```

Aprire il file di archivio di MongoDB da modificare. Aggiungere le righe seguenti:

```sh
[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
```

Installare MongoDB usando `yum` come di seguito:

```bash
sudo yum install -y mongodb-org
```

Per impostazione predefinita, alle immagini CentOS è applicato SELinux, che impedisce di accedere a MongoDB. Con il codice illustrato di seguito, installare gli strumenti per la gestione dei criteri e configurare SELinux in modo tale da consentire a MongoDB di operare sulla porta TCP 27017 predefinita. 

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Avviare il servizio MongoDB come di seguito:

```bash
sudo service mongod start
```

Verificare l'installazione di MongoDB connettendosi tramite il client `mongo` locale:

```bash
mongo
```

A questo punto, testare l'istanza di MongoDB aggiungendo alcuni dati ed eseguendo la ricerca:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Se lo si desidera, configurare MongoDB per l'avvio automatico durante il riavvio del sistema:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Creare un'istanza di MongoDB di base su CentOS usando un modello
Per creare un'istanza di MongoDB di base in una singola VM CentOS, è possibile usare il seguente modello di avvio rapido di Azure in GitHub. Usando l'estensione dello script personalizzata, questo modello consente a Linux di aggiungere un repository `yum` alla VM CentOS appena creata, per poi installare MongoDB.

* [Istanza di MongoDB di base su CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos): https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Per creare questo ambiente, è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure tramite [az login](/cli/azure/#login). Creare prima un gruppo di risorse con [az group create](/cli/azure/group#create). Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `West US`:

```azurecli
az group create --name myResourceGroup --location westus
```

Quindi distribuire il modello MongoDB con [az group deployment create](/cli/azure/group/deployment#create). Definire i nomi di risorsa e le dimensioni, se necessario, ad esempio per `newStorageAccountName`, `virtualNetworkName`, e `vmSize`:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"},
    "virtualNetworkName": {"value": "myVnet"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myVM"},
    "publicIPAddressName": {"value": "myPublicIP"},
    "nicName": {"value": "myNic"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Eseguire l'accesso alla VM usando l'indirizzo DNS pubblico della VM. È possibile visualizzare l'indirizzo DNS pubblico con [az vm show](/cli/azure/vm#show):

```azurecli
az vm show -g myResourceGroup -n myVM -d --query [fqdns] -o tsv
```

Eseguire SSH sulla VM usando il proprio nome utente e l'indirizzo DNS pubblico:

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Verificare l'installazione di MongoDB connettendosi tramite il client `mongo` locale, come di seguito:

```bash
mongo
```

A questo punto, testare l'istanza aggiungendo alcuni dati ed eseguendo la ricerca seguente:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Creare un cluster complesso di MongoDB partizionato in CentOS usando un modello
Per creare un cluster complesso di MongoDB partizionato, è possibile usare il seguente modello di avvio rapido in GitHub. Questo modello segue le [procedure consigliate per cluster MongoDB partizionati](https://docs.mongodb.com/manual/core/sharded-cluster-components/) per garantire ridondanza e disponibilità elevata. Il modello crea due partizioni, con tre nodi in ogni set di repliche. Inoltre, nel server di configurazione viene creato un set di repliche con tre nodi, più due server router `mongos` per garantire coerenza tra le applicazioni delle varie partizioni.

* [Cluster di partizionamento di MongoDB su CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos): https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> La distribuzione di questo cluster complesso di MongoDB partizionato richiede più di 20 core, che in genere è il numero di core predefinito per ogni area di una sottoscrizione. Per aumentare il numero di core, aprire una richiesta di supporto tecnico di Azure.

Per creare questo ambiente, è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure tramite [az login](/cli/azure/#login). Creare prima un gruppo di risorse con [az group create](/cli/azure/group#create). Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `West US`:

```azurecli
az group create --name myResourceGroup --location westus
```

Quindi distribuire il modello MongoDB con [az group deployment create](/cli/azure/group/deployment#create). Definire i nomi di risorsa e le dimensioni, se necessario, ad esempio per `mongoAdminUsername`, `sizeOfDataDiskInGB`, e `configNodeVmSize`:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster --no-wait
```

Questa distribuzione può impiegare più di un'ora per distribuire e configurare tutte le istanze di VM. Il flag `--no-wait` viene usato alla fine del comando precedente per restituire il controllo al prompt dei comandi dopo che la distribuzione del modello è stata accettata dalla piattaforma Azure. È quindi possibile visualizzare lo stato della distribuzione con [az group deployment show](/cli/azure/group/deployment#show). L'esempio seguente visualizza lo stato per la distribuzione `myMongoDBCluster` nel gruppo di risorse `myResourceGroup`:

```azurecli
az group deployment show --resource-group myResourceGroup --name myMongoDBCluster \
    --query [properties.provisioningState] --output tsv
```

## <a name="next-steps"></a>Passaggi successivi
In questi esempi si effettua la connessione all'istanza di MongoDB locale dalla VM. Se si desidera connettersi all'istanza di MongoDB da un'altra VM o un'altra rete, accertarsi di [creare le regole del gruppo di sicurezza di rete](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) appropriate.

Per altre informazioni sulla creazione tramite modelli, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

I modelli di Azure Resource Manager usano l'estensione dello script personalizzata per scaricare ed eseguire script nelle VM. Per altre informazioni, vedere [Using the Azure Custom Script Extension with Linux Virtual Machines](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Usare l'estensione dello script personalizzata di Azure con macchine virtuali Linux).


