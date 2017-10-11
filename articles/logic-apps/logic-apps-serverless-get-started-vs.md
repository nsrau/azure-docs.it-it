---
title: Compilare un'app senza server in Visual Studio | Microsoft Docs
description: Introduzione alla prima app senza server con questa guida sulla creazione, la distribuzione e la gestione di app in Visual Studio.
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 3672beda8a502e5fe2c8182076a8edef7ee9ebf6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="build-a-serverless-app-in-visual-studio-with-logic-apps-and-functions"></a>Compilare un'app senza server in Visual Studio con App per la logica e Funzioni

Gli strumenti e le funzionalità senza server disponibili in Azure consentono di sviluppare e distribuire applicazioni cloud.  Questo documento è incentrato su come iniziare a usare Visual Studio per creare un'applicazione senza server.  In [questo articolo](logic-apps-serverless-overview.md) è contenuta una panoramica degli strumenti senza server disponibili in Azure.

## <a name="getting-everything-ready"></a>Preparazione

Di seguito sono elencati i prerequisiti necessari per compilare un'applicazione senza server da Visual Studio:

* [Visual Studio 2017](https://www.visualstudio.com/vs/) o Visual Studio 2015 - Community, Professional o Enterprise
* [Strumenti di App per la logica per Visual Studio](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551)
* [Versione più recente di Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 o versione successiva)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* [Strumenti di base di Funzioni di Azure](https://www.npmjs.com/package/azure-functions-core-tools) per eseguire il debug di Funzioni in locale
* Accesso al Web mentre viene usata la finestra di progettazione integrata di App per la logica

## <a name="getting-started-with-a-deployment-template"></a>Introduzione a un modello di distribuzione

La gestione delle risorse viene eseguita in Azure all'interno di un gruppo di risorse,  ovvero in un raggruppamento logico di risorse.  I gruppi di risorse consentono la distribuzione e la gestione di una raccolta di risorse.  Per un'applicazione senza server in Azure, il gruppo di risorse contiene sia App per la logica di Azure sia Funzioni di Azure.  Usando il progetto Gruppo di risorse in Visual Studio, è possibile sviluppare, gestire e distribuire l'intera applicazione come un singolo asset.

### <a name="create-a-resource-group-project-in-visual-studio"></a>Creare un progetto Gruppo di risorse in Visual Studio

1. In Visual Studio fare clic su **Nuovo progetto** per aggiungerlo.
1. Nella categoria **Cloud** selezionare l'opzione per la creazione di un progetto **Gruppo di risorse** in Azure  
 * Se la categoria o il progetto non sono elencati, verificare di avere installato Azure SDK per Visual Studio
1. Assegnare al progetto un nome e un percorso e selezionare **OK** per creare i prompt di Visual Studio per la selezione di un modello.  È possibile scegliere di iniziare da un modello vuoto, da App per la logica o da un'altra risorsa.  In questo caso, tuttavia, per iniziare la creazione di un'app senza server si userà un modello di avvio rapido di Azure.
1. Selezionare questa opzione per visualizzare i modelli da **Guida introduttiva di Azure** ![modelli selezionare avvio rapido di Azure][1]
1. Selezionare il modello di avvio rapido senza server: **101-logic-app-and-function-app** e fare clic su **OK**

Il modello di avvio rapido crea un modello di distribuzione nel progetto Gruppo di risorse,  contenente una semplice App per la logica che chiama un'istanza di Funzioni di Azure e restituisce il risultato.  Se si apre il file `azuredeploy.json` in Esplora soluzioni, è possibile visualizzare le risorse disponibili per l'app senza server.

## <a name="deploying-the-serverless-application"></a>Distribuzione dell'applicazione senza server

Prima di poter aprire la finestra di progettazione visiva di App per la logica in Visual Studio, è necessario che sia presente un progetto Gruppo di risorse di Azure pre-distribuito.  In questo modo, infatti, il progettista può creare e usare le connessioni a servizi e risorse disponibili nell'app per la logica.  Per iniziare, è sufficiente distribuire la soluzione creata.

1. Fare clic sul progetto in Visual Studio, selezionare **Distribuisci**e creare un **New** distribuzione ![selezionando una nuova distribuzione di risorse][2]
1. Selezionare una sottoscrizione di Azure valida e un gruppo di risorse
1. Selezionare l'opzione **Distribuisci** per distribuire la soluzione
1. Immettere il nome per l'app per la logica e per l'app per le funzioni di Azure.  Il nome dell'app per le funzioni di Azure deve essere globalmente univoco

La soluzione senza server viene distribuita nel gruppo di risorse specificato.  Nella finestra **Output** di Visual Studio è possibile visualizzare lo stato della distribuzione.

## <a name="editing-the-logic-app-in-visual-studio"></a>Modifica dell'app per la logica in Visual Studio

Dopo aver distribuito la soluzione in un gruppo di risorse, è possibile usare la finestra di progettazione visiva per apportare modifiche all'app per la logica.

1. Fare doppio clic sul file `azuredeploy.json` in Esplora soluzioni e selezionare **Open With Logic Apps Designer** (Apri con la finestra di progettazione di App per la logica)
1. Selezionare il **Gruppo di risorse** e il **Percorso** in cui è stata distribuita la soluzione e fare clic su **OK**

La finestra di progettazione visiva dell'app per la logica è ora visibile in Visual Studio.  È possibile continuare ad aggiungere passaggi, modificare il flusso di lavoro e salvare le modifiche.  È possibile anche creare app per la logica da Visual Studio.  Se si fa clic con il pulsante destro del mouse su **Risorse** in Selezione modelli, è possibile scegliere di aggiungere un'**App per logica** al progetto.  Le app per la logica vuote vengono caricate nella finestra di progettazione visiva senza essere pre-distribuite in un gruppo di risorse.

### <a name="managing-and-viewing-run-history-for-a-deployed-logic-app"></a>Gestione e visualizzazione della cronologia di esecuzione di un'app per la logica distribuita

È possibile anche gestire e visualizzare la cronologia di esecuzione delle app per la logica distribuite in Azure.  Se si apre lo strumento **Cloud Explorer** in Visual Studio, è possibile fare clic con il pulsante destro del mouse su un'app per la logica e scegliere di modificarla, disattivarla, visualizzarne le proprietà o visualizzarne la cronologia di esecuzione.  Facendo clic sull'opzione di modifica è possibile anche scaricare un'app per la logica pubblicata in un progetto Gruppo di risorse di Visual Studio.  In questo modo, anche se la creazione dell'app per la logica è già stata iniziata nel portale di Azure, è comunque possibile importarla e gestirla in Visual Studio.

## <a name="developing-an-azure-function-in-visual-studio"></a>Sviluppo di una funzione di Azure in Visual Studio

Il modello di distribuzione consente di distribuire qualsiasi funzione di Azure contenuta nella soluzione per il repository git specificato nelle variabili `azuredeploy.json`.  Se si crea un progetto di funzione all'interno della soluzione, lo si verifica nel controllo del codice sorgente (GitHub, Visual Studio Team Services e così via) e si aggiorna la variabile `repo`, il modello distribuirà la funzione di Azure.

### <a name="creating-an-azure-function-project"></a>Creazione di un progetto funzione di Azure

Se si usa JavaScript, Python, F#, Bash, Batch o PowerShell, seguire la [procedura dscritta nell'interfaccia della riga di comando di Funzioni](../azure-functions/functions-run-local.md) per creare un progetto.  Se si sta sviluppando una funzione in C#, per la funzione di Azure è possibile usare una [libreria di classi C#](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/) nella soluzione corrente.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come creare un dashboard social senza server](logic-apps-scenario-social-serverless.md)
* [Gestire un'app per la logica da Visual Studio Cloud Explorer](logic-apps-manage-from-vs.md)
* [Linguaggio di definizione del flusso di lavoro delle app per la logica](logic-apps-workflow-definition-language.md)

<!-- Image references -->
[1]: ./media/logic-apps-serverless-get-started-vs/select-template.png
[2]: ./media/logic-apps-serverless-get-started-vs/deploy.png
