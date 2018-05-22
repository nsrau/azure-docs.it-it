---
title: Esercitazione per l'integrazione di Funzioni di Azure con il servizio Azure SignalR | Microsoft Docs
description: Questa esercitazione spiega come usare Funzioni di Azure con il servizio Azure SignalR
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2018
ms.author: wesmc
ms.openlocfilehash: b1bb6b3fe545d5a42fa985ab85bd7a914128e56b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-integrate-azure-functions-with-azure-signalr-service"></a>Esercitazione: Integrare Funzioni di Azure con il servizio Azure SignalR

Questa esercitazione si basa sull'applicazione di chat room creata nell'esercitazione precedente. Se non sono stati completati gli esercizi [Creare una chat room con il servizio SignalR](signalr-quickstart-dotnet-core.md) e [Autenticazione del servizio Azure SignalR](./signalr-authenticate-oauth.md), completarli adesso. 

Uno scenario comune per le applicazioni in tempo reale è quello degli aggiornamenti del contenuto proveniente da un server che deve essere pubblicato in client Web. [Funzioni di Azure](../azure-functions/functions-overview.md) è un ottimo candidato per la generazione di questi aggiornamenti del contenuto. Un vantaggio fondamentale di usare Funzioni di Azure è la possibilità di eseguire il codice su richiesta senza doversi preoccupare dell'architettura di un'intera applicazione o dell'infrastruttura di esecuzione. Inoltre si paga solo per il tempo effettivo di esecuzione del codice.  

Normalmente questo scenario potrebbe costituire un problema quando si tenta di usare SignalR perché SignalR prova a mantenere una connessione tra client e server per eseguire il push degli aggiornamenti del contenuto. Poiché il codice viene eseguito solo su richiesta, non è possibile gestire una connessione. Tuttavia, il servizio Azure SignalR può supportare questo scenario perché gestisce le connessioni automaticamente in fase di esecuzione.

In questa esercitazione si userà Funzioni di Azure per generare messaggi mediante una funzione timer all'inizio di ogni minuto. La funzione pubblicherà i messaggi su tutti i client della chat creata nelle esercitazioni precedenti. Per altre informazioni sulle funzioni timer, vedere [Funzione timer](../azure-functions/functions-create-scheduled-function.md).

Per completare i passaggi descritti in questa guida di avvio rapido è possibile usare qualsiasi editor di codice. Tuttavia, [Visual Studio Code](https://code.visualstudio.com/) è un'ottima scelta per le piattaforme Windows, macOS e Linux.

Il codice per questa esercitazione è disponibile per il download nel [repository GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer).

![App di chat con messaggi del server](./media/signalr-integrate-functions/signalr-functions-complete.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una nuova funzione timer con Funzioni di Azure usando l'interfaccia della riga di comando di Azure.
> * Configurare la funzione timer per la distribuzione del repository Git locale.
> * Connettere il timer al servizio SignalR per eseguire il push degli aggiornamenti ogni minuto

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione, sono necessari i prerequisiti seguenti:

* [Git](https://git-scm.com/)
* [ASP.NET Core SDK](https://www.microsoft.com/net/download/windows) 
* [Azure Cloud Shell configurato](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* Scaricare o clonare il repository GitHub [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

È necessario creare un'app per le funzioni per definire l'ambiente di esecuzione per le funzioni. L'app per le funzioni consente anche di raggruppare più funzioni come unità logica per facilitare la gestione, la distribuzione e la condivisione delle risorse. Per altre informazioni, vedere [Creare la prima funzione usando l'interfaccia della riga di comando di Azure](../azure-functions/functions-create-first-azure-function-azure-cli.md).

In questa sezione si userà Azure Cloud Shell per creare una nuova app di Funzioni di Azure configurata per la distribuzione da un repository Git locale. 

Quando si creano le risorse dell'app per le funzioni, crearle nello stesso gruppo di risorse utilizzato nelle esercitazioni precedenti. Questo approccio facilita la gestione di tutte le risorse dell'esercitazione.

Copiare lo script seguente nell'editor di testo e sostituire i valori dei parametri variabili con i valori validi per le risorse. Copiare e incollare lo script aggiornato in Azure Cloud Shell, quindi premere **Invio** per creare un account di archiviazione e un'app per le funzioni.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your values from previous    === 
#=== tutorials.                                                   ===
#====================================================================
ResourceGroupName=SignalRTestResources
location=eastus

#====================================================================
#=== Update these variables for the new function app and storage  ===
#=== account.                                                     ===
#====================================================================
functionappName=mysignalfunctionapp
storageAccountName=mystorageaccount

# Create a storage account to hold function app code and settings
az storage account create --resource-group $ResourceGroupName \
--name $storageAccountName \
--location $location --sku Standard_LRS

# Create the function app
az functionapp create --resource-group $ResourceGroupName \
--name $functionappName \
--consumption-plan-location $location \
--storage-account $storageAccountName

```

| Parametro | Description |
| -------------------- | --------------- |
| ResourceGroupName | Questo nome di gruppo di risorse è stato suggerito nelle esercitazioni precedenti. È consigliabile mantenere tutte le risorse dell'esercitazione raggruppate insieme. Usare lo stesso gruppo di risorse utilizzato nelle esercitazioni precedenti. | 
| location | Aggiornare questa variabile allo stesso percorso utilizzato per creare il gruppo di risorse nelle esercitazioni precedenti. | 
| functionappName | Aggiornare questa variabile con un nome univoco per la nuova app per le funzioni. Ad esempio signalrfunctionapp22665120. | 
| storageAccountName | Immettere un nome per il nuovo account di archiviazione che dovrà contenere il codice e le impostazioni dell'app per le funzioni. | 



## <a name="configure-the-function-app"></a>Configurare l'app per le funzioni

In questa sezione si configurerà l'app per le funzioni con un'impostazione di app contenente la stringa di connessione per la risorsa servizio Azure SignalR. Il codice della funzione userà questa impostazione per connettersi e pubblicare messaggi nella chat room. Si configurerà anche l'app per le funzioni per la distribuzione da un repository Git locale.

Copiare lo script seguente e sostituire i valori dei parametri. Incollare lo script aggiornato in Azure Cloud Shell, quindi premere **Invio**.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your resources.              === 
#====================================================================
ResourceGroupName=SignalRTestResources
functionappName=mysignalfunctionapp

#========================================================================
#=== Replace this value with the connection string for your           ===
#=== SignalR Service resource.                                        ===
#========================================================================
connstring="Endpoint=<service_endpoint>;AccessKey=<access_key>;"

# Add the SignalR Service connection string app setting
az functionapp config appsettings set --resource-group $ResourceGroupName \
    --name $functionappName \
    --setting "Azure:SignalR:ConnectionString=$connstring"

# configure for deployment from a local Git repository
az functionapp deployment source config-local-git --name $functionappName \
    --resource-group $ResourceGroupName

```

| Parametro | Description |
| -------------------- | --------------- |
| ResourceGroupName | Questo nome di gruppo di risorse è stato suggerito nelle esercitazioni precedenti. È consigliabile mantenere tutte le risorse dell'esercitazione raggruppate insieme. Usare lo stesso gruppo di risorse utilizzato nelle esercitazioni precedenti. | 
| functionappName | Aggiornare questa variabile con un nome univoco per la nuova app per le funzioni. Ad esempio signalrfunctionapp22665120. | 
| connstring | Immettere la stringa di connessione per la risorsa servizio SignalR. È possibile recuperare questa stringa di connessione dalla pagina della risorsa servizio SignalR nel portale di Azure facendo clic su **Chiavi** sotto **IMPOSTAZIONI**. | 



Annotare l'URL di distribuzione Git restituito dall'ultimo comando. Questo URL verrà usato per distribuire il codice della funzione.


## <a name="the-timer-function"></a>La funzione timer

La funzione timer di esempio si trova nella directory di download */samples/Timer* o nel clone del repository github [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer). Il codice della funzione timer si trova in *TimerFunction.cs*. Questo codice usa la stringa di connessione archiviata con la chiave di configurazione predefinita (*Azure:SignalR:ConnectionString*) per creare un proxy per l'hub. Poiché il codice della funzione è in esecuzione sul lato server, non è necessario richiederlo per eseguire l'autenticazione come normale client. Il codice è considerato attendibile per usare la stringa di connessione. Usando il proxy dell'hub, il codice della funzione può chiamare uno dei metodi che sono stati definiti nell'hub. Il codice chiama il metodo `BroadcastMessage` per pubblicare un messaggio contenente l'ora corrente quando è stato attivato il trigger.

Il trigger per il codice della funzione è un *timerTrigger*, definito nelle associazioni in *TimerFunction/function.json*. Include un'[espressione CRON](https://wikipedia.org/wiki/Cron#CRON_expression) per impostare il trigger del timer all'inizio di ogni minuto.

```json
{
  "bindings": [
    {
      "type": "timerTrigger",
      "schedule": "0 * * * * *",
      "useMonitor": true,
      "runOnStartup": false,
      "name": "myTimer"
    }
  ],
  "disabled": false,
  "scriptFile": "../Timer.dll",
  "entryPoint": "Timer.TimerFunction.Run"
}
```


## <a name="building-the-timer-function"></a>Compilazione della funzione timer

Usare l'[interfaccia della riga di comando (CLI) di .NET Core](https://docs.microsoft.com/dotnet/core/tools/) nei passaggi seguenti per compilare la funzione e prepararne la distribuzione:

1. Passare alla sottodirectory di download */samples/Timer* o al clone del repository github [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples).

2. Ripristinare i pacchetti NuGet con il comando seguente:

        dotnet restore

3. Compilare l'app per le funzioni *Timer* con il comando seguente:

        msbuild /p:Configuration=Release


## <a name="create-and-deploy-the-local-git-repo"></a>Creare e distribuire il repository Git locale

1. In una shell Git passare alla sottodirectory di compilazione */samples/Timer/bin/Release/net461*.

        cd ./AzureSignalR-samples/samples/Timer/bin/Release/net461

2. Inizializzare la directory come un nuovo repository Git con il comando seguente:

        git init

3. Aggiungere un nuovo commit per tutti i file nella directory di compilazione.

        git add -A
        git commit -v -a -m "Initial Timer function commit"        

4. Aggiungere un endpoint remoto per l'URL di distribuzione Git annotato durante la configurazione dell'app per le funzioni:

        git remote add Azure <enter your Git deployment URL>

5. Distribuire il codice della funzione

        git push Azure master

   Dopo aver distribuito il codice, il timer si avvierà immediatamente ogni minuto per eseguire il codice.

## <a name="test-the-chat-app"></a>Testare l'app di chat

Passare all'applicazione di chat: la funzione timer appena creata adesso segnalerà l'ora all'inizio di ogni minuto.

![App di chat con messaggi del server](./media/signalr-integrate-functions/signalr-functions-complete.png)



## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole continuare a testare l'applicazione, è possibile mantenere le risorse che sono state create.

In caso contrario, se si è terminato il lavoro con l'applicazione di esempio, è possibile eliminare le risorse di Azure per evitare i costi correlati. 

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile e comporta l'eliminazione definitiva del gruppo di risorse e di tutte le risorse incluse nel gruppo. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se le risorse per questo esempio sono state create all'interno di un gruppo di risorse esistente che contiene anche elementi da mantenere, è possibile eliminare ogni elemento singolarmente dai rispettivi pannelli anziché eliminare il gruppo di risorse.
> 
> 

Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.

Nella casella di testo **Filtra per nome...** immettere il nome del gruppo di risorse. Le istruzioni di questo articolo usano un gruppo di risorse denominato *SignalRTestResources*. Nel gruppo di risorse nell'elenco dei risultati fare clic su **...**  quindi su **Elimina gruppo di risorse**.

   
![Delete](./media/signalr-integrate-functions/signalr-delete-resource-group.png)


Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere il nome del gruppo di risorse per confermare e fare clic su **Elimina**.
   
Dopo alcuni minuti il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come integrare Funzione di Azure per eseguire il push degli aggiornamenti ai client sulla base di trigger di Funzioni di Azure. Per altre informazioni sull'uso di Azure SignalR Server, continuare con gli esempi dell'interfaccia della riga di comando di Azure per il servizio SignalR.

> [!div class="nextstepaction"]
> [Esempi dell'interfaccia della riga di comando di Azure SignalR](./signalr-cli-samples.md)



