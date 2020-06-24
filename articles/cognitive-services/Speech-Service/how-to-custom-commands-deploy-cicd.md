---
title: Distribuzione continua con Azure DevOps (anteprima)
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come configurare la distribuzione continua per le applicazioni di comandi personalizzati. È possibile creare gli script per supportare i flussi di lavoro di distribuzione continua.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: f4dde6831902c0d15d5f985208e382963125d200
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307727"
---
# <a name="continuous-deployment-with-azure-devops"></a>Distribuzione continua con Azure DevOps

Questo articolo illustra come configurare la distribuzione continua per le applicazioni di comandi personalizzati. Gli script per il supporto del flusso di lavoro CI/CD sono forniti all'utente.

## <a name="prerequisite"></a>Prerequisito
> [!div class = "checklist"]
> * Applicazione di comandi personalizzata per lo sviluppo (DEV)
> * Un'applicazione di comandi personalizzati per la produzione (PROD)
> * Iscriversi per [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)

## <a name="exportimportpublish"></a>Esportazione/importazione/pubblicazione

Gli script sono ospitati in [Assistente vocale di servizi cognitivi-comandi personalizzati](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Clonare gli script nella directory bash nel repository. Assicurarsi di mantenere lo stesso percorso.

### <a name="set-up-a-pipeline"></a>Configurare una pipeline 

1. Passare ad **Azure DevOps-Pipelines** e fare clic su "New pipeline".
1. Nella sezione **Connetti** selezionare il percorso del repository in cui si trovano questi script
1. Nella sezione **Seleziona** selezionare il repository
1. Nella sezione **Configura** selezionare "pipeline iniziale".
1. Si otterrà quindi un editor con un file YAML e si sostituirà la sezione "Steps" con questo script.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. Si noti che questi script presuppongono che si stia usando l'area, in caso `westus2` contrario aggiornare gli argomenti delle attività in modo appropriato

    > [!div class="mx-imgBorder"]
    > ![Payload dell'attività Send](media/custom-commands/cicd-new-pipeline-yaml.png)

1. Nel pulsante "Salva ed Esegui" aprire l'elenco a discesa e fare clic su "Salva".

### <a name="hook-up-the-pipeline-with-your-application"></a>Associare la pipeline all'applicazione

1. Passare alla pagina principale della pipeline.
1. Nell'elenco a discesa angolo superiore destro selezionare **modifica pipeline**. Consente di ottenere un editor YAML. 
1. Nell'angolo in alto a destra accanto al pulsante "Esegui" selezionare **variabili**. Fare clic su **nuova variabile**.
1. Aggiungere le variabili seguenti:
    
    | Variabile | Descrizione |
    | ------- | --------------- | ----------- |
    | SourceAppId | ID dell'applicazione DEV |
    | TargetAppId | ID dell'applicazione PROD |
    | SubscriptionKey | Chiave di sottoscrizione usata per entrambe le applicazioni |
    | Impostazioni cultura | Impostazioni cultura delle applicazioni (ad esempio en-US) |

    > [!div class="mx-imgBorder"]
    > ![Payload dell'attività Send](media/custom-commands/cicd-edit-pipeline-variables.png)

1. Fare clic su "Esegui" e quindi su "processo" in esecuzione. 

    Verrà visualizzato un elenco delle attività in esecuzione che contengono: "Esporta app origine", "Importa nell'app di destinazione" & "app di destinazione di training e pubblicazione"

## <a name="deploy-from-source-code"></a>Distribuire dal codice sorgente

Se si vuole lasciare la definizione dell'applicazione in un repository, vengono forniti gli script per le distribuzioni dal codice sorgente. Poiché gli script sono in bash, se si usa Windows è necessario installare il [sottosistema Linux](https://docs.microsoft.com/windows/wsl/install-win10).

Gli script sono ospitati in [Assistente vocale di servizi cognitivi-comandi personalizzati](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Clonare gli script nella directory bash nel repository. Assicurarsi di mantenere lo stesso percorso.

### <a name="prepare-your-repository"></a>Preparare il repository

1. Creare una directory per l'applicazione, nell'esempio crearne una denominata "Apps".
1. Aggiornare gli argomenti dello script bash seguente ed eseguire. Il modello di finestra di dialogo dell'applicazione verrà importato nel file myapp.js
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | Argomenti | Descrizione |
    | ------- | --------------- | ----------- |
    | region | area dell'applicazione, ad esempio westus2. |
    | subscriptionkey | chiave di sottoscrizione della risorsa di riconoscimento vocale. |
    | appid | ID applicazione dei comandi personalizzati che si desidera esportare. |

1. Eseguire il push di queste modifiche nel repository.

### <a name="set-up-a-pipeline"></a>Configurare una pipeline 

1. Passare ad **Azure DevOps-Pipelines** e fare clic su "New pipeline".
1. Nella sezione **Connetti** selezionare il percorso del repository in cui si trovano questi script
1. Nella sezione **Seleziona** selezionare il repository
1. Nella sezione **Configura** selezionare "pipeline iniziale".
1. Si otterrà quindi un editor con un file YAML e si sostituirà la sezione "Steps" con questo script.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > questi script presuppongono che si stia usando l'area westus2, in caso contrario, aggiornare gli argomenti delle attività in modo appropriato

1. Nel pulsante "Salva ed Esegui" aprire l'elenco a discesa e fare clic su "Salva".

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>Associare la pipeline alle applicazioni di destinazione

1. Passare alla pagina principale della pipeline.
1. Nell'elenco a discesa angolo superiore destro selezionare **modifica pipeline**. Consente di ottenere un editor YAML. 
1. Nell'angolo in alto a destra accanto al pulsante "Esegui" selezionare **variabili**. Fare clic su **nuova variabile**.
1. Aggiungere le variabili seguenti:

    | Variabile | Descrizione |
    | ------- | --------------- | ----------- |
    | TargetAppId | ID dell'applicazione PROD |
    | SubscriptionKey | Chiave di sottoscrizione usata per entrambe le applicazioni |
    | Impostazioni cultura | Impostazioni cultura delle applicazioni (ad esempio en-US) |

1. Fare clic su "Esegui" e quindi su "processo" in esecuzione.
    Verrà visualizzato un elenco delle attività in esecuzione che contengono: "Importa app" & "Esegui il training e la pubblicazione dell'app"

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Vedere gli esempi in GitHub](https://aka.ms/speech/cc-samples)