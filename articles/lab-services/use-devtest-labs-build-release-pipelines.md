---
title: Usare DevTest Labs nelle pipeline di compilazione e rilascio di Azure DevOps | Microsoft Docs
description: Informazioni su come usare Azure DevTest Labs nelle pipeline di compilazione e versione di Azure DevOps.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2019
ms.author: spelluru
ms.openlocfilehash: 606563cd7d7adcdd79bf9561876eb0640fb68b21
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620896"
---
# <a name="use-devtest-labs-in-azure-devops-build-and-release-pipelines"></a>Usare DevTest Labs nelle pipeline di compilazione e versione di Azure DevOps
Questo articolo fornisce informazioni sul modo in cui è possibile usare DevTest Labs nelle pipeline di compilazione e rilascio di Azure DevOps. 

## <a name="overall-flow"></a>Flusso generale
Il flusso di base consiste nel disporre di una **pipeline di compilazione** che esegue le attività seguenti:

1. Compilare il codice dell'applicazione.
1. Creare l'ambiente di base in DevTest Labs.
1. Aggiornare l'ambiente con informazioni personalizzate.
1. Distribuire l'applicazione nell'ambiente DevTest Labs
1. Testare il codice. 

Una volta completata la compilazione, la pipeline di **rilascio** utilizzerà gli elementi di compilazione per distribuire la gestione temporanea o la produzione. 

Una delle sedi necessarie è che tutte le informazioni necessarie per ricreare l'ecosistema testato sono disponibili negli elementi di compilazione, inclusa la configurazione delle risorse di Azure. Poiché le risorse di Azure comportano un costo quando vengono usate, le aziende vogliono controllare o tenere traccia dell'uso di queste risorse. In alcune situazioni, i modelli di Azure Resource Manager usati per creare e configurare le risorse possono essere gestiti da un altro reparto, ad esempio. Questi modelli possono essere archiviati in un repository diverso. Ciò comporta una situazione interessante in cui verrà creata e testata una compilazione e il codice e la configurazione devono essere archiviati all'interno degli elementi di compilazione per ricreare correttamente il sistema in produzione. 

Utilizzando DevTest Labs durante la fase di compilazione/test, è possibile aggiungere Azure Resource Manager modelli e i file di supporto alle origini di compilazione in modo che durante la fase di rilascio la configurazione esatta utilizzata per il test di venga distribuita nell'ambiente di produzione. L'attività **crea Azure DevTest Labs ambiente** con la configurazione corretta salverà i modelli di gestione risorse all'interno degli elementi di compilazione. Per questo esempio verrà usato il codice [dell'esercitazione: Consente di compilare un'app Web .NET Core e database SQL nel](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)servizio app Azure per distribuire e testare l'app Web in Azure.

![Flusso generale](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Configurare le risorse di Azure
È necessario creare prima di tutto un paio di elementi:

- Due repository. Il primo con il codice dell'esercitazione e un modello di Gestione risorse con due macchine virtuali aggiuntive. Il secondo conterrà il modello di Azure Resource Manager di base (configurazione esistente).
- Un gruppo di risorse per la distribuzione del codice di produzione e della configurazione.
- Un lab deve essere configurato con una [connessione al repository di configurazione](devtest-lab-create-environment-from-arm.md) per la pipeline di compilazione. Il modello di Gestione risorse deve essere archiviato nel repository di configurazione come file azuredeploy. JSON con Metadata. JSON per consentire a DevTest Labs di riconoscere e distribuire il modello.

La pipeline di compilazione creerà un ambiente DevTest Labs e distribuirà il codice per il test.

## <a name="set-up-a-build-pipeline"></a>Configurare una pipeline di compilazione
In Azure DevOps creare una pipeline di compilazione usando il codice [dell'esercitazione: Creare un'app Web .NET Core e database SQL nel servizio](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)app Azure. Usare il modello di **ASP.NET Core** , che consente di popolare l'attività necessaria per compilare, testare e pubblicare il codice.

![Selezionare il modello ASP.NET](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

È necessario aggiungere altre tre attività per creare l'ambiente in DevTest Labs e distribuirlo nell'ambiente.

![Pipeline con tre attività](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Crea attività ambiente
Nell'attività crea ambiente (**Azure DevTest Labs attività crea ambiente** ) utilizzare gli elenchi a discesa per selezionare i valori seguenti:

- Sottoscrizione di Azure
- nome del Lab
- nome del repository
- nome del modello, che mostra la cartella in cui è archiviato l'ambiente. 

Si consiglia di utilizzare elenchi a discesa nella pagina anziché immettere manualmente le informazioni. Se si immettono manualmente le informazioni, immettere ID di risorsa di Azure completi. L'attività Visualizza i nomi descrittivi anziché gli ID risorsa. 

Il nome dell'ambiente è il nome visualizzato in DevTest Labs. Deve essere un nome univoco per ogni compilazione. Ad esempio:  **TestEnv $ (Build. ID compilazione)** . 

È possibile specificare il file dei parametri o i parametri per passare le informazioni nel modello di Gestione risorse. 

Selezionare l'opzione **crea variabili di output in base all'output del modello di ambiente** e immettere un nome di riferimento. Per questo esempio, immettere **BaseEnv** per il nome di riferimento. Questo BaseEnv verrà usato durante la configurazione dell'attività successiva. 

![Crea attività Azure DevTest Labs ambiente](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Popola attività ambiente
La seconda attività (**Azure DevTest Labs popolare l'attività dell'ambiente** ) consiste nell'aggiornare l'ambiente DevTest Labs esistente. L'attività crea ambiente restituisce **BaseEnv. environmentResourceId** usato per configurare il nome dell'ambiente per questa attività. Il modello di Gestione risorse per questo esempio ha due parametri: **con valori AdminUsername** e **adminPassword**. 

![Popola Azure DevTest Labs attività ambiente](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>Attività di distribuzione del servizio app
La terza attività è l'attività di **distribuzione del servizio app Azure** . Il tipo di app è impostato su **app Web** e il nome del servizio app è impostato su **$ (sito Web)** .

![Attività di distribuzione del servizio app](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="setup-release-pipeline"></a>Installazione della pipeline di rilascio
Si crea una pipeline di rilascio con due attività: **Distribuzione di Azure: Creare o aggiornare il gruppo** di risorse e **distribuire app Azure servizio**. 

Per la prima attività, specificare il nome e il percorso del gruppo di risorse. Il percorso del modello è un artefatto collegato. Se il modello di Gestione risorse include modelli collegati, è necessario implementare una distribuzione del gruppo di risorse personalizzata. Il modello si trova nell'artefatto di rilascio pubblicato. Eseguire l'override dei parametri di modello per il modello di Gestione risorse. È possibile lasciare le impostazioni rimanenti con i valori predefiniti. 

Per la seconda attività **distribuire il servizio app Azure**, specificare la sottoscrizione di Azure, selezionare **app Web** per il **tipo di app**e **$ (sito Web)** per il **nome del servizio app**. È possibile lasciare le impostazioni rimanenti con i valori predefiniti. 

## <a name="test-run"></a>Esecuzione dei test
Ora che entrambe le pipeline sono configurate, accodare manualmente una compilazione e visualizzarne il funzionamento. Il passaggio successivo consiste nell'impostare il trigger appropriato per la compilazione e connettere la compilazione alla pipeline di rilascio.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Integrare Azure DevTest Labs nella pipeline di integrazione e recapito continua di Azure DevOps](devtest-lab-integrate-ci-cd-vsts.md)
- [Integra gli ambienti nelle pipeline CI/CD di Azure DevOps](integrate-environments-devops-pipeline.md)
