---
title: Procedura consigliate per la distribuzione
description: Informazioni sui meccanismi principali per la distribuzione nel servizio app Azure. Individuare raccomandazioni specifiche del linguaggio e altre avvertenze.
keywords: servizio app di Azure, app Web, distribuzione, distribuzione, pipeline, compilazione
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: addc4edba734c350a1e0e4246203c64315f345dd
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88081052"
---
# <a name="deployment-best-practices"></a>Procedure consigliate per la distribuzione

Ogni team di sviluppo dispone di requisiti specifici che possono rendere difficile l'implementazione di una pipeline di distribuzione efficiente in qualsiasi servizio cloud. Questo articolo presenta i tre componenti principali della distribuzione nel servizio app: origini di distribuzione, pipeline di compilazione e meccanismi di distribuzione. Questo articolo illustra anche alcune procedure consigliate e suggerimenti per gli stack di linguaggi specifici.

## <a name="deployment-components"></a>Componenti di distribuzione

### <a name="deployment-source"></a>Origine distribuzione

Un'origine di distribuzione è il percorso del codice dell'applicazione. Per le app di produzione, l'origine della distribuzione è in genere un repository ospitato dal software di controllo della versione, ad esempio [GitHub, Bitbucket o Azure Repos](deploy-continuous-deployment.md). Per gli scenari di sviluppo e test, l'origine della distribuzione può essere [un progetto nel computer locale](deploy-local-git.md). Il servizio app supporta anche [le cartelle OneDrive e Dropbox](deploy-content-sync.md) come origini di distribuzione. Sebbene le cartelle cloud possano facilitare l'introduzione al servizio app, non è in genere consigliabile usare questa origine per le applicazioni di produzione di livello aziendale. 

### <a name="build-pipeline"></a>Pipeline di compilazione

Una volta scelta un'origine di distribuzione, il passaggio successivo consiste nel scegliere una pipeline di compilazione. Una pipeline di compilazione legge il codice sorgente dall'origine della distribuzione ed esegue una serie di passaggi, ad esempio la compilazione di codice, minimizzazione HTML e JavaScript, l'esecuzione di test e la creazione di pacchetti di componenti, per ottenere l'applicazione in uno stato eseguibile. I comandi specifici eseguiti dalla pipeline di compilazione dipendono dallo stack della lingua. Queste operazioni possono essere eseguite in un server di compilazione, ad esempio Azure Pipelines, oppure eseguite localmente.

### <a name="deployment-mechanism"></a>Meccanismo di distribuzione

Il meccanismo di distribuzione è l'azione usata per inserire l'applicazione compilata nella directory */Home/site/wwwroot* dell'app Web. La directory */wwwroot* è una posizione di archiviazione montata condivisa da tutte le istanze dell'app Web. Quando il meccanismo di distribuzione inserisce l'applicazione in questa directory, le istanze riceveranno una notifica per sincronizzare i nuovi file. Il servizio app supporta i meccanismi di distribuzione seguenti:

- Endpoint Kudu: [Kudu](https://github.com/projectkudu/kudu/wiki) è lo strumento open source per la produttività degli sviluppatori che viene eseguito come processo separato nel servizio app di Windows e come secondo contenitore nel servizio app Linux. Kudu gestisce le distribuzioni continue e fornisce endpoint HTTP per la distribuzione, ad esempio zipdeploy.
- FTP e WebDeploy: usando il [sito o le credenziali utente](deploy-configure-credentials.md), è possibile caricare i file [tramite FTP](deploy-ftp.md) o WebDeploy. Questi meccanismi non passano attraverso Kudu.  

Gli strumenti di distribuzione come Azure Pipelines, Jenkins e i plug-in dell'editor usano uno di questi meccanismi di distribuzione.

## <a name="use-deployment-slots"></a>Usare gli slot di distribuzione

Quando possibile, usare gli [slot di distribuzione](deploy-staging-slots.md) quando si distribuisce una nuova compilazione di produzione. Quando si usa un piano di servizio app standard o una soluzione migliore, è possibile distribuire l'app in un ambiente di staging, convalidare le modifiche ed eseguire test di fumo. Quando si è pronti, è possibile scambiare gli slot di gestione temporanea e di produzione. L'operazione di scambio consente di scaldare le istanze del ruolo di lavoro necessarie in base alla scala di produzione, eliminando così i tempi di inattività.

### <a name="continuously-deploy-code"></a>Distribuzione continua del codice

Se il progetto ha designato rami per i test, il controllo di qualità e la gestione temporanea, ognuno di questi branch deve essere distribuito continuamente in uno slot di staging. Questa operazione è nota come [progettazione Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow). Ciò consente alle parti interessate di valutare e testare facilmente il ramo distribuito. 

La distribuzione continua non dovrebbe mai essere abilitata per lo slot di produzione. Al contrario, il ramo di produzione (spesso Master) deve essere distribuito in uno slot non di produzione. Quando si è pronti per rilasciare il ramo di base, scambiarlo nello slot di produzione. Lo scambio in produzione, anziché la distribuzione nell'ambiente di produzione, impedisce tempi di inattività e consente di eseguire il rollback delle modifiche eseguendo di nuovo lo swapping. 

![Visualizzazione di utilizzo slot](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Distribuire i contenitori in modo continuo

Per i contenitori personalizzati da Docker o altri registri contenitori, distribuire l'immagine in uno slot di staging e scambiarla in produzione per evitare tempi di inattività. L'automazione è più complessa della distribuzione del codice perché è necessario effettuare il push dell'immagine in un registro contenitori e aggiornare il tag immagine in webapp.

Per ogni ramo da distribuire in uno slot, configurare l'automazione per eseguire le operazioni seguenti in ogni commit nel ramo.

1. **Compilare e contrassegnare l'immagine**. Come parte della pipeline di compilazione, contrassegnare l'immagine con l'ID commit Git, il timestamp o altre informazioni di identificazione. È preferibile non usare il tag "Latest" predefinito. In caso contrario, è difficile tenere traccia del codice attualmente distribuito, rendendo molto più difficile il debug.
1. Eseguire **il push dell'immagine con tag**. Una volta che l'immagine è stata compilata e contrassegnata, la pipeline esegue il push dell'immagine nel registro contenitori. Nel passaggio successivo, lo slot di distribuzione effettuerà il pull dell'immagine con tag dal registro contenitori.
1. **Aggiornare lo slot di distribuzione con il nuovo tag di immagine**. Quando questa proprietà viene aggiornata, il sito riavvierà automaticamente e effettuerà il pull della nuova immagine del contenitore.

![Visualizzazione di utilizzo slot](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Di seguito sono riportati alcuni esempi per i Framework di automazione comuni.

### <a name="use-azure-devops"></a>Usare Azure DevOps

Il servizio app offre un [recapito continuo integrato](deploy-continuous-deployment.md) per i contenitori tramite il centro distribuzione. Passare all'app nel [portale di Azure](https://portal.azure.com/) e selezionare **centro distribuzione** in **distribuzione**. Seguire le istruzioni per selezionare il repository e il ramo. Verrà configurata una pipeline di compilazione e versione di DevOps per compilare, contrassegnare e distribuire automaticamente il contenitore quando viene eseguito il push di nuovi commit nel branch selezionato.

### <a name="use-github-actions"></a>Usare le azioni di GitHub

È anche possibile automatizzare la distribuzione del contenitore [con le azioni di GitHub](deploy-container-github-action.md).  Il file del flusso di lavoro seguente consente di compilare e contrassegnare il contenitore con l'ID commit, eseguirne il push in un registro contenitori e aggiornare lo slot del sito specificato con il nuovo tag di immagine.

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

### <a name="use-other-automation-providers"></a>Usare altri provider di automazione

I passaggi elencati in precedenza si applicano ad altre utilità di automazione, ad esempio CircleCI o Travis CI. Tuttavia, è necessario usare l'interfaccia della riga di comando di Azure per aggiornare gli slot di distribuzione con i nuovi tag di immagine nel passaggio finale. Per usare l'interfaccia della riga di comando di Azure nello script di automazione, generare un'entità servizio usando il comando seguente.

```shell
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

Nello script accedere usando `az login --service-principal` , fornendo le informazioni dell'entità. È quindi possibile usare `az webapp config container set` per impostare il nome del contenitore, il tag, l'URL del registro di sistema e la password del registro di sistema. Di seguito sono riportati alcuni collegamenti utili per costruire il processo di integrazione continua del contenitore.

- [Come accedere all'interfaccia della riga di comando di Azure in Circle CI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Considerazioni specifiche della lingua

### <a name="java"></a>Java

Usare l'API Kudu [zipdeploy/](deploy-zip.md) per la distribuzione di applicazioni jar e [wardeploy/](deploy-zip.md#deploy-war-file) per le app War. Se si usa Jenkins, è possibile usare tali API direttamente nella fase di distribuzione. Per altre informazioni, vedi [questo articolo](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Nodo

Per impostazione predefinita, Kudu esegue i passaggi di compilazione per l'applicazione Node ( `npm install` ). Se si usa un servizio di compilazione come Azure DevOps, la compilazione Kudu non è necessaria. Per disabilitare la compilazione Kudu, creare un'impostazione dell'app, `SCM_DO_BUILD_DURING_DEPLOYMENT` , con il valore `false` .

### <a name="net"></a>.NET 

Per impostazione predefinita, Kudu esegue i passaggi di compilazione per l'applicazione .NET ( `dotnet build` ). Se si usa un servizio di compilazione come Azure DevOps, la compilazione Kudu non è necessaria. Per disabilitare la compilazione Kudu, creare un'impostazione dell'app, `SCM_DO_BUILD_DURING_DEPLOYMENT` , con il valore `false` .

## <a name="other-deployment-considerations"></a>Altre considerazioni sulla distribuzione

### <a name="local-cache"></a>cache locale

Il contenuto del Servizio app di Azure viene memorizzato in Archiviazione di Azure e presentato in modo permanente come condivisione del contenuto. Tuttavia, alcune app necessitano solo di un archivio del contenuto a prestazioni elevate di sola lettura che possono essere eseguite con disponibilità elevata. Queste app possono trarre vantaggio dall'uso [della cache locale](overview-local-cache.md). La cache locale non è consigliata per i siti di gestione dei contenuti, ad esempio WordPress.

Usare sempre la cache locale in combinazione con gli [slot di distribuzione](deploy-staging-slots.md) per evitare tempi di inattività. Per informazioni sull'uso combinato di queste funzionalità, vedere [questa sezione](overview-local-cache.md#best-practices-for-using-app-service-local-cache) .

### <a name="high-cpu-or-memory"></a>CPU o memoria elevata

Se il piano di servizio app usa oltre il 90% della CPU o della memoria disponibile, la macchina virtuale sottostante potrebbe avere problemi di elaborazione della distribuzione. Quando si verifica questo problema, aumentare temporaneamente il numero di istanze per eseguire la distribuzione. Al termine della distribuzione, è possibile restituire il numero di istanze al valore precedente.

Per altre informazioni sulle procedure consigliate, vedere la pagina relativa alla [diagnostica del servizio app](https://docs.microsoft.com/azure/app-service/overview-diagnostics) per individuare le procedure consigliate di utilità pratica specifiche per la risorsa.

- Passare all'app Web nel [portale di Azure](https://portal.azure.com).
- Fare clic su **diagnostica e risoluzione dei problemi** nel percorso di spostamento a sinistra, che consente di aprire la diagnostica del servizio app.
- Scegliere riquadro **Best Practices** Homepage.
- Fare clic su procedure consigliate **per la disponibilità & le prestazioni** o le procedure consigliate **per la configurazione ottimale** per visualizzare lo stato corrente dell'app per quanto riguarda queste procedure consigliate.

È anche possibile usare questo collegamento per aprire direttamente la diagnostica del servizio app per la risorsa: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` .
