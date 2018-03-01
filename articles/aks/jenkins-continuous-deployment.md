---
title: Distribuzione continua di Jenkins con Kubernetes nel servizio contenitore di Azure
description: Come automatizzare un processo di distribuzione continua con Jenkins per distribuire e aggiornare un'app nei contenitori in Kubernetes nel servizio contenitore di Azure
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 1293fda45602203570a0f7f75481f67bdcb6edf3
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="continuous-deployment-with-jenkins-and-azure-container-service"></a>Distribuzione continua con Jenkins e il servizio contenitore di Azure

Questo documento illustra come configurare un flusso di lavoro di base per la distribuzione continua tra Jenkins e un cluster del servizio contenitore di Azure (AKS). 

Il flusso di lavoro di esempio include i passaggi seguenti:

> [!div class="checklist"]
> * Distribuire l'applicazione di voto di Azure nel cluster di Kubernetes.
> * Aggiornare il codice dell'applicazione di voto di Azure e inviarlo a un repository GitHub, che avvia il processo di distribuzione continua.
> * Jenkins clona il repository e compila una nuova immagine del contenitore con il codice aggiornato.
> * Viene eseguito il push dell'immagine nel Registro contenitori di Azure (ACR).
> * L'applicazione in esecuzione nel cluster AKS viene aggiornata con la nuova immagine del contenitore.

## <a name="prerequisites"></a>prerequisiti

Per completare la procedura descritta in questo articolo, è necessario soddisfare i requisiti seguenti.

- Avere una conoscenza di base di Kubernetes, Git, CI/CD e Registro contenitori di Azure (ACR).
- Avere un [cluster del servizio contenitore di Azure (AKS)][aks-quickstart] e le [credenziali di AKS configurate][aks-credentials] nel sistema di sviluppo.
- Avere un [registro del Registro contenitori di Azure (ACR)][acr-quickstart], il nome del server di accesso di ACR e le [credenziali di ACR][acr-authentication] con accesso push e pull.
- Avere l'interfaccia della riga di comando di Azure installata nel sistema di sviluppo.
- Avere il docker installato nel sistema di sviluppo.
- Avere un account GitHub, il [token di accesso personale di GitHub][git-access-token] e il client Git installato nel sistema di sviluppo.

## <a name="prepare-application"></a>Preparare l'applicazione

L'applicazione di voto di Azure usata in questo documento contiene un'interfaccia Web ospitata in uno o più pod e un secondo pod che ospita Redis per l'archiviazione temporanea dei dati. 

Prima di compilare l'integrazione di Jenkins/AKS, preparare e distribuire l'applicazione di voto di Azure nel cluster AKS. Questa può essere considerata come la prima versione dell'applicazione.

Creare una copia tramite fork del repository GitHub seguente.

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

Una volta creato il fork, clonarlo nel sistema di sviluppo. Assicurarsi che sia in uso l'URL del fork durante la clonazione di questo repository.

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Cambiare directory in modo da usare la directory clonata.

```bash
cd azure-voting-app-redis
```

Eseguire il file `docker-compose.yaml` per creare l'immagine del contenitore `azure-vote-front` e avviare l'applicazione.

```bash
docker-compose up -d
```

Al termine usare il comando [docker images][docker-images] per vedere le immagini create.

Si noti che sono state scaricate o create tre immagini. L'immagine `azure-vote-front` contiene l'applicazione e usa l'immagine `nginx-flask` come base. L'immagine `redis` viene usata per avviare un'istanza di Redis.

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Ottenere il server di accesso di ACR con il comando [az acr list][az-acr-list]. Assicurarsi di aggiornare il nome del gruppo di risorse con il gruppo di risorse che ospita il registro di ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Usare il comando [docker tag][docker-tag] per contrassegnare l'immagine con il nome del server di accesso e il numero di versione `v1`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Aggiornare il valore del server di accesso di ACR con il nome del server di accesso di ACR ed eseguite il push dell'immagine `azure-vote-front` nel registro. 

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>Distribuire un'applicazione in Kubernetes

Un file manifesto di Kubernetes è disponibile nella radice del repository di voto di Azure e può essere usato per distribuire l'applicazione nel cluster di Kubernetes.

Innanzitutto aggiornare il file manifesto **azure-vote-all-in-one-redis.yaml** con la posizione del registro di ACR. Aprire il file con un editor di testo qualsiasi e sostituire `microsoft` con il nome del server di accesso di ACR. Questo valore è presente nella riga **47** del file manifesto.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Successivamente usare il comando [kubectl create][kubectl-create] per eseguire l'applicazione. Questo comando analizza il file manifesto e crea gli oggetti Kubernetes definiti.

```bash
kubectl create -f azure-vote-all-in-one-redis.yaml
```

Viene creato un [servizio di Kubernetes][kubernetes-service] che espone l'applicazione in Internet. Il processo potrebbe richiedere alcuni minuti. 

Per monitorare lo stato, usare il comando [kubectl get service][kubectl-get] con l'argomento `--watch`.

```bash
kubectl get service azure-vote-front --watch
```

*EXTERNAL-IP* per il servizio *azure-vote-front* inizialmente viene visualizzato come *pending*.
  
```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Dopo il passaggio di *EXTERNAL-IP* da *pending* a un *indirizzo IP*, usare `control+c` per arrestare il processo kubectl watch. 

```
azure-vote-front   10.0.34.242   13.90.150.118   80:30676/TCP   2m
```

Per vedere l'applicazione, passare all'indirizzo IP esterno.

![Immagine del cluster Kubernetes in Azure](media/aks-jenkins/azure-vote-safari.png)

## <a name="deploy-jenkins-to-vm"></a>Distribuire Jenkins in una macchina virtuale

In precedenza è già stato creato uno script per distribuire una macchina virtuale, configurare l'accesso alla rete e completare un'installazione di base di Jenkins. Inoltre, lo script copia il file di configurazione di Kubernetes dal sistema di sviluppo al sistema di Jenkins. Questo file viene usato per l'autenticazione tra il cluster di AKS e Jenkins.

Eseguire i comandi seguenti per scaricare ed eseguire lo script. L'URL seguente può essere usato anche per esaminare il contenuto dello script.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Al completamento, lo script restituisce un indirizzo per il server di Jenkins e una chiave per sbloccare Jenkins. Passare all'URL, immettere la chiave e completare la configurazione di Jenkins seguendo le istruzioni visualizzate sullo schermo.

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

## <a name="jenkins-environment-variables"></a>Variabili di ambiente di Jenkins

Una variabile di ambiente di Jenkins viene usata per mettere in attesa il nome del server di accesso del Registro contenitori di Azure (ACR). Durante il processo di distribuzione continua di Jenkins si fa riferimento a questa variabile.

Nel portale di amministrazione di Jenkins fare clic su **Manage Jenkins** (Gestisci Jenkins) > **Configure System** (Configura sistema).

In **Global Properties** (Proprietà globali) selezionare **Environment variables** (Variabili di ambiente) e aggiungere una variabile con il nome `ACR_LOGINSERVER` e il valore del server di accesso di ACR.

![Variabili di ambiente di Jenkins](media/aks-jenkins/env-variables.png)

Al termine, fare clic su **Save** (Salva) nella pagina di configurazione di Jenkins.

## <a name="jenkins-credentials"></a>Credenziali di Jenkins

A questo punto archiviare le credenziali di ACR in un oggetto credenziale di Jenkins. Durante il processo di compilazione di Jenkins si farà riferimento a queste credenziali.

Tornare al portale di amministrazione di Jenkins, fare clic su **Credentials** (Credenziali) > **Jenkins** > **Global credentials (unrestricted)** (Credenziali globali senza restrizioni) > **Add Credentials** (Aggiungi credenziali).

Verificare che il tipo di credenziali sia **Username with password** (Nome utente con password) e immettere gli elementi seguenti:

- **Nome utente**: ID dell'uso dell'entità servizio per l'autenticazione con il registro di ACR.
- **Password**: segreto client dell'uso dell'entità servizio per l'autenticazione con il registro di ACR.
- **ID**: identificatore di credenziali, ad esempio `acr-credentials`.

Al termine, il modulo delle credenziali deve aver un aspetto simile all'immagine seguente:

![Credenziali di ACR](media/aks-jenkins/acr-credentials.png)

Fare clic su **OK** e tornare al portale di amministrazione di Jenkins.

## <a name="create-jenkins-project"></a>Creare il progetto Jenkins

Nel portale di amministrazione di Jenkins fare clic su **New Item** (Nuovo elemento).

Assegnare un nome al progetto, ad esempio `azure-vote`, selezionare **Freestyle Project** (Progetto Freestyle) e fare clic su **OK**. 

![Progetto Jenkins](media/aks-jenkins/jenkins-project.png)

In **General** (Generale) selezionare **GitHub project** (Progetto GitHub) e immettere l'URL nel fork del progetto GitHub di voto di Azure.

![Progetto GitHub](media/aks-jenkins/github-project.png)

In **Source Code Management** (Gestione del codice sorgente) selezionare **Git**, immettere l'URL nel fork del repository GitHub di voto di Azure. 

Per le credenziali, fare clic su **Add** (Aggiungi) > **Jenkins**. In **Kind** (Tipo) selezionare **Secret text** (Testo segreto) e immettere il [token di accesso personale di GitHub][git-access-token] come segreto. 

Al termine selezionare **Add** (Aggiungi).

![Credenziali per GitHub](media/aks-jenkins/github-creds.png)

In **Build Triggers** (Trigger di compilazione) selezionare **GitHub hook trigger for GITScm polling** (Trigger di hook GitHub per polling GITScm).

![Trigger di compilazione di Jenkins](media/aks-jenkins/build-triggers.png)

In **Build Environment** (Ambiente di compilazione) selezionare **Use secret texts or files** (Usa testi o file segreti).

![Ambiente di compilazione di Jenkins](media/aks-jenkins/build-environment.png)

In **Bindings** (Associazioni) selezionare **Add** (Aggiungi) > **Username and password (separated)** (Nome utente e password separati). 

Immettere `ACR_ID` per **Username Variable** (Variabile nome utente) e `ACR_PASSWORD` per **Password Variable** (Variabile password).

![Associazioni di Jenkins](media/aks-jenkins/bindings.png)

Aggiungere un'**istruzione di compilazione** di tipo **Execute shell** (Esegui shell) e usare il testo seguente. Questo script compila una nuova immagine del contenitore e la inserisce nel registro di ACR.

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

Aggiungere un'altra **istruzione di compilazione** di tipo **Execute shell** (Esegui shell) e usare il testo seguente. Questo script aggiorna la distribuzione di Kubernetes.

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

Al termine, fare clic su **Save** (Salva).

## <a name="test-the-jenkins-build"></a>Testare la build di Jenkins

Prima di procedere, testare la build di Jenkins. In questo modo si convalida che il processo di compilazione sia stato configurato correttamente, che sia presente il file di autenticazione di Kubernetes corretto e che siano state inserite le credenziali di ACR corrette.

Fare clic su **Build Now** (Compila ora) nel menu a sinistra del progetto. 

![Build di test di Jenkins](media/aks-jenkins/test-build.png)

Durante questo processo, il repository di GitHub viene clonato nel server di compilazione di Jenkins. Viene compilata una nuova immagine del contenitore, che viene inserita nel registro di ACR. Infine l'applicazione di voto di Azure in esecuzione nel cluster di AKS viene aggiornata per usare la nuova immagine. Poiché non sono state apportate modifiche al codice dell'applicazione, questa non è modificata.

Al completamento del processo, è possibile fare clic su **build #1** nella cronologia di compilazione e selezionare **Console Output** (Output console) per visualizzare tutti gli output del processo di compilazione. La riga finale deve indicare che la build ha avuto esito positivo. 

## <a name="create-github-webhook"></a>Creare webhook di GitHub

Successivamente agganciare il repository dell'applicazione al server di compilazione di Jenkins in modo che in qualsiasi commit venga attivata una nuova build.

1. Passare al repository GitHub con fork.
2. Selezionare **Settings** (Impostazioni) e quindi **Integrations & services** (Integrazioni e servizi) sul lato sinistro.
3. Scegliere **Add Service** (Aggiungi servizio), immettere `Jenkins (GitHub plugin)` nella casella filtro e selezionare il plug-in.
4. Per l'URL di aggancio di Jenkins, immettere `http://<publicIp:8080>/github-webhook/` in cui `publicIp` è l'indirizzo IP del server di Jenkins. Assicurarsi di includere la barra finale (/).
5. Selezionare Add service (Aggiungi servizio).
  
![Webhook di GitHub](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>Testare il processo CI/CD end-to-end

Nel computer di sviluppo aprire l'applicazione clonata con un editor di codice. 

Nella directory **/azure-vote/azure-vote** è possibile trovare un file denominato **config_file.cfg**. Aggiornare i valori di voto di questo file usando un valore diverso da quelli inseriti. 

L'esempio seguente mostra un file **config_file.cfg** aggiornato.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Al termine, salvare il file, eseguire il commit delle modifiche ed eseguirne il push nel fork del repository di GitHub. Al termine del commit, il webhook di GitHub attiva una nuova build di Jenkins, che aggiorna l'immagine del contenitore e la distribuzione di AKS. Monitorare il processo di compilazione nella console di amministrazione di Jenkins. 

Al termine della compilazione, passare di nuovo all'endpoint applicazione per osservare le modifiche.

![Voto di Azure aggiornato](media/aks-jenkins/azure-vote-updated-safari.png)

A questo punto, è stato completato un semplice processo di distribuzione continua. La procedura e le configurazioni illustrate in questo esempio consentono di creare un'automazione di compilazione continua più solida e pronta per la produzione.

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az_acr_list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli