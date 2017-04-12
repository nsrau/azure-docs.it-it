---
title: CI/CD con il servizio contenitore di Azure e DC/OS | Microsoft Docs
description: Informazioni su come automatizzare completamente lo sviluppo e la distribuzione di un&quot;applicazione Docker multi-contenitore in un cluster del servizio contenitore di Azure che esegue DC/OS.
services: container-service
documentationcenter: 
author: spboyer
manager: wpickett
editor: 
tags: acs, azure-container-service
keywords: Docker, Contenitori, Micro-servizi, Mesos, Azure
ms.assetid: b16b767a-2eaa-418a-becc-8c032713a1f2
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: johnsta
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: acfba48585b960a1ce39b77e35d292189aa9732b
ms.lasthandoff: 04/03/2017


---

# <a name="continuous-integration-and-deployment-of-multi-container-docker-applications-to-azure-container-service"></a>Integrazione e distribuzione continue di applicazioni Docker multi-contenitore nel servizio contenitore di Azure 
In questa esercitazione viene illustrato come automatizzare completamente lo sviluppo e la distribuzione di un'applicazione Docker multi-contenitore in un cluster del servizio contenitore di Azure che esegue DC/OS. Benché i vantaggi dell'integrazione e della distribuzione continue siano noti, è necessario valutare alcuni aspetti specifici durante l'integrazione di contenitori nel flusso di lavoro. Usando il nuovo registro contenitori di Azure e i comandi dell'interfaccia della riga di comando, è possibile configurare un flusso end-to-end personalizzabile.

## <a name="get-started"></a>Introduzione
È possibile eseguire questa esercitazione in OS X, Windows o Linux.
- È necessaria una sottoscrizione di Azure. Se non si ha un account, è possibile [iscriversi per ottenere un account](https://azure.microsoft.com/).
- Installare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2).

## <a name="what-well-create"></a>Risultati della procedura
Ecco alcuni aspetti principali dell'app e del rispettivo flusso di distribuzione in fase di configurazione:
* **L'applicazione è costituita da più servizi**. Asset di Docker, Dockerfile e docker-compose.yml, per definire i servizi nell'app, ognuno dei quali è in esecuzione in contenitori separati. Ciò consente la scalabilità indipendente di alcune parti dell'app e ogni servizio può essere scritto in un linguaggio di programmazione e in un framework diverso. Il codice dell'app può essere ospitato in uno o più repository Git di origine. Gli strumenti supportano attualmente GitHub o Visual Studio Team Services.

* L'app viene eseguita in un **cluster del servizio contenitore di Azure configurato con DC/OS**. L'agente di orchestrazione del contenitore può gestire l'integrità del cluster e assicurare l'esecuzione continua del numero necessario di istanze del contenitore. 

* Il processo di **sviluppo e distribuzione di immagini del contenitore è completamente automatico, senza tempi di inattività**, in modo da consentire agli sviluppatori del team di eseguire il 'git push' in un ramo, per attivare automaticamente un processo di integrazione, ovvero creare e assegnare tag a immagini dei contenitori, eseguire test su ogni contenitore e quindi eseguire il push di queste immagini in un registro Docker privato. Le nuove immagini vengono quindi distribuite automaticamente in un ambiente di pre-produzione condiviso in un cluster del servizio contenitore di Azure per altri test.

* **Facilitare il rilascio da un ambiente al successivo**, ad esempio Sviluppo -> Test -> Staging -> Produzione. Ogni volta che si alza di livello a un ambiente downstream, non sarà necessario ricompilare le immagini dei contenitori per assicurare di distribuire le stesse immagini testate in un ambiente precedente. Questo processo corrisponde al concetto di *servizi non modificabili* e riduce la probabilità dell'inserimento di errori non rilevati in produzione.

* Per usare in modo ottimale le risorse di calcolo nel cluster del servizio contenitore di Azure, si usa lo stesso cluster per eseguire attività di compilazione, in modo da inserire completamente in contenitori le procedure di compilazione e distribuzione. Il cluster ospita anche gli ambienti multipli di sviluppo/test/produzione.


## <a name="create-an-azure-container-service-cluster-configured-with-dcos"></a>Creare un cluster del servizio contenitore di Azure configurato con DC/OS

>[!IMPORTANT]
> Per creare un cluster sicuro, passare il file di chiave SSH pubblica da usare per la chiamata ad `az acs create` . È possibile fare in modo che l'interfaccia della riga di comando di Azure versione 2.0 generi e passi contemporaneamente le chiavi tramite l'opzione `--generate-ssh-keys` oppure è possibile passare il percorso delle chiavi usando l'opzione `--ssh-key-value`. Il percorso predefinito in Linux è `~/.ssh/id_rsa.pub` e in Windows è `%HOMEPATH%\.ssh\id_rsa.pub`, ma è possibile modificarlo.
<!---Loc Comment: What do you mean by "you pass your SSH public key file to pass"? Thank you.--->
> Per creare file di chiave SSH pubblica e privata in Linux, vedere [Creare chiavi SSH in Linux e Mac](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fcontainer-services%2ftoc.json). 
> Per creare file di chiave SSH pubblica e privata in Windows, vedere [Creare chiavi SSH in Windows](../virtual-machines/linux/ssh-from-windows.md?toc=%2fazure%2fcontainer-services%2ftoc.json). 

1. Digitare prima di tutto il comando [az login](/cli/azure/#login) in una finestra del terminale per accedere alla sottoscrizione di Azure con l'interfaccia della riga di comando di Azure: 

    `az login`

1. Creare un gruppo di risorse in cui inserire il cluster tramite [az group create](/cli/azure/group#create):
    
    `az group create --name myacs-rg --location westus`

    È consigliabile specificare l'[area del data center di Azure](https://azure.microsoft.com/regions) più vicino. 

1. Creare un cluster del servizio contenitore di Azure con impostazioni predefinite usando [az acs create](/cli/azure/acs#create) e passando il percorso al file di chiave SSH pubblica: 

    ```azurecli
    az acs create \
    --resource-group myacs-rg 
    --name myacs \
    --dns-prefix myacs \
    --ssh-key-value ~/.ssh/id_rsa.pub
    ```
    
Questo passaggio richiede alcuni minuti.  Il comando `acs create` restituisce informazioni sul cluster appena creato. In alternativa, è possibile elencare i cluster del servizio contenitore di Azure disponibili nella sottoscrizione con il comando `az acs list`. Per altre opzioni di configurazione del servizio contenitore di Azure, [leggere le informazioni sulla creazione e sulla configurazione di un cluster del servizio contenitore di Azure](container-service-deployment.md).

## <a name="set-up-sample-code"></a>Configurare il codice di esempio
Durante la creazione del cluster, è possibile configurare il codice di esempio da distribuire nel servizio contenitore di Azure.

1. Creare una copia tramite [fork](https://help.github.com/articles/fork-a-repo/) del repository GitHub di esempio, in modo da avere una copia personalizzata: [https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git](https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git). L'app è essenzialmente una versione multi-contenitore di "hello world".
1. Dopo avere creato un fork nell'account GitHub personale, clonare localmente il repository nel computer:

    ```bash
    git clone  https://github.com/your-github-account/container-service-dotnet-continuous-integration-multi-container.git
    cd container-service-dotnet-continuous-integration-multi-container
    ```
    
Ecco informazioni più approfondite sul codice:
* `/service-a` è un'app Web basata su Angular.js con back-end Node.js.
* `/service-b` è un servizio .NET Core e viene chiamato da `service-a` tramite REST.
* Sia `service-a` che `service-b` contengono un `Dockerfile` in ogni directory che descrive rispettivamente immagini di contenitori basate su Node.js e .NET Core. 
* `docker-compose.yml` dichiara il set di servizi da compilare e distribuire.
* Oltre a `service-a` e `service-b`, un terzo servizio denominato `cache` esegue una cache Redis che può essere usata da `service-a`. `cache` si differenzia dai primi due servizi perché il codice corrispondente non è disponibile nel repository di origine. Si recupera invece un'immagine `redis:alpine` già pronta dall'hub Docker e la si distribuisce nel servizio contenitore di Azure.
* `/service-a/server.js` contiene codice in cui `service-a` chiama `service-b` e `cache`. Si noti che il codice `service-a` fa riferimento a `service-b` e `cache` in base al rispettivo nome specificato in `docker-compose.yml`. Se si eseguono questi servizi nel computer locale tramite `docker-compose`, Docker assicura che i servizi sono tutti in rete in modo appropriato per consentire l'individuazione reciproca tramite nome. L'esecuzione dei servizi in un ambiente cluster con rete con bilanciamento del carico la rende molto più complessa rispetto all'esecuzione locale. I comandi dell'interfaccia della riga di comando di Azure configurano un flusso di integrazione e distribuzione continue che assicura l'esecuzione continua del codice lineare di individuazione di servizi così com'è nel servizio contenitore di Azure. 

    ![Panoramica dell'app di esempio multi-contenitore](media/container-service-setup-ci-cd/multi-container-sample-app-overview.PNG)

## <a name="set-up-continuous-integration-and-deployment"></a>Configurare l'integrazione e la distribuzione continue
1. Assicurare che il cluster del servizio contenitore di Azure sia pronto: eseguire [az acs list](/cli/azure/acs#list) e confermare che il cluster del servizio contenitore di Azure sia elencato. Nota: il servizio contenitore di Azure deve eseguire DC/OS 1.8 o versione successiva.

1. [Creare un token di accesso GitHub personale](https://help.github.com/articles/creating-an-access-token-for-command-line-use/), concedendo al token almeno l'ambito `repo`.  Occorre di ricordare di copiare il token negli Appunti, perché verrà usato nel comando successivo per configurare un [webhook](https://help.github.com/articles/about-webhooks/) nel repository GitHub. 

1. Impostare la directory corrente sulla radice del repository di origine clonato e creare una pipeline di compilazione e versione, usando il token _&lt;GitHubPersonalAccessToken&gt; appena creato:
    
    `cd container-service-dotnet-continuous-integration-multi-container`

    ```azurecli
    az container release create \
    --target-name myacs \
    --target-resource-group myacs-rg \
    --remote-access-token <GitHubPersonalAccessToken>
    ```

    Dove `--target-name` è il nome del cluster del servizio contenitore di Azure e `--target-resource-group` è il nome del gruppo di risorse del cluster del servizio contenitore di Azure.

Alla prima esecuzione il completamento di questo comando può richiedere circa un minuto. Dopo il completamento, vengono restituite informazioni importanti sulla pipeline di compilazione e versione creata:
* `sourceRepo`: viene configurato un [webhook](https://help.github.com/articles/about-webhooks/) per il repository di origine, in modo che la pipeline di compilazione e di versione venga attivata automaticamente a ogni inserimento di codice sorgente nella pipeline.  
* `vstsProject`: [Visual Studio Team Services](https://www.visualstudio.com/team-services/) (VSTS) è configurato per *gestire* il flusso di lavoro. Le attività effettive di compilazione e distribuzione vengono eseguite nei contenitori nel servizio contenitore di Azure. Per usare un account e un progetto di VSTS specifici, è possibile definirli usando i parametri `--vsts-account-name` e `--vsts-project-name`.
* `buildDefinition`: definisce le attività eseguite per ogni compilazione. Le immagini dei contenitori vengono prodotte per ogni servizio definito in docker-compose.yml e quindi ne viene eseguito il push in un registro contenitori Docker. 
* `containerRegistry`: il registro contenitori di Azure è un servizio gestito che esegue un registro contenitori Docker. Un nuovo registro contenitori di Azure viene creato con il nome predefinito. In alternativa, è possibile specificare un nome di registro contenitori di Azure tramite il parametro `--registry-name`.
* `releaseDefinition`: definisce le attività eseguite per ogni distribuzione. Viene eseguito il pull delle immagini di contenitori per i servizi definiti in docker-compose.yml dal registro contenitori e le immagini vengono distribuite nel cluster del servizio contenitore di Azure. Per impostazione predefinita, vengono creati tre ambienti: *Sviluppo*, *Test* e *Produzione*. La definizione di versione viene configurata per impostazione predefinita per la distribuzione automatica nell'ambiente di *Sviluppo* a ogni completamento corretto della compilazione. Una versione può essere alzata di livello manualmente a *Test* o *Produzione* senza che sia necessaria la ricompilazione. Il flusso predefinito può essere personalizzato in VSTS. 
* `containerService`: cluster del servizio contenitore di Azure di destinazione. Deve eseguire DC/OS 1.8.


Il frammento di codice seguente è un comando di esempio da digitare se si ha già un registro contenitori di Azure esistente denominato `myregistry`. Creare e compilare le definizioni di versione con un account VSTS all'indirizzo `myvstsaccount.visualstudio.com` e un progetto VSTS esistente `myvstsproject`:
        
```azurecli
az container release create \
--target-name myacs \
--target-resource-group myacs-rg \
--registry-name myregistry \
--vsts-account-name myvstsaccount \
--vsts-project-name myvstsproject \
--remote-access-token <GitHubPersonalAccessToken>
```

## <a name="view-deployment-pipeline-progress"></a>Visualizzare lo stato della pipeline di distribuzione
Dopo la creazione della pipeline, viene avviata automaticamente una prima compilazione e distribuzione. Le compilazioni successive vengono attivate ogni volta che viene eseguito il push di codice nel repository di origine. È possibile controllare lo stato di una compilazione e/o versione aprendo il browser in corrispondenza degli URL della definizione di compilazione o di versione.

È sempre possibile trovare l'URL della definizione di versione associato a un cluster del servizio contenitore di Azure eseguendo questo comando:

```azurecli
az container release list \
--target-name myacs \
--target-resource-group myacs-rg
``` 

![Compilazione di VSTS](media/container-service-setup-ci-cd/vsts-build.PNG)

*Screenshot di VSTS che mostra i risultati della integrazione continua dell'app multi-contenitore*

![Versione di VSTS](media/container-service-setup-ci-cd/vsts-release.PNG)

*Versione di docker-compose in VSTS con più ambienti*

## <a name="view-the-application"></a>Visualizzare l'applicazione
A questo punto l'applicazione è stata distribuita nell'ambiente di sviluppo condiviso e non è esposta pubblicamente. Usare nel frattempo il dashboard DC/OS per visualizzare e gestire i servizi e [creare un tunnel SSH verso gli endpoint correlati a DC/OS](https://azure.microsoft.com/documentation/articles/container-service-connect/) oppure eseguire un comando rapido fornito dall'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> Durante la prima distribuzione assicurarsi che la versione VSTS sia stata distribuita correttamente prima di continuare.

> [!NOTE]
> Solo Windows: è necessario configurare [Pageant](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) per completare questa sezione.
> 
>* Avviare *PuttyGen* e caricare la chiave SSH privata usata per creare il cluster del servizio contenitore di Azure (%homepath%\id_rsa).
>* Salvare la chiave SSH privata come `id_rsa.ppk` nella stessa cartella.
>* Avviare *Pageant*. Verrà iniziata l'esecuzione e verrà visualizzata un'icona nell'area di notifica in basso a destra.
>* Fare clic con il pulsante destro del mouse sull'icona dell'area di notifica e scegliere *Aggiungi chiave*.
>* Aggiungere il file `id_rsa.ppk`.
> 
> 

1. Aprire il dashboard DC/OS del cluster del servizio contenitore di Azure usando il comando rapido dell'interfaccia della riga di comando:
    
    `az acs dcos browse -g myacs-rg -n myacs`

    * `-g` è il nome del gruppo di risorse del cluster di destinazione del servizio contenitore di Azure.
    * `-n` è il nome del cluster di destinazione del servizio contenitore di Azure.
    * È possibile che venga richiesta la password dell'account locale, perché questo comando richiede privilegi di amministratore. Questo comando crea un tunnel SSH verso un endpoint DC/OS, apre il browser predefinito in corrispondenza di tale endpoint e configura temporaneamente il proxy Web del browser. 

    > [!TIP]
    > Se è necessario cercare il nome del cluster del servizio contenitore di Azure, è possibile elencare tutti i cluster del servizio contenitore di Azure disponibili nella sottoscrizione eseguendo `az acs list`. 

1. Nel dashboard DC/OS fare clic su **Servizi** nel menu di spostamento a sinistra ([http://localhost/#/services](http://localhost/#/services)). I servizi distribuiti tramite la pipeline vengono raggruppati in una cartella radice denominata *dev*, in base all'ambiente di sviluppo nella definizione di versione VSTS. 

![Interfaccia utente di Marathon](media/container-service-setup-ci-cd/marathon-ui.png)

È possibile eseguire molte operazioni utili nel dashboard DC/OS

* Verifica dello stato della distribuzione per ogni servizio
* Visualizzazione dei requisiti relativi a CPU e memoria
* Visualizzazione dei log
* Aumento del numero di istanze per ogni servizio

**Per visualizzare l'applicazione Web per service-a**: partire dalla cartella radice *dev*, quindi eseguire il drill-down della gerarchia di cartelle fino a raggiungere `service-a`. Questa visualizzazione elenca le attività in esecuzione (o istanze dei contenitori) per `service-a`.

![service-a](media/container-service-setup-ci-cd/service-a.png)

Fare clic su un'attività per aprire la rispettiva visualizzazione, quindi selezionare uno degli endpoint disponibili.

![Attività di service-a](media/container-service-setup-ci-cd/service-a-task.PNG)

Questa semplice app Web chiama `service-a`, che chiama `service-b` e restituisce un messaggio "hello world". Un contatore viene incrementato in Redis ogni volta che viene effettuata una richiesta.

![App Web service-a](media/container-service-setup-ci-cd/service-a-web-app.PNG)

### <a name="optional-reaching-a-service-from-the-command-line"></a>(Facoltativo) Raggiungere un servizio dalla riga di comando
Se si vuole raggiungere un servizio tramite curl dalla riga di comando:

1. Eseguire `az acs dcos browse --verbose -g myacs-rg -n myacs` e annotare la riga "Proxy running on <ip-address>:<port>" dopo avere immesso la password.
1. In una nuova finestra del terminale digitare:

    `export http_proxy=http://<web-proxy-service-ip>:<portnumber>`

    Ad esempio: `export http_proxy=http://127.0.0.1:55405`

1. È ora possibile eseguire il curl rispetto all'endpoint di servizio, `curl http://service-url`, dove `service-url` è l'indirizzo visualizzato quando si passa all'endpoint di servizio dall'interfaccia utente di Marathon. Per annullare la configurazione della variabile http_proxy dalla riga di comando, digitare `unset http_proxy`.
 

## <a name="scale-services"></a>Ridimensionare i servizi
È possibile ridimensionare i servizi nel dashboard DC/OS.
1. Passare all'applicazione nella sottocartella *dev*.
1. Passare il puntatore del mouse su `service-b`, fare clic sull'icona a forma di ingranaggio e selezionare **Ridimensiona**.

    ![Menu Azione](media/container-service-setup-ci-cd/marathon-ui-action-menu.PNG)

1. Aumentare il numero a 3 e fare clic su **Scale Service** (Ridimensiona servizio).

    ![Ridimensionare i servizi](media/container-service-setup-ci-cd/marathon-ui-scale-service.png)

1. Tornare all'app Web in esecuzione e fare ripetutamente clic sul pulsante *Say It Again* (Pronunciala di nuovo). Si noti che le chiamate a `service-b` iniziano a eseguire il round robin su una raccolta di nomi host, mentre la singola istanza di `service-a` continua a segnalare lo stesso host.   

## <a name="promote-a-release-to-downstream-environments-without-rebuilding-container-images"></a>Alzare di livello una versione in ambienti downstream senza ricompilare le immagini dei contenitori
La pipeline di versione VSTS configura tre ambienti per impostazione predefinita: *Sviluppo*, *Test* e *Produzione*. La distribuzione è stata eseguita nell'ambiente di *Sviluppo*. È ora possibile esaminare come alzare di livello una versione all'ambiente downstream successivo, *Test*, senza ricompilazione delle immagini di contenitori. Questo flusso di lavoro assicura che vengano distribuite esattamente le stesse immagini testate nell'ambiente precedente e corrisponde al concetto di *servizi non modificabili*, quindi riduce la probabilità dell'inserimento in produzione di errori non rilevati.

1. Nell'interfaccia utente Web di VSTS passare a **Versioni**

    ![Menu Versioni di VSTS](media/container-service-setup-ci-cd/vsts-releases-menu.PNG)

1. Aprire la versione più recente.

1. Nella barra dei menu della definizione di versione fare clic su **Distribuisci**, quindi selezionare **Test** come nuovo ambiente in cui si vuole eseguire la distribuzione per avviare una nuova distribuzione, che usa di nuovo le stesse immagini distribuite in precedenza nell'ambiente di *Sviluppo*. Fare clic su **Log** se si vogliono ottenere informazioni dettagliate sulla distribuzione.

    ![VSTS promuove la versione](media/container-service-setup-ci-cd/vsts-promote-release.PNG)

Al termine della distribuzione nell'ambiente *Test*, nell'interfaccia utente di Marathon viene visualizzata una nuova cartella radice denominata *test* che contiene i servizi in esecuzione per tale ambiente. 

![Sottocartelle per ogni ambiente in DC/OS](media/container-service-setup-ci-cd/marathon-ui-dev-test-environments.png)

## <a name="trigger-a-new-build-and-deployment"></a>Attivare una nuova compilazione e una nuova distribuzione
È possibile simulare lo scenario in cui uno sviluppatore del team esegue il push di una modifica del codice nel repository di origine.

1. Nell'editor di codice aprire `service-a/public/index.html`. 
1. Modificare questa riga di codice:

    `<h2>Server Says</h2>`

    In modo da ottenere un risultato simile a questo:

    `<h2>Server Says Hello</h2>`

1. Salvare il file, eseguire il commit e quindi eseguire il push della modifica del codice nel repository di origine.

    ```bash
    git commit -am 'updated title'
    git push
    ```

Il commit attiva automaticamente una nuova compilazione e una nuova versione da distribuire nell'ambiente di *Sviluppo*. I servizi negli ambienti downstream (*Test* o *Produzione*) vengono modificati solo quando si decide di alzare di livello una versione specifica in tale ambiente.

Se si apre la definizione di compilazione in VSTS, si otterrà un risultato simile a questo: 

![Nuova compilazione attivata da git push](media/container-service-setup-ci-cd/new-build.png)



## <a name="expose-public-endpoint-for-production"></a>Esporre l'endpoint pubblico per la produzione

1. Aggiungere il codice yaml seguente a un nuovo file denominato `docker-compose.env.production.yml` nella cartella radice del repository di origine. Verrà aggiunta un'etichetta che provoca l'esposizione di un endpoint pubblico per `service-a`. 
    
    ```yaml
    version: "2"
    services:
      service-a:
        labels:
          com.microsoft.acs.dcos.marathon.vhost: "<FQDN, or custom domain>"
    ```

    * Come valore dell'etichetta è possibile specificare l'URL del nome di dominio completo (FQDN) dell'agente del servizio contenitore di Azure oppure un dominio personalizzato, ad esempio app.contoso.com. Per trovare il nome di dominio completo dell'agente del servizio contenitore di Azure, eseguire il comando `az acs list`, quindi controllare la proprietà per `agentPoolProfiles.fqdn`. Ad esempio: `myacsagents.westus.cloudapp.azure.com`.
    * Per impostazione predefinita, l'applicazione di esempio è in ascolto sulla porta 80. Per coloro che hanno le proprie applicazioni docker in ascolto su altre porte, ad esempio `port 8080` o `443`, collegare il numero di porta al nome di dominio completo. Ad esempio: `myacsagents.westus.cloudapp.azure.com:8080`. Quando si tenta di accedere all'applicazione dall'esterno, è necessario tuttavia eseguire una query sulla porta 80.
    * Seguendo la convenzione relativa al nome file docker-compose.env.*nome-ambiente*.yml, queste impostazioni influiscono solo sull'ambiente indicato, in questo caso l'ambiente denominato *Produzione*. Esaminare la definizione di versione in VSTS. L'attività di distribuzione di ogni ambiente è configurata per la lettura da un file docker-compose il cui nome è basato su questa convenzione.

1. Eseguire il commit e il push del file nel repository di origine master per avviare un'altra compilazione.

    ```bash
    git add .
    git commit -am "expose public port for service-a"
    git push
    ```

1. Attendere il completamento della compilazione e della distribuzione dell'aggiornamento nell'ambiente di *Sviluppo*, quindi alzarlo di livello in *Test* e infine in *Produzione*. Per le finalità di questa esercitazione, è possibile eseguire la distribuzione direttamente in *Produzione*, ma è utile provare a eseguire la distribuzione solo nell'ambiente downstream successivo.

1. (Facoltativo) **Se è stato specificato un dominio personalizzato** per vhost, ad esempio app.contoso.com, aggiungere un record DNS nelle impostazioni del provider di dominio. Accedere all'interfaccia utente di amministrazione del provider di dominio e aggiungere il record DNS come indicato di seguito:

    * Tipo: CNAME
    * Host: dominio personalizzato, ad esempio app.contoso.com
    * Risposta: nome di dominio completo dell'agente del servizio contenitore di Azure, ad esempio myacsagents.westus.cloudapp.azure.com
    * TTL (facoltativo): in alcuni casi il provider di dominio consente di modificare la durata (TTL). Se si specifica un valore inferiore, l'aggiornamento del record DNS viene propagato più rapidamente.   

1. Dopo la distribuzione in *Produzione*, la versione risulta accessibile a tutti. Aprire il browser inserendo l'URL specificato per l'etichetta `com.microsoft.acs.dcos.marathon.vhost`. Nota: le versioni negli ambienti di pre-produzione continuano a essere private.

## <a name="summary"></a>Riepilogo
Congratulazioni. È stato illustrato come creare un cluster del servizio contenitore di Azure con DC/OS e configurare una pipeline di compilazione e distribuzione completamente automatizzata e inserita in contenitori per un'app multi-contenitore.

Vedere anche:
* **Ridimensionare gli agenti VSTS.** Se è necessaria una velocità effettiva maggiore per l'esecuzione di attività di compilazione e rilascio, è possibile aumentare il numero di istanze dell'agente VSTS. Passare a **Servizi** nel dashboard DC/OS, aprire la cartella vsts-agents e provare a modificare il numero di istanze dell'agente VSTS.
* **Integrare gli unit test.** Questo repository di GitHub illustra come eseguire unit test e test di integrazione nei contenitori e come includerli nelle attività di compilazione: [https://github.com/mindaro/sample-app](https://github.com/mindaro/sample-app). 
    * Suggerimento: esaminare i file `service-a/unit-tests.js`, `service-a/service-tests.js`, `docker-compose.ci.unit-tests.yml` e `docker-compose.ci.service-tests.yml` nel repository.

## <a name="clean-up"></a>Eseguire la pulizia
Per limitare gli addebiti per risorse di calcolo correlati a questa esercitazione, eseguire il comando seguente e annotare le risorse della pipeline di distribuzione correlate a un cluster del servizio contenitore di Azure:

```azurecli    
az container release list --resource-name myacs --resource-group myacs-rg
```

Eliminare il cluster del servizio contenitore di Azure:
1. Accedere al [portale di Azure](https://portal.azure.com)
1. Cercare il gruppo di risorse che contiene il cluster del servizio contenitore di Azure.
1. Aprire l'interfaccia utente del pannello del gruppo di risorse e fare clic su **Elimina** sulla barra dei comandi del pannello.

Eliminare il registro contenitori di Azure: nel portale di Azure cercare il registro contenitori di Azure ed eliminarlo. 

L'[account Visual Studio Team Services offre il livello di accesso Basic per i primi cinque utenti](https://azure.microsoft.com/en-us/pricing/details/visual-studio-team-services/), ma è possibile eliminare le definizioni di compilazione e di versione.

Eliminare la definizione di compilazione di VSTS:
        
1. Aprire l'URL della definizione di compilazione nel browser, quindi fare clic sul collegamento **Definizioni di compilazione** accanto al nome della definizione di compilazione attualmente visualizzata.
2. Fare clic sul menu Azione accanto alla definizione di compilazione da eliminare, quindi selezionare **Elimina la definizione**

`![Eliminare la definizione di compilazione di VSTS](media/container-service-setup-ci-cd/vsts-delete-build-def.png) 

Eliminare la definizione di versione di VSTS:

1. Aprire l'URL della definizione di versione nel browser.
2. Nell'elenco di definizioni di rilascio disponibile a sinistra fare clic sulla freccia a discesa accanto alla definizione di versione da eliminare, quindi selezionare **Elimina**.

`![Eliminare la definizione di versione di VSTS](media/container-service-setup-ci-cd/vsts-delete-release-def.png)

