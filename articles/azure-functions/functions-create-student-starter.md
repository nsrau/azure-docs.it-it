---
title: Creare una funzione usando Azure for Students Starter
description: Informazioni su come creare una funzione di Azure da una sottoscrizione di Azure for Students Starter
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: ecb2cc20bec602101d683dbfdd3f82a2e199aa16
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386963"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Creare una funzione usando Azure for Students Starter

In questa esercitazione si creerà una funzione HTTP "hello world" in una sottoscrizione di Azure for Students Starter. Verranno anche illustrate le funzionalità disponibili in Funzioni di Azure in questo tipo di sottoscrizione.

Microsoft *Azure for Students Starter* permette di iniziare a usare i prodotti di Azure che servono per sviluppare sul cloud gratuitamente. [Altre informazioni su questa offerta.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Funzioni di Azure consente di eseguire il codice in un ambiente [senza server](https://azure.microsoft.com/solutions/serverless/), senza dover prima creare una macchina virtuale o pubblicare un'applicazione Web. [Altre informazioni su Funzioni di Azure.](./functions-overview.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function"></a>Creare una funzione

 Questo articolo illustra come usare Funzioni di Azure per creare una funzione "hello world" attivata tramite HTTP nel portale di Azure.

![Creare un'app per le funzioni nel portale di Azure](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Per ospitare l'esecuzione delle funzioni è necessaria un'app per le funzioni. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione, il ridimensionamento e la condivisione delle risorse.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Si creerà ora una funzione nella nuova app per le funzioni.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Creare una funzione Trigger HTTP

1. Selezionare **Funzioni** nel menu a sinistra della finestra **Funzioni** e quindi **Aggiungi** nel menu in alto. 
 
1. Nella finestra **Nuova funzione**, selezionare **Trigger HTTP**.

    ![Scegliere la funzione Trigger HTTP](./media/functions-create-student-starter/function-app-select-http-trigger.png)

1. Nella finestra **Nuova funzione**, accettare il nome predefinito per la **nuova funzione** oppure immettere un nuovo nome. 

1. Scegliere **Anonimo** nell'elenco a discesa **Livello di autorizzazione**, quindi selezionare **Crea funzione**.

    A questo punto Azure crea la funzione Trigger HTTP. Ora è possibile eseguire la nuova funzione inviando una richiesta HTTP.

## <a name="test-the-function"></a>Testare la funzione

1. Nella nuova funzione Trigger HTTP, selezionare **Codice + test** dal menu a sinistra, quindi selezionare **Ottieni URL funzione** dal menu in alto.

    ![Selezionare Ottieni URL funzione](./media/functions-create-student-starter/function-app-select-get-function-url.png)

1. Nella finestra di dialogo **Ottieni URL funzione**, selezionare **Predefinito** dall'elenco a discesa, quindi l'icona **Copia negli Appunti**. 

    ![Creare l'URL della funzione dal portale di Azure](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

1. Incollare l'URL della funzione nella barra degli indirizzi del browser. Aggiungere il valore della stringa di query `?name=<your_name>` alla fine dell'URL e premere INVIO per eseguire la richiesta. 

    L'esempio seguente mostra la risposta nel browser:

    ![Risposta della funzione nel browser.](./media/functions-create-student-starter/function-app-browser-testing.png)

    L'URL della richiesta include una chiave necessaria per impostazione predefinita per accedere a una funzione tramite HTTP.

1. Quando viene eseguita la funzione, vengono scritte nei log informazioni di traccia. Per visualizzare l'output di traccia, tornare alla pagina **Codice + test** nel portale ed espandere la freccia **Log** nella parte inferiore della pagina.

   ![Visualizzatore log di Funzioni nel portale di Azure.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Funzionalità supportate in Azure for Students Starter

Azure for Students Starter offre l'accesso alla maggior parte delle funzionalità del runtime di Funzioni di Azure, ma con diverse limitazioni chiave, elencate di seguito:

* Il trigger HTTP è l'unico tipo di trigger supportato.
    * Sono supportati tutti i binding di input e output. [L'elenco completo è disponibile qui.](functions-triggers-bindings.md)
* Linguaggi supportati: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 e 10)
    * F# (.NET Core 2)
    * [Linguaggi supportati nei piani superiori](supported-languages.md)
* Windows è l'unico sistema operativo supportato.
* Il ridimensionamento è limitato a [una sola istanza di livello gratuito](https://azure.microsoft.com/pricing/details/app-service/windows/) eseguita per un massimo di 60 minuti al giorno. È possibile ridimensionare automaticamente in modalità serverless da 0 a 1 istanza quando si riceve traffico HTTP, ma non oltre.
* Sono supportate solo la [versione 2.x e successive](functions-versions.md) del runtime di Funzioni.
* Sono supportati tutti gli strumenti di sviluppo per la modifica e la pubblicazione di funzioni, inclusi VS Code, Visual Studio, l'interfaccia della riga di comando di Azure e il portale di Azure. Se non si vuole usare il portale, occorre prima creare un'app nel portale e quindi selezionarla come destinazione della distribuzione nello strumento che si preferisce.

## <a name="next-steps"></a>Passaggi successivi

È stata completata la creazione di un'app per le funzioni con una funzione Trigger HTTP semplice. È possibile ora esplorare gli strumenti locali, altri linguaggi, funzionalità di monitoraggio e integrazioni.

 * [Creare la prima funzione con Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Creare la prima funzione con Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Guida per gli sviluppatori JavaScript di Funzioni di Azure](./functions-reference-node.md)
 * [Usare Funzioni di Azure per connettersi al database SQL di Azure](./functions-scenario-database-table-cleanup.md)
 * [Trigger e associazioni HTTP di Funzioni di Azure](./functions-bindings-http-webhook.md)
 * [Monitorare Funzioni di Azure](./functions-monitoring.md)
