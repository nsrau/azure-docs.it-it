---
title: Installare MongoDB in una VM Linux usando l'interfaccia della riga di comando di Azure 1.0 | Documentazione Microsoft
description: Informazioni su come installare e configurare MongoDB su una macchina virtuale Linux in Azure usando il modello di distribuzione di Resource Manager.
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: ff9e23de41245ea21ba6e9c3efe13ca13b0b0ae1
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2017
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm-using-the-azure-cli-10"></a>Come installare e configurare MongoDB in una VM Linux usando l'interfaccia della riga di comando di Azure 1.0
[MongoDB](http://www.mongodb.org) è un diffuso database NoSQL open source a prestazioni elevate. Questo articolo illustra come installare e configurare MongoDB su una VM Linux in Azure usando il modello di distribuzione di Resource Manager. Alcuni esempi illustrano in dettaglio come fare a:

* [Installare e configurare manualmente un'istanza di MongoDB di base](#manually-install-and-configure-mongodb-on-a-vm)
* [Creare un'istanza di MongoDB di base usando un modello di Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Creare un cluster complesso di MongoDB partizionato con set di repliche usando un modello di Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- Interfaccia della riga di comando di Azure 1.0: interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](create-cli-complete-nodejs.md): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Installare e configurare manualmente MongoDB su una VM
MongoDB [fornisce le istruzioni di installazione](https://docs.mongodb.com/manual/administration/install-on-linux/) per i sistemi operativi Linux Red Hat/CentOS, SUSE, Ubuntu e Debian. L'esempio seguente crea una macchina virtuale *CentOS* usando una chiave SSH archiviata in *~/.ssh/id_rsa.pub*. Rispondere ai messaggi per l'inserimento delle informazioni su nome dell'account di archiviazione, nome DNS e credenziali di amministratore:

```azurecli
azure vm quick-create \
    --image-urn CentOS \
    --ssh-publickey-file ~/.ssh/id_rsa.pub 
```

Accedere alla VM usando l'indirizzo IP pubblico visualizzato alla fine del precedente passaggio per la creazione della VM:

```bash
ssh azureuser@40.78.23.145
```

Per aggiungere le origini di installazione di MongoDB, creare un file di archivio **yum** come illustrato di seguito:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.4.repo
```

Aprire il file di archivio di MongoDB da modificare. Aggiungere le righe seguenti:

```sh
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

Installare MongoDB usando **yum** come illustrato di seguito:

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

Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nell'area `eastus`. Immettere valori personalizzati come di seguito:

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [!NOTE]
> L'interfaccia della riga di comando di Azure restituisce un avviso a pochi secondi dalla creazione della distribuzione, ma per completare l'installazione e la configurazione serve qualche minuto. Controllare lo stato della distribuzione con `azure group deployment show myResourceGroup`, immettendo il nome del gruppo di risorse appropriato. Attendere che **ProvisioningState** indichi *Succeeded* prima di provare la connessione SSH alla macchina virtuale.

Al termine della distribuzione, effettuare la connessione SSH alla VM. Ottenere l'indirizzo IP della VM usando il comando `azure vm show` come nell'esempio seguente:

```azurecli
azure vm show --resource-group myResourceGroup --name myLinuxVM
```

Verso la fine dell'output, viene visualizzato l'indirizzo IP pubblico. Effettuare la connessione SSH alla VM con l'indirizzo IP di quest'ultima:

```bash
ssh azureuser@138.91.149.74
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
> La distribuzione di questo cluster complesso di MongoDB partizionato richiede più di 20 CPU virtuali, che in genere è il numero di CPU virtuali predefinito per ogni area di una sottoscrizione. Per aumentare il numero di CPU virtuali, aprire una richiesta di supporto tecnico di Azure.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*. Immettere valori personalizzati come di seguito:

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [!NOTE]
> L'interfaccia della riga di comando di Azure restituisce un avviso a pochi secondi dalla creazione della distribuzione, ma per completare l'installazione e la configurazione può servire più di un'ora. Controllare lo stato della distribuzione con `azure group deployment show myResourceGroup`, adeguando il nome del gruppo di risorse di conseguenza. Attendere che **ProvisioningState** indichi *Succeeded* prima di eseguire la connessione alla macchina virtuale.


## <a name="next-steps"></a>Passaggi successivi
In questi esempi si effettua la connessione all'istanza di MongoDB locale dalla VM. Se si desidera connettersi all'istanza di MongoDB da un'altra VM o un'altra rete, accertarsi di [creare le regole del gruppo di sicurezza di rete](nsg-quickstart.md) appropriate.

Per altre informazioni sulla creazione tramite modelli, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

I modelli di Azure Resource Manager usano l'estensione dello script personalizzata per scaricare ed eseguire script nelle VM. Per altre informazioni, vedere [Using the Azure Custom Script Extension with Linux Virtual Machines](extensions-customscript.md) (Usare l'estensione dello script personalizzata di Azure con macchine virtuali Linux).

