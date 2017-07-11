---
title: Creare la prima funzione in Azure con Visual Studio | Microsoft Docs
description: Creare e pubblicare una semplice funzione attivata tramite HTTP in Azure usando Azure Functions Tools for Visual Studio.
services: functions
documentationcenter: na
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: Funzioni di Azure, funzioni, elaborazione eventi, calcolo, architettura senza server
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/25/2017
ms.author: rachelap, glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 64769458ad90f14c2f7a87b9a405b80616a478be
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017


---
<a id="create-your-first-function-using-visual-studio" class="xliff"></a>

# Creare la prima funzione con Visual Studio 

Funzioni di Azure consente di eseguire il codice in un ambiente senza server senza dover prima creare una macchina virtuale o pubblicare un'applicazione Web. 

Questo argomento illustra come usare Azure Function Tools for Visual Studio 2017 per creare e testare una funzione "hello world" in locale. Il codice della funzione verrà quindi pubblicato in Azure.

![Codice di Funzioni di Azure in un progetto di Visual Studio](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

<a id="prerequisites" class="xliff"></a>

## Prerequisiti

Per completare questa esercitazione, installare:

* [Visual Studio 2017 Preview versione 15.3](https://www.visualstudio.com/vs/preview/), con il carico di lavoro **Sviluppo di Azure**.
    
    ![Installare Visual Studio 2017 con il carico di lavoro Sviluppo di Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="install-azure-functions-tools-for-visual-studio-2017" class="xliff"></a>

## Installare Azure Functions Tools for Visual Studio 2017

Prima di iniziare, è necessario scaricare e installare Azure Functions Tools for Visual Studio 2017. Questi strumenti possono essere usati solo con Visual Studio 2017 Preview versione 15.3 o successiva. Se è già stata eseguita l'installazione di Azure Functions Tools, è possibile ignorare questa sezione.

[!INCLUDE [Install the Azure Functions Tools for Visual Studio](../../includes/functions-install-vstools.md)]   

<a id="create-an-azure-functions-project-in-visual-studio" class="xliff"></a>

## Creare un progetto Funzioni di Azure in Visual Studio

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Ora che è stato creato il progetto, si può creare la prima funzione.

<a id="create-the-function" class="xliff"></a>

## Creare la funzione

In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Aggiungi** > **Nuovo elemento**. Selezionare **Funzione di Azure** e fare clic su **Aggiungi**.

Selezionare **HttpTrigger**, digitare un **nome di funzione**, selezionare **Anonimo** in **Diritti di accesso** e fare clic su **Crea**. La funzione creata è accessibile da una richiesta HTTP proveniente da qualsiasi client. 

![Creare una nuova funzione di Azure](./media/functions-create-your-first-function-visual-studio/functions-vstools-add-new-function-2.png)

Ora che è stata creata una funzione attivata tramite HTTP, si può testare la funzione nel computer locale.

<a id="test-the-function-locally" class="xliff"></a>

## Testare la funzione in locale

[!INCLUDE [Test the function locally](../../includes/functions-vstools-test.md)]

Copiare l'URL della funzione dall'output di runtime di Funzioni di Azure.  

![Runtime locale di Azure](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

 Incollare l'URL per la richiesta HTTP nella barra degli indirizzi del browser. Aggiungere la stringa di query `&name=<yourname>` all'URL ed eseguire la richiesta. Di seguito è illustrata la risposta nel browser alla richiesta GET locale restituita dalla funzione: 

![Risposta localhost della funzione nel browser](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

Per interrompere il debug, fare clic sul pulsante **Interrompi** sulla barra degli strumenti di Visual Studio.

Dopo aver verificato la corretta esecuzione della funzione nel computer locale, è possibile pubblicare il progetto in Azure.

<a id="publish-the-project-to-azure" class="xliff"></a>

## Pubblicare il progetto in Azure

Per poter pubblicare il progetto, è necessario che la sottoscrizione di Azure includa un'app per le funzioni. È possibile creare un'app per le funzioni direttamente da Visual Studio.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

<a id="test-your-function-in-azure" class="xliff"></a>

## Testare la funzione in Azure

Copiare l'URL di base dell'app per le funzioni dalla pagina del profilo di pubblicazione. Sostituire la parte `localhost:port` dell'URL usato durante il test della funzione in locale con il nuovo URL di base. Come prima, assicurarsi di accodare la stringa di query `&name=<yourname>` all'URL ed eseguire la richiesta.

L'URL che chiama la funzione attivata tramite HTTP sarà simile al seguente:

    http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

Incollare questo nuovo URL per la richiesta HTTP nella barra degli indirizzi del browser. Di seguito è illustrata la risposta nel browser alla richiesta GET remota restituita dalla funzione: 

![Risposta della funzione nel browser](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)
 
<a id="next-steps" class="xliff"></a>

## Passaggi successivi

È stato usato Visual Studio per creare un'app per le funzioni C# con una semplice funzione attivata tramite HTTP. 

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]

Per altre informazioni sul test e il debug in locale con Azure Functions Core Tools, vedere [Scrivere codice per le funzioni di Azure e testarle in locale](functions-run-local.md). 


