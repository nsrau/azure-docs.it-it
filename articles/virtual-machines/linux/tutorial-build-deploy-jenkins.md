---
title: Esercitazione - CI/CD da Jenkins in macchine virtuali di Azure con Azure DevOps Services | Microsoft Docs
description: In questa esercitazione viene descritto come configurare l'integrazione continua (CI) e la distribuzione continua (CD) di un'app Node.js usando Jenkins in macchine virtuali di Azure da Release Management in Visual Studio Team Services o Microsoft Team Foundation Server
author: tomarchermsft
manager: jpconnock
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: jenkins
ms.workload: infrastructure
ms.date: 07/31/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 42e1ede7c762fef1f33436ea4c757b0c0531a8f2
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828435"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-with-using-jenkins-and-azure-devops-services"></a>Esercitazione: Distribuire l'app su macchine virtuali Linux in Azure con Jenkins e Azure DevOps Services

L'integrazione continua (CI) e la distribuzione continua (CD) formano una pipeline con cui è possibile compilare, rilasciare e distribuire codice. Azure DevOps Services offre un set di strumenti di automazione di integrazione continua/distribuzione continua completo per la distribuzione in Azure. Jenkins è uno strumento di terze parti diffuso basato su server per CI/CD che offre anche l'automazione CI/CD. È possibile usare insieme Azure DevOps Services e Jenkins per personalizzare la distribuzione dell'app o del servizio cloud.

In questa esercitazione si compila un'app Web Node.js usando Jenkins. Usare quindi Azure DevOps per la distribuzione

in un [gruppo di distribuzione](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups/index?view=vsts) che contiene macchine virtuali Linux. Si apprenderà come:

> [!div class="checklist"]
> * Ottenere l'app di esempio.
> * Configurare i plug-in Jenkins.
> * Configurare un progetto Freestyle di Jenkins per Node.js.
> * Configurare Jenkins per l'integrazione con Azure DevOps Services.
> * Creare un endpoint di servizio di Jenkins.
> * Creare un gruppo di distribuzione per le macchine virtuali di Azure.
> * Creare una pipeline di versione in Azure Pipelines.
> * Eseguire distribuzioni manuali e attivate da CI.

## <a name="before-you-begin"></a>Prima di iniziare

* È necessario l'accesso a un server Jenkins. Se non si è ancora creato un server Jenkins, vedere [Creare un master Jenkins in una macchina virtuale di Azure](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Accedere all'organizzazione di Azure DevOps Services (**https://{yourorganization}.visualstudio.com**). 
  È possibile ottenere un'[organizzazione di Azure DevOps Services gratuita](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Per altre informazioni, vedere [Connettersi ad Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  È necessaria una macchina virtuale Linux per una destinazione di distribuzione.  Per altre informazioni, vedere [Creare e gestire VM Linux con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Aprire la porta 80 in ingresso per la macchina virtuale. Per altre informazioni, vedere [Creare gruppi di sicurezza di rete mediante il portale di Azure](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-the-sample-app"></a>Ottenere l'app di esempio

È necessario disporre di un'app da distribuire archiviata in un repository Git.
Per questa esercitazione, si consiglia di usare [questa app di esempio disponibile in GitHub](https://github.com/azure-devops/fabrikam-node). Questa esercitazione contiene uno script di esempio usato per installare Node.js e un'applicazione. Per usare il proprio repository, è consigliabile configurare un esempio simile.

Creare un fork di questa app e prendere nota del percorso (URL) per l'uso nei passaggi successivi di questa esercitazione. Per altre informazioni, vedere [Creare una copia tramite fork di un repository](https://help.github.com/articles/fork-a-repo/).    

> [!NOTE]
> L'app è stata compilata tramite [Yeoman](https://yeoman.io/learning/index.html). Usa Express, bower e grunt. Include alcuni pacchetti npm come dipendenze.
> L'esempio contiene anche uno script che configura Nginx e distribuisce l'app. Viene eseguito sulle macchine virtuali. In particolare, lo script:
> 1. Installa Node, Nginx e PM2.
> 2. Configura Nginx e PM2.
> 3. Avvia l'app Node.

## <a name="configure-jenkins-plug-ins"></a>Configurare i plug-in Jenkins

Prima di tutto, è necessario configurare due plug-in Jenkins: **NodeJS** e **VS Team Services Continuous Deployment**.

1. Aprire l'account Jenkins e selezionare **Manage Jenkins** (Gestisci Jenkins).
2. Nella pagina **Manage Jenkins** (Gestisci Jenkins) selezionare **Manage Plugins** (Gestisci plug-in).
3. Filtrare l'elenco per individuare il plug-in **NodeJS** e selezionare l'opzione **Install without restart** (Installa senza riavvio).
    ![Aggiunta del plug-in NodeJS a Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtrare l'elenco per trovare il plug-in **VS Team Services Continuous Deployment** (Distribuzione continua di VS Team Services) e selezionare l'opzione **Installa senza riavvio**.
5. Tornare al dashboard di Jenkins e selezionare **Manage Jenkins** (Gestisci Jenkins).
6. Selezionare **Global Tool Configuration** (Configurazione strumenti globale). Trovare **NodeJS** e selezionare **NodeJS installations** (Installazioni NodeJS).
7. Selezionare l'opzione **Install automatically** (Installa automaticamente) e quindi immettere un valore per **Name** (Nome).
8. Selezionare **Salva**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Configurare un progetto Freestyle di Jenkins per Node.js

1. Selezionare **Nuovo elemento**. Immettere un nome di elemento.
2. Selezionare **Freestyle project** (Progetto Freestyle). Selezionare **OK**.
3. Nella scheda **Source Code Management** (Gestione codice sorgente) selezionare **Git** e immettere i dettagli del repository e del ramo contenenti il codice dell'app.    
    ![Aggiungere un repository alla build](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. Nella scheda **Build Triggers** (Trigger build) selezionare **Poll SCM** (Polling SCM) e immettere la pianificazione `H/03 * * * *` per il polling delle modifiche al repository Git ogni tre minuti. 
5. Nella scheda **Build Environment** (Ambiente build) selezionare **Provide Node &amp; npm bin/ folder PATH** (Fornisci nodo e PERCORSO cartella npm bin/) e selezionare il valore **NodeJS Installation** (Installazione NodeJS). Lasciare **npmrc file** impostato su **use system default** (usa impostazioni predefinite di sistema).
6. Nella scheda **Build** (Compila) selezionare **Execute shell** (Esegui shell) e immettere il comando `npm install` per assicurarsi che tutte le dipendenze siano aggiornate.


## <a name="configure-jenkins-for-azure-devops-services-integration"></a>Configurare Jenkins per l'integrazione con Azure DevOps Services

> [!NOTE]
> Assicurarsi che il token di accesso personale usato per i passaggi seguenti contenga l'autorizzazione di *versione* (lettura, scrittura, esecuzione e gestione) in Azure DevOps Services.
 
1.  Creare un token di accesso personale nell'organizzazione di Azure DevOps Services, se non è già disponibile. Jenkins richiede questa informazione per accedere all'organizzazione di Azure DevOps Services. Assicurarsi di archiviare le informazioni del token per i passaggi successivi di questa sezione.
  
    Per informazioni su come generare un token, vedere [How do I create a personal access token for Azure DevOps Services?](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts) (Come creare un token di accesso personale per Azure DevOps Services).
2. Nella scheda **Post-build Actions** (Azioni post-compilazione) selezionare **Add post-build action** (Aggiungi azione post-compilazione). Selezionare **Archive the artifacts** (Archivia gli elementi).
3. Per **Files to archive** (File da archiviare) immettere `**/*` per includere tutti i file.
4. Per creare un'altra azione, selezionare **Add post-build action** (Aggiungi azione post-compilazione).
5. Selezionare **Trigger release in TFS/Team Services** (Attiva rilascio in TFS/Team Services). Immettere l'URI per l'organizzazione di Azure DevOps Services, ad esempio **https://{your-organization-name}.visualstudio.com**.
6. Immettere il nome del **progetto**.
7. Scegliere un nome per la pipeline di versione. Si crea questa pipeline di versione in un momento successivo in Azure DevOps Services.
8. Scegliere le credenziali per connettersi all'ambiente Azure DevOps Services o Team Foundation Server:
   - Lasciare vuoto **Username** (Nome utente) se si usa Azure DevOps Services. 
   - Immettere nome utente e password se si usa una versione locale di Team Foundation Server.    
   ![Configurazione delle azioni di post-compilazione in Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Salvare il progetto Jenkins.


## <a name="create-a-jenkins-service-endpoint"></a>Creare un endpoint servizio di Jenkins

Un endpoint di servizio consente ad Azure DevOps Services di connettersi a Jenkins.

1. Aprire la pagina **Servizi** in Azure DevOps Services, aprire l'elenco **Nuovo endpoint servizio** e selezionare **Jenkins**.
   ![Aggiungere un endpoint di Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Immettere un nome per la connessione.
3. Immettere l'URL del server Jenkins e selezionare l'opzione **Accetta i certificati SSL non attendibili**. Un esempio di URL è **http://{URLJenkinspersonale}.westcentralus.cloudapp.azure.com**.
4. Immettere nome utente e password per l'account Jenkins.
5. Selezionare **Verifica connessione** per assicurarsi che le informazioni siano corrette.
6. Selezionare **OK** per creare l'endpoint servizio.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Creare un gruppo di distribuzione per Macchine virtuali di Azure

È necessario un [gruppo di distribuzione](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) per registrare l'agente di Azure DevOps Services in modo che la pipeline di versione possa essere distribuita nella macchina virtuale. I gruppi di distribuzione facilitano la definizione di gruppi logici di computer di destinazione e l'installazione dell'agente necessario in ogni computer.

   > [!NOTE]
   > Nella procedura seguente assicurarsi di installare i prerequisiti e di *non eseguire lo script con privilegi sudo*.

1. Aprire la scheda **Versioni** nell'hub **Compilazione e versione**, quindi aprire **Gruppi di distribuzione** e selezionare **+ Nuovo**.
2. Immettere un nome per il gruppo di distribuzione e una descrizione facoltativa, Selezionare quindi **Crea**.
3. Scegliere il sistema operativo per la macchina virtuale di destinazione di distribuzione. Ad esempio selezionare **Ubuntu 16.04+** .
4. Selezionare **Usa un token di accesso personale nello script per l'autenticazione**.
5. Selezionare il collegamento **Prerequisiti di sistema**. Installare i prerequisiti per il sistema operativo.
6. Selezionare **Copia script negli appunti** per copiare lo script.
7. Accedere alla macchina virtuale di destinazione di distribuzione ed eseguire lo script. Non eseguire lo script con privilegi sudo.
8. Dopo l'installazione, vengono chiesti i tag dei gruppi di distribuzione. Accettare i valori predefiniti.
9. In Azure DevOps Services cercare la nuova macchina virtuale registrata in **Destinazioni** sotto **Gruppi di distribuzione**.

## <a name="create-an-azure-pipelines-release-pipeline"></a>Creare una pipeline di versione in Azure Pipelines

Una pipeline di versione specifica il processo usato da Azure Pipelines per distribuire l'app. In questo esempio si esegue uno script della shell.

Per creare la pipeline di versione in Azure Pipelines:

1. Aprire la scheda **Versioni** nell'hub **Compilazione e versione** e selezionare **Crea pipeline di versione**. 
2. Selezionare il modello **Vuoto** scegliendo l'avvio con un **processo vuoto**.
3. Nella sezione **Elementi** selezionare **+ Aggiungi elemento** e scegliere **Jenkins** per **Tipo di origine**. Selezionare la connessione all'endpoint servizio Jenkins, quindi selezionare il processo di origine Jenkins e selezionare **Aggiungi**.
4. Selezionare i puntini di sospensione accanto ad **Ambiente 1**. Selezionare **Aggiungi fase per gruppo di distribuzione**.
5. Scegliere il gruppo di distribuzione.
5. Selezionare **+** per aggiungere un'attività alla **fase gruppo di distribuzione**.
6. Selezionare l'attività **Script della shell** e selezionare **Aggiungi**. L'attività **Script della Shell** fornisce la configurazione di esecuzione di uno script in ogni server al fine di installare Node.js e avviare l'app.
8. Per **Percorso script** immettere **$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh**.
9. Selezionare **Avanzate** e quindi abilitare **Specifica directory di lavoro**.
10. Per **Directory di lavoro** immettere **$(System.DefaultWorkingDirectory)/Fabrikam-Node**.
11. Modificare il nome della pipeline di versione in quello specificato nella scheda **Post-build Actions** (Azioni post-compilazione) nella compilazione in Jenkins. Jenkins richiede questo nome per poter attivare una nuova versione quando vengono aggiornati gli elementi di origine.
12. Selezionare **Salva** e quindi **OK** per salvare la pipeline di versione.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Eseguire distribuzioni manuali e attivate da CI

1. Selezionare **+ Versione** e selezionare **Crea versione**.
2. Selezionare la build completata nell'elenco a discesa evidenziato e quindi **Accoda**.
3. Scegliere il collegamento di versione nel messaggio popup. Ad esempio:  "Versione **Versione-1** creata".
4. Aprire la scheda **Log** per osservare l'output della console della versione.
5. Nel browser aprire l'URL di uno dei server aggiunti al gruppo di distribuzione. Ad esempio, immettere **http://{indirizzo-ip-server-personale}** .
6. Passare al repository Git di origine e modificare il contenuto del titolo **h1** nel file app/views/index.jade con un testo modificato.
7. Eseguire il commit delle modifiche.
8. Dopo alcuni minuti, si noterà una nuova versione creata nella pagina **Versioni** di Azure DevOps. Aprire la versione per visualizzare la distribuzione in corso. Congratulazioni!

## <a name="troubleshooting-the-jenkins-plugin"></a>Risoluzione dei problemi del plug-in Jenkins

Se si rilevano bug con i plug-in Jenkins, segnalare un problema in [Jenkins JIRA](https://issues.jenkins-ci.org/) per il componente specifico.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata automatizzata la distribuzione di un'app in Azure usando Jenkins per la compilazione e Azure DevOps Services per la versione. Si è appreso come:

> [!div class="checklist"]
> * Creare l'app in Jenkins.
> * Configurare Jenkins per l'integrazione con Azure DevOps Services.
> * Creare un gruppo di distribuzione per le macchine virtuali di Azure.
> * Creare una pipeline di versione che configuri le macchine virtuali e distribuisca l'app.

Per informazioni su come distribuire uno stack LAMP (Linux, Apache, MySQL e PHP), passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Distribuire lo stack LAMP](tutorial-lamp-stack.md)
