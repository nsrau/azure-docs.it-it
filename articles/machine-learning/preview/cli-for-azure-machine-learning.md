---
title: Installare e usare l'interfaccia della riga di comando di apprendimento automatico per le principali attività di Azure Machine Learning
description: Informazioni su come installare e usare l'interfaccia della riga di comando per le più comuni attività di apprendimento automatico in Azure Machine Learning.
services: machine-learning
author: haining
ms.author: haining
manager: mwinkler
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 03/10/2018
ms.openlocfilehash: f34c247728c854c47f486925d440eee0dc5b1945
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>Installare e usare l'interfaccia della riga di comando di apprendimento automatico per le principali attività in Azure Machine Learning

I servizi di Azure Machine Learning sono una soluzione integrata di data science e analisi avanzata end-to-end. I data scientist professionisti possono usare i servizi di Azure Machine Learning per preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud. 

Azure Machine Learning fornisce un'interfaccia della riga di comando con cui è possibile:
+ Gestire l'area di lavoro e i progetti
+ Configurare le destinazioni di calcolo
+ Eseguire gli esperimenti di training
+ Visualizzare la cronologia e le metriche per le esecuzione passate
+ Distribuire i modelli nell'ambiente di produzione come servizi Web
+ Gestire i modelli e i servizi di produzione

Questo articolo presenta alcuni dei comandi dell'interfaccia della riga di comando più utili. 

![Interfaccia della riga di comando di Azure Machine Learning](media/cli-for-azure-machine-learning/flow.png)

>[!NOTE]
>L'interfaccia della riga di comando disponibile con i servizi di Azure Machine Learning è diversa dall'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest), usata per la gestione delle risorse di Azure.

## <a name="get-and-start-cli"></a>Ottenere e avviare l'interfaccia della riga di comando

Per ottenere questa interfaccia della riga di comando, installare Azure Machine Learning Workbench, scaricabile da qui:
    + Windows - https://aka.ms/azureml-wb-msi 
    + MacOS: https://aka.ms/azureml-wb-dmg 

Per avviare l'interfaccia della riga di comando:
+ In Azure Machine Learning Workbench avviare l'interfaccia della riga di comando dal menu **File -> Apri prompt dei comandi**.

## <a name="get-command-help"></a>Ottenere la guida per i comandi 

È possibile ottenere informazioni aggiuntive sui comandi dell'interfaccia della riga di comando usando `--debug` o `--help` dopo i comandi, ad esempio `az ml <xyz> --debug` dove `<xyz>` è il nome del comando. Ad esempio: 
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Attività comuni dell'interfaccia della riga di comando per Azure Machine Learning 

In questa sezione vengono illustrate le più comuni attività che è possibile eseguire con l'interfaccia della riga di comando, tra cui:
+ [Configurazione delle destinazioni di calcolo](#target)
+ [Invio di processi per l'esecuzione remota](#jobs)
+ [Uso di Jupyter Notebook](#jupyter)
+ [Interazione con le cronologie di esecuzione](#history)
+ [Configurazione dell'ambiente per l'operazionalizzazione](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>Configurare una destinazione di calcolo

È possibile calcolare il modello di apprendimento automatico in destinazioni diverse, tra cui:
+ In modalità locale
+ In una VM di data science (DSVM)
+ In un cluster HDInsight

Per associare una destinazione della VM di data science:
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

Per associare una destinazione di HDInsight:
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

Nella cartella **aml_config** è possibile modificare le dipendenze conda. 

È anche possibile usare PySpark, Python o Python in una DSVM GPU. 

Per definire la modalità operativa di Python:
+ Per Python, aggiungere `Framework:Python` in `<target name>.runconfig` 

+ Per PySpark, aggiungere `Framework:PySpark` in `<target name>.runconfig` 

+ Per Python in una DSVM GPU,
    1. Aggiungere `Framework:Python` in `<target name>.runconfig` 

    1. Aggiungere anche `baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true` in `<target name>.compute`

Per preparare la destinazione di calcolo:
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>Per visualizzare la sottoscrizione:<br/>
>`az account show`<br/>
>
>Per impostare la sottoscrizione:<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>Inviare processi remoti

Per inviare un processo a una destinazione remota:
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>Usare Jupyter Notebook

Per avviare Jupyter Notebook:
```azurecli
az ml notebook start
```

Questo comando avvia Jupyter Notebook in localhost. È possibile lavorare in locale selezionando il kernel Python 3 o lavorare nella VM remota selezionando il kernel `<target name>`.

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>Interagire con la cronologia di esecuzione ed esplorarla

Per elencare la cronologia di esecuzione:
```azurecli
az ml history list -o table
```

Per elencare tutte le esecuzioni completate:
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

Per trovare le esecuzioni con la precisione migliore:
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

È anche possibile scaricare i file generati da ogni esecuzione. 

Per alzare di livello un modello salvato nella cartella outputs:
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

Per scaricare il modello:
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>Configurare l'ambiente per l'operazionalizzazione

Per configurare l'ambiente per l'operazionalizzazione, è necessario creare:

> [!div class="checklist"]
> * Un gruppo di risorse 
> * Un account di archiviazione
> * Un registro contenitori di Azure (ACR)
> * Un account Application Insights
> * Una distribuzione Kubernetes in un cluster del Servizio contenitore di Azure (ACS)


Per configurare una distribuzione locale per il testing in un contenitore Docker:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

Per configurare un cluster del servizio contenitore di Azure con Kubernetes:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

Per monitorare lo stato della distribuzione:
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

Per impostare l'ambiente da usare:
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

Iniziare con uno di questi articoli: 
+ [Installare e iniziare a usare Azure Machine Learning](quickstart-installation.md)
+ [Esercitazione sulla classificazione dei dati Iris: Parte 1](tutorial-classifying-iris-part-1.md)

Approfondire l'argomento con uno di questi articoli:
+ [Comandi dell'interfaccia della riga di comando per la gestione del modelli](model-management-cli-reference.md)