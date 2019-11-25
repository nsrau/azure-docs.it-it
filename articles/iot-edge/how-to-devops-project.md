---
title: Pipeline CI/CD con Azure DevOps Projects - Azure IoT Edge | Microsoft Docs
description: Azure DevOps Projects permette di iniziare a usare Azure velocemente. Con pochi rapidi passaggi, è possibile avviare un'app in un servizio di Azure IoT Edge a scelta.
author: shizn
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ccf6ea567143180daa848566d1e7e1420c181c5f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457387"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Create a CI/CD pipeline for IoT Edge with Azure DevOps Projects

Configurare integrazione continua (CI, Continuous Integration) e recapito continuo (CD, Continuous Delivery) per un'applicazione IoT Edge con DevOps Projects. DevOps Projects semplifica la configurazione iniziale di una pipeline di compilazione e di versione in Azure Pipelines.

Se non si ha una sottoscrizione di Azure attiva, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

DevOps Projects crea una pipeline CI/CD in Azure DevOps. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. DevOps Projects crea anche risorse di Azure nella sottoscrizione di Azure di propria scelta.

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com).

1. Nel riquadro a sinistra selezionare **Crea una risorsa** e quindi cercare **DevOps Projects**.  

1.  Selezionare **Create** (Crea).

## <a name="create-a-new-application-pipeline"></a>Creare una nuova pipeline applicazione 

1. I moduli Azure IoT Edge possono essere scritti in [ C# ](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) e [Java](tutorial-java-module.md). Selezionare il linguaggio di programmazione preferito per avviare una nuova applicazione: **.NET**, **Node.js**, **Python**, **C** o **Java**. Selezionare **Avanti** per continuare.

   ![Selezionare un linguaggio di programmazione per creare una nuova applicazione](./media/how-to-devops-project/select-language.png)

2. Select **Simple IoT** as your application framework, and then select **Next**.

   ![Selezionare il framework IoT semplice](media/how-to-devops-project/select-iot.png)

3. Selezionare **IoT Edge** come servizio di Azure che distribuisce l'applicazione e quindi selezionare **Avanti**.

   ![Selezionare servizio IoT Edge](media/how-to-devops-project/select-iot-edge.png)

4. Creare una nuova organizzazione gratuita di Azure DevOps Services o scegliere un'organizzazione esistente.

   1. Specificare un nome per il progetto. 

   2. Selezionare l'organizzazione di Azure DevOps. Se non si ha un'organizzazione esistente, selezionare **Impostazioni aggiuntive** per crearne una. 

   3. Selezionare la sottoscrizione di Azure.

   4. Usare il nome dell'hub IoT generato dal nome del progetto o specificare un proprio nome.

   5. Accept the default location, or choose one close to you. 

   5. Selezionare **Impostazioni aggiuntive** per configurare le risorse di Azure create da DevOps Projects per conto dell'utente.

   6. Selezionare **Fine** per completare la creazione del progetto. 

   ![Non è possibile creare l'applicazione](media/how-to-devops-project/select-devops.png)

Dopo pochi minuti, il dashboard di DevOps Projects viene visualizzato nel portale di Azure. Selezionare il nome del progetto per visualizzare lo stato di avanzamento. Potrebbe essere necessario aggiornare la pagina. Viene configurata un'applicazione di esempio in un repository nell'organizzazione Azure DevOps, viene eseguita una compilazione e l'applicazione viene distribuita in Azure. Questo dashboard fornisce visibilità su repository di codice, pipeline CI/CD e applicazione in Azure.

   ![View application in Azure portal](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Eseguire il commit delle modifiche al codice e la pipeline di CI/CD

DevOps Projects ha creato un repository Git per il progetto in Azure Repos. In questa sezione si visualizza il repository e si apportano modifiche al codice dell'applicazione.

1. Per passare al repository creato per il progetto, selezionare **Repository** nel menu del dashboard del progetto.  

   ![Visualizzare il repository generato in Azure Repos](./media/how-to-devops-project/view-repositories.png)

2. I passaggi seguenti descrivono come usare il Web browser per apportare modifiche al codice. Se si preferisce clonare il repository in locale, selezionare **Clona** nella parte superiore destra della finestra. Usare l'URL fornito per clonare il repository Git in Visual Studio Code o nel proprio strumento di sviluppo preferito. 

3. The repository already contains code for a module called **FilterModule** based on the application language that you chose in the creation process. Open the **modules/FilterModule/module.json** file.

   ![Aprire il file module.json in Azure Repos](./media/how-to-devops-project/open-module-json.png)

4. Notice that this file uses [Azure DevOps build variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) in the **version** parameter. This configuration ensures that a new version of the module will be created every time a new build runs. 


## <a name="examine-the-cicd-pipeline"></a>Esaminare la pipeline CI/CD

Nelle sezioni precedenti, Azure DevOps Projects ha configurato automaticamente una pipeline CI/CD completa per l'applicazione IoT Edge. Ora è possibile esplorare e personalizzare la pipeline in base alle esigenze. Use the following steps to familiarize yourself with the Azure DevOps build and release pipelines.

1. Per visualizzare le pipeline di compilazione nel progetto DevOps, selezionare **Pipeline di compilazione** nel menu del dashboard del progetto. Questo collegamento apre una scheda del browser e la pipeline di compilazione di Azure DevOps per il nuovo progetto.

   ![Visualizzare le pipeline di compilazione in Azure Pipelines](./media/how-to-devops-project/view-build-pipelines.png)

2. Selezionare **Modifica**.

    ![Modificare la pipeline di compilazione](media/how-to-devops-project/click-edit-button.png)

3. Nel pannello che viene aperto è possibile esaminare le attività che si verificano durante l'esecuzione della pipeline di compilazione. La pipeline di compilazione esegue diverse attività, ad esempio il recupero delle origini dal repository Git, la compilazione delle immagini di moduli IoT Edge, l'esecuzione del push dei moduli IoT Edge in un registro contenitori e la pubblicazione degli output usati per le distribuzioni. Per altre informazioni sulle attività Azure IoT Edge in Azure DevOps, vedere [Configurare Azure Pipelines per l'integrazione continua](how-to-ci-cd.md#configure-continuous-integration).

4. Selezionare l'intestazione **Pipeline** nella parte superiore della pipeline di compilazione per aprire i dettagli della pipeline. Modificare il nome della pipeline di compilazione scegliendo un testo più descrittivo.

   ![Modificare i dettagli della pipeline](./media/how-to-devops-project/edit-build-pipeline.png)

5. Selezionare **Salva e accoda** e quindi **Salva**.

6. Select **Triggers** from the build pipeline menu. DevOps Projects ha creato automaticamente un trigger di integrazione continua e ogni commit nel repository avvia una nuova compilazione.  Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

7. Selezionare **Conservazione**. A seconda dello scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

8. Selezionare **Cronologia**. Il riquadro della cronologia contiene un audit trail delle modifiche recenti alla compilazione. Azure Pipelines tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

9. Una volta completata l'esplorazione della pipeline di compilazione, passare alla pipeline di versione corrispondente. Selezionare **Versioni** sotto **Pipeline**, quindi selezionare **Modifica** per visualizzare i dettagli della pipeline.

    ![Visualizza la pipeline di versione](media/how-to-devops-project/release-pipeline.png)

10. In **Artefatti** selezionare **Elimina**. La pipeline di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto. 

11. Accanto all'icona **Elimina** selezionare l'icona a forma di fulmine del **Trigger di distribuzione continua**. Questa pipeline di versione ha abilitato il trigger, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale.  

12. Nel menu della pipeline di versione selezionare **Attività**, quindi scegliere la fase **dev** dall'elenco a discesa. DevOps Projects ha creato una fase di versione che crea un hub IoT, crea un dispositivo IoT Edge in tale hub, distribuisce il modulo di esempio dalla pipeline di compilazione ed effettua il provisioning di una macchina virtuale da eseguire come dispositivo IoT Edge. Per altre informazioni sulle attività Azure IoT Edge per la distribuzione continua, vedere [Configurare la distribuzione continua](how-to-ci-cd.md#configure-continuous-deployment).

    ![Visualizza attività di distribuzione continua](media/how-to-devops-project/dev-release.png)

13. A destra, selezionare **Visualizza versioni**. Questa visualizzazione mostra una cronologia delle versioni.

14. Selezionare il nome di una versione per visualizzare altre informazioni su di essa.


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare il Servizio app di Azure e altre risorse correlate creati in precedenza. Usare la funzionalità **Elimina** nel dashboard di DevOps Projects.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle attività per Azure IoT Edge su Azure DevOps in [Integrazione continua e distribuzione continua in Azure IoT Edge](how-to-ci-cd.md)
* Per altre informazioni sulla distribuzione IoT Edge, vedere [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md) (Informazioni sulle distribuzioni IoT Edge per singoli dispositivi o su vasta scala)
* Eseguire le procedure per creare, aggiornare o eliminare una distribuzione in [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md).
