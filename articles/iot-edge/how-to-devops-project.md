---
title: Pipeline CI/CD con Azure DevOps Projects - Azure IoT Edge | Microsoft Docs
description: Azure DevOps Projects permette di iniziare a usare Azure velocemente. Con pochi rapidi passaggi, è possibile avviare un'app in un servizio di Azure IoT Edge a scelta.
author: shizn
manager: ''
ms.author: xshi
ms.date: 12/04/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: ebb7e515f9d9205f364d50b3d686c68a2988f86a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074215"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Creare una pipeline CI/CD per IoT Edge con Azure DevOps Projects (Anteprima)

Configurare integrazione continua (CI, Continuous Integration) e recapito continuo (CD, Continuous Delivery) per un'applicazione IoT Edge con DevOps Projects. DevOps Projects semplifica la configurazione iniziale di una pipeline di compilazione e di versione in Azure Pipelines.

Se non si ha una sottoscrizione di Azure attiva, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

DevOps Projects crea una pipeline CI/CD in Azure DevOps. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. DevOps Projects crea anche risorse di Azure nella sottoscrizione di Azure di propria scelta.

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com).

1. Nel riquadro a sinistra, selezionare l'icona **Crea una risorsa** sulla barra di spostamento sinistra e quindi cercare **DevOps Projects**.  

1.  Selezionare **Create**.

## <a name="select-a-sample-application-and-azure-service"></a>Selezionare un'applicazione di esempio e un servizio di Azure

1. I moduli Azure IoT Edge possono essere scritti in [ C# ](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) e [Java](tutorial-java-module.md). Selezionare il linguaggio di programmazione preferito per avviare una nuova applicazione. Di conseguenza, è possibile selezionare **.NET**, **Node.js**, **Python**, **C**, o **Java**, quindi fare clic su **Avanti**.

    ![Selezionare un linguaggio di programmazione per creare una nuova applicazione](./media/how-to-devops-project/select-language.png)

2. Selezionare **IoT semplice (anteprima)**, quindi fare clic su **Avanti**.

    ![Selezionare il framework IoT semplice](media/how-to-devops-project/select-iot.png)

3. Selezionare **IoT Edge** e quindi fare clic su **Avanti**.

    ![Selezionare servizio IoT Edge](media/how-to-devops-project/select-iot-edge.png)

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurare Azure DevOps e una sottoscrizione di Azure

1. Creare una nuova organizzazione gratuita di Azure DevOps Services o scegliere un'organizzazione esistente.

    a. Scegliere un nome per il progetto. 

    b. Selezionare la sottoscrizione di Azure e la posizione, scegliere un nome per l'applicazione, quindi selezionare **Fine**.  

    ![Non è possibile creare l'applicazione](media/how-to-devops-project/select-devops.png)

1. Dopo pochi minuti, il dashboard di DevOps Projects viene visualizzato nel portale di Azure. Viene configurata un'applicazione di esempio in un repository nell'organizzazione Azure DevOps, viene eseguita una compilazione e l'applicazione viene distribuita in Azure. Questo dashboard fornisce visibilità su repository di codice, pipeline CI/CD e applicazione in Azure.

    ![Visualizza applicazione nel portale di DevOps](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Eseguire il commit delle modifiche al codice e la pipeline di CI/CD

DevOps Projects ha creato un repository Git in Azure Repos o GitHub. Per visualizzare il repository e apportare modifiche al codice nell'applicazione, seguire la procedura seguente:

1. A sinistra del dashboard di DevOps Projects selezionare il collegamento per il ramo **master**.  
Questo collegamento apre una visualizzazione del repository Git appena creato.

1. Per visualizzare l'URL clone del repository, selezionare **Clona** in alto a destra nel browser. È possibile clonare il repository Git in Visual Studio Code o in altri strumenti preferiti. Nei passaggi successivi, è possibile usare il browser Web per apportare modifiche al codice ed eseguirne il commit direttamente nel ramo master.

    ![Clona repository Git](media/how-to-devops-project/clone.png)

1. A sinistra del browser, passare al file **modules/FilterModule/module.json**.

1. Selezionare **Modifica** e quindi apportare una modifica a `"version"` al di sotto di `"tag"`. Ad esempio, è possibile aggiornare a `"version": "${BUILD_BUILDID}"` per utilizzare [le variabili di compilazione DevOps](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) come parte del tag di immagine del modulo di Azure IoT Edge.

    ![Modificare la versione per accettare le variabili di compilazione](media/how-to-devops-project/update-module-json.png)

1. Selezionare **Esegui commit** e quindi salvare le modifiche.

1. Nel browser passare al dashboard del progetto DevOps di Azure.  Dovrebbe venire visualizzata una compilazione in corso. Le modifiche apportate vengono compilate e distribuite automaticamente tramite una pipeline CI/CD.

    ![Visualizza i lavori in corso](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>Esaminare la pipeline CI/CD

Nel passaggio precedente, Azure DevOps Projects ha configurato automaticamente una pipeline CI/CD completa per l'applicazione IoT Edge. Esplorare e personalizzare la pipeline in base alle esigenze. Eseguire questa procedura per acquisire familiarità con le pipeline di compilazione e di versione di Azure DevOps.

1. Selezionare **Pipeline di compilazione** nella parte superiore di DevOps Projects.  
Questo collegamento apre una scheda del browser e la pipeline di compilazione di Azure DevOps per il nuovo progetto.

1. Selezionare **Modifica**.

    ![Modificare la pipeline di compilazione](media/how-to-devops-project/click-edit-button.png)

1. In questo riquadro è possibile esaminare le diverse attività per la pipeline di compilazione. La compilazione esegue diverse attività, ad esempio il recupero delle origini dal repository Git, la compilazione delle immagini del modulo IoT Edge, l'esecuzione del push dei moduli IoT Edge e la pubblicazione degli output usati per le distribuzioni. Per altre informazioni sulle attività di Azure IoT Edge per CI, visitare [Configurare Azure Pipelines per l'integrazione continua](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-integration).

    ![Visualizza trigger di integrazione continua](media/how-to-devops-project/ci.png)

1. Nella parte superiore della pipeline di compilazione selezionare il nome della pipeline di compilazione.

1. Modificare il nome della pipeline di compilazione scegliendo un testo più descrittivo, selezionare **Salva e accoda**, quindi selezionare **Salva**.

1. Sotto il nome della pipeline di compilazione selezionare **Cronologia**.   
Nel riquadro **Cronologia** verrà visualizzato un log di controllo delle modifiche recenti per la compilazione.  Azure Pipelines tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

1. Selezionare **Trigger**. DevOps Projects ha creato automaticamente un trigger di integrazione continua e ogni commit nel repository avvia una nuova compilazione.  Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

1. Selezionare **Conservazione**. A seconda dello scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

1. Selezionare **Release** sotto **Pipeline**. DevOps Projects crea una pipeline di versione per gestire le distribuzioni in Azure IoT Edge.

    ![Visualizza la pipeline di versione](media/how-to-devops-project/release-pipeline.png)

1. Selezionare **Modifica**. La pipeline di versione contiene una pipeline che definisce il processo per la versione.  

1. In **Artefatti** selezionare **Elimina**. La pipeline di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto. 

1. Accanto all'icona **Elimina** selezionare **Trigger di distribuzione continua**.  
Questa pipeline di versione ha un trigger di distribuzione continua abilitato, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale.  

1. A sinistra selezionare **Attività**. Le attività sono le operazioni eseguite dal processo di distribuzione. In questo esempio è stata creata un'attività per la distribuzione delle immagini del modulo ad Azure IoT Edge. Per altre informazioni sulle attività di Azure IoT Edge per CD, visitare [Configurare Azure Pipelines per l'integrazione continua](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-deployment).

    ![Visualizza attività di distribuzione continua](media/how-to-devops-project/dev-release.png)

1. A destra, selezionare **Visualizza versioni**. Questa visualizzazione mostra una cronologia delle versioni.

1. Selezionare i puntini di sospensione (...) accanto a una delle versioni e quindi selezionare **Apri**.  
Ci sono diversi menu da esplorare, ad esempio un riepilogo delle versioni, gli elementi di lavoro associati e i test.

1. Selezionare **Commit**. Questa visualizzazione mostra i commit di codice associati alla distribuzione specifica. 

1. Selezionare **Log**. I log contengono informazioni utili sul processo di distribuzione. Possono essere visualizzati durante e dopo le distribuzioni.


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare il Servizio app di Azure e altre risorse correlate creati in precedenza. Usare la funzionalità **Elimina** nel dashboard di DevOps Projects.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle attività per Azure IoT Edge su Azure DevOps in [Integrazione continua e distribuzione continua in Azure IoT Edge](how-to-ci-cd.md)
* Per altre informazioni sulla distribuzione IoT Edge, vedere [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md) (Informazioni sulle distribuzioni IoT Edge per singoli dispositivi o su vasta scala)
* Eseguire le procedure per creare, aggiornare o eliminare una distribuzione in [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md).
