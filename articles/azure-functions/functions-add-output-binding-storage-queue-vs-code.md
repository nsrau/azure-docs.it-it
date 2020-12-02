---
title: Connettere Funzioni di Azure ad Archiviazione di Azure con Visual Studio Code
description: Informazioni su come connettere Funzioni di Azure a una coda di Archiviazione di Azure aggiungendo un binding di output nel progetto di Visual Studio Code.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-js
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 1729e0b27dd50519359cf6a39bfa81ba7b3b41e9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185148"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Connettere Funzioni di Azure ad Archiviazione di Azure con Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Questo articolo illustra come usare Visual Studio Code per connettere Archiviazione di Azure alla funzione creata nel precedente argomento di avvio rapido. Il binding di output che si aggiunge a questa funzione scrive i dati della richiesta HTTP in un messaggio della coda di Archiviazione code di Azure. 

La maggior parte dei binding richiede una stringa di connessione archiviata che verrà usata da Funzioni per accedere al servizio associato. Per semplicità, usare l'account di archiviazione creato con l'app per le funzioni. La connessione a questo account è già archiviata in un'impostazione dell'app denominata `AzureWebJobsStorage`.  

## <a name="configure-your-local-environment"></a>Configurare l'ambiente locale

Prima di iniziare questo articolo, è necessario soddisfare i requisiti seguenti:

* Installare l'[estensione Archiviazione di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).

* Installare [Azure Storage Explorer](https://storageexplorer.com/). Storage Explorer è uno strumento che consente di esaminare i messaggi della coda generati dal binding di output. Storage Explorer è supportato nei sistemi operativi macOS, Windows e Linux.

::: zone pivot="programming-language-csharp"
* Installare gli [strumenti dell'interfaccia della riga di comando di .NET Core](/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

::: zone pivot="programming-language-csharp"  
* Completare i passaggi descritti nella [parte 1 dell'argomento di avvio rapido su Visual Studio Code](create-first-function-vs-code-csharp.md). 
::: zone-end  
::: zone pivot="programming-language-javascript"  
* Completare i passaggi descritti nella [parte 1 dell'argomento di avvio rapido su Visual Studio Code](create-first-function-vs-code-node.md). 
::: zone-end   
::: zone pivot="programming-language-java"  
* Completare i passaggi descritti nella [parte 1 dell'argomento di avvio rapido su Visual Studio Code](create-first-function-vs-code-java.md). 
::: zone-end   
::: zone pivot="programming-language-typescript"  
* Completare i passaggi descritti nella [parte 1 dell'argomento di avvio rapido su Visual Studio Code](create-first-function-vs-code-typescript.md). 
::: zone-end   
::: zone pivot="programming-language-python"  
* Completare i passaggi descritti nella [parte 1 dell'argomento di avvio rapido su Visual Studio Code](create-first-function-vs-code-python.md). 
::: zone-end   
::: zone pivot="programming-language-powershell"  
* Completare i passaggi descritti nella [parte 1 dell'argomento di avvio rapido su Visual Studio Code](create-first-function-vs-code-powershell.md). 
::: zone-end   

Questo articolo presuppone che sia già stato eseguito l'accesso alla sottoscrizione di Azure da Visual Studio Code. È possibile accedere eseguendo `Azure: Sign In` dal riquadro comandi. 

## <a name="download-the-function-app-settings"></a>Scaricare le impostazioni dell'app per le funzioni

Nel [precedente argomento di avvio rapido](./create-first-function-vs-code-csharp.md) è stata creata un'app per le funzioni in Azure insieme all'account di archiviazione necessario. La stringa di connessione per questo account è archiviata in modo sicuro nelle impostazioni dell'app in Azure. In questo articolo verranno scritti messaggi in una coda di archiviazione dello stesso account. Per connettersi all'account di archiviazione durante l'esecuzione della funzione in locale, è necessario scaricare le impostazioni dell'app nel file local.settings.json. 

1. Premere F1 per aprire il riquadro comandi, quindi cercare ed eseguire il comando `Azure Functions: Download Remote Settings....`. 

1. Scegliere l'app per le funzioni creata nell'articolo precedente. Selezionare **Sì per tutti** per sovrascrivere le impostazioni locali esistenti. 

    > [!IMPORTANT]  
    > Il file local.settings.json contiene segreti, quindi non viene mai pubblicato e viene escluso dal controllo del codice sorgente.

1. Copiare il valore `AzureWebJobsStorage`, che corrisponde alla chiave per il valore della stringa di connessione dell'account di archiviazione. Usare questa connessione per verificare se il binding di output funziona come previsto.

## <a name="register-binding-extensions"></a>Registrare le estensioni delle associazioni

Dato che si usa un binding di output di Archiviazione code, è necessario che l'estensione dei binding di archiviazione sia installata prima di eseguire il progetto. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

Il progetto è stato configurato per l'uso di [bundle di estensione](functions-bindings-register.md#extension-bundles), che installano automaticamente un set predefinito di pacchetti di estensione. 

I bundle di estensione vengono abilitati nella radice del progetto in un file host.json come il seguente:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

::: zone pivot="programming-language-csharp"

Ad eccezione dei trigger HTTP e timer, i binding vengono implementati come pacchetti di estensione. Eseguire il comando [dotnet add package](/dotnet/core/tools/dotnet-add-package) seguente nella finestra del terminale per aggiungere il pacchetto di estensione di archiviazione nel progetto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage
```

::: zone-end

Ora è possibile aggiungere il binding di output di archiviazione nel progetto.

## <a name="add-an-output-binding"></a>Aggiungere un binding di output

In Funzioni ogni tipo di binding richiede di definire `direction`, `type` e un valore univoco `name` nel file function.json. Il modo in cui si definiscono questi attributi dipende dal linguaggio dell'app per le funzioni.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [functions-add-output-binding-java](../../includes/functions-add-output-binding-java.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Aggiungere il codice che usa l'associazione di output

Una volta definito il binding, è possibile usare il relativo valore `name` per accedervi come attributo nella firma della funzione. Usando un binding di output, non è necessario usare il codice di Azure Storage SDK per l'autenticazione, per recuperare un riferimento alla coda o per scrivere dati. Queste attività vengono eseguite automaticamente dal runtime di Funzioni e dal binding di output.

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"  

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

::: zone-end  

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-storage-binding-java-code](../../includes/functions-add-storage-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end  

<!--- Local testing section --->

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Quando il binding di output viene usato per la prima volta, nell'account di archiviazione viene creata dal runtime di Funzioni una nuova coda denominata **outqueue**. Usare Storage Explorer per verificare che siano stati creati la coda e un nuovo messaggio.

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end

### <a name="connect-storage-explorer-to-your-account"></a>Connettere Storage Explorer all'account

Ignorare questa sezione se Azure Storage Explorer è già stato installato e connesso all'account di archiviazione.

1. Eseguire lo strumento [Azure Storage Explorer], selezionare l'icona della connessione a sinistra, quindi selezionare **Aggiungi un account**.

    ![Aggiungere un account Azure a Microsoft Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. Nella finestra di dialogo **Connect** (Connessione) scegliere **Add an Azure account** (Aggiungi un account Azure), scegliere il proprio **ambiente di Azure** e selezionare **Sign in** (Accedi). 

    ![Accedere con l'account Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Dopo aver eseguito l'accesso all'account, verranno visualizzate le sottoscrizioni di Azure associate.

### <a name="examine-the-output-queue"></a>Esaminare la coda di output

1. In Visual Studio Code premere F1 per aprire il riquadro comandi, quindi cercare ed eseguire il comando `Azure Storage: Open in Storage Explorer` e scegliere il nome dell'account di archiviazione. L'account di archiviazione si apre in Azure Storage Explorer.  

1. Espandere il nodo **Code** e quindi selezionare la coda denominata **outqueue**. 

   La coda contiene il messaggio creato dall'associazione di output della coda quando è stata eseguita la funzione attivata da HTTP. Se la funzione è stata richiamata con il valore predefinito di `name`*Azure*, il messaggio della coda è *Name passed to the function: Azure*.

    ![Messaggio della coda visualizzato in Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Eseguire di nuovo la funzione e inviare un'altra richiesta. Nella coda verrà visualizzato un nuovo messaggio.  

Ora è il momento di ripubblicare l'app per le funzioni aggiornata in Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Ridistribuire e verificare l'app aggiornata

1. In Visual Studio Code premere F1 per aprire il riquadro comandi. Nel riquadro comandi cercare e selezionare `Azure Functions: Deploy to function app...`.

1. Scegliere l'app per le funzioni creata nel primo articolo. Poiché il progetto viene ridistribuito nella stessa app, selezionare **Distribuisci** per ignorare l'avviso sulla sovrascrittura di file.

1. Al termine della distribuzione, è possibile usare di nuovo cURL o un browser per testare la funzione ridistribuita. Come prima, aggiungere la stringa di query `&name=<yourname>` all'URL, come nell'esempio seguente:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. [Visualizzare di nuovo il messaggio nella coda di archiviazione](#examine-the-output-queue) per verificare che il binding di output genera anche in questo caso un nuovo messaggio nella coda.

## <a name="clean-up-resources"></a>Pulire le risorse

In Azure il termine *risorse* si riferisce ad app per le funzioni, funzioni, account di archiviazione e così via. Le risorse sono raggruppate in *gruppi di risorse*, ed è possibile eliminare tutti gli elementi in un gruppo eliminando il gruppo.

Per completare queste guide introduttive sono state create risorse. Per tali risorse potrebbero venire addebitati costi, a seconda dello [stato dell'account](https://azure.microsoft.com/account/) e dei [prezzi dei servizi](https://azure.microsoft.com/pricing/). Se le risorse non sono più necessarie, ecco come eliminarle:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Passaggi successivi

La funzione di trigger HTTP è stata aggiornata per scrivere dati in una coda di archiviazione. A questo punto, è possibile ottenere maggiori informazioni sullo sviluppo di funzioni con Visual Studio Code:

+ [Sviluppare Funzioni di Azure con Visual Studio Code](functions-develop-vs-code.md)

+ [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
::: zone pivot="programming-language-csharp"  
+ [Esempi di progetti di Funzioni completi in C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Esempi di progetti di Funzioni completi in JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Guida per gli sviluppatori JavaScript di Funzioni di Azure](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Esempi di progetti di Funzioni completi in Java](/samples/browse/?products=azure-functions&languages=java).

+ [Guida per sviluppatori Java per Funzioni di Azure](functions-reference-java.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Esempi di progetti di Funzioni completi in TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Guida per sviluppatori TypeScript di Funzioni di Azure](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Esempi di progetti di Funzioni completi in Python](/samples/browse/?products=azure-functions&languages=python)

+ [Guida per sviluppatori Python per Funzioni di Azure](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Esempi di progetti di Funzioni completi in PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Guida per sviluppatori PowerShell per Funzioni di Azure](functions-reference-powershell.md) 
::: zone-end