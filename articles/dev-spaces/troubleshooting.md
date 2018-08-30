---
title: Risoluzione dei problemi | Documentazione Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contenitori
manager: douge
ms.openlocfilehash: 001d58aa22d4fc52acebfc88ba07d2467c1be08e
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "42141565"
---
# <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

Questa guida contiene informazioni sui problemi comuni in cui si potrebbe incorrere quando si usa Azure Dev Spaces.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Errore "Failed to create Azure Dev Spaces controller" (Non è stato possibile creare il controller di Azure Dev Spaces)

In caso di problemi durante la creazione del controller, è possibile che venga visualizzato questo errore. Se si tratta di un errore temporaneo, è possibile correggerlo eliminando e ricreando il controller.

### <a name="try"></a>Soluzione:

Per eliminare il controller, usare l'interfaccia della riga di comando di Azure Dev Spaces. Non è possibile eseguire questa operazione in Visual Studio o in Cloud Shell. Per installare l'interfaccia della riga di comando di Azure Dev Spaces, installare innanzitutto l'interfaccia della riga di comando di Azure e quindi eseguire questo comando:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Eseguire quindi questo comando per eliminare il controller:

```cmd
azds remove -g <resource group name> -n <cluster name>
```

Per ricreare il controller è possibile usare l'interfaccia della riga di comando o Visual Studio. Seguire le istruzioni nelle esercitazioni come se si iniziasse per la prima volta.


## <a name="error-service-cannot-be-started"></a>Errore "Service cannot be started" (Impossibile avviare il servizio).

Si potrebbe verificare questo errore quando il codice del servizio non viene avviato. La causa è spesso nel codice utente. Per ottenere altre informazioni di diagnostica, apportare le modifiche seguenti ai comandi e alle impostazioni:

### <a name="try"></a>Soluzione:

Sulla riga di comando:

Quando si usa _azds.exe_, usare l'opzione della riga di comando --verbose e usare l'opzione della riga di comando --output per specificare il formato di output.
 
    ```cmd
    azds up --verbose --output json
    ```

In Visual Studio:

1. Aprire **Strumenti > Opzioni** e in **Projects and Solutions** (Progetti e soluzioni), scegliere e **Build and Run** (Compila ed esegui).
2. Modificare le impostazioni per **MSBuild project build output verbosity** (Livello di dettaglio output di compilazione progetto MSBuild) in **Detailed** (Dettagliato) oppure in **Diagnostic** (Diagnostica).

    ![Screenshot della finestra di dialogo Strumenti > Opzioni](media/common/VerbositySetting.PNG)
    
## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Risoluzione dei nomi DNS non completa l'operazione per un URL pubblico associato al servizio Dev Spaces

In questo caso, potrebbe essere visualizzato un errore "Impossibile visualizzare la pagina" o "Impossibile raggiungere il sito" nel browser Web quando si prova a connettersi all'URL pubblico associato al servizio Dev Spaces.

### <a name="try"></a>Soluzione:

È possibile usare il comando seguente per elencare tutti gli URL associati ai servizi Dev Spaces:

```cmd
azds list-uris
```

Se un URL mostra lo stato *In sospeso* significa che Dev Spaces è ancora in attesa di completamento della registrazione DNS. In alcuni casi, sono necessari alcuni minuti per completare l'operazione. Dev Spaces offre anche un tunnel localhost per ogni servizio, che è possibile usare durante l'attesa della registrazione DNS.

Se un URL rimane nello stato *In sospeso* per più di 5 minuti, potrebbe indicare un problema con il pod DNS esterno che crea l'endpoint pubblico e/o con il pod del controller di ingresso nginx che è responsabile dell'acquisizione dell'endpoint pubblico. È possibile usare i comandi seguenti per eliminare i POD. Questi verranno ricreati automaticamente.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Errore "Required tools and configurations are missing" (Gli strumenti e le configurazioni richiesti sono mancanti)

Questo errore può verificarsi quando si avvia VS Code: "[Azure Dev Spaces] Required tools and configurations to build and debug '[project name]' are missing". ([Azure Dev Spaces] Gli strumenti e le configurazioni richiesti per la compilazione di '[nome progetto]' sono mancanti).
Questo errore indica che azds.exe non è presente nella variabile di ambiente PATH, come illustrato in VS Code.

### <a name="try"></a>Soluzione:

Avviare VS Code da un prompt dei comandi in cui la variabile di ambiente PATH è impostata correttamente.

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Errore 'azds' non riconosciuto come comando interno o esterno, programma eseguibile o file batch
 
È possibile visualizzare questo errore se azds.exe non è installato o configurato correttamente.

### <a name="try"></a>Soluzione:

1. Controllare il percorso %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI (Preview) per individuare azds.exe. Se è presente, aggiungere tale percorso alla variabile di ambiente PATH.
2. Se azds.exe non è installato, eseguire il comando seguente:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Errore "upstream connect error or disconnect/reset before headers" (errore di connessione a monte o disconnessione/reset prima delle intestazioni)
Questo errore potrebbe essere visualizzato quando si tenta di accedere al servizio. Ad esempio, quando si passa all'URL del servizio in un browser. 

### <a name="reason"></a>Motivo 
La porta del contenitore non è disponibile. Questo problema può verificarsi perché: 
* Il contenitore è ancora in fase di compilazione e distribuzione. Il problema si può verificare se si esegue `azds up` o si avvia il debugger e quindi si tenta di accedere al contenitore prima che sia stato distribuito correttamente.
* La configurazione della porta non è coerente tra il _Dockerfile_, il grafico Helm e il codice server che consente di aprire una porta.

### <a name="try"></a>Soluzione:
1. Se il contenitore è in fase di compilazione o distribuzione, attendere 2-3 secondi e tentare di accedere nuovamente al servizio. 
1. Verificare la configurazione della porta. I numeri di porta specificati devono essere **identici** in tutti gli asset seguenti:
    * **Dockerfile:** specificato dall'istruzione `EXPOSE`.
    * **[Grafico Helm](https://docs.helm.sh):** specificato dai valori `externalPort` e `internalPort` per un servizio (spesso si trova in un file `values.yml`),
    * Qualsiasi porta da aprire nel codice applicazione, ad esempio in Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Il file di configurazione non è stato trovato
Si esegue `azds up` e viene visualizzato l'errore seguente: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Motivo
È necessario eseguire `azds up` dalla directory radice del codice da eseguire ed è necessario inizializzare la cartella del codice per l'esecuzione con Azure Dev Spaces.

### <a name="try"></a>Soluzione:
1. Impostare come directory corrente la cartella radice contenente il codice del servizio. 
1. Se nella cartella del codice non è presente un file _azds.yaml_, eseguire `azds prep` per generare le risorse Docker, Kubernetes e Azure Dev Spaces.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Errore: "The pipe program 'azds' exited unexpectedly with code 126." (Il programma della pipe 'azds' è stato chiuso in modo imprevisto con codice 126.)
L'avvio del debugger di VS Code può talvolta generare questo errore. Questo è un problema noto.

### <a name="try"></a>Soluzione:
1. Chiudere e riaprire VS Code.
2. Premere di nuovo F5.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Errore di debug 'Non è stato possibile trovare l'estensione del debugger per tipo: coreclr'
L'esecuzione del debugger di VS Code genera l'errore: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Motivo
Non si dispone dell'estensione di Visual Studio Code per C# installata nel computer di sviluppo che include il supporto del debug per.Net Core (CoreCLR).

### <a name="try"></a>Soluzione:
Installare l'[estensione di Visual Studio Code per C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Errore di debug "Configured debug type 'coreclr' is not supported" (Il tipo di debug configurato 'coreclr' non è supportato)
L'esecuzione del debugger di VS Code genera l'errore: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Motivo
Nel computer di sviluppo non è installata l'estensione VS Code per Azure Dev Spaces.

### <a name="try"></a>Soluzione:
Installare l'[estensione VS Code per Azure](get-started-netcore.md).

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Non è stato trovato il nome o il tipo spazio dei nomi "MyLibrary"

### <a name="reason"></a>Motivo 
Il contesto di compilazione è a livello di progetto/servizio per impostazione predefinita pertanto, qualsiasi progetto libreria usato non verrà trovato.

### <a name="try"></a>Soluzione:
Quali azioni è necessario eseguire:
1. Modificare il file _azds.yaml_ per impostare il contesto di compilazione a livello di soluzione.
2. Modificare il _Dockerfile_ e i file _Dockerfile.develop_ in modo che facciano riferimento ai file di progetto (_.csproj_) in modo corretto, nel rispetto del nuovo contesto di compilazione.
3. Collocare un file con estensione _.dockerignore_ accanto al file con estensione sln e modificare in base alle esigenze.

È possibile trovare un esempio in https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Errore di autorizzazione "Microsoft.DevSpaces/register/action"
Si potrebbe verificare l'errore seguente quando si gestisce un Azure Dev Space e si lavora in una sottoscrizione di Azure per la quale non si dispone dell'accesso di tipo Proprietario o Collaboratore.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Motivo
La sottoscrizione di Azure selezionata non ha registrato lo spazio dei nomi `Microsoft.DevSpaces`.

### <a name="try"></a>Soluzione:
Un utente con accesso Proprietario o Collaboratore per la sottoscrizione di Azure può eseguire questo comando dell'interfaccia della riga di comando di Azure per registrare manualmente lo spazio dei nomi `Microsoft.DevSpaces`:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces non sembra utilizzare il Dockerfile esistente per compilare un contenitore 

### <a name="reason"></a>Motivo
Azure Dev Spaces può essere configurato per puntare a un _Dockerfile_ specifico all'interno del progetto. Se sembra che Azure Dev Spaces non utilizzi il _Dockerfile_ previsto per compilare i contenitori, potrebbe essere necessario indicare in modo esplicito a Azure Dev Spaces dove si trova il file. 

### <a name="try"></a>Soluzione:
Aprire il file _azds.yaml_ generato da Azure Dev Spaces nel progetto. Usare la direttiva `configurations->develop->build->dockerfile` per puntare al Dockerfile che si desidera utilizzare:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```
