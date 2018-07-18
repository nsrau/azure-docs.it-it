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
ms.openlocfilehash: 371bb9195266f3511d115de2532e6b64f49ef26f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199297"
---
# <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

Questa guida contiene informazioni sui problemi comuni in cui si potrebbe incorrere quando si usa Azure Dev Spaces.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Errore "upstream connect error or disconnect/reset before headers" (errore di connessione a monte o disconnessione/reset prima delle intestazioni)
Questo errore potrebbe essere visualizzato quando si tenta di accedere al servizio. Ad esempio, quando si passa all'URL del servizio in un browser. 

### <a name="reason"></a>Motivo 
La porta del contenitore non è disponibile. Il motivo potrebbe essere uno dei seguenti: 
* Il contenitore è ancora in fase di compilazione e distribuzione. Ciò può avvenire se si esegue `azds up` o si avvia il debugger e quindi si tenta di accedere al contenitore prima che sia stato distribuito correttamente.
* La configurazione della porta non è coerente tra il Dockerfile, il grafico Helm e il codice server che consente di aprire una porta.

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
1. Se nella cartella del codice non è presente un file azds.yaml, eseguire `azds prep` per generare le risorse Docker, Kubernetes e Azure Dev Spaces.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Errore: "The pipe program 'azds' exited unexpectedly with code 126." (Il programma della pipe 'azds' è stato chiuso in modo imprevisto con codice 126.)
L'avvio del debugger di VS Code può talvolta generare questo errore. Questo è un problema noto.

### <a name="try"></a>Soluzione:
1. Chiudere e riaprire VS Code.
2. Premere di nuovo F5.


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
1. Modificare il file azds.yaml per impostare il contesto di compilazione a livello di soluzione.
2. Modificare il Dockerfile e i file Dockerfile.develop in modo che facciano riferimento ai file csproj in modo corretto, nel rispetto del nuovo contesto di compilazione.
3. Collocare un file con estensione dockerignore accanto al file con estensione sln e modificare in base alle esigenze.

È possibile trovare un esempio in https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftconnectedenvironmentregisteraction-authorization-error"></a>Errore di autorizzazione "Microsoft.ConnectedEnvironment/register/action"
Si potrebbe verificare l'errore seguente quando si gestisce un Azure Dev Space e si lavora in una sottoscrizione di Azure per la quale non si dispone dell'accesso di tipo Proprietario o Collaboratore.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.ConnectedEnvironment/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Motivo
La sottoscrizione di Azure selezionata non ha registrato lo spazio dei nomi Microsoft.ConnectedEnvironment.

### <a name="try"></a>Soluzione:
Un utente con accesso Proprietario o Collaboratore per la sottoscrizione di Azure può eseguire il seguente comando dell'interfaccia della riga di comando di Azure per registrare manualmente lo spazio dei nomi Microsoft.ConnectedEnvironment:

```cmd
az provider register --namespace Microsoft.ConnectedEnvironment
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces non sembra utilizzare il Dockerfile esistente per compilare un contenitore 

### <a name="reason"></a>Motivo
Azure Dev Spaces può essere configurato per puntare a un Dockerfile specifico all'interno del progetto. Se sembra che Azure Dev Spaces non utilizzi il Dockerfile previsto per compilare i contenitori, potrebbe essere necessario indicare in modo esplicito a Azure Dev Spaces dove si trova il file. 

### <a name="try"></a>Soluzione:
Aprire il file `azds.yaml` generato da Azure Dev Spaces nel progetto. Usare la direttiva `configurations->develop->build->dockerfile` per puntare al Dockerfile che si desidera utilizzare:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```