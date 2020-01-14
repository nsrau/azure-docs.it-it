---
title: 'Esercitazione: CI/CD nelle VM di Azure con Azure Pipelines'
description: Questa esercitazione illustra come configurare l'integrazione continua (CI) e la distribuzione continua (CD) di un'app Node.js in VM di Azure usando la pipeline di Azure basata su YAML.
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: c9d8ec2ce78746352b1fc5d2f337ad8686213839
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75662481"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Esercitazione: Distribuire l'app su macchine virtuali Linux in Azure con Azure DevOps Services e Azure Pipelines

L'integrazione continua (CI) e la distribuzione continua (CD) formano una pipeline con cui è possibile compilare, rilasciare e distribuire codice dopo ogni commit. Questo documento contiene i passaggi associati alla configurazione di una pipeline CI/CD per eseguire distribuzioni in più computer usando Azure Pipelines.

Azure Pipelines offre un set completo di strumenti di automazione CI/CD per le distribuzioni in macchine virtuali, sia in locale che su qualsiasi cloud.

In questa esercitazione si configurerà una pipeline CI/CD basata su YAML per distribuire l'app in un [ambiente](https://docs.microsoft.com/azure/devops/pipelines/process/environments?view=azure-devops) di Azure Pipelines con macchine virtuali Linux come risorse, ognuna delle quali funge da server Web per l'esecuzione dell'app.

Si apprenderà come:

> [!div class="checklist"]
> * Ottenere un'app di esempio.
> * Creare una pipeline CI di Azure Pipelines basata su YAML per compilare l'app di esempio.
> * Creare un ambiente di Azure Pipelines per le macchine virtuali di Azure
> * Creare una pipeline CD di Azure Pipelines.
> * Eseguire distribuzioni manuali e attivate da CI.

## <a name="before-you-begin"></a>Prima di iniziare

* Accedere all'organizzazione di Azure DevOps Services ( **https://dev.azure.com/** ). 
  È possibile ottenere un'[organizzazione di Azure DevOps Services gratuita](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Per altre informazioni, vedere [Connettersi ad Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  È necessaria una macchina virtuale Linux per una destinazione di distribuzione.  Per altre informazioni, vedere [Creare e gestire VM Linux con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Aprire la porta 80 in ingresso per la macchina virtuale. Per altre informazioni, vedere [Creare gruppi di sicurezza di rete mediante il portale di Azure](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-your-sample-app-code"></a>Ottenere il codice dell'app di esempio

Se si ha già un'app in GitHub che si vuole distribuire, è possibile provare a creare una pipeline per tale codice.

Per i nuovi utenti, è invece preferibile iniziare con il codice di esempio. In questo caso, creare una copia tramite fork di questo repository in GitHub:

#### <a name="javatabjava"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic è un'applicazione [Java Spring Boot](https://spring.io/guides/gs/spring-boot) creata usando [Maven](https://spring.io/guides/gs/maven/).

#### <a name="javascripttabjava-script"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Questa app Node.js è stata compilata tramite [Yeoman](https://yeoman.io/learning/index.html). Usa Express, bower e grunt. Include alcuni pacchetti npm come dipendenze.
> L'esempio contiene anche uno script che configura Nginx e distribuisce l'app. Viene eseguito sulle macchine virtuali. In particolare, lo script:
> 1. Installa Node, Nginx e PM2.
> 2. Configura Nginx e PM2.
> 3. Avvia l'app Node.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Prerequisiti per le VM Linux

Le app di esempio menzionate in precedenza sono state testate in Ubuntu 16.04 ed è consigliabile usare la stessa versione della VM Linux per questo argomento di avvio rapido.
Seguire i passaggi aggiuntivi descritti di seguito in base allo stack di runtime usato per l'app.

#### <a name="javatabjava"></a>[Java](#tab/java)

- Per la distribuzione di app basate su Java Spring Boot e Spring Cloud, creare una VM Linux in Azure usando [questo](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) modello, che fornisce un runtime completamente supportato basato su OpenJDK.
- Per la distribuzione di servlet Java in server Tomcat, creare una VM Linux con Java 8 usando [questo](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) modello di Azure e [configurare Tomcat 9.x come servizio](https://tomcat.apache.org/tomcat-9.0-doc/setup.html).
- Per la distribuzione di app basate su Java EE, usare un modello di Azure per creare una [VM Linux + Java + WebSphere 9.x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90), una [VM Linux + Java + WebLogic 12.x](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleWebLogicServer12cEnterprise) o una [VM Linux + Java + WildFly/JBoss 14](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) 

#### <a name="javascripttabjava-script"></a>[JavaScript](#tab/java-script)

Per installare un'app JavaScript o un'app Node.js, è necessaria una VM Linux con server Web Nginx per distribuirla.
Se non si ha già una VM Linux con Nginx, crearne una ora in Azure usando la procedura descritta in [questo esempio](/azure/virtual-machines/linux/quick-create-cli).

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Creare un ambiente di Azure Pipelines per le macchine virtuali di Azure

Le macchine virtuali possono essere aggiunte come risorse all'interno di [ambienti](https://docs.microsoft.com/azure/devops/pipelines/process/environments) e possono essere destinate a distribuzioni in più computer. Le visualizzazioni della cronologia delle distribuzioni all'interno dell'ambiente forniscono la tracciabilità dalla macchina virtuale alla pipeline e quindi al commit.

È possibile creare un ambiente nell'hub "**Ambienti**" all'interno della sezione "**Pipeline**".
1.  Accedere all'organizzazione di Azure DevOps e passare al progetto.
2.  Nel progetto passare alla pagina **Pipeline**. Quindi scegliere **Ambienti** e fare clic su **Crea ambiente**. Specificare un valore per **Nome** (obbligatorio) e per **Descrizione** per l'ambiente.
3.  Scegliere **Macchine virtuali** come **Risorsa** da aggiungere all'ambiente e fare clic su **Avanti**.
4.  Scegliere il sistema operativo (Windows/Linux) e **copiare lo script di registrazione PS**. 
5.  A questo punto, eseguire lo script copiato come amministratore da un prompt dei comandi di PowerShell in ognuna delle VM di destinazione da registrare con questo ambiente.
    > [!NOTE]
    > - Il token di accesso personale dell'utente connesso è preinserito nello script che scade nello stesso giorno, rendendo lo script copiato inutilizzabile da quel momento in poi.
    > - Se nella VM è già in esecuzione un agente, specificare un nome univoco per l'agente per la registrazione con l'ambiente.
6.  Una volta registrata, la VM inizierà ad essere visualizzata come risorsa dell'ambiente nella relativa scheda "risorse".

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Per aggiungere altre VM, è possibile visualizzare e copiare di nuovo lo script facendo clic su "Aggiungi risorsa" e scegliendo "Macchine virtuali" come risorsa. Questo script rimane uguale per tutte le VM da aggiungere in questo ambiente. 
8.  Ogni computer interagisce con Azure Pipelines per coordinare la distribuzione dell'app.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. È possibile aggiungere tag alla VM come parte dello script di registrazione interattivo di PS oppure è anche possibile aggiungere/rimuovere gli stessi dalla visualizzazione risorse facendo clic sui tre puntini alla fine di ogni risorsa VM in questa visualizzazione.

   I tag assegnati consentono di limitare la distribuzione a specifiche macchine virtuali quando l'ambiente viene usato in un processo di distribuzione. Ogni tag può essere composto da un massimo di 256 caratteri, ma non è previsto un limite per il numero di tag da usare.

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>Definire la pipeline di compilazione CI

È necessaria una pipeline di compilazione di integrazione continua (CI) che pubblica l'applicazione Web, oltre a uno script di distribuzione che può essere eseguito localmente nel server Ubuntu. Configurare una pipeline di compilazione CI in base al runtime che si vuole usare. 

1. Accedere all'organizzazione di Azure DevOps e passare al progetto.

1. Nel progetto passare alla pagina **Pipeline**. Quindi scegliere l'azione per creare una nuova pipeline.

1. Eseguire i passaggi della procedura guidata selezionando prima di tutto **GitHub** come posizione del codice sorgente.

1. Si potrebbe essere reindirizzati a GitHub per l'accesso. In questo caso, immettere le credenziali di GitHub.

1. Quando viene visualizzato l'elenco dei repository, selezionare quello dell'app di esempio.

1. Azure Pipelines analizzerà il repository e consiglierà un modello di pipeline appropriato.

#### <a name="javatabjava"></a>[Java](#tab/java)

Selezionare il modello **starter** e copiare il frammento di codice YAML seguente per creare il progetto Java ed eseguire i test con Apache Maven:

```YAML
- job: Build
    displayName: Build Maven Project
    steps:
    - task: Maven@3
      displayName: 'Maven Package'
      inputs:
        mavenPomFile: 'pom.xml'
    - task: CopyFiles@2
      displayName: 'Copy Files to artifact staging directory'
      inputs:
        SourceFolder: '$(System.DefaultWorkingDirectory)'
        Contents: '**/target/*.?(war|jar)'
        TargetFolder: $(Build.ArtifactStagingDirectory)
    - upload: $(Build.ArtifactStagingDirectory)
      artifact: drop
```

Per altre informazioni, seguire la procedura descritta in [Compilare l'app Java con Maven](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/java).

#### <a name="javascripttabjava-script"></a>[JavaScript](#tab/java-script)

Selezionare il modello **starter** e copiare il frammento di codice YAML seguente per creare un progetto Node.js generale con npm.

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

Per altre informazioni, seguire la procedura descritta in [Compilare l'app Node.js con gulp](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/javascript).

- Esaminare la pipeline per verificarne il funzionamento. Assicurarsi che tutti gli input predefiniti siano appropriati per il codice.

- Selezionare **Salva ed esegui**, quindi **Esegui il commit direttamente nel ramo master** e infine di nuovo **Salva ed esegui**.

- Viene avviata una nuova esecuzione. Attendere che venga completata.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Definire i passaggi di CD per la distribuzione nella VM Linux

1. Modificare la pipeline precedente e includere un [processo di distribuzione](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs) facendo riferimento all'ambiente e alle risorse VM definite in precedenza usando la sintassi YAML seguente:

   ```YAML
   jobs:  
     - deployment: VMDeploy
       displayName: web
       environment:
         name:  <environment name>
         resourceType: VirtualMachine
         tags: web1
       strategy:
   ```
2. È possibile selezionare set specifici di macchine virtuali dell'ambiente per ricevere la distribuzione specificando i **tag** definiti per ogni macchina virtuale nell'ambiente.
[Qui](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema#deployment-job) è disponibile lo schema YAML completo per il processo di distribuzione.

3. È possibile specificare `runOnce` o `rolling` come strategia di distribuzione. 

   `runOnce` è la strategia di distribuzione più semplice, in cui tutti gli hook del ciclo di vita, in particolare `preDeploy` `deploy`, `routeTraffic` e `postRouteTraffic`, vengono eseguiti una sola volta. Viene quindi eseguita `on:` `success` o `on:` `failure`.

   Di seguito è riportato il frammento di codice YAML di esempio per `runOnce`:
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. Di seguito è riportato un esempio del frammento YAML che è possibile usare per definire una strategia di aggiornamenti in sequenza per un totale di 5 macchine virtuali di destinazione in ogni iterazione. `maxParallel` determinerà il numero di destinazioni in cui è possibile eseguire la distribuzione in parallelo. La selezione rappresenta il numero assoluto o la percentuale di destinazioni che devono rimanere disponibili in qualsiasi momento, escluse le destinazioni in cui viene eseguita la distribuzione. Viene inoltre usata per determinare le condizioni di esito positivo e negativo durante la distribuzione.

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 2  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   A ogni esecuzione di questo processo, la cronologia delle distribuzioni viene registrata nell'ambiente `<environment name>` in cui sono state create e registrate le VM.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>Eseguire la pipeline e ottenere le visualizzazioni di tracciabilità nell'ambiente
La visualizzazione delle distribuzioni dell'ambiente fornisce la tracciabilità completa dei commit e degli elementi di lavoro, oltre a una cronologia delle distribuzioni tra pipeline per ogni ambiente/risorsa.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Passaggi successivi
- È possibile procedere a [personalizzare la pipeline](https://docs.microsoft.com/azure/devops/pipelines/customize-pipeline) appena creata.
- Per informazioni sulle altre operazioni che è possibile eseguire nelle pipeline YAML, vedere le [informazioni di riferimento sullo schema YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema).
