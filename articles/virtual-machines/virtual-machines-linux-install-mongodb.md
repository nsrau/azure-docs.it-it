---
title: Installare MongoDB in una VM Linux in Azure | Documentazione Microsoft
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
ms.date: 12/20/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 370bcf5189c855185f11277518e0cbd5377993ab
ms.openlocfilehash: fd323a828ee879d6093e2473accbea883f861420


---
# <a name="install-and-configure-mongodb-on-a-linux-vm-in-azure"></a>Installare e configurare MongoDB su una VM Linux in Azure
[MongoDB](http://www.mongodb.org) è un diffuso database NoSQL open source a prestazioni elevate. Questo articolo illustra come installare e configurare MongoDB su una VM Linux in Azure usando il modello di distribuzione di Resource Manager. Alcuni esempi illustrano in dettaglio come fare a:

* [Installare e configurare manualmente un'istanza di MongoDB di base](#manually-install-and-configure-mongodb-on-a-vm)
* [Creare un'istanza di MongoDB di base usando un modello di Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Creare un cluster complesso di MongoDB partizionato con set di repliche usando un modello di Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)

## <a name="prerequisites"></a>Prerequisiti
Questo articolo richiede:

* Un account Azure. È possibile [ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Accesso tramite `azure login` per l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md).
* L'interfaccia della riga di comando di Azure *deve essere impostata obbligatoriamente* sulla modalità Azure Resource Manager con `azure config mode arm`.

## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Installare e configurare manualmente MongoDB su una VM
MongoDB [fornisce le istruzioni di installazione](https://docs.mongodb.com/manual/administration/install-on-linux/) per i sistemi operativi Linux Red Hat/CentOS, SUSE, Ubuntu e Debian. L'esempio seguente crea una VM `CentOS` usando la chiave SSH archiviata in `~/.ssh/id_rsa.pub`. Rispondere ai messaggi per l'inserimento delle informazioni su nome dell'account di archiviazione, nome DNS e credenziali di amministratore:

```azurecli
azure vm quick-create --ssh-publickey-file ~/.ssh/id_rsa.pub --image-urn CentOS
```

Accedere alla VM usando l'indirizzo IP pubblico visualizzato alla fine del precedente passaggio per la creazione della VM:

```bash
ssh ops@40.78.23.145
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
Per creare un'istanza di MongoDB di base in una singola VM CentOS, è possibile usare il seguente modello di avvio rapido di Azure in Github. Usando l'estensione dello script personalizzata, questo modello consente a Linux di aggiungere un repository `yum` alla VM CentOS appena creata, per poi installare MongoDB.

* [Istanza di MongoDB di base su CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos): https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nell'area `WestUS`. Immettere valori personalizzati come di seguito:

```azurecli
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [!NOTE]
> L'interfaccia della riga di comando di Azure restituisce un avviso a pochi secondi dalla creazione della distribuzione, ma per completare l'installazione e la configurazione serve qualche minuto. Controllare lo stato della distribuzione con `azure group deployment show myResourceGroup`, immettendo il nome del gruppo di risorse appropriato. Attendere che `ProvisioningState` indichi l'operazione come riuscita, prima di tentare la connessione SSH alla VM.
> 
> 

Al termine della distribuzione, effettuare la connessione SSH alla VM. Ottenere l'indirizzo IP della VM usando il comando `azure vm show` come nell'esempio seguente:

```azurecli
azure vm show --resource-group myResourceGroup --name myLinuxVM
```

Verso la fine dell'output, viene visualizzato il valore `Public IP address`. Effettuare la connessione SSH alla VM con l'indirizzo IP di quest'ultima:

```bash
ssh ops@138.91.149.74
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
Per creare un cluster complesso di MongoDB partizionato, è possibile usare il seguente modello di avvio rapido in Github. Questo modello segue le [procedure consigliate per cluster MongoDB partizionati](https://docs.mongodb.com/manual/core/sharded-cluster-components/) per garantire ridondanza e disponibilità elevata. Il modello crea due partizioni, con tre nodi in ogni set di repliche. Inoltre, nel server di configurazione viene creato un set di repliche con tre nodi, più due server router `mongos` per garantire coerenza tra le applicazioni delle varie partizioni.

* [Cluster di partizionamento di MongoDB su CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos): https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> La distribuzione di questo cluster complesso di MongoDB partizionato richiede più di 20 core, che in genere è il numero di core predefinito per ogni area di una sottoscrizione. Per aumentare il numero di core, aprire una richiesta di supporto tecnico di Azure.
> 
> 

Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nell'area `WestUS`. Immettere valori personalizzati come di seguito:

```azurecli
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [!NOTE]
> L'interfaccia della riga di comando di Azure restituisce un avviso a pochi secondi dalla creazione della distribuzione, ma per completare l'installazione e la configurazione può servire più di un'ora. Controllare lo stato della distribuzione con `azure group deployment show myResourceGroup`, adeguando il nome del gruppo di risorse di conseguenza. Attendere che `ProvisioningState` indichi l'operazione come riuscita, prima di connettersi alle VM.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
In questi esempi si effettua la connessione all'istanza di MongoDB locale dalla VM. Se si desidera connettersi all'istanza di MongoDB da un'altra VM o un'altra rete, accertarsi di [creare le regole del gruppo di sicurezza di rete](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) appropriate.

Per altre informazioni sulla creazione tramite modelli, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

I modelli di Azure Resource Manager usano l'estensione dello script personalizzata per scaricare ed eseguire script nelle VM. Per altre informazioni, vedere [Using the Azure Custom Script Extension with Linux Virtual Machines](virtual-machines-linux-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Usare l'estensione dello script personalizzata di Azure con macchine virtuali Linux).




<!--HONumber=Feb17_HO2-->


