---
title: Installare MongoDB in una macchina virtuale Linux con l'interfaccia della riga di comando di Azure | Documentazione Microsoft
description: Informazioni su come installare e configurare MongoDB in una macchina virtuale Linux usando l'interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: b21b33a265d499136dbe3e72538923d8295e9876
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Come installare e configurare MongoDB in una macchina virtuale Linux
[MongoDB](http://www.mongodb.org) è un diffuso database NoSQL open source a prestazioni elevate. Questo articolo illustra come installare e configurare MongoDB in una VM Linux usando l'interfaccia della riga di comando di Azure 2.0. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](install-mongodb-nodejs.md). Alcuni esempi illustrano in dettaglio come fare a:

* [Installare e configurare manualmente un'istanza di MongoDB di base](#manually-install-and-configure-mongodb-on-a-vm)
* [Creare un'istanza di MongoDB di base usando un modello di Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Creare un cluster complesso di MongoDB partizionato con set di repliche usando un modello di Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Installare e configurare manualmente MongoDB su una VM
MongoDB [fornisce le istruzioni di installazione](https://docs.mongodb.com/manual/administration/install-on-linux/) per i sistemi operativi Linux Red Hat/CentOS, SUSE, Ubuntu e Debian. L'esempio seguente crea una macchina virtuale *CentOS*. Per creare questo ambiente, è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure tramite [az login](/cli/azure/reference-index#az_login).

Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Creare una VM con il comando [az vm create](/cli/azure/vm#az_vm_create). L'esempio seguente crea una macchina virtuale denominata *myVM* con un utente chiamato *azureuser* usando l'autenticazione con chiave pubblica SSH

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Eseguire SSH sulla macchina virtuale usando il proprio nome utente e l'indirizzo `publicIpAddress` elencato nell'output ottenuto nel passaggio precedente:

```bash
ssh azureuser@<publicIpAddress>
```

Per aggiungere le origini di installazione di MongoDB, creare un file di archivio **yum** come illustrato di seguito:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Aprire il file del repository di MongoDB per la modifica, ad esempio con `vi` o `nano`. Aggiungere le righe seguenti:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Installare MongoDB usando **yum** come illustrato di seguito:

```bash
sudo yum install -y mongodb-org
```

Per impostazione predefinita, alle immagini CentOS è applicato SELinux, che impedisce di accedere a MongoDB. Installare gli strumenti per la gestione dei criteri e configurare SELinux in modo tale da consentire a MongoDB di operare sulla porta TCP 27017 predefinita, come illustrato di seguito:

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
Per creare un'istanza di MongoDB di base in una singola VM CentOS, è possibile usare il seguente modello di avvio rapido di Azure in GitHub. Usando l'estensione dello script personalizzata, questo modello consente a Linux di aggiungere un archivio **yum** alla macchina virtuale CentOS appena creata, per poi installare MongoDB.

* [Istanza di MongoDB di base su CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos): https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Per creare questo ambiente, è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure tramite [az login](/cli/azure/reference-index#az_login). Creare prima un gruppo di risorse con [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Quindi distribuire il modello MongoDB con [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create). Se richiesto, immettere i propri valori univoci per *newStorageAccountName*, *dnsNameForPublicIP* e nome utente e password dell'amministratore:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Eseguire l'accesso alla VM usando l'indirizzo DNS pubblico della VM. È possibile visualizzare l'indirizzo DNS pubblico con [az vm show](/cli/azure/vm#az_vm_show):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

Eseguire SSH sulla VM usando il proprio nome utente e l'indirizzo DNS pubblico:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
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
Per creare un cluster complesso di MongoDB partizionato, è possibile usare il seguente modello di avvio rapido in GitHub. Questo modello segue le [procedure consigliate per cluster MongoDB partizionati](https://docs.mongodb.com/manual/core/sharded-cluster-components/) per garantire ridondanza e disponibilità elevata. Il modello crea due partizioni, con tre nodi in ogni set di repliche. Inoltre, nel server di configurazione viene creato un set di repliche con tre nodi, più due server router **mongos** per garantire coerenza tra le applicazioni delle varie partizioni.

* [Cluster di partizionamento di MongoDB su CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos): https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> La distribuzione di questo cluster complesso di MongoDB partizionato richiede più di 20 core, che in genere è il numero di core predefinito per ogni area di una sottoscrizione. Per aumentare il numero di core, aprire una richiesta di supporto tecnico di Azure.

Per creare questo ambiente, è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure tramite [az login](/cli/azure/reference-index#az_login). Creare prima un gruppo di risorse con [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Quindi distribuire il modello MongoDB con [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create). Definire i nomi e le dimensioni delle risorse desiderati dove necessario per *mongoAdminUsername*, *sizeOfDataDiskInGB* e *configNodeVmSize*:

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
  --name myMongoDBCluster \
  --no-wait
```

Questa distribuzione può impiegare più di un'ora per distribuire e configurare tutte le istanze di VM. Il flag `--no-wait` viene usato alla fine del comando precedente per restituire il controllo al prompt dei comandi dopo che la distribuzione del modello è stata accettata dalla piattaforma Azure. È quindi possibile visualizzare lo stato della distribuzione con [az group deployment show](/cli/azure/group/deployment#az_group_deployment_show). L'esempio seguente visualizza lo stato per la distribuzione *myMongoDBCluster* nel gruppo di risorse *myResourceGroup*:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Passaggi successivi
In questi esempi si effettua la connessione all'istanza di MongoDB locale dalla VM. Se si desidera connettersi all'istanza di MongoDB da un'altra VM o un'altra rete, accertarsi di [creare le regole del gruppo di sicurezza di rete](nsg-quickstart.md) appropriate.

Questi esempi consentono di distribuire l'ambiente MongoDB di base per scopi di sviluppo. Applicare le opzioni di configurazione della sicurezza necessarie per l'ambiente in uso. Per altre informazioni, vedere i [documenti sulla sicurezza di MongoDB](https://docs.mongodb.com/manual/security/).

Per altre informazioni sulla creazione tramite modelli, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

I modelli di Azure Resource Manager usano l'estensione dello script personalizzata per scaricare ed eseguire script nelle VM. Per altre informazioni, vedere [Using the Azure Custom Script Extension with Linux Virtual Machines](extensions-customscript.md) (Usare l'estensione dello script personalizzata di Azure con macchine virtuali Linux).

