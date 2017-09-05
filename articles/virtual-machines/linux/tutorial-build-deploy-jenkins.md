---
title: CI/CD da Jenkins in macchine virtuali di Azure con Team Services | Microsoft Docs
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
ms.date: 06/15/2017
ms.author: ahomer
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: a40e26a8681df31fad664e4d1df4c1513311900d
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-team-services"></a>Distribuire l'app in macchine virtuali Linux usando Jenkins e Team Services

Integrazione continua (CI) e distribuzione continua (CD) rappresentano una pipeline con cui è possibile compilare, rilasciare e distribuire codice. Team Services offre un set di strumenti completo per l'automazione CI/CD e la distribuzione in Azure. Jenkins è uno strumento di terze parti diffuso basato su server per CI/CD e offre anche l'automazione CI/CD. È possibile usare insieme entrambi gli strumenti per personalizzare la fornitura dell'app o del servizio cloud.

In questa esercitazione si userà Jenkins per creare un'**app Web Node.js** e Visual Studio Team Services per distribuirla in un [gruppo di distribuzione](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) contenente macchine virtuali Linux.

Si apprenderà come:

> [!div class="checklist"]
> * Creare l'app in Jenkins
> * Configurare Jenkins per l'integrazione con Team Services
> * Creare un gruppo di distribuzione per le macchine virtuali di Azure
> * Creare una definizione di versione che configuri le macchine virtuali e distribuisca l'app

## <a name="before-you-begin"></a>Prima di iniziare

* È necessario l'accesso a un account Jenkins. Se non è ancora stato creato un server Jenkins, vedere la [documentazione di Jenkins](https://jenkins.io/doc/). 

* Accedere al proprio account di Team Services (`https://{youraccount}.visualstudio.com`). 
  È possibile ottenere un [account di Team Services gratuito](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Per altre informazioni, vedere [Connettersi a Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).

* Se non si dispone di un token di accesso personale nell'account di Team Services, crearne uno. Jenkins richiede queste informazioni per accedere all'account di Team Services.
  Leggere [Come creare un token di accesso personale per Team Services e TFS](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) per informazioni su come generarne uno.

## <a name="get-the-sample-app"></a>Ottenere l'app di esempio

È necessario disporre di un'app da distribuire archiviata in un repository Git.
Per questa esercitazione, si consiglia di usare [questa app di esempio disponibile da GitHub](https://github.com/azooinmyluggage/fabrikam-node).

1. Creare un fork di questa app e prendere nota del percorso (URL) per l'uso nei passaggi successivi di questa esercitazione.

1. Rendere il fork **pubblico** per semplificare la connessione a GitHub in un secondo momento.

> [!NOTE]
> Per ulteriori informazioni, vedere la pagina relativa alla [biforcazione di un repository](https://help.github.com/articles/fork-a-repo/) e a come [rendere pubblico un repository privato](https://help.github.com/articles/making-a-private-repository-public/).

> [!NOTE]
> L'app è stata creata tramite [Yeoman](http://yeoman.io/learning/index.html); Usa **Express**, **bower** e **grunt** e ha alcuni pacchetti **npm** come dipendenze.
> L'app di esempio contiene un set di [modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) usati per creare in modo dinamico le macchine virtuali per la distribuzione in Azure. Questi modelli vengono usati dalle attività nella [definizione di versione di Team Services](https://www.visualstudio.com/docs/build/actions/work-with-release-definitions).
> Il modello principale crea un gruppo di sicurezza di rete, una macchina virtuale e una rete virtuale. Assegna un indirizzo IP pubblico e apre la porta 80 in ingresso. Aggiunge anche un tag che viene usato dal gruppo di distribuzione per selezionare i computer che ricevono la distribuzione.
>
> L'esempio contiene anche uno script che configura Nginx e distribuisce l'app. Viene eseguito su tutte le macchine virtuali. In particolare, lo script installa Noe, Nginx e PM2, configura Nginx e PM2 e quindi avvia l'app Node.

## <a name="configure-jenkins-plugins"></a>Configurare i plug-in di Jenkins

In primo luogo è necessario configurare due plug-in di Jenkins per **NodeJS** e l'**integrazione con Team Services**.

1. Aprire l'account di Jenkins e scegliere **Manage Jenkins** (Gestisci Jenkins).

1. Nella pagina **Manage Jenkins** fare clic su **Manage Plugins** (Gestisci plug-in).

1. Filtrare l'elenco per individuare il plug-in **NodeJS** e installarlo senza riavvio.

   ![Aggiunta del plug-in NodeJS a Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)

1. Filtrare l'elenco per trovare il plug-in **Team Foundation Server** e installarlo (questo plug-in funziona sia con Team Services che con Team Foundation Server). Il riavvio di Jenkins non è necessario.

## <a name="configure-jenkins-build-for-nodejs"></a>Configurare la compilazione di Jenkins per Node.js

In Jenkins, creare un nuovo progetto di compilazione e configurarlo come segue:

1. Nella scheda **General** (Generale), immettere un nome per il progetto di compilazione.

1. Nella scheda **Source Code Managament** (Gestione codice sorgente) selezionare **Git** e immettere i dettagli del repository e del ramo contenenti il codice dell'app.

   ![Aggiungere un repository alla build](media/tutorial-build-deploy-jenkins/jenkins-git.png)

   > [!NOTE]
   > Se il repository non è pubblico, scegliere **Add** (Aggiungi) e fornire le credenziali per la connessione.

1. Nella scheda **Build Triggers** (Trigger build) selezionare **Poll SCM** (Polling SCM) e immettere la pianificazione `H/03 * * * *` per il polling delle modifiche al repository Git ogni tre minuti. 

1. Nella scheda **Build Environment** (Ambiente build) selezionare **Provide Node &amp; npm bin/ folder PATH** e immettere `NodeJS` per il valore Node JS Installation (Installazione Node JS). Lasciare **npmrc file** impostato su "use system default" (usa impostazioni predefinite di sistema).

1. Nella scheda **Build** (Compila), immettere il comando `npm install` per assicurarsi che tutte le dipendenze siano aggiornate.

## <a name="configure-jenkins-for-team-services-integration"></a>Configurare Jenkins per l'integrazione con Team Services

1. Nella scheda **Post-build Actions** (Azioni post-compilazione) per **Files to archive** (File da archiviare) immettere `**/*` per includere tutti i file.

1. Per **Trigger release in TFS/Team Services** (Rilascio trigger in TFS/Team Services), immettere l'URL completo dell'account, ad esempio `https://your-account-name.visualstudio.com`, il nome del progetto, un nome per la definizione di versione che verrà creata in un secondo momento e le credenziali per connettersi all'account.
   Sono necessari il nome utente e il token di accesso personale creati in precedenza. 

   ![Configurazione delle azioni di post-compilazione in Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)

1. Salvare il progetto di compilazione.

## <a name="create-a-jenkins-service-endpoint"></a>Creare un endpoint servizio di Jenkins

Un endpoint servizio consente a Team Services di connettersi a Jenkins.

1. Aprire la pagina **Servizi** in Team Services, aprire l'elenco **Nuovo endpoint servizio** e scegliere **Jenkins**.

   ![Aggiungere un endpoint di Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)

1. Immettere un nome da usare come riferimento per questa connessione.

1. Immettere l'URL del server Jenkins e contrassegnare l'opzione **Accetta i certificati SSL non attendibili**.

1. Immettere il nome utente e la password per l'account di Jenkins.

1. Scegliere **Verifica connessione** per assicurarsi che le informazioni siano corrette.

1. Scegliere **OK** per creare l'endpoint servizio.

## <a name="create-a-deployment-group"></a>Creare un gruppo di distribuzione

È necessario un [gruppo di distribuzione](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) per contenere le macchine virtuali.

1. Aprire la scheda **Versioni** nell'hub **Build &amp; Versione**, quindi aprire la scehda **Gruppi di distribuzione** e scegliere **+ Nuovo**.

1. Immettere un nome per il gruppo di distribuzione e una descrizione facoltativa,
   quindi scegliere **Crea**.

L'attività di distribuzione di un gruppo di risorse di Azure crea e registra le macchine virtuali quando viene eseguito con il modello di Azure Resource Manager.
Non è necessario creare e registrare manualmente le macchine virtuali.

## <a name="create-a-release-definition"></a>Creare una definizione di versione

Una definizione di versione specifica il processo eseguito da Team Services per distribuire l'app.
Per creare una definizione di versione in Team Services:

1. Aprire la scheda **Versioni** nell'hub **Build &amp; Versione**, aprire l'elenco a discesa **+** di definizioni di versione e scegliere **Crea definizione di versione**. 

1. Selezionare il modello **Vuoto** e scegliere **Avanti**.

1. Nella sezione **Elementi** fare clic su **Collega un elemento** e scegliere **Jenkins**. Selezionare la connessione all'endpoint servizio Jenkins, quindi selezionare il processo di origine Jenkins e scegliere **Crea**. 

1. Nella nuova definizione di versione, scegliere **+ Aggiungi attività** e aggiungere un'attività **Distribuzione gruppo di risorse di Azure** per l'ambiente predefinito.

1. Scegliere la freccia a discesa accanto al collegamento **+ Aggiungi attività** e aggiungere una fase di gruppo di distribuzione alla definizione.

   ![Aggiunta di una fase di gruppo di distribuzione](media/tutorial-build-deploy-jenkins/deployment-group-phase-in-release-definition.png) 

1. Nel catalogo delle attività, aprire la sezione **Utilità** e aggiungere un'istanza dell'attività **Script della Shell**.

1. Il modello di parametri usato nell'attività Distribuzione gruppo di risorse di Azure imposta la password amministratore usata per connettersi alle macchine virtuali.
   Fornire la password con la variabile **$(adminpassword)**:
   
   - Aprire la scheda **Variabili** e, nella sezione **Variabili**, immettere il nome `adminpassword`.

   - Immettere la password amministratore.

   - Scegliere l'icona "lucchetto" accanto alla casella di testo del valore per proteggere la password. 

## <a name="configure-the-azure-resource-group-deployment-task"></a>Configurare l'attività Distribuzione gruppo di risorse di Azure

L'attività **Distribuzione gruppo di risorse di Azure** viene usata per creare il gruppo di distribuzione. Configurare l'app come segue:

* **Sottoscrizione di Azure:** selezionare una connessione dall'elenco in **Connessioni ai servizi di Azure disponibili**. 
  Se non viene visualizzata alcuna connessione, scegliere **Gestisci**, selezionare **Nuovo endpoint servizio**, quindi **Azure Resource Manager** e seguire le istruzioni.
  Tornare alla definizione di versione, aggiornare l'elenco **Sottoscrizione di AzureRM** e selezionare la connessione creata.

* **Gruppo di risorse**: immettere un nome per il gruppo di risorse creato in precedenza.

* **Posizione**: selezionare un'area per la distribuzione.

  ![Creare un nuovo gruppo di risorse](media/tutorial-build-deploy-jenkins/provision-web-server.png)

* **Percorso del modelli**:`URL of the file`

* **Collegamento al modello**:`{your-git-repo}/ARM-Templates/UbuntuWeb1.json`

* **Collegamento ai parametri del modello**:`{your-git-repo}/ARM-Templates/UbuntuWeb1.parameters.json`

* **Esegui override dei parametri del modello**: un elenco dei valori di override, ad esempio: `-location {location} -virtualMachineName {machine] -virtualMachineSize Standard_DS1_v2 -adminUsername {username} -virtualNetworkName fabrikam-node-rg-vnet -networkInterfaceName fabrikam-node-websvr1 -networkSecurityGroupName fabrikam-node-websvr1-nsg -adminPassword $(adminpassword) -diagnosticsStorageAccountName fabrikamnodewebsvr1 -diagnosticsStorageAccountId Microsoft.Storage/storageAccounts/fabrikamnodewebsvr1 -diagnosticsStorageAccountType Standard_LRS -addressPrefix 172.16.8.0/24 -subnetName default -subnetPrefix 172.16.8.0/24 -publicIpAddressName fabrikam-node-websvr1-ip -publicIpAddressType Dynamic`.<br />Inserire i valori specifici per i {segnaposto}. 

* **Abilita i prerequisiti**: `Configure with Deployment Group agent`

* **Endpoint TFS/VSTS**: scegliere **Aggiungi** e, nella finestra di dialogo "Aggiungi nuova connessione Team Foundation Server/Team Services", selezionare **Autenticazione basata su Token**. Immettere un nome per la connessione e l'URL del progetto team. Quindi generare e immettere un [token di accesso personale]( https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) per autenticare la connessione al progetto team.

  ![Creare un token di accesso personale](media/tutorial-build-deploy-jenkins/create-a-pat.png)

* **Progetto team**: selezionare il progetto corrente.

* **Gruppo di distribuzione**: immettere lo stesso nome del gruppo di distribuzione usato per il parametro **Gruppo di risorse**.

Le impostazioni predefinite per l'attività Distribuzione gruppo di risorse di Azure creano o aggiornano una risorsa in modo incrementale. L'attività crea le macchine virtuali la prima volta che viene eseguita. In seguito, si limita ad aggiornarle.

## <a name="configure-the-shell-script-task"></a>Configurare l'attività Script della Shell

L'attività **Script della Shell** viene usata per fornire la configurazione di esecuzione di uno script in ogni server, al fine di installare Node.js e avviare l'app. Configurare l'app come segue:

* **Percorso script**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`

* **Specifica directory di lavoro**: `Checked`

* **Directory di lavoro**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node`
   
## <a name="rename-and-save-the-release-definition"></a>Rinominare e salvare la definizione di versione

1. Modificare il nome della definizione di versione in quello specificato nella scheda **Post-build Actions** (Azioni post-compilazione) nella build in Jenkins. Jenkins richiede questo nome per poter attivare una nuova versione quando vengono aggiornati gli elementi di origine.

1. Facoltativamente, modificare il nome dell'ambiente facendo clic sul nome. 

1. Scegliere **Salva** e quindi **OK**.

## <a name="start-a-manual-deployment"></a>Avviare una distribuzione manuale

1. Scegliere **+ Versione** e selezionare **Crea versione**.

1. Selezionare la build completata nell'elenco a discesa evidenziato e scegliere **Crea**.

1. Scegliere il collegamento di versione nel messaggio popup. Ad esempio: "Versione **Versione-1** creata."

1. Aprire la scheda **Log** per osservare l'output della console della versione.

1. Nel browser, aprire l'URL di uno dei server aggiunti al gruppo di distribuzione. Ad esempio, immettere `http://{your-server-ip-address}`

## <a name="start-a-cicd-deployment"></a>Avviare una distribuzione CI/CD

1. Nella definizione di versione, deselezionare la casella di controllo **Abilitato** nella sezione **Opzioni di controllo** delle impostazioni dell'attività Distribuzione gruppo di risorse di Azure.
   Per le distribuzioni future al gruppo di distribuzione esistente non è necessario eseguire nuovamente questa attività.

1. Passare al repository Git di origine e di modificare il contenuto del titolo **h1** nel file [app/views/index.jade](https://github.com/azooinmyluggage/fabrikam-node/blob/master/app/views/index.jade).

1. Eseguire il commit delle modifiche.

1. Dopo alcuni minuti, si noterà una nuova versione creata nella pagina **Versioni** di Team Services o TFS. Aprire la versione per visualizzare la distribuzione in corso. Congratulazioni.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato automatizzata la distribuzione di un'app in Azure tramite la compilazione in Jenkins e Team Services per il rilascio. Si è appreso come:

> [!div class="checklist"]
> * Creare l'app in Jenkins
> * Configurare Jenkins per l'integrazione con Team Services
> * Creare un gruppo di distribuzione per le macchine virtuali di Azure
> * Creare una definizione di versione che configuri le macchine virtuali e distribuisca l'app

Passare all'esercitazione successiva per ottenere altre informazioni su come distribuire uno stack LAMP (Linux, Apache, MySQL e PHP).

> [!div class="nextstepaction"]
> [Distribuire lo stack LAMP](tutorial-lamp-stack.md)
