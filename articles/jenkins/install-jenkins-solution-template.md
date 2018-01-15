---
title: Creare un server Jenkins in Azure
description: Installare Jenkins in una macchina virtuale Linux di Azure dal modello di soluzione Jenkins e compilare un'applicazione Java di esempio.
author: mlearned
manager: douge
ms.service: multiple
ms.workload: web
ms.devlang: java
ms.topic: article
ms.date: 08/21/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 422d133841a380b1ef02e95245207c464089138d
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Creare un server Jenkins in una VM Linux di Azure dal portale di Azure

Questa guida introduttiva illustra come installare [Jenkins](https://jenkins.io) in una VM Ubuntu Linux con gli strumenti e i plug-in configurati per usare Azure. Al termine, si avrà un server Jenkins in esecuzione in Azure che compila un'app Java di esempio da [GitHub](https://github.com).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure.
* Accesso a SSH nella riga di comando del computer (ad esempio, la shell Bash o [PuTTY](http://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Creare la VM Jenkins dal modello di soluzione

Aprire l'[immagine del Marketplace per Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview) nel Web browser e selezionare **SCARICA ADESSO** sul lato sinistro della pagina. Esaminare i dettagli relativi ai prezzi e selezionare **Continua** e quindi **Crea** per configurare il server Jenkins nel portale di Azure. 
   
![Finestra di dialogo del portale di Azure](./media/install-jenkins-solution-template/ap-create.png)

Nella scheda **Configura impostazioni di base** compilare i campi seguenti:

![Configurare le impostazioni di base](./media/install-jenkins-solution-template/ap-basic.png)

* Usare **Jenkins** come **Nome**.
* Immettere un valore in **Nome utente**. Il nome utente deve soddisfare [specifici requisiti](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
* Selezionare **Password** come **Tipo di autenticazione** e immettere una password. La password deve contenere un carattere maiuscolo, un numero e un carattere speciale.
* Usare **myJenkinsResourceGroup** come **Gruppo di risorse**.
* Scegliere l'[area di Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) **Stati Uniti orientali** nell'elenco a discesa **Località**.

Selezionare **OK** per passare alla scheda **Configure additional options** (Configura opzioni aggiuntive). Immettere un nome di dominio univoco per identificare il server Jenkins e selezionare **OK**.

![Configurare le opzioni aggiuntive](./media/install-jenkins-solution-template/ap-addtional.png)  

 Al termine della convalida, selezionare di nuovo **OK** nella scheda **Riepilogo**. Selezionare infine **Acquista** per creare la VM Jenkins. Quando il server è pronto, viene visualizzata una notifica nel portale di Azure:   

![Notifica che informa che Jenkins è pronto](./media/install-jenkins-solution-template/jenkins-deploy-notification-ready.png)

## <a name="connect-to-jenkins"></a>Connettersi a Jenkins

Passare alla macchina virtuale (ad esempio, http://jenkins2517454.eastus.cloudapp.azure.com/) nel Web browser. Dato che la console Jenkins non è accessibile tramite HTTP non protetto, nella pagina verranno visualizzate istruzioni per accedere alla console Jenkins in modo sicuro dal computer usando un tunnel SSH.

![Sbloccare Jenkins](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

Configurare il tunnel usando il comando `ssh` sulla pagina dalla riga di comando, sostituendo `username` con il nome dell'utente amministratore della macchina virtuale scelto in precedenza durante la configurazione della macchina virtuale dal modello di soluzione.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Dopo aver avviato il tunnel, passare a http://localhost:8080/ nel computer locale. 

Ottenere la password iniziale eseguendo questo comando nella riga di comando mentre si è connessi tramite SSH alla VM Jenkins:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Sbloccare il dashboard di Jenkins per la prima volta con questa password iniziale.

![Sbloccare Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Selezionare **Install suggested plugins** (Installa plug-in consigliati) nella pagina successiva e quindi creare un utente amministratore di Jenkins che verrà usato per accedere al dashboard di Jenkins.

![Jenkins è pronto per l'uso.](./media/install-jenkins-solution-template/jenkins-welcome.png)

Il server Jenkins è ora pronto per la compilazione di codice.

## <a name="create-your-first-job"></a>Creare il primo processo

Selezionare **Create new jobs** (Crea nuovi processi) nella console Jenkins, assegnare il nome **mySampleApp** e selezionare **Freestyle project** (Progetto Freestyle) e quindi **OK**.

![Creare un nuovo processo](./media/install-jenkins-solution-template/jenkins-new-job.png) 

Selezionare la scheda **Source Code Management** (Gestione del codice sorgente), abilitare **Git** e immettere l'URL seguente nel campo **Repository URL** (URL del repository): `https://github.com/spring-guides/gs-spring-boot.git`

![Definire il repository Git](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

Selezionare la scheda **Build** (Compilazione) e quindi **Add build step** (Aggiungi passaggio di compilazione) e **Invoke Gradle script** (Richiama script Gradle). Selezionare **Use Gradle Wrapper** (Usa wrapper di Gradle) e quindi immettere `complete` in **Wrapper location** (Percorso wrapper) e `build` in **Tasks** (Attività).

![Usare il wrapper di Gradle per la compilazione](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

Selezionare **Advanced** (Avanzate) e quindi immettere `complete` nel campo **Root Build script** (Script di compilazione radice). Selezionare **Salva**.

![Configurare le impostazioni avanzate nel passaggio di compilazione del wrapper di Gradle](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Compilare il codice

Selezionare **Build Now** (Compila) per compilare il codice e creare il pacchetto dell'app di esempio. Al termine della compilazione, selezionare il collegamento **Workspace** (Area di lavoro) per il progetto.

![Passare all'area di lavoro per ottenere il file JAR della compilazione](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

Passare a `complete/build/libs` e verificare che sia presente il file `gs-spring-boot-0.1.0.jar` per assicurarsi che la compilazione sia stata completata correttamente. Il server Jenkins è ora pronto per la compilazione dei progetti dell'utente in Azure.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere VM di Azure come agenti di Jenkins](jenkins-azure-vm-agents.md)
