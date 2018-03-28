---
title: Creare un server Jenkins in Azure
description: Installare Jenkins in una macchina virtuale Linux di Azure dal modello di soluzione Jenkins e compilare un'applicazione Java di esempio.
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: c9f86ab2536d3c598bb8c7084524395b41f18db0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Creare un server Jenkins in una VM Linux di Azure dal portale di Azure

Questa guida introduttiva illustra come installare [Jenkins](https://jenkins.io) in una VM Ubuntu Linux con gli strumenti e i plug-in configurati per usare Azure. Al termine, si avrà un server Jenkins in esecuzione in Azure che compila un'app Java di esempio da [GitHub](https://github.com).

## <a name="prerequisites"></a>prerequisiti

* Una sottoscrizione di Azure.
* Accesso a SSH nella riga di comando del computer (ad esempio, la shell Bash o [PuTTY](http://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Creare la VM Jenkins dal modello di soluzione
Jenkins supporta un modello in cui il server Jenkins delega il lavoro a uno o più agenti per consentire a una singola installazione di Jenkins di ospitare un numero elevato di progetti o di fornire ambienti diversi necessari per le compilazioni o i test. I passaggi in questa sezione consentono di installare e configurare un server Jenkins in Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

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
