---
title: Distribuire un cluster del servizio contenitore di Azure | Documentazione Microsoft
description: Distribuire un cluster del servizio contenitore di Azure usando il portale di Azure, l&quot;interfaccia della riga di comando di Azure o PowerShell.
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
ms.date: 12/20/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: dc7ce9f0524567b861a40940f02cd07e0b7b22bf
ms.openlocfilehash: 52331c92a4e3e254c044c9cba85a937b6ba95011


---
# <a name="deploy-an-azure-container-service-cluster"></a>Distribuire un cluster del servizio contenitore di Azure
Il servizio contenitore di Azure consente la distribuzione rapida delle soluzioni open source di clustering e orchestrazione dei contenitori più diffuse. Con il servizio contenitore di Azure è possibile distribuire cluster DC/OS, Kubernetes e Docker Swarm usando i modelli di Azure Resource Manager o il portale di Azure. I cluster vengono distribuiti tramite set di scalabilità di macchine virtuali di Azure e sfruttano i vantaggi delle offerte di rete e di archiviazione di Azure. Per accedere al servizio contenitore di Azure è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

> [!NOTE]
> Il supporto per Kubernetes nel servizio contenitore di Azure è attualmente disponibile in anteprima.
>

Questo documento illustra come distribuire un cluster del servizio contenitore di Azure usando il [portale di Azure](#creating-a-service-using-the-azure-portal), l'[interfaccia della riga di comando di Azure](#creating-a-service-using-the-azure-cli) e il [modulo Azure PowerShell](#creating-a-service-using-powershell).  

## <a name="create-a-service-by-using-the-azure-portal"></a>Creare un servizio usando il portale di Azure
1. Accedere al portale di Azure, selezionare **Nuovo** e quindi cercare **Azure Container Service** (Servizio contenitore di Azure) in Azure Marketplace.

    ![Creare una distribuzione 1](media/acs-portal1.png)  <br />

2. Selezionare **Azure Container Service** (Servizio contenitore di Azure) e fare clic su **Crea**.

    ![Creare una distribuzione 2](media/acs-portal2.png)  <br />

3. Immettere le seguenti informazioni:

    * **Nome utente**: verrà usato per un account in ogni macchina virtuale e ogni set di scalabilità di macchine virtuali nel cluster del servizio contenitore di Azure.
    * **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente o crearne uno nuovo.
    * **Località**: selezionare un'area di Azure per la distribuzione del servizio contenitore di Azure.
    * **Chiave pubblica SSH**: aggiungere la chiave pubblica che verrà usata per l'autenticazione delle macchine virtuali nel servizio contenitore di Azure. È molto importante che questa chiave sia priva di interruzioni di riga e che includa il prefisso 'ssh-rsa' e il suffisso 'username@domain'. L'aspetto della chiave dovrebbe essere simile al seguente: **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. Per indicazioni sulla creazione di chiavi SSH (Secure Shell), vedere gli articoli per [Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) e [Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/).

4. Quando si è pronti per continuare, fare clic su **OK** .

    ![Creare una distribuzione 3](media/acs-portal3.png)  <br />

5. Selezionare un tipo di orchestrazione. Le opzioni sono:

    * **DC/OS**: distribuisce un cluster DC/OS.
    * **Swarm**: distribuisce un cluster Docker Swarm.
    * **Kubernetes**: consente di distribuire un cluster Kubernetes.

6. Quando si è pronti per continuare, fare clic su **OK** .

    ![Creare una distribuzione 4](media/acs-portal4-new.png)  <br />

7. Se è selezionato **Kubernetes** nell'elenco a discesa, è necessario immettere l'ID e il segreto del client dell'entità servizio. Per altre informazioni, vedere [About the service principal for a Kubernetes cluster](container-service-kubernetes-service-principal.md) (Informazioni sull'entità servizio per un cluster Kubernetes).

    ![Creare una distribuzione 4.5](media/acs-portal10.PNG)  <br />

7. Nel pannello di impostazioni del **servizio contenitore di Azure** immettere le informazioni seguenti.

    * **Master count**(Conteggio master): numero di master nel cluster. Se è selezionato Kubernetes, il numero di master è impostato sul valore predefinito 1.
    * **Conteggio agenti**: per Docker Swarm e Kubernetes sarà il numero iniziale di agenti nel set di scalabilità degli agenti. Per DC/OS sarà il numero iniziale di agenti in un set di scalabilità privato. Viene creato anche un set di scalabilità pubblico contenente un numero predeterminato di agenti. Il numero di agenti in questo set di scalabilità pubblico viene determinato in base al numero di master creati nel cluster, ovvero un agente pubblico per un master e due agenti pubblici per tre o cinque master.
    * **Agent virtual machine size**(Dimensioni macchina virtuale dell'agente): specifica le dimensioni delle macchine virtuali dell'agente.
    * **Prefisso DNS**: nome univoco globale che verrà usato come prefisso delle parti chiave dei nomi di dominio completo per il servizio.

8. Quando si è pronti per continuare, fare clic su **OK** .

    ![Creare una distribuzione 5](media/acs-portal5.png)  <br />

9. Al termine della convalida del servizio, fare clic su **OK** .

    ![Creare una distribuzione 6](media/acs-portal6.png)  <br />

10. Fare quindi clic su **Purchase** (Acquista) per avviare il processo di distribuzione.

    ![Creare una distribuzione 7](media/acs-portal7.png)  <br />

    Se si è scelto di aggiungere la distribuzione al portale di Azure, si potrà visualizzarne lo stato.

    ![Creare una distribuzione 8](media/acs-portal8.png)  <br />

Una volta completata la distribuzione, il cluster del servizio contenitore di Azure è pronto per l'uso.

## <a name="create-a-service-by-using-the-azure-cli"></a>Creare un servizio usando l'interfaccia della riga di comando di Azure
Per creare un'istanza del servizio contenitore di Azure usando la riga di comando, è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). L'interfaccia della riga di comando di Azure deve essere [installata](../xplat-cli-install.md) e [configurata](../xplat-cli-connect.md).

1. Per distribuire un cluster DC/OS, Docker Swarm o Kubernetes, selezionare uno dei modelli seguenti da GitHub. 

    * [Modello DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Modello Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Modello Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Assicurarsi quindi che l'interfaccia della riga di comando di Azure sia collegata a una sottoscrizione di Azure. A questo scopo, è possibile eseguire questo comando:

    ```bash
    azure account show
    ```
    Se non viene restituito un account Azure, usare il comando seguente per registrare l'interfaccia della riga di comando in Azure.

    ```bash
    azure login -u user@domain.com
    ```

3. Configurare gli strumenti dell'interfaccia della riga di comando di Azure per l'uso di Azure Resource Manager.

    ```bash
    azure config mode arm
    ```

4. Creare un gruppo di risorse e un cluster del servizio contenitore di Azure con il comando seguente, dove:

    * **RESOURCE_GROUP** è il nome del gruppo di risorse da usare per questo servizio.
    * **LOCATION** è l'area di Azure in cui verrà creata la distribuzione del gruppo di risorse e del servizio contenitore di Azure.
    * **TEMPLATE_URI** è il percorso del file di distribuzione. Si noti che deve trattarsi del file RAW, non di un puntatore all'interfaccia utente di GitHub. Per trovare questo URL, selezionare il file azuredeploy.json in GitHub e fare clic sul pulsante **RAW** .

    > [!NOTE]
    > Quando si esegue questo comando, la shell richiede i valori dei parametri di distribuzione.
    > 

    ```bash
    azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
    ```

### <a name="provide-template-parameters"></a>Fornire i parametri del modello
Questa versione del comando richiede all'utente di definire i parametri in modo interattivo. Per specificare i parametri, ad esempio una stringa in formato JSON, è possibile usare l'opzione `-p` . ad esempio:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
```

In alternativa è possibile fornire un file di parametri in formato JSON usando l'opzione `-e` :

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
```

Per visualizzare un file di parametri di esempio denominato `azuredeploy.parameters.json`, cercarlo tra i modelli del servizio contenitore di Azure in GitHub.

## <a name="create-a-service-by-using-powershell"></a>Creare un servizio usando PowerShell
È anche possibile distribuire un cluster del servizio contenitore di Azure con PowerShell. Questo documento si basa sul [modulo Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/)versione 1.0.

1. Per distribuire un cluster DC/OS, Docker Swarm o Kubernetes, selezionare uno dei modelli seguenti. Si noti che i modelli si equivalgono, differiscono unicamente nella selezione dell'agente di orchestrazione predefinito.

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

4. Se si esegue la distribuzione in un nuovo gruppo di risorse, è necessario creare prima di tutto il gruppo di risorse. Per creare un nuovo gruppo di risorse, usare il comando `New-AzureRmResourceGroup` e specificare un'area di destinazione e il nome di un gruppo risorse:

    ```powershell
    New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
    ```

5. Dopo aver creato il gruppo di risorse è possibile creare il cluster con il comando seguente. Specificare l'URI del modello per il parametro `-TemplateUri` . Quando si esegue questo comando, PowerShell richiede i valori dei parametri di distribuzione.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

### <a name="provide-template-parameters"></a>Fornire i parametri del modello
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




<!--HONumber=Dec16_HO3-->


