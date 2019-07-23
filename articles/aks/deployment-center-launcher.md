---
title: Usare il Centro distribuzione nel Servizio app di Azure
description: Il Centro distribuzione in Azure DevOps semplifica la configurazione di una pipeline Azure DevOps affidabile per l'applicazione.
ms.author: puagarw
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
monikerRange: vsts
ms.openlocfilehash: 8d1e467906b74c97c8b4f4e5c14af0814dd098f7
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67861515"
---
# <a name="deployment-center-launcher"></a>Avvio con il Centro distribuzione

Il Centro distribuzione in Azure DevOps semplifica la configurazione di una pipeline DevOps affidabile per l'applicazione. Per impostazione predefinita, configura una pipeline DevOps per distribuire gli aggiornamenti dell'applicazione nel cluster Kubernetes. È possibile estendere la pipeline DevOps configurata predefinita e aggiungere le funzionalità di DevOps più avanzate, ovvero approvazioni prima della distribuzione, provisioning di altre risorse di Azure, esecuzione di script, aggiornamento dell'applicazione o persino esecuzione di ulteriori test di convalida.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare una pipeline DevOps per distribuire gli aggiornamenti dell'applicazione nel cluster Kubernetes
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Pulire le risorse

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. È possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

* Cluster del servizio Azure Kubernetes

## <a name="create-aks-cluster"></a>Creare un cluster del servizio Azure Container

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare il pulsante [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) nel menu nell'angolo in alto a destra del portale di Azure.

1. Per creare il cluster del servizio Azure Kubernetes, eseguire i comandi seguenti.

    ```cmd
    # The below command creates Resource Group in the south india location

    az group create --name azooaks --location southindia

    # The below command creates a cluster named azookubectl with one node. 

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-k8s-cluster"></a>Distribuire gli aggiornamenti dell'applicazione nel cluster Kubernetes

1. Passare al gruppo di risorse creato in precedenza.

1. Selezionare il cluster del servizio Azure Kubernetes e fare clic sulle impostazioni nel pannello sinistro in **Centro distribuzione (anteprima)** . Fare clic su **Inizia**.

   ![Scheda Impostazioni](media/deployment-center-launcher/settings.png)

1. Scegliere il percorso del codice e fare clic su **Avanti**. Al momento, i repository supportati sono **[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** e **GitHub**. Seguire i passaggi seguenti in base al repository selezionato.

    Azure Repos è un set di strumenti di controllo della versione che è possibile usare per gestire il codice. Indipendentemente dalle dimensioni del progetto software, è consigliabile usare il controllo della versione appena possibile.

    - **Azure Repos**: scegliere un repository dal progetto e dall'organizzazione esistenti.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: autorizzare e selezionare il repository per l'account GitHub.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. Verrà analizzato il repository per rilevare il Dockerfile. Se si vuole aggiornarlo, è possibile modificare il numero di porta identificato.

    ![Impostazioni dell'applicazione](media/deployment-center-launcher/application-settings.png)

    Se il repository non contiene il Dockerfile, il sistema visualizzerà un messaggio per eseguire il commit di un file. 

    ![Dockerfile](media/deployment-center-launcher/dockerfile.png)

1. Selezionare o creare un Registro Container esistente e fare clic su **Fine**. La pipeline verrà creata automaticamente e accoderà una compilazione in [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops).

    Azure Pipelines è un servizio cloud che è possibile usare per compilare e testare automaticamente il progetto di codice e renderlo disponibile ad altri utenti. Azure Pipelines combina integrazione continua (CI) e recapito continuo (CD) per testare e compilare in modo costante e coerente il codice e distribuirlo in qualsiasi destinazione.

    ![Registro Container](media/deployment-center-launcher/container-registry.png)

1. Fare clic sul collegamento per visualizzare la pipeline in corso.

1. I log riusciti verranno visualizzati come illustrato al termine della distribuzione.

    ![Log](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Esaminare la pipeline di integrazione continua

Il Centro distribuzione configura automaticamente la pipeline CI/CD dell'organizzazione di Azure DevOps. La pipeline può essere esplorata e personalizzata. 

1. Passare al dashboard del Centro distribuzione.  

1. Fare clic sul numero di build nell'elenco dei log riusciti per visualizzare la pipeline di compilazione per il progetto. 

1. Fare clic sui puntini di sospensione (...) nell'angolo in alto a destra. Viene visualizzato un menu con opzioni per eseguire diverse attività, ad esempio accodare una nuova compilazione, sospendere una compilazione e modificare la pipeline di compilazione. Scegliere l'opzione **Modifica pipeline**. 

1. In questo riquadro è possibile esaminare le diverse attività disponibili per la pipeline di compilazione. La compilazione esegue diverse attività, ad esempio la raccolta delle origini dal repository GIT, la creazione di un'immagine, l'esecuzione del push di un'immagine nel registro contenitori e la pubblicazione degli output usati per le distribuzioni.

1. Nella parte superiore della pipeline di compilazione selezionare il nome della pipeline.

1. Modificare il nome della pipeline di compilazione scegliendo un testo più descrittivo, selezionare **Salva e accoda** e quindi **Salva**.

1. Sotto la pipeline di compilazione selezionare **Cronologia**. Questo riquadro mostra un audit trail delle modifiche recenti apportate alla compilazione. Azure DevOps monitora tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

1. Scegliere **Trigger**. Facoltativamente, è possibile includere o escludere rami dal processo di integrazione continua.

1. Scegliere **Conservazione**. A seconda dello scenario, è possibile specificare i criteri per conservare o rimuovere un determinato numero di compilazioni.

## <a name="examine-the-cd-pipeline"></a>Esaminare la pipeline di distribuzione continua

Il Centro distribuzione crea e configura automaticamente i passaggi necessari per la distribuzione dall'organizzazione di Azure DevOps alla sottoscrizione di Azure. Questi passaggi includono la configurazione di una connessione al servizio di Azure per eseguire l'autenticazione della sottoscrizione di Azure con Azure DevOps. L'automazione crea anche una pipeline di versione che fornisce la distribuzione continua in Azure.

1. Scegliere **Pipelines** e quindi **Versioni**.

1. Per modificare la pipeline di versione, fare clic su **Modifica**.

1. In **Artefatti** selezionare **Elimina**. Nei passaggi precedenti la pipeline di costruzione esaminata produce l'output usato per l'artefatto. 

1. A destra dell'icona **Elimina** selezionare il trigger **Distribuzione continua**. Questa pipeline di versione ha un trigger di distribuzione continua abilitato, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. Facoltativamente, è possibile disabilitare il trigger per richiedere l'esecuzione manuale delle distribuzioni.

1. Per esaminare tutte le attività della pipeline, fare clic su **Attività**. La versione imposta l'ambiente Tiller, configura imagePullSecrets, installa gli strumenti Helm e distribuisce i grafici Helm nel cluster Kubernetes.

1. Per visualizzare la cronologia delle versioni, fare clic su **Visualizza versioni**. 

1. Per visualizzare il riepilogo, fare clic su **Versione**. Fare clic su una delle fasi per esplorare più menu, ad esempio un riepilogo della versione, gli elementi di lavoro associati e i test. 

1. Selezionare **Commit**. Questa visualizzazione mostra i commit del codice correlati a questa distribuzione. È possibile confrontare le versioni per visualizzare le differenze di commit tra le distribuzioni.

1. Selezionare **Log**. I log contengono informazioni utili sulla distribuzione. È possibile visualizzarli durante e dopo le distribuzioni.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare le risorse correlate create in precedenza. Usare la funzionalità Elimina nel dashboard di DevOps Projects.

## <a name="next-steps"></a>Passaggi successivi

Facoltativamente, è possibile modificare queste pipeline di compilazione e di versione in base alle esigenze del team. È anche possibile usare questo modello di CI/CD come modello per altre pipeline. Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare una pipeline DevOps per distribuire gli aggiornamenti dell'applicazione nel cluster Kubernetes
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Pulire le risorse
