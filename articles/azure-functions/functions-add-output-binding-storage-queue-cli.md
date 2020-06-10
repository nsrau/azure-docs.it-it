---
title: Connettere Funzioni di Azure ad Archiviazione di Azure con gli strumenti da riga di comando
description: Informazioni su come connettere Funzioni di Azure a una coda di Archiviazione di Azure aggiungendo un binding di output nel progetto da riga di comando.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: tracking-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 8a5f9cd42d13b7cfb4bc6e95809112a3b7045fe4
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560108"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Connettere Funzioni di Azure ad Archiviazione di Azure con gli strumenti da riga di comando

In questo articolo si integra una coda di Archiviazione di Azure con la funzione e l'account di archiviazione creati nell'[argomento di avvio rapido precedente](functions-create-first-azure-function-azure-cli.md). È possibile ottenere questa integrazione usando un *binding di output* che scrive i dati di una richiesta HTTP in un messaggio della coda. Il completamento di questo articolo non comporta costi aggiuntivi oltre ai pochi centesimi di dollaro USA dell'argomento di avvio rapido precedente. Per altre informazioni sui binding, vedere [Concetti su trigger e binding di Funzioni di Azure](functions-triggers-bindings.md).

## <a name="configure-your-local-environment"></a>Configurare l'ambiente locale

Prima di iniziare, è necessario completare l'articolo [Avvio rapido: Creare un progetto di Funzioni di Azure dalla riga di comando](functions-create-first-azure-function-azure-cli.md). Se è già stata eseguita la pulizia delle risorse alla fine di tale articolo, eseguire di nuovo i passaggi per ricreare l'app per le funzioni e le risorse correlate in Azure.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

Per altre informazioni sui dettagli dei binding, vedere [Concetti su trigger e binding di Funzioni di Azure](functions-triggers-bindings.md) e la [configurazione dell'output della coda](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Aggiungere il codice per usare il binding di output

Una volta definita l'associazione della coda, è possibile aggiornare la funzione per ricevere il parametro di output `msg` e scrivere i messaggi nella coda.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

Si noti che *non* è necessario scrivere codice per l'autenticazione, ottenendo un riferimento alla coda o scrivendo dati. Tutte queste attività di integrazione vengono gestite facilmente nel runtime di Funzioni di Azure e nel binding di output della coda.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Visualizzare il messaggio nella coda di Archiviazione di Azure

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>Ridistribuire il progetto in Azure

Dopo aver verificato in locale che la funzione ha scritto un messaggio nella coda di Archiviazione di Azure, è possibile ridistribuire il progetto per aggiornare l'endpoint in esecuzione in Azure.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
Nella cartella *LocalFunctionsProj* usare il comando [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) per ridistribuire il progetto, sostituendo `<APP_NAME>` con il nome dell'app.

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

Nella cartella del progetto locale usare il comando Maven seguente per ripubblicare il progetto:
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>Eseguire la verifica in Azure

1. Come nell'argomento di avvio rapido precedente, usare un browser o un CURL per testare la funzione ridistribuita.

    # <a name="browser"></a>[Browser](#tab/browser)
    
    Copiare l'**URL di richiamo** completo visualizzato nell'output del comando publish nella barra degli indirizzi di un browser, aggiungendo il parametro di query `&name=Functions`. Nel browser dovrebbe essere visualizzato un output simile a quello visualizzato quando è stata eseguita la funzione in locale.

    ![L'output della funzione eseguita in Azure in un browser](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    Eseguire [`curl`](https://curl.haxx.se/) con l'**URL di richiamo**, aggiungendo il parametro `&name=Functions`. L'output del comando dovrebbe essere il testo "Hello Functions".
    
    ![L'output della funzione eseguita in Azure con CURL](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Esaminare di nuovo la coda di archiviazione, come descritto nella sezione precedente, per verificare che contenga il nuovo messaggio scritto nella coda.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine, usare il comando seguente per eliminare il gruppo di risorse e tutte le risorse contenute per evitare in incorrere in ulteriori costi.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Passaggi successivi

La funzione di trigger HTTP è stata aggiornata per scrivere dati in una coda di archiviazione. Ora è possibile ottenere altre informazioni sullo sviluppo di Funzioni dalla riga di comando usando Core Tools e l'interfaccia della riga di comando di Azure:

+ [Usare Azure Functions Core Tools](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [Esempi di progetti di Funzioni completi in C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Esempi di progetti di Funzioni completi in JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Guida per gli sviluppatori JavaScript di Funzioni di Azure](functions-reference-node.md)  
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
+ [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

+ [Pagina dei prezzi di Funzioni](https://azure.microsoft.com/pricing/details/functions/)

+ [Stima dei costi del piano a consumo](functions-consumption-costs.md) 
