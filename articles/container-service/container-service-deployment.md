---
title: Distribuire un cluster per contenitori Docker in Azure | Documentazione Microsoft
description: Distribuire un cluster del servizio contenitore di Azure usando il portale di Azure o il modello di Azure Resource Manager.
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenitori, Micro-services, Mesos, Azure, dcos, swarm, kubernetes, servizio contenitore di azure, acs
ms.assetid: 696a736f-9299-4613-88c6-7177089cfc23
ms.service: container-service
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 01fe5302e1c596017755c4669103bac910e3452c
ms.openlocfilehash: 470bf39bf0e61325f36a2f45316f57545c69e3de


---
# <a name="deploy-an-azure-container-service-cluster"></a>Distribuire un cluster del servizio contenitore di Azure



Il servizio contenitore di Azure consente la distribuzione rapida delle soluzioni open source di clustering e orchestrazione dei contenitori più diffuse. Questo documento illustra la distribuzione di un cluster del servizio contenitore di Azure usando il portale di Azure o il modello di avvio rapido di Azure Resource Manager. 

> [!NOTE]
> Il supporto per Kubernetes nel servizio contenitore di Azure è attualmente disponibile in anteprima.

È anche possibile distribuire un cluster del servizio contenitore di Azure usando l'[interfaccia della riga di comando di Azure 2.0 (anteprima)](container-service-create-acs-cluster-cli.md) o le API del servizio contenitore di Azure.



## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**: nel caso in cui non sia disponibile è possibile usare una [versione di prova gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

* **Chiave pubblica SSH**: durante la distribuzione tramite il portale o uno dei modelli di avvio rapido di Azure, è necessario fornire la chiave pubblica per l'autenticazione nelle macchine virtuali del servizio contenitore di Azure. Per creare chiavi SSH (Secure Shell), vedere le informazioni aggiuntive per [OS X e Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) e [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

* **ID client e segreto dell'entità servizio** (solo Kubernetes): per altre informazioni e istruzioni sulla creazione di un'entità servizio, vedere [Informazioni sull'entità servizio per un cluster Kubernetes](container-service-kubernetes-service-principal.md).



## <a name="create-a-cluster-by-using-the-azure-portal"></a>Creare un cluster usando il portale di Azure
1. Accedere al portale di Azure, selezionare **Nuovo** e quindi cercare **Azure Container Service** (Servizio contenitore di Azure) in Azure Marketplace.

    ![Servizio contenitore di Azure nel Marketplace](media/container-service-deployment/acs-portal1.png)  <br />

2. Selezionare **Azure Container Service** (Servizio contenitore di Azure) e fare clic su **Crea**.

    ![Creare un servizio contenitore](media/container-service-deployment/acs-portal2.png)  <br />

3. Immettere le seguenti informazioni:

    * **Nome utente**: nome utente che verrà usato per un account in ogni macchina virtuale e in ogni set di scalabilità di macchine virtuali del cluster del servizio contenitore di Azure.
    * **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente o crearne uno nuovo.
    * **Località**: selezionare un'area di Azure per la distribuzione del servizio contenitore di Azure.
    * **Chiave pubblica SSH**: aggiungere la chiave pubblica usata per l'autenticazione nelle macchine virtuali nel servizio contenitore di Azure. È molto importante che questa chiave sia priva di interruzioni di riga e includa il prefisso `ssh-rsa`. Il suffisso `username@domain` è facoltativo. La chiave dovrebbe essere simile alla seguente: **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. 

4. Quando si è pronti per continuare, fare clic su **OK** .

    ![Impostazioni di base](media/container-service-deployment/acs-portal3.png)  <br />

5. Selezionare un tipo di orchestrazione. Le opzioni sono:

  * **DC/OS**: distribuisce un cluster DC/OS.
  * **Swarm**: distribuisce un cluster Docker Swarm.
  * **Kubernetes**: consente di distribuire un cluster Kubernetes


6. Quando si è pronti per continuare, fare clic su **OK** .

    ![Scegliere un agente di orchestrazione](media/container-service-deployment/acs-portal4-new.png)  <br />

7. Se è selezionato **Kubernetes** nell'elenco a discesa, è necessario immettere l'ID e il segreto del client dell'entità servizio. Per altre informazioni, vedere [About the service principal for a Kubernetes cluster](container-service-kubernetes-service-principal.md) (Informazioni sull'entità servizio per un cluster Kubernetes).

    ![Immettere l'entità servizio per Kubernetes](media/container-service-deployment/acs-portal10.png)  <br />

7. Nel pannello di impostazioni del **servizio contenitore di Azure** immettere le informazioni seguenti.

    * **Master count**(Conteggio master): numero di master nel cluster. Se è selezionato Kubernetes, il numero di master è impostato sul valore predefinito 1.
    * **Conteggio agenti**: per Docker Swarm e Kubernetes, questo valore è il numero iniziale di agenti nel set di scalabilità degli agenti. Per DC/OS si tratta del numero iniziale di agenti in un set di scalabilità privato. Per DC/OS viene creato anche un set di scalabilità pubblico contenente un numero predeterminato di agenti. Il numero di agenti in questo set di scalabilità pubblico viene determinato in base al numero di master creati nel cluster, ovvero un agente pubblico per un master e due agenti pubblici per tre o cinque master.
    * **Agent virtual machine size**(Dimensioni macchina virtuale dell'agente): specifica le dimensioni delle macchine virtuali dell'agente.
    * **Prefisso DNS**: nome univoco globale usato come prefisso delle parti chiave dei nomi di dominio completi per il servizio.
    * **Diagnostica delle macchine virtuali**: per alcuni agenti di orchestrazione è possibile scegliere di abilitare la diagnostica delle macchine virtuali.

8. Quando si è pronti per continuare, fare clic su **OK** .

    ![Impostazioni del servizio contenitore](media/container-service-deployment/acs-portal5.png)  <br />

9. Al termine della convalida del servizio, fare clic su **OK** .

    ![Convalida](media/container-service-deployment/acs-portal6.png)  <br />

10. Leggere le condizioni. Fare quindi clic su **Acquista** per avviare il processo di distribuzione.

    ![Purchase](media/container-service-deployment/acs-portal7.png)  <br />

    Se si è scelto di aggiungere la distribuzione al portale di Azure, si potrà visualizzarne lo stato.

    ![Deployment Status](media/container-service-deployment/acs-portal8.png)  <br />

La distribuzione richiede alcuni minuti. Al termine, il cluster del servizio contenitore di Azure è pronto per l'uso.



## <a name="create-a-cluster-by-using-a-quickstart-template"></a>Creare un cluster usando un modello di avvio rapido
Sono disponibili modelli di avvio rapido di Azure per distribuire un cluster nel servizio contenitore di Azure. I modelli di avvio rapido forniti possono essere modificati per includere una configurazione di Azure aggiuntiva o avanzata. Per creare un'istanza del servizio contenitore di Azure usando un modello di avvio rapido di Azure è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una [versione di prova gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). 

Seguire questi passaggi per distribuire un cluster usando un modello e l'interfaccia della riga di comando di Azure 2.0 (anteprima). Vedere le [istruzioni di installazione e configurazione](/cli/azure/install-az-cli2.md).

> [!NOTE] 
> In un sistema Windows è possibile usare una procedura simile per distribuire un modello tramite Azure PowerShell. Vedere i passaggi più avanti in questa sezione. È anche possibile distribuire un modello con il [portale](../azure-resource-manager/resource-group-template-deploy-portal.md) o altri metodi.

1. Per distribuire un cluster DC/OS, Docker Swarm o Kubernetes, selezionare uno dei modelli seguenti da GitHub. Si noti che i modelli DC/OS e Swarm si equivalgono, differiscono unicamente nella selezione dell'agente di orchestrazione predefinito.

    * [Modello DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Modello Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Modello Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Accedere all'account Azure (`az login`) e assicurarsi che l'interfaccia della riga di comando di Azure sia collegata alla sottoscrizione di Azure. È possibile visualizzare la sottoscrizione predefinita usando il comando seguente:

    ```azurecli
    az account show
    ```
    
    Se si ha più di una sottoscrizione ed è necessario impostare una sottoscrizione predefinita diversa, eseguire `az account set --subscription` e specificare il nome o l'ID sottoscrizione.

3. Come procedura consigliata, usare un nuovo gruppo di risorse per la distribuzione. Per creare un gruppo di risorse, usare il comando `az group create` e specificare il nome e il percorso del gruppo di risorse: 

    ```azurecli
    az group create --name "RESOURCE_GROUP" --location "LOCATION"
    ```

4. Creare un file JSON contenente i parametri obbligatori per il modello. Scaricare il file dei parametri denominato `azuredeploy.parameters.json` disponibile con il modello `azuredeploy.json` del servizio contenitore di Azure in GitHub. Immettere i valori dei parametri necessari per il cluster. 

    Per usare ad esempio il [modello DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos), specificare i valori di parametro per `dnsNamePrefix` e `sshRSAPublicKey`. Vedere le descrizioni in `azuredeploy.json` e le opzioni per gli altri parametri.  
 

5. Creare un cluster del servizio contenitore passando il file dei parametri di distribuzione con il comando seguente, dove:

    * **RESOURCE_GROUP** è il nome del gruppo di risorse creato nel passaggio precedente.
    * **DEPLOYMENT_NAME** (facoltativo) è il nome assegnato alla distribuzione.
    * **TEMPLATE_URI** è il percorso del file di distribuzione `azuredeploy.json`. Questo URI deve essere il file RAW, non un puntatore all'interfaccia utente di GitHub. Per trovare questo URI, selezionare il file `azuredeploy.json` in GitHub e fare clic sul pulsante **Raw**.  

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters @azuredeploy.parameters.json
    ```

    È anche possibile specificare i parametri come stringa in formato JSON nella riga di comando. Usare un comando simile al seguente:

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters "{ \"param1\": {\"value1\"} … }"
    ```

    > [!NOTE]
    > La distribuzione richiede alcuni minuti.
    > 

### <a name="equivalent-powershell-commands"></a>Comandi di PowerShell equivalenti
È anche possibile distribuire un modello di cluster del servizio contenitore di Azure con PowerShell. Questo documento si basa sul [modulo Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/)versione 1.0.

1. Per distribuire un cluster DC/OS, Docker Swarm o Kubernetes, selezionare uno dei modelli seguenti. Si noti che i modelli DC/OS e Swarm si equivalgono, differiscono unicamente nella selezione dell'agente di orchestrazione predefinito.

    * [Modello DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Modello Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Modello Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Prima di creare un cluster nella sottoscrizione di Azure, verificare che la sessione di PowerShell sia stata registrata in Azure. A questo scopo, usare il comando `Get-AzureRMSubscription` :

    ```powershell
    Get-AzureRmSubscription
    ```

3. Se è necessario accedere ad Azure, usare il comando `Login-AzureRMAccount` :

    ```powershell
    Login-AzureRmAccount
    ```

4. Come procedura consigliata, usare un nuovo gruppo di risorse per la distribuzione. Per creare un gruppo di risorse, usare il comando `New-AzureRmResourceGroup` e specificare il nome e l'area di destinazione del gruppo di risorse:

    ```powershell
    New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
    ```

5. Dopo aver creato il gruppo di risorse è possibile creare il cluster con il comando seguente. Specificare l'URI del modello per il parametro `-TemplateUri` . Quando si esegue questo comando, PowerShell richiede i valori dei parametri di distribuzione.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

#### <a name="provide-template-parameters"></a>Fornire i parametri del modello
Se si ha familiarità con PowerShell, è possibile cercare un cmdlet tra i parametri disponibili digitando un segno meno (-) e quindi premendo TAB. Questa stessa funzionalità può essere usata anche con i parametri definiti nel modello. Quando si digita il nome del modello, il cmdlet recupera il modello, analizza i parametri e aggiunge i relativi parametri al comando in modo dinamico. In questo modo è molto semplice specificare i valori dei parametri del modello. Inoltre, se si dimentica un valore per un parametro obbligatorio, PowerShell richiede il valore.

Di seguito viene riportato il comando completo con i parametri inclusi. È possibile specificare i propri valori per i nomi delle risorse.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Passaggi successivi
Ora che si ha a disposizione un cluster funzionante, vedere i documenti seguenti per informazioni dettagliate sulla connessione e la gestione:

* [Connettersi a un cluster del servizio contenitore di Azure](container-service-connect.md)
* [Gestione di contenitori tramite l'API REST](container-service-mesos-marathon-rest.md)
* [Gestione dei contenitori con Docker Swarm](container-service-docker-swarm.md)
* [Uso del servizio contenitore di Azure e Kubernetes](container-service-kubernetes-walkthrough.md)



<!--HONumber=Feb17_HO1-->


