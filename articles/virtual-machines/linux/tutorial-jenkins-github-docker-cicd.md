---
title: Esercitazione - Creare una pipeline di sviluppo in Azure con Jenkins | Microsoft Docs
description: Esercitazione - In questa esercitazione si apprenderà come creare una macchina virtuale di Jenkins in Azure che esegue il pull da GitHub in ogni commit di codice e compila un nuovo contenitore Docker per l'esecuzione dell'app.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 875285b6a168d9aa9820d660d9c366a36545d319
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299418"
---
# <a name="tutorial-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>Esercitazione: Creare un'infrastruttura di sviluppo in una macchina virtuale Linux in Azure con Jenkins, GitHub e Docker

Per automatizzare le fasi di compilazione e test dello sviluppo di un'applicazione, è possibile usare una pipeline per l'integrazione e la distribuzione continue (CI/CD). In questa esercitazione viene creata una pipeline CI/CD in una macchina virtuale di Azure e viene illustrato come:

> [!div class="checklist"]
> * Creare una macchina virtuale Jenkins
> * Installare e configurare Jenkins
> * Creare un'integrazione webhook tra GitHub e Jenkins
> * Creare e attivare processi di compilazione Jenkins da commit GitHub
> * Creare un'immagine Docker per l'app
> * Verificare che i commit GitHub compilino una nuova immagine Docker e gli aggiornamenti che eseguono l'app

Questa esercitazione usa l'interfaccia della riga di comando all'interno di [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), che viene costantemente aggiornato alla versione più recente. Per aprire Cloud Shell, selezionare **Prova** nella parte superiore di qualsiasi blocco di codice.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-jenkins-instance"></a>Creare l'istanza di Jenkins
In un'esercitazione precedente, [How to customize a Linux virtual machine on first boot](tutorial-automate-vm-deployment.md) (Come personalizzare una macchina virtuale Linux al primo avvio), è stato descritto come personalizzare una macchina virtuale al primo avvio con cloud-init. Questa esercitazione usa un file cloud-init per installare Jenkins e Docker in una macchina virtuale. Jenkins è un server di automazione open source molto diffuso che si integra uniformemente con Azure per consentire l'integrazione continua e il recapito continuo. Per altre esercitazioni sull'uso di Jenkins, vedere [Jenkins in Azure](https://docs.microsoft.com/azure/jenkins/).

Nella shell corrente creare un file denominato *cloud-init-jenkins.txt* e incollare la configurazione seguente. Ad esempio, creare il file in Cloud Shell anziché nel computer locale. Immettere `sensible-editor cloud-init-jenkins.txt` per creare il file e visualizzare un elenco degli editor disponibili. Assicurarsi che l'intero file cloud-init venga copiato correttamente, in particolare la prima riga:

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - apt install openjdk-8-jre-headless -y
  - wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
  - sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

Per poter creare una macchina virtuale è prima necessario creare un gruppo di risorse con il comando [az group create](/cli/azure/group). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupJenkins* nella posizione *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Creare quindi una macchina virtuale con il comando [az vm create](/cli/azure/vm). Usare il parametro `--custom-data` per specificare il file di configurazione di cloud-init. Se il file è stato salvato all'esterno della directory di lavoro corrente, specificare il percorso completo di *cloud-init-jenkins.txt*.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

Per creare e configurare la macchina virtuale sono necessari alcuni minuti.

Per consentire al traffico Web di raggiungere la macchina virtuale, usare [az vm open-port](/cli/azure/vm) per aprire la porta *8080* per il traffico Jenkins e la porta *1337* per l'app Node.js che viene usata per eseguire un'app di esempio:

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Configurare Jenkins
Per accedere all'istanza di Jenkins, ottenere l'indirizzo IP pubblico della macchina virtuale:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Per motivi di sicurezza, è necessario immettere la password amministratore iniziale che viene archiviata in un file di testo nella macchina virtuale per avviare l'installazione di Jenkins. Usare l'indirizzo IP pubblico ottenuto nel passaggio precedente per la connessione SSH alla macchina virtuale:

```bash
ssh azureuser@<publicIps>
```

Verificare che Jenkins sia in esecuzione con il comando `service`:

```bash
$ service jenkins status
● jenkins.service - LSB: Start Jenkins at boot time
   Loaded: loaded (/etc/init.d/jenkins; generated)
   Active: active (exited) since Tue 2019-02-12 16:16:11 UTC; 55s ago
     Docs: man:systemd-sysv-generator(8)
    Tasks: 0 (limit: 4103)
   CGroup: /system.slice/jenkins.service

Feb 12 16:16:10 myVM systemd[1]: Starting LSB: Start Jenkins at boot time...
...
```

Visualizzare la password `initialAdminPassword` per l'installazione di Jenkins e copiarla:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Se il file non è ancora disponibile, attendere ancora qualche minuto che cloud-init completi l'installazione di Jenkins e Docker.

Aprire un Web browser e passare a `http://<publicIps>:8080`. Completare la configurazione iniziale di Jenkins come segue:

- Scegliere **Select plugins to install** (Selezionare il plug-in da installare)
- Cercare *GitHub* nella casella di testo nella parte superiore. Selezionare la casella relativa a *GitHub* e quindi selezionare **Install** (Installa).
- Creare il primo utente amministratore. Immettere un nome utente, ad esempio **admin**, quindi fornire una password sicura. Digitare infine un nome completo e un indirizzo di posta elettronica.
- Selezionare **Save and Finish** (Salva e completa).
- Quando Jenkins è pronto, selezionare **Start using Jenkins** (Inizia a usare Jenkins).
  - Se il Web browser visualizza una pagina vuota quando si inizia a usare Jenkins, riavviare il servizio Jenkins. Dalla sessione SSH digitare `sudo service jenkins restart` e quindi aggiornare il Web browser.
- Se necessario, accedere a Jenkins con il nome utente e la password creati.


## <a name="create-github-webhook"></a>Creare webhook di GitHub
Per configurare l'integrazione con GitHub, aprire l'[app di esempio Node.js Hello World](https://github.com/Azure-Samples/nodejs-docs-hello-world) dal repository di esempi di Azure. Per creare il fork del repository nel proprio account GitHub, selezionare il pulsante **Fork** nell'angolo superiore destro.

Creare un webhook all'interno del fork creato:

- Selezionare **Impostazioni** e quindi **Webhook** sul lato sinistro.
- Scegliere **Add webhook** (Aggiungi webhook) e quindi immettere *Jenkins* nella casella del filtro.
- In **Payload URL** (URL payload) immettere `http://<publicIps>:8080/github-webhook/`. Assicurarsi di includere la barra finale (/).
- Per **Content type** (Tipo di contenuto) selezionare *application/x-www-form-urlencoded*.
- Per **Which events would you like to trigger this webhook?** (Eventi che attiveranno il webhook) selezionare *Just the push event* (Solo evento push).
- Selezionare **Active** (Attivo).
- Fare clic su **Add webhook** (Aggiungi webhook).

![Aggiunta del webhook di GitHub al repository con fork](media/tutorial-jenkins-github-docker-cicd/github_webhook.png)


## <a name="create-jenkins-job"></a>Creare un processo di Jenkins
Per fare in modo che Jenkins risponda a un evento in GitHub, ad esempio l'esecuzione del commit di codice, creare un processo di Jenkins. Usare gli URL del proprio fork di GitHub.

Nel sito Web di Jenkins selezionare **Create new jobs** (Crea nuovi processi) dalla home page:

- Immettere *HelloWorld* come nome del processo. Scegliere **Freestyle project** (Progetto Freestyle) e quindi selezionare **OK**.
- Nella sezione **General** (Generale) selezionare **GitHub project** (Progetto GirHub) e immettere l'URL della copia del repository creata tramite fork, ad esempio *https://github.com/cynthn/nodejs-docs-hello-world*
- Nella sezione **Source code management** (Gestione del codice sorgente) selezionare **Git** e immettere l'URL *.git* del repository con fork, ad esempio *https://github.com/cynthn/nodejs-docs-hello-world.git*
- Nella sezione **Build Triggers** (Trigger di compilazione) selezionare **GitHub hook trigger for GITScm polling** (Trigger di hook GitHub per polling GITScm).
- Nella sezione **Build** (Compilazione) scegliere **Add build step** (Aggiungi istruzione di compilazione). Selezionare **Execute shell** (Esegui shell) e quindi immettere `echo "Test"` nella finestra di comando.
- Selezionare **Save** (Salva) nella parte inferiore della finestra dei processi.


## <a name="test-github-integration"></a>Testare l'integrazione di GitHub
Per testare l'integrazione di GitHub con Jenkins, eseguire il commit di una modifica nel fork. 

Nell'interfaccia utente Web di GitHub selezionare il repository con fork e quindi fare clic sul file **index.js**. Selezionare l'icona a forma di matita per modificare il file in modo che la riga 6 corrisponda a:

```javascript
response.end("Hello World!");
```

Per eseguire il commit delle modifiche, selezionare il pulsante **Commit changes** (Esegui il commit delle modifiche) nella parte inferiore.

In Jenkins viene avviata una nuova compilazione nella sezione **Build history** (Cronologia compilazione) nell'angolo inferiore sinistro della pagina del processo. Scegliere il collegamento del numero di build e selezionare **Console output** (Output console) sul lato sinistro. È possibile visualizzare i passaggi eseguiti in Jenkins mentre viene eseguito il pull del codice da GitHub e l'azione di compilazione genera il messaggio `Test` nella console. Ogni volta che si esegue un'operazione di commit in GitHub, il webhook contatta Jenkins e attiva una nuova compilazione in questo modo.


## <a name="define-docker-build-image"></a>Definire l'immagine di compilazione di Docker
Per visualizzare l'app Node.js in esecuzione sulla base dei commit GitHub, è necessario creare un'immagine Docker per l'esecuzione dell'app. L'immagine viene creata da un file Dockerfile che definisce come configurare il contenitore che esegue l'app. 

Dalla connessione SSH alla macchina virtuale, passare alla directory dell'area di lavoro di Jenkins denominata sulla base del processo creato in un passaggio precedente. In questo esempio è stata denominata *HelloWorld*.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

Creare un file con `sudo sensible-editor Dockerfile` in questa directory dell'area di lavoro e incollare il contenuto seguente. Assicurarsi che l'intero file Docker venga copiato correttamente, in particolare la prima riga:

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

Questo file Dockerfile usa l'immagine di base Node.js con Alpine Linux, espone la porta 1337 in cui viene eseguita l'app Hello World, quindi copia i file dell'app e la inizializza.


## <a name="create-jenkins-build-rules"></a>Creare regole di compilazione di Jenkins
In un passaggio precedente è stata creata una regola di compilazione di base di Jenkins che genera un messaggio nella console. Ora è necessario creare l'istruzione di compilazione per usare il file Dockerfile ed eseguire l'app.

Nell'istanza di Jenkins selezionare il processo creato in un passaggio precedente. Selezionare **Configure** (Configura) sul lato sinistro e scorrere fino alla sezione **Build** (Compilazione):

- Rimuovere l'istruzione di compilazione `echo "Test"` esistente. Selezionare la croce rossa nell'angolo superiore destro della casella dell'istruzione di compilazione esistente.
- Scegliere **Add build step** (Aggiungi istruzione di compilazione) e quindi selezionare **Execute shell** (Esegui shell).
- Nella casella **Command** (Comando) immettere i comandi Docker seguenti, quindi selezionare **Save** (Salva):

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

Le istruzioni di compilazione Docker creano un'immagine contrassegnata con il numero di build Jenkins, in modo da mantenere una cronologia delle immagini. Gli eventuali contenitori esistenti che eseguono l'app vengono arrestati e quindi rimossi. Si avvia quindi un nuovo contenitore usando l'immagine e si esegue l'app Node.js in base agli ultimi commit in GitHub.


## <a name="test-your-pipeline"></a>Testare la pipeline
Per visualizzare l'intera pipeline in azione, modificare nuovamente il file *index.js* nel repository GitHub con fork e selezionare **Commit change** (Esegui il commit della modifica). Viene avviato un nuovo processo in Jenkins in base al webhook per GitHub. Potrebbero essere necessari alcuni secondi per creare l'immagine Docker e avviare l'app in un nuovo contenitore.

Se necessario, ottenere nuovamente l'indirizzo IP pubblico della macchina virtuale:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Aprire un Web browser e immettere `http://<publicIps>:1337`. Viene visualizzata l'app Node.js che rispecchia gli ultimi commit nel fork GitHub come illustrato di seguito:

![Esecuzione dell'app Node.js](media/tutorial-jenkins-github-docker-cicd/running_nodejs_app.png)

Apportare un'altra modifica al file *index.js* in GitHub ed eseguire il commit della modifica. Attendere alcuni secondi per il completamento del processo in Jenkins, quindi aggiornare il Web browser per visualizzare la versione aggiornata dell'app in esecuzione in un nuovo contenitore come segue:

![Esecuzione dell'app Node.js dopo un altro commit GitHub](media/tutorial-jenkins-github-docker-cicd/another_running_nodejs_app.png)


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato configurato GitHub per eseguire un processo di compilazione Jenkins in ogni commit di codice e quindi distribuire un contenitore Docker per testare l'app. Si è appreso come:

> [!div class="checklist"]
> * Creare una macchina virtuale Jenkins
> * Installare e configurare Jenkins
> * Creare un'integrazione webhook tra GitHub e Jenkins
> * Creare e attivare processi di compilazione Jenkins da commit GitHub
> * Creare un'immagine Docker per l'app
> * Verificare che i commit GitHub compilino una nuova immagine Docker e gli aggiornamenti che eseguono l'app

Passare all'esercitazione successiva per ottenere altre informazioni su come integrare Jenkins con Azure DevOps Services.

> [!div class="nextstepaction"]
> [Distribuire l'app con Jenkins e Azure DevOps Services](tutorial-build-deploy-jenkins.md)
