---
title: Installazione e configurazione di Gestione modelli di Azure Machine Learning | Microsoft Docs
description: Questo documento descrive la procedura e i concetti coinvolti nell'installazione e nella configurazione di Gestione modelli in Azure Machine Learning.
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/29/2017
ms.openlocfilehash: c89596a6d721c4cba899b8a6e2859ee36cba7b80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="model-management-setup"></a>Installazione di Gestione modelli

## <a name="overview"></a>Panoramica
Questo documento fornisce un'introduzione all'uso di Gestione modelli di Azure Machine Learning per distribuire e gestire i modelli di Machine Learning come servizi Web. 

Usando Gestione modelli di Machine Learning di Azure, è possibile distribuire e gestire in modo efficiente i modelli di Machine Learning compilati tramite alcuni framework, tra cui SparkML, Keras, TensorFlow, Microsoft Cognitive Toolkit o Python. 

Alla fine di questo documento sarà possibile configurare l'ambiente di gestione dei modelli e renderlo pronto per la distribuzione dei modelli di Machine Learning.

## <a name="what-you-need-to-get-started"></a>Elementi necessari per iniziare
Per sfruttare al meglio questa guida, è necessario disporre dell'accesso come proprietario ad una sottoscrizione di Azure su cui è possibile distribuire i modelli.
L'interfaccia della riga di comando preinstallata in Azure Machine Learning Workbench e in [Azure DSVMs](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-virtual-machine-overview) (DSVM di Azure).

## <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando
Per usare le interfacce della riga di comando (CLI) dal Workbench, fare clic su **File** -] **Open CommandLine Interface** (Apri interfaccia della riga di comando). 

In una macchina virtuale data science (DSVM, Data Science Virtual Machine) collegarsi e aprire il prompt dei comandi. Digitare `az ml -h` per visualizzare le opzioni. Per altre informazioni sui comandi, usare il flag help.

In tutti gli altri sistemi è necessario installare l'interfaccia della riga di comando.

### <a name="installing-or-updating-on-windows"></a>Installazione o aggiornamento in Windows

Installare Python da https://www.python.org/. Verificare che si è scelto di installare pip.

Aprire un prompt dei comandi tramite Esegui come amministratore ed eseguire questi comandi:

```cmd
pip install azure-cli
pip install azure-cli-ml
```
 
>[!NOTE]
>Se si dispone di una versione precedente, in primo luogo disinstallarla usando il comando seguente:
>

```cmd
pip uninstall azure-cli-ml
```

### <a name="installing-or-updating-on-linux"></a>Installazione o aggiornamento su Linux
Eseguire questo comando dalla riga di comando e seguire i prompt:

```bash
sudo -i
pip install azure-cli
pip install azure-cli-ml
```

Al termine dell'installazione eseguire questo comando:

```bash
sudo /opt/microsoft/azureml/initial_setup.sh
```

>[!NOTE]
>Disconnettersi e accedere di nuovo alla sessione SSH per rendere effettive le modifiche.
>È possibile usare i comandi precedenti per aggiornare una versione precedente delle interfacce della riga di comando nel DSVM.
>

## <a name="deploying-your-model"></a>Distribuzione del modello
Per distribuire i modelli come servizi Web usare le interfacce della riga di comando. I servizi Web possono essere distribuiti localmente o in un cluster.

Iniziare con una distribuzione locale, verificare che il modello e il codice funzionino, quindi distribuire in un cluster per l'uso della scalabilità di produzione.

Per iniziare è necessario configurare l'ambiente di distribuzione. L'impostazione dell'ambiente è una attività che ha luogo una sola volta. Una volta completata l'installazione è possibile riusare l'ambiente per distribuzioni successive. Per maggiori dettagli, vedere la sezione seguente.

Una volta completata l'installazione dell'ambiente:
- Viene visualizzata la richiesta di accedere ad Azure. Per accedere, usare un Web browser per aprire la pagina https://aka.ms/devicelogin e immettere il codice fornito per l'autenticazione.
- Durante il processo di autenticazione viene richiesta l'autenticazione con un account. Importante: selezionare un account che dispone di una sottoscrizione di Azure valida e di autorizzazioni sufficienti per creare risorse nell'account. -Quando il log è completo, vengono presentate le informazioni riguardanti la sottoscrizione e viene chiesto se si desidera continuare con l'account selezionato.

### <a name="environment-setup"></a>Configurazione dell'ambiente
Per avviare il processo di installazione, è necessario registrare il provider dell'ambiente immettendo il comando seguente:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
```

#### <a name="local-deployment"></a>Distribuzione locale
Per distribuire e testare il servizio Web nel computer locale impostare un ambiente locale usando il comando seguente:

```azurecli
az ml env setup -l [location of Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>La distribuzione del servizio Web locale richiede di installare il Docker nel computer locale. 
>

Il comando di configurazione dell'ambiente locale crea le risorse seguenti nella sottoscrizione:
- Un gruppo di risorse (se non fornito)
- Un account di archiviazione
- Un registro contenitori di Azure (ACR)
- Application Insights

Una volta completata correttamente l'installazione impostare l'ambiente da usare con il comando seguente:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Distribuzione cluster
Usare la distribuzione cluster per scenari di produzione su vasta scala. Viene configurato un cluster ACS con Kubernetes come agente di orchestrazione. Il cluster ACS può essere aumentato per gestire una velocità effettiva maggiore per le chiamate al servizio Web.

Per distribuire il servizio web in un ambiente di produzione, prima di tutto configurare l'ambiente tramite il comando seguente:

```azurecli
az ml env setup -c --name [your environment name] --location [Azure region e.g. eastus2] [-g [resource group]]
```

Il comando di configurazione dell'ambiente del cluster crea le risorse seguenti nella sottoscrizione:
- Un gruppo di risorse (se non fornito)
- Un account di archiviazione
- Un registro contenitori di Azure (ACR)
- Una distribuzione Kubernetes in un cluster del Servizio contenitore di Azure (ACS)
- Application Insights

Il gruppo di risorse, l'account di archiviazione e il record di controllo di accesso vengono creati rapidamente. La distribuzione ACS può richiedere fino a 20 minuti. 

Al termine della configurazione è necessario impostare l'ambiente da usare per questa distribuzione. Usare il comando seguente:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> Dopo aver creato l'ambiente, per le distribuzioni successive è sufficiente usare comando di impostazione indicato in precedenza per riusarlo.
>

>[!NOTE] 
>Per creare un endpoint HTTPS, specificare un certificato SSL quando si crea un cluster usando le opzioni --cert-name e --cert-pem in az ml env setup. Il cluster verrà impostato per elaborare le richieste su HTTPS, con la protezione tramite il certificato fornito. Al termine della configurazione, creare un record DNS CNAME che punta al nome di dominio completo (FQDN) del cluster.

### <a name="create-an-account"></a>Creare un account
È necessario un account per la distribuzione dei modelli. È necessario eseguire questa operazione una volta per ogni account ed è possibile riusare lo stesso account in distribuzioni multiple.

Per creare un nuovo account usare il comando seguente:

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

### <a name="next-steps"></a>Passaggi successivi
Provare uno dei molti esempi nella Raccolta.
