---
title: CI/CD da Jenkins in macchine virtuali di Azure con VSTS | Microsoft Docs
description: Configurare l'integrazione continua (CI) e la distribuzione continua (CD) di un'app Node.js usando Jenkins in macchine virtuali di Azure da Release Management in Visual Studio Team Services (VSTS) o Microsoft Team Foundation Server (TFS)
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/19/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: d5c15d6ab36a8454d1c69bac498be89b990c7e33
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-vsts"></a>Distribuire l'app in macchine virtuali Linux usando Jenkins e VSTS

Integrazione continua (CI) e distribuzione continua (CD) sono una pipeline con cui è possibile compilare, rilasciare e distribuire codice. Visual Studio Team Services (VSTS) offre un set di strumenti di automazione CI/CD completo per la distribuzione in Azure. Jenkins è uno strumento di terze parti diffuso basato su server per CI/CD che offre anche l'automazione CI/CD. È possibile usare insieme entrambi gli strumenti per personalizzare la fornitura dell'app o del servizio cloud.

In questa esercitazione si userà Jenkins per creare un'**app Web Node.js** e VSTS o Team Foundation Server (TFS) per distribuirla in un [gruppo di distribuzione](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) contenente macchine virtuali Linux.

Si apprenderà come:

> [!div class="checklist"]
> * Ottenere l'app di esempio
> * Configurare i plug-in di Jenkins
> * Configurare un progetto Freestyle di Jenkins per Node.js
> * Configurare Jenkins per l'integrazione con VSTS
> * Creare un endpoint servizio di Jenkins
> * Creare un gruppo di distribuzione per le macchine virtuali di Azure
> * Creare una definizione di versione di VSTS
> * Eseguire distribuzioni manuali e attivate da CI

## <a name="before-you-begin"></a>Prima di iniziare

* È necessario l'accesso a un server Jenkins. Se non si è ancora creato un server Jenkins, vedere [Creare un master Jenkins in una macchina virtuale di Azure](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template). 

* Accedere al proprio account VSTS (`https://{youraccount}.visualstudio.com`). 
  È possibile ottenere un [account VSTS gratuito](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Per altre informazioni, vedere [Connettersi a VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).

*  È necessaria una macchina virtuale Linux per una destinazione di distribuzione.  Per altre informazioni, vedere [Creare e gestire VM Linux con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm).

*  Aprire la porta 80 in ingresso per la macchina virtuale.  Per altre informazioni, vedere [Creare gruppi di sicurezza di rete mediante il portale di Azure](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

## <a name="get-the-sample-app"></a>Ottenere l'app di esempio

È necessario disporre di un'app da distribuire archiviata in un repository Git.
Per questa esercitazione, si consiglia di usare [questa app di esempio disponibile da GitHub](https://github.com/azooinmyluggage/fabrikam-node).  Questa esercitazione contiene uno script di esempio usato per installare Node.js e un'applicazione.  Per usare il proprio repository, è consigliabile configurare un esempio simile.

1. Creare un fork di questa app e prendere nota del percorso (URL) per l'uso nei passaggi successivi di questa esercitazione.  Per altre informazioni, vedere [Creare una copia tramite fork di un repository](https://help.github.com/articles/fork-a-repo/)    

> [!NOTE]
> L'app è stata creata con [Yeoman](http://yeoman.io/learning/index.html), usa **Express**, **Bower** e **Grunt**, e ha alcuni pacchetti **npm** come dipendenze.
> L'esempio contiene anche uno script che configura Nginx e distribuisce l'app. Viene eseguito sulle macchine virtuali. In particolare, lo script installa Noe, Nginx e PM2, configura Nginx e PM2 e quindi avvia l'app Node.

## <a name="configure-jenkins-plugins"></a>Configurare i plug-in di Jenkins

In primo luogo è necessario configurare due plug-in di Jenkins per **NodeJS** e la **distribuzione continua di VS Team Services**.

1. Aprire l'account di Jenkins e scegliere **Manage Jenkins** (Gestisci Jenkins).
2. Nella pagina **Manage Jenkins** fare clic su **Manage Plugins** (Gestisci plug-in).
3. Filtrare l'elenco per individuare il plug-in **NodeJS** e scegliere l'opzione **Install without restart** (Installa senza riavvio).
    ![Aggiunta del plug-in NodeJS a Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtrare l'elenco per trovare il plug-in **VS Team Services Continuous Deployment** (Distribuzione continua di VS Team Services) e scegliere l'opzione **Installa senza riavvio**.
5. Tornare al dashboard di Jenkins e scegliere **Manage Jenkins** (Gestisci Jenkins).
6. Scegliere **Global Tool Configuration** (Configurazione strumenti globale).  Trovare **NodeJS** e fare clic su **NodeJS installations** (Installazioni NodeJS).
7. Abilitare l'opzione **Install automatically** (Installa automaticamente) e quindi immettere un valore per **Name** (Nome).
8. Fare clic su **Salva**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Configurare un progetto Freestyle di Jenkins per Node.js

1. Fare clic su **New Item** (Nuovo elemento).  Immettere un **nome di elemento**.
2. Scegliere **Freestyle project** (Progetto Freestyle).  Fare clic su **OK**.
3. Nella scheda **Source Code Managament** (Gestione codice sorgente) selezionare **Git** e immettere i dettagli del repository e del ramo contenenti il codice dell'app.    
    ![Aggiungere un repository alla build](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. Nella scheda **Build Triggers** (Trigger build) selezionare **Poll SCM** (Polling SCM) e immettere la pianificazione `H/03 * * * *` per il polling delle modifiche al repository Git ogni tre minuti. 
5. Nella scheda **Build Environment** (Ambiente build) selezionare **Provide Node &amp; npm bin/ folder PATH** (Fornisci nodo e PERCORSO cartella npm bin/) e selezionare il valore **NodeJS Installation** (Installazione NodeJS). Lasciare **npmrc file** impostato su "use system default" (usa impostazioni predefinite di sistema).
6. Nella scheda **Build** (Compila) scegliere **Execute shell** (Esegui shell) e immettere il comando `npm install` per assicurarsi che tutte le dipendenze siano aggiornate.


## <a name="configure-jenkins-for-vsts-integration"></a>Configurare Jenkins per l'integrazione con VSTS

  > [!NOTE]
  Assicurarsi che il token di accesso personale usato per i passaggi seguenti contenga l'**autorizzazione di rilascio (lettura, scrittura, esecuzione e gestione) in VSTS**.
 
1.  Creare un token di accesso personale nell'account VSTS, se non è già disponibile. Jenkins richiede questa informazione per accedere all'account VSTS.  Assicurarsi di **archiviare** le informazioni del token per i passaggi successivi di questa sezione.
  Vedere [How do I create a personal access token for VSTS and TFS](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) (Come creare un token di accesso personale per VSTS e TFS) per informazioni su come generarne uno.
2. Nella scheda **Post-build Actions** (Azioni post-compilazione) fare clic su **Add post-build action** (Aggiungi azione post-compilazione). Scegliere **Archive the artifacts** (Archivia gli elementi).
3. Per **Files to archive** (File da archiviare) immettere `**/*` per includere tutti i file.
4. Per creare un'altra azione, fare clic su **Add post-build action** (Aggiungi azione post-compilazione).
5. Scegliere **Trigger release in TFS/Team Services** (Attiva rilascio in TFS/Team Services) e immettere l'URI per l'account VSTS, ad esempio: `https://{your-account-name}.visualstudio.com`.
6. Immettere il nome in **Team Project** (Progetto team).
7. Scegliere un nome per la **definizione di versione**. Questa definizione di versione verrà creata più avanti in VSTS.
8. Scegliere le credenziali per connettersi all'ambiente VSTS o TFS.  Lasciare vuoto **Username** (Nome utente) se si usa VSTS.
   Immettere **nome utente e password** se si usa una versione locale di TFS.    
    ![Configurazione delle azioni di post-compilazione in Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. **Salvare** il progetto di Jenkins.

## <a name="create-a-jenkins-service-endpoint"></a>Creare un endpoint servizio di Jenkins

Un endpoint servizio consente a VSTS di connettersi a Jenkins.

1. Aprire la pagina **Servizi** in VSTS, aprire l'elenco **Nuovo endpoint servizio** e scegliere **Jenkins**.
    ![Aggiungere un endpoint di Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Immettere un nome per la connessione.
3. Immettere l'URL del server Jenkins e contrassegnare l'opzione **Accetta i certificati SSL non attendibili**.  Un URL di esempio è: `http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com`
4. Immettere **nome utente e password** per l'account Jenkins.
5. Scegliere **Verifica connessione** per assicurarsi che le informazioni siano corrette.
6. Scegliere **OK** per creare l'endpoint servizio.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Creare un gruppo di distribuzione per Macchine virtuali di Azure

È necessario un [gruppo di distribuzione](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) per registrare l'agente VSTS in modo che la definizione di versione possa eseguire la distribuzione nella macchina virtuale.  I gruppi di distribuzione facilitano la definizione di gruppi logici di computer di destinazione e l'installazione dell'agente necessario in ogni computer.

   > [!NOTE]
   > Nei passaggi seguenti assicurarsi di installare i prerequisiti e **non eseguire lo script con privilegi sudo**.

1. Aprire la scheda **Versioni** nell'hub **Compilazione e versione**, quindi aprire **Gruppi di distribuzione** e scegliere **+ Nuovo**.
2. Immettere un nome per il gruppo di distribuzione e una descrizione facoltativa,
   quindi scegliere **Crea**.
3. Scegliere il sistema operativo per la macchina virtuale di destinazione di distribuzione.  Scegliere ad esempio **Ubuntu 16.04+**.
4. Selezionare **Usa un token di accesso personale nello script per l'autenticazione**.
5. Selezionare il collegamento **Prerequisiti di sistema**.  Installare i prerequisiti per il sistema operativo.
6. Selezionare **Copia script negli appunti** per copiare lo script.
7. **Accedere** alla macchina virtuale di destinazione di distribuzione ed **eseguire** lo script.  **Non** eseguire lo script con privilegi sudo.
8. Dopo l'installazione, vengono chiesti i tag dei gruppi di distribuzione.  Accettare i valori predefiniti.
9. In VSTS cercare la nuova macchina virtuale registrata in **Destinazioni** sotto **Gruppi di distribuzione**.

## <a name="create-a-vsts-release-definition"></a>Creare una definizione di versione di VSTS

Una definizione di versione specifica il processo eseguito da VSTS per distribuire l'app.  In questo esempio si esegue uno script della shell.

Per creare una definizione di versione in VSTS:

1. Aprire **Versioni** nell'hub **Compilazione e versione** e scegliere **Crea definizione di versione**. 
2. Selezionare il modello **Vuoto** scegliendo l'avvio con un **processo vuoto**.
3. Nella sezione **Elementi** fare clic su **+ Aggiungi elemento** e scegliere **Jenkins** per **Tipo di origine**. Selezionare la connessione all'endpoint servizio Jenkins, quindi selezionare il processo di origine Jenkins e scegliere **Aggiungi**.
4.  Fare clic sui puntini di sospensione accanto ad **Ambiente 1**.  Fare clic su **Aggiungi fase per gruppo di distribuzione**.
5.  Scegliere il **gruppo di distribuzione**.
5. Fare clic su **+** per aggiungere un'attività alla **fase gruppo di distribuzione**.
6. Scegliere l'attività **Script della shell** e fare clic su **Aggiungi**.    
    L'attività **Script della Shell** viene usata per fornire la configurazione di esecuzione di uno script in ogni server, al fine di installare Node.js e avviare l'app. Configurare l'app come segue:
8. **Percorso script**:     
    `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`
9.  Fare clic su **Avanzate** e quindi abilitare **Specifica directory di lavoro**.
10.  **Directory di lavoro**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node`
11. Modificare il nome della definizione di versione in quello specificato nella scheda **Post-build Actions** (Azioni post-compilazione) nella build in Jenkins. Jenkins richiede questo nome per poter attivare una nuova versione quando vengono aggiornati gli elementi di origine.
12. Fare clic su **Salva** e quindi su **OK** per salvare la definizione di versione.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Eseguire distribuzioni manuali e attivate da CI

1. Scegliere **+ Versione** e selezionare **Crea versione**.
2. Selezionare la build completata nell'elenco a discesa evidenziato e scegliere **Accoda**.
3. Scegliere il collegamento di versione nel messaggio popup. Ad esempio: "Versione **Versione-1** creata."
4. Aprire la scheda **Log** per osservare l'output della console della versione.
5. Nel browser aprire l'URL di uno dei server aggiunti al gruppo di distribuzione. Ad esempio, immettere `http://{your-server-ip-address}`
6. Passare al repository Git di origine e modificare il contenuto del titolo **h1** nel file app/views/index.jade con un testo modificato.
7. **Eseguire il commit** della modifica.
8. Dopo alcuni minuti, si noterà una nuova versione creata nella pagina **Versioni** di VSTS o TFS. Aprire la versione per visualizzare la distribuzione in corso. Congratulazioni.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata automatizzata la distribuzione di un'app in Azure usando la compilazione in Jenkins e VSTS per il rilascio. Si è appreso come:

> [!div class="checklist"]
> * Creare l'app in Jenkins
> * Configurare Jenkins per l'integrazione con VSTS
> * Creare un gruppo di distribuzione per le macchine virtuali di Azure
> * Creare una definizione di versione che configuri le macchine virtuali e distribuisca l'app

Passare all'esercitazione successiva per ottenere altre informazioni su come distribuire uno stack LAMP (Linux, Apache, MySQL e PHP).

> [!div class="nextstepaction"]
> [Distribuire lo stack LAMP](tutorial-lamp-stack.md)