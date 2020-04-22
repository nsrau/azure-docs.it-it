---
title: Procedura consigliate per la distribuzione
description: Informazioni sui meccanismi chiave della distribuzione nel servizio app di Azure.Learn about the key mechanisms of deploying to Azure App Service. Trova consigli specifici per la lingua e altri avvertimenti.
keywords: servizio app di azure, app Web, distribuzione, distribuzione, pipeline, compilazione
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 4dd959d75fd582d787e68db4a415a4a694b9cda8
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770663"
---
# <a name="deployment-best-practices"></a>Procedure consigliate per la distribuzione

Ogni team di sviluppo ha requisiti unici che possono rendere difficile l'implementazione di una pipeline di distribuzione efficiente in qualsiasi servizio cloud. In questo articolo vengono presentati i tre componenti principali della distribuzione nel servizio app: origini di distribuzione, pipeline di compilazione e meccanismi di distribuzione. In questo articolo vengono inoltre illustrate alcune procedure consigliate e suggerimenti per stack di linguaggio specifici.

## <a name="deployment-components"></a>Componenti di distribuzione

### <a name="deployment-source"></a>Origine distribuzione

Un'origine di distribuzione è il percorso del codice dell'applicazione. Per le app di produzione, l'origine di distribuzione è in genere un repository ospitato da un software di controllo della versione, ad esempio [GitHub, BitBucket o Azure Repos](deploy-continuous-deployment.md). Per gli scenari di sviluppo e test, l'origine di distribuzione può essere [un progetto nel computer locale.](deploy-local-git.md) Il servizio app supporta anche le [cartelle OneDrive e Dropbox](deploy-content-sync.md) come origini di distribuzione. Mentre le cartelle cloud possono semplificare iniziare a usare il servizio app, in genere non è consigliabile usare questa origine per le applicazioni di produzione a livello aziendale. 

### <a name="build-pipeline"></a>Pipeline di compilazione

Dopo aver deciso un'origine di distribuzione, il passaggio successivo consiste nel scegliere una pipeline di compilazione. Una pipeline di compilazione legge il codice sorgente dall'origine di distribuzione ed esegue una serie di passaggi (ad esempio la compilazione di codice, la modifica di HTML e JavaScript, l'esecuzione di test e la creazione di pacchetti di componenti) per ottenere l'applicazione in uno stato di esecuzione. I comandi specifici eseguiti dalla pipeline di compilazione dipendono dallo stack del linguaggio. Queste operazioni possono essere eseguite in un server di compilazione, ad esempio le pipeline di Azure, o eseguite in locale.

### <a name="deployment-mechanism"></a>Meccanismo di distribuzione

Il meccanismo di distribuzione è l'azione utilizzata per inserire l'applicazione compilata nella directory */home/site/wwwroot* dell'app Web. La directory */wwwroot* è un percorso di archiviazione montato condiviso da tutte le istanze dell'app Web. Quando il meccanismo di distribuzione inserisce l'applicazione in questa directory, le istanze ricevono una notifica per sincronizzare i nuovi file. Il servizio app supporta i meccanismi di distribuzione seguenti:App Service supports the following deployment mechanisms:

- Endpoint Kudu: Kudu è lo strumento di produttività per sviluppatori open source che viene eseguito come processo separato nel servizio app di Windows e come secondo contenitore nel servizio app Linux.Kudu endpoints: [Kudu](https://github.com/projectkudu/kudu/wiki) is the open-source developer productivity tool that runs as a separate process in Windows App Service, and as a second container in Linux App Service. Kudu gestisce le distribuzioni continue e fornisce endpoint HTTP per la distribuzione, ad esempio zipdeploy.
- FTP e WebDeploy: utilizzando le credenziali del [sito o dell'utente,](deploy-configure-credentials.md)è possibile caricare i file [tramite FTP](deploy-ftp.md) o WebDeploy. Questi meccanismi non passano attraverso Kudu.  

Gli strumenti di distribuzione come Pipeline di Azure, Jenkins e i plug-in dell'editor usano uno di questi meccanismi di distribuzione.

## <a name="use-deployment-slots"></a>Usare gli slot di distribuzioneUse deployment slots

Quando possibile, usare [gli slot](deploy-staging-slots.md) di distribuzione quando si distribuisce una nuova build di produzione. Quando si usa un livello Piano di servizio app standard o versione superiore, è possibile distribuire l'app in un ambiente di gestione temporanea, convalidare le modifiche ed eseguire test di fumo. Quando si è pronti, è possibile scambiare gli slot di gestione temporanea e produzione. L'operazione di scambio riscalda le istanze di lavoro necessarie in modo che corrispondano alla scala di produzione, eliminando così i tempi di inattività.

### <a name="continuously-deploy-code"></a>Distribuzione continua del codice

Se il progetto dispone di rami designati per il test, il controllo di controllo e la gestione temporanea, ognuno di questi rami deve essere distribuito continuamente in uno slot di gestione temporanea. (Questa operazione è nota come [progettazione Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow). Ciò consente alle parti interessate di valutare e testare facilmente il ramo distribuito. 

La distribuzione continua non deve mai essere abilitata per lo slot di produzione. Al contrario, il ramo di produzione (spesso master) deve essere distribuito in uno slot non di produzione. Quando si è pronti a rilasciare il ramo di base, scambiarlo nello slot di produzione. Lo scambio nell'ambiente di produzione, anziché la distribuzione nell'ambiente di produzione, impedisce i tempi di inattività e consente di eseguire nuovamente il rollback delle modifiche scambiandolo nuovamente. 

![Immagine di utilizzo dello slot](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Distribuisci continuamente i contenitori

Per i contenitori personalizzati da Docker o altri registri contenitori, distribuire l'immagine in uno slot di gestione temporanea e scambiare nell'ambiente di produzione per evitare tempi di inattività. L'automazione è più complessa della distribuzione del codice perché è necessario eseguire il push dell'immagine in un registro contenitori e aggiornare il tag dell'immagine nella webapp.

Per ogni ramo che si desidera distribuire in uno slot, configurare l'automazione in modo che esegua le operazioni seguenti in ogni commit nel ramo.

1. **Compilare e contrassegnare l'immagine.** Come parte della pipeline di compilazione, contrassegnare l'immagine con l'ID di commit git, timestamp o altre informazioni identificabili. È consigliabile non utilizzare il tag predefinito "latest". In caso contrario, è difficile risalire a quale codice è attualmente distribuito, il che rende il debug molto più difficile.
1. **Spingere l'immagine con tag**. Una volta che l'immagine viene compilata e contrassegnata, la pipeline invia l'immagine nel registro contenitori. Nel passaggio successivo, lo slot di distribuzione estrarrà l'immagine con tag dal Registro di sistema del contenitore.
1. **Aggiornare lo slot di distribuzione con il nuovo tag immagine.** Quando questa proprietà viene aggiornata, il sito verrà riavviato automaticamente ed estrarla.

![Immagine di utilizzo dello slot](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Di seguito sono riportati esempi per i framework di automazione comuni.

### <a name="use-azure-devops"></a>Usare DevOps di AzureUse Azure DevOps

Il servizio app include [la distribuzione continua integrata](deploy-continuous-deployment.md) per i contenitori tramite il Centro distribuzione. Passare all'app nel portale di [Azure](https://portal.azure.com/) e selezionare **Centro distribuzione** in **Distribuzione**. Seguire le istruzioni per selezionare il repository e il ramo. Verrà configurata una pipeline di compilazione e rilascio DevOps per compilare, contrassegnare e distribuire automaticamente il contenitore quando vengono inviati nuovi commit nel ramo selezionato.

### <a name="use-github-actions"></a>Usare le azioni GitHubUse GitHub Actions

È anche possibile automatizzare la distribuzione del contenitore [con GitHub Actions](containers/deploy-container-github-action.md).  Il file del flusso di lavoro riportato di seguito consente di compilare e contrassegnare il contenitore con l'ID di commit, eseguirne il push in un registro contenitori e aggiornare lo slot del sito specificato con il nuovo tag di immagine.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@master

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>Utilizzare altri provider di automazione

I passaggi elencati in precedenza si applicano ad altre utilità di automazione, ad esempio CircleCI o Travis CI. Tuttavia, è necessario usare l'interfaccia della riga di comando di Azure per aggiornare gli slot di distribuzione con nuovi tag immagine nel passaggio finale. Per usare l'interfaccia della riga di comando di Azure nello script di automazione, generare un'entità servizio usando il comando seguente.

```shell
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

Nello script accedere utilizzando `az login --service-principal`, fornendo le informazioni dell'entità. È quindi `az webapp config container set` possibile utilizzare per impostare il nome del contenitore, il tag, l'URL del Registro di sistema e la password del Registro di sistema. Di seguito sono riportati alcuni collegamenti utili per la creazione del processo di contenitore CI.

- [Come accedere all'interfaccia della riga di comando di Azure in Circle CI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Considerazioni specifiche del linguaggio

### <a name="java"></a>Java

Usare kudu [zipdeploy/API](deploy-zip.md) per la distribuzione di applicazioni JAR e [wardeploy/](deploy-zip.md#deploy-war-file) per le app WAR. Se si usa Jenkins, è possibile usare tali API direttamente nella fase di distribuzione. Per altre informazioni, vedere [questo articolo](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Nodo

Per impostazione predefinita, Kudu esegue le`npm install`istruzioni di compilazione per l'applicazione Node ( ). Se si usa un servizio di compilazione, ad esempio Azure DevOps, la build Kudu non è necessaria. Per disabilitare la build Kudu, `SCM_DO_BUILD_DURING_DEPLOYMENT`creare un'impostazione dell'app, , con un valore di `false`.

### <a name="net"></a>.NET 

Per impostazione predefinita, Kudu esegue i passaggi`dotnet build`di compilazione per l'applicazione .NET ( ). Se si usa un servizio di compilazione, ad esempio Azure DevOps, la build Kudu non è necessaria. Per disabilitare la build Kudu, `SCM_DO_BUILD_DURING_DEPLOYMENT`creare un'impostazione dell'app, , con un valore di `false`.

## <a name="other-deployment-considerations"></a>Altre considerazioni sulla distribuzione

### <a name="local-cache"></a>cache locale

Il contenuto del Servizio app di Azure viene memorizzato in Archiviazione di Azure e presentato in modo permanente come condivisione del contenuto. Tuttavia, alcune app richiedono solo un archivio del contenuto ad alte prestazioni e di sola lettura che possono essere eseguite con disponibilità elevata. Queste app possono trarre vantaggio dall'utilizzo della [cache locale.](overview-local-cache.md) La cache locale non è consigliata per i siti di gestione dei contenuti come WordPress.

Utilizzare sempre la cache locale insieme agli slot di [distribuzione](deploy-staging-slots.md) per evitare tempi di inattività. Vedere [questa sezione](overview-local-cache.md#best-practices-for-using-app-service-local-cache) per informazioni sull'utilizzo di queste funzionalità insieme.

### <a name="high-cpu-or-memory"></a>CPU o memoria elevata

Se il piano di servizio app utilizza oltre il 90% della CPU o della memoria disponibile, la macchina virtuale sottostante potrebbe avere problemi durante l'elaborazione della distribuzione. In questo caso, aumentare temporaneamente il numero di istanze per eseguire la distribuzione. Al termine della distribuzione, è possibile riportare il numero di istanze al valore precedente.

Per altre informazioni sulle procedure consigliate, vedere [Diagnostica del servizio app](https://docs.microsoft.com/azure/app-service/overview-diagnostics) per trovare le procedure consigliate utilizzabili per la risorsa.

- Passare all'app Web nel portale di [Azure.](https://portal.azure.com)
- Fare clic su **Diagnostica e risolvi i problemi** nel riquadro di spostamento sinistro, che apre Diagnostica servizio app.
- Scegliere il riquadro della home page **Procedure consigliate.**
- Fare clic su **Procedure consigliate per** la disponibilità & prestazioni o Procedure consigliate per la configurazione **ottimale** per visualizzare lo stato corrente dell'app in relazione a queste procedure consigliate.

È inoltre possibile utilizzare questo collegamento per aprire `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`direttamente App Service Diagnostics per la risorsa: .
