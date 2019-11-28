---
title: Eseguire la distribuzione nel servizio Azure Kubernetes usando Jenkins e il modello di distribuzione blu/verde
description: Di seguito viene spiegato come eseguire la distribuzione nel servizio Azure Kubernetes usando Jenkins e il modello di distribuzione di tipo blu-verde.
keywords: jenkins, azure, devops, kubernetes, k8s, servizio Azure Kubernetes, distribuzione di tipo blu-verde, recapito continuo, cd
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: ae9c496cd820bf1263cac50fb676990ed65ed0ba
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158547"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Eseguire la distribuzione nel servizio Azure Kubernetes usando Jenkins e il modello di distribuzione di tipo blu-verde

Il servizio Azure Kubernetes gestisce l'ambiente Kubernetes ospitato consentendo di distribuire e gestire applicazioni in contenitori in modo semplice e rapido senza competenze nell'orchestrazione di contenitori. Il servizio Azure Kubernetes elimina anche l'impegno delle operazioni e della manutenzione continui effettuando il provisioning, l'aggiornamento e il ridimensionamento delle risorse su richiesta, senza portare le applicazioni offline. Per altre informazioni sul servizio Azure Kubernetes, vedere la [documentazione specifica](/azure/aks/).

La distribuzione di tipo blu-verde è un modello di recapito continuo Azure DevOps che si basa sul mantenimento di una versione esistente (blu) attiva mentre viene distribuita una versione nuova (verde). In genere questo modello usa il bilanciamento del carico per indirizzare volumi di traffico crescenti alla distribuzione verde. Se il monitoraggio rileva un evento imprevisto, il traffico può essere reindirizzato alla distribuzione di tipo blu che è ancora in esecuzione. Per altre informazioni sul recapito continuo, consultare [Informazioni sul recapito continuo](/azure/devops/what-is-continuous-delivery).

In questa esercitazione è possibile scoprire come si eseguono le seguenti attività:

> [!div class="checklist"]
> * Apprendere informazioni sul modello di distribuzione di tipo blu-verde.
> * Creare un cluster Kubernetes gestito
> * Eseguire uno script di esempio per configurare un cluster Kubernetes.
> * Configurare manualmente un cluster Kubernetes.
> * Creare ed eseguire un processo Jenkins.

## <a name="prerequisites"></a>Prerequisiti
- [Account GitHub](https://github.com): è necessario per clonare il repository di esempio.
- [Interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest): viene usata per creare il cluster Kubernetes.
- [Chocolatey](https://chocolatey.org): un sistema di gestione pacchetti usato per installare kubectl.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): interfaccia della riga di comando usata per l'esecuzione di comandi per i cluster Kubernetes.
- [jq](https://stedolan.github.io/jq/download/): processore JSON leggero da riga di comando.

## <a name="clone-the-sample-app-from-github"></a>Clonare l'app di esempio da GitHub

Nel repository Microsoft in GitHub è possibile trovare un'app di esempio che illustra come distribuire nel servizio Azure Kubernetes usando Jenkins e il modello di tipo blu-verde. In questa sezione si crea una copia tramite fork di questo repository in GitHub e si clona l'app nel sistema locale.

1. Passare al repository GitHub per individuare l'app di esempio [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git).

    ![Screenshot di esempio nel repository GitHub Microsoft](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Creare una copia tramite fork del repository selezionando **Fork** nell'angolo superiore destro della pagina e seguire le istruzioni per creare una copia tramite fork del repository nell'account GitHub personale.

    ![Screenshot dell’opzione di GitHub per eseguire la copia tramite fork](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Dopo la creazione della copia tramite fork del repository si noti che il nome dell'account diventa il nome dell'account personale e una nota indica da dove è stata creata una copia tramite fork del repository (Microsoft).

    ![Screenshot con il nome e la nota dell’account GitHub](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Selezionare **Clona o scarica**.

    ![Screenshot dell’opzione di GitHub per clonare o scaricare un repository](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. Nella finestra **Clone with HTTPS** (Clona con HTTPS) selezionare l'icona di **copia**.

    ![Screenshot dell'opzione GitHub per copiare l'URL di clonazione negli Appunti](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Aprire una finestra di Git Bash o del terminale.

1. Cambiare directory fino alla posizione in cui si vuole memorizzare la copia locale (clone) del repository.

1. Usando il comando `git clone`, clonare l'URL copiato in precedenza.

    ![Schermata del comando di clonazione in Git Bash](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Premere INVIO per avviare il processo di clonazione.

    ![Schermata dei risultati del comando di clonazione in Git Bash](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Passare alla directory appena creata che contiene il clone dell'origine dell'app.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Creare e configurare un cluster Kubernetes gestito

In questa sezione si segue questa procedura:

- Usare l'interfaccia della riga di comando di Azure 2.0 per creare un cluster Kubernetes gestito.
- Informazioni su come configurare un cluster usando lo script di installazione o manualmente.
- Creare un’istanza del servizio di Registro Azure Container.

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Usare l'interfaccia della riga di comando di Azure 2.0 per creare un cluster Kubernetes gestito
Per creare un cluster Kubernetes gestito con l'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), assicurarsi di usare la versione dell'interfaccia della riga di comando di Azure 2.0.25 o successiva.

1. Accedere all'account Azure. Dopo avere immesso il seguente comando, vengono fornite le istruzioni che illustrano come completare l'accesso. 
    
    ```bash
    az login
    ```

1. Quando si esegue il comando `az login` nel passaggio precedente, viene visualizzato un elenco di tutte le sottoscrizioni di Azure, oltre ai relativi ID di sottoscrizione. In questo passaggio si imposta la sottoscrizione di Azure predefinita. Sostituire il segnaposto &lt;your-subscription-id> con l'ID della sottoscrizione di Azure desiderata. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Creare un gruppo di risorse. Sostituire il segnaposto &lt;your-resource-group-name > con il nome del nuovo gruppo di risorse e sostituire il segnaposto &lt;your-location > con la località. Il comando `az account list-locations` visualizza tutte le località di Azure. Durante l'anteprima di servizio Azure Kubernetes non tutte le località sono disponibili. Se si immette una località non valida in questo momento, nel messaggio di errore vengono elencate le località disponibili.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Creare il cluster del servizio Azure Kubernetes. Sostituire il segnaposto &lt;your-resource-group-name > con il nome del gruppo di risorse creato nel passaggio precedente e sostituire &lt;your-kubernetes-cluster-name> con il nome del nuovo cluster. Il processo può richiedere alcuni minuti.

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Configurare il cluster Kubernetes

È possibile configurare una distribuzione di tipo blu/verde in servizio Azure Kubernetes manualmente o con uno script di configurazione fornito nell'esempio clonato in precedenza. In questa sezione verranno illustrati entrambi gli scenari.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Configurare il cluster Kubernetes tramite lo script di configurazione di esempio
1. Modificare il file **deploy/aks/setup/setup.sh** sostituendo i segnaposto seguenti con i valori appropriati per l'ambiente: 

   - **&lt;your-resource-group-name>**
   - **&lt;your-kubernetes-cluster-name>**
   - **&lt;your-location>**
   - **&lt;your-dns-name-suffix>**

     ![Screenshot script setup.sh in bash, con diversi segnaposto evidenziati](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Eseguire lo script di configurazione.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Configurare manualmente un cluster Kubernetes 
1. Scaricare la configurazione di Kubernetes nella cartella del profilo.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Passare alla directory **deploy/aks/setup**. 

1. Eseguire i seguenti comandi **kubectl** per configurare i servizi per l'endpoint pubblico e i due endpoint di test.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Aggiornare il nome DNS per l'endpoint pubblico e quelli di test. Quando si crea un cluster Kubernetes, si crea anche un [gruppo di risorse aggiuntive](https://github.com/Azure/AKS/issues/3) con il modello di denominazione **MC_&lt;nome-gruppo-risorsa> _&lt;nome-cluster-kubernetes>_ &lt;posizione>** .

    Individuare l'indirizzo IP pubblico nel gruppo di risorse.

    ![Screenshot dell'indirizzo IP pubblico nel gruppo di risorse](./media/jenkins-aks-blue-green-deployment/publicip.png)

    Per ognuno dei servizi individuare l'indirizzo IP esterno eseguendo il comando seguente:
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Aggiornare il nome DNS per l'indirizzo IP corrispondente con il comando seguente:

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Ripetere la chiamata per `todoapp-test-blue` e `todoapp-test-green`:

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    È necessario che il nome DNS sia univoco nella sottoscrizione. Per garantire l'univocità, è possibile usare `<your-dns-name-suffix>`.

### <a name="create-an-instance-of-container-registry"></a>Creare un'istanza di Registro contenitori

1. Eseguire il comando `az acr create` per creare un’istanza di Registro contenitori. Nella sezione successiva, è quindi possibile usare `login server` come URL del registro Docker.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Eseguire il comando `az acr credential` per visualizzare le credenziali del Registro contenitori. Annotare il nome utente e la password del registro Docker poiché saranno necessari nella sezione successiva.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Preparare il server Jenkins

In questa sezione verrà illustrato come preparare il server Jenkins per eseguire una compilazione corretta per i test. Tuttavia, è necessario utilizzare un [agente di macchina virtuale di Azure](https://plugins.jenkins.io/azure-vm-agents) o un [agente di contenitore di Azure](https://plugins.jenkins.io/azure-container-agents) per eseguire la creazione di un agente in Azure che si occupi dell’esecuzione di compilazioni. Per ulteriori informazioni, vedere l'articolo Jenkins sulle [implicazioni per la sicurezza di compilazione su master](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

1. Distribuire un [master Jenkins in Azure](https://aka.ms/jenkins-on-azure).

1. Connettersi al server tramite SSH e installare gli strumenti di compilazione sul server in cui si esegue la compilazione.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Installare Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Verificare che l'utente `jenkins` disponga dell'autorizzazione per eseguire i comandi `docker`.

1. [Installare kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Scaricare jq](https://stedolan.github.io/jq/download/).

1. Installare jq con il comando seguente:

   ```bash
   sudo apt-get install jq
   ```
   
1. Installare i plug-in Jenkins eseguendo la procedura seguente nel dashboard di Jenkins:

    1. Selezionare **Manage Jenkins (Gestisci Jenkins) > Manage plugins (Gestisci plug-in) > Available (Disponibili)** .
    1. Cercare e installare il plug-in del servizio Azure Container.

1. Aggiungere le credenziali per gestire le risorse in Azure. Installare il plug-in per le **credenziali di Azure**, se non è già disponibile.

1. Aggiungere le credenziali dell'entità servizio di Azure come il tipo **Entità servizio di Microsoft Azure**.

1. Aggiungere il nome utente e la password del registro Docker di Azure (ottenuti nella sezione "Creare un’istanza di Registro contenitori") come il tipo **Nome utente con password**.

## <a name="edit-the-jenkinsfile"></a>Modificare il file Jenkinsfile

1. Nel proprio repository passare a `/deploy/aks/` e aprire `Jenkinsfile`.

2. Aggiornare il file come indicato di seguito:

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    Aggiornare l'ID delle credenziali del Registro contenitori:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Creare il processo
1. Aggiungere un nuovo processo nel tipo **Pipeline**.

1. Selezionare **Pipeline** > **Definition** > **Pipeline script from SCM** (Pipeline > Definizione > Script di pipeline da gestione controllo codice sorgente).

1. Immettere l'URL del repository di gestione controllo codice sorgente con il valore &lt;your forked-repo>.

1. Immettere il percorso dello script come `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Eseguire il processo

1. Verificare che sia possibile eseguire correttamente il progetto nell'ambiente locale. Ecco come: [Eseguire il progetto sul computer locale](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it).

1. Eseguire il processo Jenkins. La prima volta che si esegue il processo, l'app di elenco attività verrà distribuita da Jenkins nell'ambiente blu, che è l'ambiente inattivo predefinito. 

1. Per verificare che sia stato eseguito il processo, passare agli URL:
    - Endpoint HTTP pubblico: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Endpoint ambiente blu: `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Endpoint ambiente verde: `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Gli endpoint di test dell'ambiente blu e pubblico hanno lo stesso aggiornamento, mentre l'endpoint dell'ambiente verde mostra l'immagine Tomcat predefinita. 

Se si esegue la compilazione più volte, il ciclo passa attraverso le distribuzioni blu e verde. In altre parole, se l'ambiente corrente è blu, il processo viene distribuito e testato in un ambiente verde. Quindi, se i test sono positivi, il processo aggiorna l'endpoint pubblico dell'applicazione per instradare il traffico verso l'ambiente verde.

## <a name="additional-information"></a>Informazioni aggiuntive

Per altre informazioni sulla distribuzione senza tempi di inattività, consultare il [modello di avvio rapido](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create in questa esercitazione non sono più necessarie, è possibile eliminarle.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si rilevano bug con i plug-in Jenkins, segnalare un problema in [Jenkins JIRA](https://issues.jenkins-ci.org/) per il componente specifico.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come eseguire la distribuzione nel servizio Azure Kubernetes usando Jenkins e il modello di distribuzione di tipo blu/verde. Per altre informazioni sul provider Jenkins in Azure, vedere Jenkins nel sito di Azure.

> [!div class="nextstepaction"]
> [Jenkins in Azure](/azure/jenkins/)