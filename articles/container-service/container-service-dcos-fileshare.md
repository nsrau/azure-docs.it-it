---
title: Condivisione dei file per cluster DC/OS di Azure | Microsoft Docs
description: Creare e montare una condivisione di file per un cluster DC/OS nel servizio contenitore di Azure
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service
keywords: Docker, contenitori, micro-servizi, Mesos, Azure, FileShare, CIFS
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 6d40821327a9df47bb85ea12ecd33e4a0f49e39e
ms.lasthandoff: 03/22/2017


---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>Creare e montare una condivisione di file per un cluster DC/OS
In questo articolo verrà illustrato come creare una condivisione file in Azure e come montarla su ciascun agente e master del cluster DC/OS. La configurazione della condivisione file semplifica la condivisione di file tra i cluster ad esempio configurazione, accesso, log e altro ancora.

Prima di eseguire questi esempi, è necessario avere un cluster DC/OS configurato nel servizio contenitore di Azure. Vedere [Distribuire un cluster del servizio contenitore di Azure](container-service-deployment.md).

## <a name="create-a-file-share-on-microsoft-azure"></a>Creare una condivisione file in Microsoft Azure
### <a name="using-the-portal"></a>Tramite il portale

1. Accedere al portale.
2. Creare un account di archiviazione.
   
  ![Il servizio contenitore di Azure crea l'account di archiviazione](media/container-service-dcos-fileshare/createSA.png)

3. Al termine della creazione, fare clic su **File** nella sezione **Servizi**.
   
  ![Sezione File del servizio contenitore di Azure](media/container-service-dcos-fileshare/filesServices.png)

4. Fare clic su **+ Condivisione file** e immettere un nome per la nuova condivisione (**Quota** non è obbligatorio).
   
  ![+ Condivisione File del servizio contenitore di Azure](media/container-service-dcos-fileshare/newFileShare.png)  

### <a name="using-azure-cli-20"></a>Tramite l'interfaccia della riga di comando di Azure 2.0

Se lo si desidera, [installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli.md).

```azurecli
################# Change these four parameters ##############
DCOS_PERS_STORAGE_ACCOUNT_NAME=anystorageaccountname
DCOS_PERS_RESOURCE_GROUP=AnyResourceGroupName
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=demoshare
#############################################################

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Montare la condivisione nel cluster

Successivamente, è necessario montare la condivisione su ogni macchina virtuale all'interno del cluster usando il protocollo o lo strumento CIFS. A tale scopo, eseguire la riga di comando seguente: `mount -t cifs`.

Ecco un esempio che usa:
* il nome dell'account di archiviazione **`anystorageaccountname`**
* la chiave dell'account fittizio **`P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==`** 
* il punto di montaggio **`/mnt/share/demoshare`**

```bash
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```

Questo comando verrà eseguito in ogni macchina virtuale del cluster (nodi primari e agente). Se si dispone di un numero elevato di agenti, è consigliabile automatizzare questo processo creando degli script.  

### <a name="set-up-scripts"></a>Configurare gli script

1. Innanzitutto, usare SSH nel master (o il primo master) del cluster basato su DC/OS. Ad esempio, `ssh userName@masterFQDN –A –p 22`, dove masterFQDN è il nome di dominio completo della macchina virtuale master.

2. Copiare la chiave privata nella directory usata (~) nel master.

3. Modificare le autorizzazioni su di esso con il comando seguente: `chmod 600 yourPrivateKeyFile`.

4. Importare la chiave privata usando il comando `ssh-add yourPrivateKeyFile`. La prima volta potrebbe essere necessario eseguire `eval ssh-agent -s` se non funziona.

5. Dal master, creare due file, usando l'editor preferito, ad esempio VI, Nano o VIM: 
  
  * uno con lo script da eseguire in ogni macchina virtuale, denominato **cifsMount.sh** 
  * un altro per avviare tutte le connessioni SSH che richiameranno il primo script, denominato **mountShares.sh**


```bash
# cifsMount.sh

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```
  
```bash
# mountShares.sh

# Install jq used for the next command
sudo apt-get install jq

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share on the current vm (master)
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes
  
# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
  do
    ssh `whoami`@$line -o StrictHostKeyChecking=no -i yourPrivateKeyFile < ./cifsMount.sh
    done
```  
> [!IMPORTANT]
> È necessario modificare il comando **"mount"** con le proprie impostazioni, ad esempio il nome dell'account di archiviazione e la password.
>  

La cartella in cui sono stati creati gli script precedenti dovrebbe avere ora 3 file:  

* **cifsMount.sh**
* **mountShares.sh**
* **yourPrivateKeyFile** 

### <a name="run-the-scripts"></a>Esecuzione degli script

Eseguire il file **mountShares.sh** con il comando seguente: `sh mountShares.sh`.

Verrà visualizzata la stampa di risultati nel terminale. Dopo aver completato gli script, è possibile usare la condivisione file nel cluster.

È possibile ottimizzare gli script, ma in questo esempio è semplice e lo scopo dell'articolo è dare informazioni aggiuntive.

> [!NOTE] 
> Questo metodo non è consigliato per scenari che richiedono elevate operazioni di I/O al secondo, ma è molto utile condividere documenti e informazioni all'interno del cluster.
>

## <a name="next-steps"></a>Passaggi successivi
* Sono disponibili altre informazioni sulla [gestione dei contenitori DC/OS](container-service-mesos-marathon-ui.md).
* Gestione dei contenitori DC/OS tramite l'[API REST Marathon](container-service-mesos-marathon-rest.md).
