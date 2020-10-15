---
title: Integrazione continua e distribuzione continua nei dispositivi Azure IoT Edge-Azure IoT Edge
description: Configurare l'integrazione continua e la distribuzione continua usando YAML-Azure IoT Edge con Azure DevOps, Azure Pipelines
author: shizn
manager: philmea
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57031d4ccdfdba73b8b36c8dc943280a8280ffcc
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048526"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>Integrazione continua e distribuzione continua nei dispositivi Azure IoT Edge

Si può facilmente adottare DevOps con le applicazioni Azure IoT Edge con le attività integrate Azure IoT Edge in Azure Pipelines. Questo articolo illustra come usare le funzionalità di integrazione continua e distribuzione continua di Azure Pipelines per compilare, testare e distribuire le applicazioni in modo rapido ed efficiente nel Azure IoT Edge usando YAML. In alternativa, è possibile [usare l'editor classico](how-to-continuous-integration-continuous-deployment-classic.md).

![Diagramma - rami CI e CD per lo sviluppo e la produzione](./media/how-to-continuous-integration-continuous-deployment/model.png)

Questo articolo illustra come usare le [attività di Azure IOT Edge](/azure/devops/pipelines/tasks/build/azure-iot-edge) predefinite per Azure pipelines per creare pipeline di compilazione e versione per la soluzione IOT Edge. Ogni attività Azure IoT Edge aggiunta alla pipeline implementa una delle quattro azioni seguenti:

 | Azione | Descrizione |
 | --- | --- |
 | Immagini del modulo di compilazione | Accetta il codice della soluzione IoT Edge e compila le immagini del contenitore.|
 | Immagini del modulo push | Inserisce le immagini del modulo nel registro contenitori specificato. |
 | Genera manifesto di distribuzione | Accetta un deployment.template.jssu file e le variabili, quindi genera il file manifesto di distribuzione IoT Edge finale. |
 | Distribuire nei dispositivi IoT Edge | Crea IoT Edge distribuzioni in uno o più dispositivi IoT Edge. |

Se non diversamente specificato, le procedure descritte in questo articolo non consentono di esplorare tutte le funzionalità disponibili tramite i parametri dell'attività. Per altre informazioni, vedere gli argomenti seguenti:

* [Versione attività](/azure/devops/pipelines/process/tasks?tabs=classic&view=azure-devops#task-versions)
* **Avanzate** : se applicabile, specificare i moduli che non si desidera compilati.
* [Opzioni di controllo](/azure/devops/pipelines/process/tasks?tabs=classic&view=azure-devops#task-control-options)
* [Variabili di ambiente](/azure/devops/pipelines/process/variables?tabs=yaml%252cbatch&view=azure-devops#environment-variables)
* [Variabili di output](/azure/devops/pipelines/process/variables?tabs=yaml%252cbatch&view=azure-devops#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Prerequisiti

* Un repository Azure Repos. Se non se ne ha uno, è possibile [creare un nuovo repository Git nel progetto](/azure/devops/repos/git/create-new-repo?tabs=new-nav&view=vsts). Per questo articolo è stato creato un repository denominato **IoTEdgeRepo**.
* Una soluzione IoT Edge di cui sia stato eseguito il commit e il push nel repository. Se si vuole creare una nuova soluzione di esempio per testare le procedure di questo articolo, seguire i passaggi descritti in [Usare Visual Studio Code per sviluppare moduli per Azure IoT Edge ed eseguirne il debug](how-to-vs-code-develop-module.md) oppure [Usare Visual Studio 2017 per sviluppare ed eseguire il debug di moduli C# per Azure IoT Edge](./how-to-visual-studio-develop-module.md). Per questo articolo è stata creata una soluzione nel repository denominata **IoTEdgeSolution**, che contiene il codice per un modulo denominato **FilterModule**.

   Per questo articolo, tutto quello che serve è la cartella della soluzione creata dai modelli IoT Edge in Visual Studio Code o Visual Studio. Non è necessario compilare, eseguire il push, distribuire o eseguire il debug di questo codice prima di procedere. Questi processi verranno configurati in Azure Pipelines.

   Se si sta creando una nuova soluzione, prima di tutto clonare il repository in locale. Quindi, quando si crea la soluzione, sarà possibile crearla direttamente nella cartella del repository. Da questa posizione è possibile eseguire facilmente il commit e il push dei nuovi file.

* Un registro contenitori in cui eseguire il push delle immagini dei moduli. È possibile usare il [Registro Azure Container](../container-registry/index.yml) o un registro di terze parti.
* Un [Hub](../iot-hub/iot-hub-create-through-portal.md) Azure tutto attivo con almeno due dispositivi IOT Edge per testare le fasi di distribuzione di test e produzione separate. È possibile seguire gli articoli di avvio rapido per creare un dispositivo IoT Edge in [Linux](quickstart-linux.md) o [Windows](quickstart.md)

Per altre informazioni sull'uso di Azure Repos, vedere [Condividere il codice con Visual Studio e Azure Repos](/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Creare una pipeline di compilazione per l'integrazione continua

In questa sezione viene creata una nuova pipeline di compilazione. La pipeline viene configurata per l'esecuzione automatica quando si archiviano le modifiche apportate alla soluzione IoT Edge di esempio e si pubblicano i log di compilazione.

1. Accedere all'organizzazione DevOps di Azure ( `https://dev.azure.com/{your organization}` ) e aprire il progetto che contiene il repository della soluzione IOT Edge.

   ![Aprire il progetto DevOps](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. Dal menu del riquadro a sinistra nel progetto selezionare **pipeline**. Selezionare **crea pipeline** al centro della pagina. In alternativa, se sono già presenti pipeline di compilazione, selezionare il pulsante **nuova pipeline** in alto a destra.

    ![Creare una nuova pipeline di compilazione usando il pulsante nuova pipeline](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. Nella pagina **dove si trova il codice** selezionare **Azure Repos git `YAML` **. Se si vuole usare l'editor classico per creare le pipeline di compilazione del progetto, vedere la [Guida dell'editor classico](how-to-continuous-integration-continuous-deployment-classic.md).

4. Selezionare il repository per cui si sta creando una pipeline.

    ![Selezionare il repository per la pipeline di compilazione](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. Nella pagina **Configura pipeline** selezionare **Starter pipeline**. Se si dispone di un file YAML preesistente Azure Pipelines che si vuole usare per creare questa pipeline, è possibile selezionare il **file yaml Azure Pipelines esistente** e fornire il ramo e il percorso nel repository al file.

    ![Selezionare la pipeline di avvio o il file YAML Azure Pipelines esistente per avviare la pipeline di compilazione](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. Nella pagina **esaminare** la pagina YAML della pipeline è possibile fare clic sul nome predefinito `azure-pipelines.yml` per rinominare il file di configurazione della pipeline.

   Selezionare **Mostra Assistente** per aprire la tavolozza **attività** .

    ![Selezionare Mostra Assistente per aprire la tavolozza attività](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. Per aggiungere un'attività, posizionare il cursore alla fine del YAML o quando si desidera che vengano aggiunte le istruzioni per l'attività. Cercare e selezionare **Azure IOT Edge**. Compilare i parametri dell'attività come indicato di seguito. Quindi selezionare **Aggiungi**.

   | Parametro | Descrizione |
   | --- | --- |
   | Azione | Selezionare **Compila immagini del modulo**. |
   | .template.jssu file | Specificare il percorso del **deployment.template.js** nel file nel repository che contiene la soluzione IOT Edge. |
   | Piattaforma predefinita | Selezionare il sistema operativo appropriato per i moduli in base al dispositivo IoT Edge di destinazione. |

    ![Usare la tavolozza delle attività per aggiungere attività alla pipeline](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > Dopo l'aggiunta di ogni attività, l'editor evidenzierà automaticamente le righe aggiunte. Per evitare la sovrascrittura accidentale, deselezionare le righe e fornire un nuovo spazio per l'attività successiva prima di aggiungere ulteriori attività.

8. Ripetere questo processo per aggiungere altre tre attività con i parametri seguenti:

   * Attività: **Azure IOT Edge**

       | Parametro | Descrizione |
       | --- | --- |
       | Azione | Selezionare le **Immagini del modulo push**. |
       | Tipo di registro contenitori | Usare il tipo predefinito: **container Registry di Azure**. |
       | Sottoscrizione di Azure | Selezionare la propria sottoscrizione. |
       | Registro Azure Container | Scegliere il registro di sistema che si vuole usare per la pipeline. |
       | .template.jssu file | Specificare il percorso del **deployment.template.js** nel file nel repository che contiene la soluzione IOT Edge. |
       | Piattaforma predefinita | Selezionare il sistema operativo appropriato per i moduli in base al dispositivo IoT Edge di destinazione. |

   * Attività: **copiare i file**

       | Parametro | Descrizione |
       | --- | --- |
       | Cartella di origine | Cartella di origine da cui eseguire la copia. Empty è la radice del repository. Usare le variabili se i file non sono presenti nel repository. Esempio: `$(agent.builddirectory)`.
       | Contenuto | Aggiungere due righe: `deployment.template.json` e `**/module.json` . |
       | Cartella di destinazione | Specificare la variabile `$(Build.ArtifactStagingDirectory)` . Vedere [variabili di compilazione](/azure/devops/pipelines/build/variables?tabs=yaml&view=azure-devops#build-variables) per informazioni sulla descrizione. |

   * Attività: **pubblicare artefatti di compilazione**

       | Parametro | Descrizione |
       | --- | --- |
       | Percorso per la pubblicazione | Specificare la variabile `$(Build.ArtifactStagingDirectory)` . Vedere [variabili di compilazione](/azure/devops/pipelines/build/variables?tabs=yaml&view=azure-devops#build-variables) per informazioni sulla descrizione. |
       | Nome dell'artefatto | Specificare il nome predefinito: `drop` |
       | Percorso di pubblicazione artefatto | Usa il percorso predefinito: `Azure Pipelines` |

9. Selezionare **Salva** dall'elenco a discesa **Salva ed Esegui** in alto a destra.

10. Il trigger per l'integrazione continua è abilitato per impostazione predefinita per la pipeline YAML. Se si desidera modificare queste impostazioni, selezionare la pipeline e fare clic su **modifica** in alto a destra. Selezionare **altre azioni** accanto al pulsante **Esegui** in alto a destra e vai a **trigger**. L' **integrazione continua** Mostra come abilitato sotto il nome della pipeline. Se si desidera visualizzare i dettagli per il trigger, selezionare la casella **Sostituisci il trigger integrazione continua YAML da qui** .

    ![Per esaminare le impostazioni del trigger della pipeline, vedere trigger in altre azioni](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

Passare alla sezione successiva per creare la pipeline di versione.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Passaggi successivi

* IoT Edge esempio di procedure consigliate [per DevOps in Azure DevOps Starter per IOT Edge](how-to-devops-starter.md)
* Per altre informazioni sulla distribuzione IoT Edge, vedere [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md) (Informazioni sulle distribuzioni IoT Edge per singoli dispositivi o su vasta scala)
* Eseguire le procedure per creare, aggiornare o eliminare una distribuzione in [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-at-scale.md).