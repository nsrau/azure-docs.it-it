---
title: Come creare DSVM e HDI come destinazioni di calcolo per Azure ML
description: Creare cluster DSVM e Spark HDI come destinazioni di calcolo per esperimenti di Machine Learning di Azure.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: 049478baa108b94c042575a5e29bdc513e527542
ms.contentlocale: it-it
ms.lasthandoff: 09/28/2017

---

# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>Creare cluster DSVM e HDI Spark come destinazioni di calcolo

È possibile applicare la scalabilità verticale o orizzontale all'esperimento di machine learning tramite l'aggiunta di destinazioni di calcolo aggiuntive, ad esempio DSVM (macchina virtuale di data science) basata su Ubuntu e Apache Spark per il cluster HDInsight di Azure. Questo articolo illustra i passaggi della creazione di queste destinazioni di calcolo in Azure. Per ulteriori informazioni sulle destinazioni di calcolo di Azure ML, fare riferimento a [Panoramica del servizio di esecuzione dell’esperimento di Azure Machine Learning](experiment-execution-configuration.md).

>[!NOTE]
>Prima di procedere, è necessario assicurarsi di avere le autorizzazioni appropriate per creare risorse, ad esempio cluster HDI e macchina virtuale in Azure. Entrambe queste risorse possono anche impiegare molti core di calcolo a seconda della configurazione. Verificare che la sottoscrizione abbia una capacità sufficiente per i core CPU virtuali. È possibile sempre contattare il supporto di Azure per aumentare il numero massimo di core consentito nella sottoscrizione.

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Creare un DSVM Ubuntu nel portale di Azure

È possibile creare un DSVM dal portale di Azure. 

1. Accedere al portale di Azure dall’indirizzo https://portal.azure.com
2. Fare clic sul collegamento **+NUOVO** e cercare "macchina virtuale di data science per Linux".
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. Scegliere **Macchina virtuale di data science per Linux (Ubuntu)** dall'elenco e seguire le istruzioni sulla schermata per creare il DSVM.

>[!IMPORTANT]
>Assicurarsi di scegliere **Password** come _tipo di autenticazione_.

![Usare pwd](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>Creare un DSVM Ubuntu mediante azure-cli

È inoltre possibile utilizzare un modello di gestione risorse di Azure per distribuire un DSVM.

>[!NOTE]
>Si presuppone che tutti i comandi seguenti vengano emessi dalla cartella radice di un progetto ML di Azure.

Creare innanzitutto un file `mydsvm.json` utilizzando l’editor di testo preferito nella cartella `docs`. (Se non si dispone di una cartella `docs` nella cartella radice del progetto, crearne una). Si usa questo file per configurare alcuni parametri di base per il modello di gestione risorse di Azure. 

Copiare e incollare il seguente frammento di codice in JSON nel file `mydsvm.json`, quindi immettere i valori appropriati:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "adminUsername": { "value" : "<admin username>"},
     "adminPassword": { "value" : "<admin password>"},
     "vmName": { "value" : "<vm name>"},
     "vmSize": { "value" : "<vm size>"}
  }
}
```

Per il campo _vmSize_ è possibile usare qualsiasi dimensione di macchina virtuale supportata elencata nel [modello di gestione risorse di Azure DSVM Ubuntu](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json). Si consiglia di usare una delle dimensioni seguenti come destinazione di calcolo per Azure ML. 

- Standard_DS2_v2 
- Standard_DS3_v2 
- Standard_DS4_v2 
- Standard_DS12_v2 
- Standard_DS13_v2 
- Standard_DS14_v2 
- Standard_NC6 
- Standard_NC12 
- Standard_NC24 
 
>[!TIP]
> Le dimensioni di macchina virtuale che iniziano con "NC" sono dotate di GPU.

Altre informazioni su queste [dimensioni delle macchine virtuali Linux in Azure](../../virtual-machines/linux/sizes.md) e i relativi [informazioni sui prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Avviare la finestra dell’interfaccia della riga di comando dall'app Azure ML Workbench facendo clic su **File** --> **Apri prompt dei comandi** o sulla voce di menu **Apri PowerShell**. 

>[!NOTE]
>È anche possibile farlo in qualsiasi ambiente della riga di comando in cui occorre avere az-cli installato.

Nella finestra della riga di comando, immettere i comandi di seguito:

```azurecli
# first make sure you have a valid Azure authentication token
$ az account get-access-token

# if you don't have a valid token, please log in to Azure first. 
# if you already do, you can skip this step.
$ az login

# list all subscriptions you have access to
$ az account list -o table

# make sure you set the subscription you want to use to create DSVM as the current subscription
$ az account set -s <subscription name or Id>

# it is always a good idea to create a resource group for the VM and associated resources to live in.
# you can use any Azure region, but it is best to create them in the region where your Azure ML Experimentation account is, e.g. eastus2, westcentralus or australiaeast.
# also, only certain Azure regions has GPU-equipped VMs available.
$ az group create -n <resource group name> -l <azure region>

# now let's create the DSVM based on the JSON configuration file you created earlier.
# note we assume the mydsvm.json config file is placed in the "docs" sub-folder.
$ az group deployment create -g <resource group name> --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/azuredeploy.json --parameters @docs/mydsvm.json

# find the FQDN (fully qualified domain name) of the VM just created. 
# you can also use IP address from the next command if FQDN is not set.
$ az vm show -g <resource group name> -n <vm name> --query "fqdns"

# find the IP address of the VM just created
$ az vm show -g <resource group name> -n <vm name> --query "publicIps"
```
## <a name="attach-a-dsvm-compute-target"></a>Collegare una destinazione di calcolo DSVM
Una volta creato il DSVM, è possibile collegarlo al progetto di Azure ML.

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> --type remotedocker

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
A questo punto si è pronti per eseguire gli esperimenti in questa DSVM.

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>Deallocare una DSVM e riavviarla in seguito
Al termine delle attività di calcolo di Azure ML, è possibile deallocare la DSVM. Questa azione arresta la macchina virtuale, rilascia le risorse di calcolo, ma conserva i dischi virtuali. Non viene applicato alcun addebito per il costo di calcolo se la macchina virtuale è stata deallocata.

Per deallocare una macchina virtuale:

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

Per ripristinare la macchina virtuale, utilizzare il comando `az ml start`:

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>Espandere il disco del sistema operativo DSVM
La macchina virtuale Linux di Azure viene in genere fornita con un disco del sistema operativo da 30 GB. Quando usato come destinazione di calcolo per Machine Learning di Azure, è possibile che venga mangiato rapidamente dal motore di Docker che sposta in modo forzato le immagini Docker e crea strati di conda sopra di esso. È consigliabile espandere il disco del sistema operativo a una dimensione maggiore (ad esempio 200 GB) per evitare l'errore "disco pieno" mentre è ancora in corso un'esecuzione. Fare riferimento a [Procedura per espandere i dischi rigidi virtuali in una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](../../virtual-machines/linux/expand-disks.md) per imparare facilmente la procedura in azure-cli. 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Creare un cluster Apache Spark per Azure HDInsight nel portale di Azure

Per eseguire i processi di scalabilità orizzontale di Spark, è necessario creare un cluster Apache Spark per Azure HDInsight nel portale di Azure.

1. Accedere al portale di Azure dall’indirizzo https://portal.azure.com
2. Fare clic sul collegamento **+NUOVO** e cercare "HDInsight".

    ![Trovare hdi](media/how-to-create-dsvm-hdi/hdi.png)
    
3. Scegliere **HDInsight** dall'elenco, quindi fare clic sul pulsante **Crea**.
4. Nella schermata di configurazione **Basic**, impostazioni **Tipo cluster**, selezionare **Spark** come _tipo di cluster_, **Linux** come _sistema operativo_ e **Spark 2.1.0 (HDI 3.6)** come versione.

    ![Configurare hdi](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >Si può notare che nella schermata sopra riportata il cluster dispone di un campo _nome utente di accesso cluster_ e un campo _nome utente Secure Shell (SSH)_. Si tratta di due diverse identità utente, anche se per motivi di praticità è possibile specificare la stessa password per entrambi gli accessi. Il _nome utente dell'accesso cluster_ viene utilizzato per accedere all’interfaccia utente Web di gestione del cluster HDI. Il _nome utente dell'accesso SSH_ viene utilizzato per accedere al nodo head del cluster, si tratta di informazioni necessarie ai fini dell’invio dei processi Spark per Azure ML.

5. Scegliere le dimensioni del cluster e le dimensioni del nodo necessarie e completare la procedura guidata. Il completamento del provisioning del cluster può richiedere fino a 30 minuti. 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>Allegare una destinazione di calcolo cluster Spark HDI

Una volta creato il cluster HDI Spark, è possibile collegarlo al progetto di Azure ML.

```azurecli
# attach the HDI compute target
$ az ml computetarget attach --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> --type cluster

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
A questo punto si è pronti per eseguire gli esperimenti in questo cluster Spark.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:
- [Panoramica del servizio di esecuzione dell'esperimento di Azure Machine Learning](experiment-execution-configuration.md)
- [File di configurazione di esecuzione di Azure Machine Learning Workbench](experiment-execution-configuration-reference.md)
- [Apache Spark per il cluster Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [Macchina virtuale di data science](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)

