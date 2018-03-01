---
title: Installazione e configurazione di Gestione modelli di Azure Machine Learning | Microsoft Docs
description: Questo documento descrive la procedura e i concetti coinvolti nell'installazione e nella configurazione di Gestione modelli in Azure Machine Learning.
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/6/2017
ms.openlocfilehash: 391c02c5c76a3be3f5338790a81bca0311fb301b
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="model-management-setup"></a>Installazione di Gestione modelli

Questo documento fornisce un'introduzione all'uso di Gestione modelli di Azure Machine Learning per distribuire e gestire i modelli di Machine Learning come servizi Web. 

Usando Gestione modelli di Machine Learning di Azure, è possibile distribuire e gestire in modo efficiente i modelli di Machine Learning compilati tramite alcuni framework, tra cui SparkML, Keras, TensorFlow, Microsoft Cognitive Toolkit o Python. 

Alla fine di questo documento sarà possibile configurare l'ambiente di gestione dei modelli e renderlo pronto per la distribuzione dei modelli di Machine Learning.

## <a name="what-you-need-to-get-started"></a>Elementi necessari per iniziare
Per sfruttare al meglio questa guida, è necessario disporre dell'accesso come collaboratore ad una sottoscrizione di Azure o a un gruppo di risorse in cui è possibile distribuire i modelli.
L'interfaccia della riga di comando preinstallata in Azure Machine Learning Workbench e in [Azure DSVMs](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview) (DSVM di Azure).

## <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando
Per usare le interfacce della riga di comando (CLI) dal Workbench, fare clic su **File** -> **Apri prompt dei comandi**. 

In una macchina virtuale di data science connettere e aprire il prompt dei comandi. Digitare `az ml -h` per visualizzare le opzioni. Per altre informazioni sui comandi, usare il flag help.

In tutti gli altri sistemi è necessario installare l'interfaccia della riga di comando.

### <a name="installing-or-updating-on-windows"></a>Installazione o aggiornamento in Windows

Installare Python da https://www.python.org/. Verificare che si è scelto di installare pip.

Aprire un prompt dei comandi tramite Esegui come amministratore ed eseguire questi comandi:

```cmd
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="installing-or-updating-on-linux"></a>Installazione o aggiornamento su Linux
Eseguire il comando seguente dalla riga di comando e seguire i prompt:

```bash
sudo -i
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="configuring-docker-on-linux"></a>Configurazione di Docker su Linux
Per poter configurare Docker su Linux per l'uso da parte degli utenti non radice, seguire le istruzioni riportate qui: [Post-installation steps for Linux](https://docs.docker.com/engine/installation/linux/linux-postinstall/) (Passaggi di post-installazione per Linux)

>[!NOTE]
> In un DSVM di Linux è possibile eseguire lo script di seguito per configurare un Docker in modo corretto. **Ricordarsi di disconnettersi e accedere di nuovo dopo aver eseguito lo script.**
>```
>sudo /opt/microsoft/azureml/initial_setup.sh
>```

## <a name="deploying-your-model"></a>Distribuzione del modello
Per distribuire i modelli come servizi web, usare l'interfaccia della riga di comando. I servizi web possono essere distribuiti in locale o in un cluster.

Iniziare con una distribuzione locale, verificare che il modello e il codice funzionino, quindi distribuire in un cluster per l'uso della scalabilità di produzione.

Per iniziare è necessario configurare l'ambiente di distribuzione. L'impostazione dell'ambiente è una attività che ha luogo una sola volta. Una volta completata l'installazione è possibile riusare l'ambiente per distribuzioni successive. Per maggiori dettagli, vedere la sezione seguente.

Una volta completata l'installazione dell'ambiente:
- Viene visualizzata la richiesta di accedere ad Azure. Per accedere, usare un Web browser per aprire la pagina https://aka.ms/devicelogin e immettere il codice fornito per l'autenticazione.
- Durante il processo di autenticazione viene richiesta l'autenticazione con un account. Importante: selezionare un account che dispone di una sottoscrizione di Azure valida e di autorizzazioni sufficienti per creare risorse nell'account. Quando il log è completo, vengono presentate le informazioni riguardanti la sottoscrizione e viene chiesto se si desidera continuare con l'account selezionato.

### <a name="environment-setup"></a>Configurazione dell'ambiente
Per avviare il processo di installazione, è necessario registrare alcuni provider di ambiente immettendo il comando seguente:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
```
#### <a name="local-deployment"></a>Distribuzione locale
Per distribuire e testare il servizio web nel computer locale, configurare un ambiente locale tramite il comando seguente. Il nome del gruppo di risorse è opzionale.

```azurecli
az ml env setup -l [Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>La distribuzione del servizio web locale richiede di installare Docker nel computer locale. 
>

Il comando di configurazione dell'ambiente locale crea le seguenti risorse nella sottoscrizione:
- Un gruppo di risorse (se non specificato o se il nome specificato non esiste)
- Un account di archiviazione
- Un registro contenitori di Azure (ACR)
- Un account di Application Insights

Una volta completata con successo l'installazione, impostare l'ambiente da usare con il comando seguente:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Distribuzione cluster
Usare la distribuzione cluster per scenari di produzione su vasta scala. Viene configurato un cluster ACS con Kubernetes come agente di orchestrazione. Il cluster ACS può essere aumentato per gestire una velocità effettiva maggiore per le chiamate al servizio Web.

Per distribuire il servizio web in un ambiente di produzione, prima di tutto configurare l'ambiente tramite il comando seguente:

```azurecli
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. eastus2] [-g [resource group]]
```

Il comando di configurazione dell'ambiente del cluster crea le seguenti risorse nella sottoscrizione:
- Un gruppo di risorse (se non specificato o se il nome specificato non esiste)
- Un account di archiviazione
- Un registro contenitori di Azure (ACR)
- Una distribuzione Kubernetes in un cluster del Servizio contenitore di Azure (ACS)
- Un account di Application Insights

>[!IMPORTANT]
> Per creare correttamente un ambiente cluster, è necessario avere accesso come collaboratore alla sottoscrizione di Azure o al gruppo di risorse.

Il gruppo di risorse, l'account di archiviazione e il record di controllo di accesso vengono creati rapidamente. La distribuzione di ACS può richiedere fino a 20 minuti. 

Per verificare lo stato di un provisioning del cluster in corso, usare il comando seguente:

```azurecli
az ml env show -n [environment name] -g [resource group]
```

Al termine della configurazione è necessario impostare l'ambiente da usare per questa distribuzione. Usare il comando seguente:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> Dopo aver creato l'ambiente, per le distribuzioni successive è sufficiente usare comando di impostazione indicato in precedenza per riusarlo.
>

### <a name="create-a-model-management-account"></a>Creare un account di Gestione modelli
Un account di gestione modelli è obbligatorio per la distribuzione dei modelli. È necessario eseguire questa operazione una volta per ogni sottoscrizione ed è possibile riutilizzare lo stesso account in distribuzioni multiple.

Per creare un nuovo account, usare il comando seguente:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Per usare un account esistente usare il comando seguente:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

### <a name="deploy-your-model"></a>Distribuire il modello
A questo punto si è pronti per distribuire il modello salvato come un servizio Web. 

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```

## <a name="next-steps"></a>Passaggi successivi
Provare uno dei molti esempi nella Raccolta.
