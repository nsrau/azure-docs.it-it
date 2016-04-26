<properties
   pageTitle="Distribuire un cluster del servizio contenitore di Azure | Microsoft Azure"
   description="Distribuire un cluster del servizio contenitore di Azure usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contenitori, Micro-servizi, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>

# Distribuire un cluster del servizio contenitore di Azure

Il servizio contenitore di Azure consente la distribuzione rapida delle soluzioni open source di clustering e orchestrazione dei contenitori più diffuse. Con il servizio contenitore di Azure è possibile distribuire cluster DC/OS e Docker Swarm usando i modelli di Azure Resource Manager o il portale di Azure. I cluster vengono distribuiti tramite set di scalabilità di macchine virtuali di Azure e sfruttano i vantaggi delle offerte di rete e di archiviazione di Azure. Per accedere al servizio contenitore di Azure è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Questo documento illustra come distribuire un cluster del servizio contenitore di Azure tramite il [portale di Azure](#creating-a-service-using-the-azure-portal), l'[interfaccia della riga di comando di Azure](#creating-a-service-using-the-azure-cli) e il [modulo di Azure PowerShell](#creating-a-service-using-powershell).

## Creare un servizio usando il portale di Azure

Accedere al portale di Azure, selezionare Nuovo e cercare **Servizio contenitore di Azure** in Azure Marketplace.

![Creare una distribuzione](media/acs-portal1.png) <br />

Selezionare **Servizio contenitore di Azure** e fare clic su **Crea**.

![Creare una distribuzione](media/acs-portal2.png) <br />

Immettere le seguenti informazioni:

- Nome utente: verrà usato per un account in ogni macchina virtuale e ogni set di scalabilità di macchine virtuali nel cluster del servizio contenitore di Azure.
- Sottoscrizione: selezionare una sottoscrizione di Azure.
- Gruppo di risorse: selezionare un gruppo di risorse esistente o crearne uno nuovo.
- Località: selezionare un'area di Azure per la distribuzione del servizio contenitore di Azure.
- Chiave pubblica SSH: aggiungere la chiave pubblica che verrà usata per l'autenticazione delle macchine virtuali nel servizio contenitore di Azure. È molto importante che questa chiave sia priva di interruzioni di riga e che includa il prefisso 'ssh-rsa' e il suffisso 'nomeutente@dominio'. Dovrà essere simile alla seguente, "**ssh-rsa AAAAB3Nz...SNIPPEDCONTENT...UcyupgH azureuser@linuxvm**". Per istruzioni sulla creazione di chiavi SSH, vedere gli articoli relativi a [Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) e [Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/).

Quando si è pronti a continuare, fare clic su **OK**.

![Creare una distribuzione](media/acs-portal3.png) <br />

Selezionare un tipo di orchestrazione. Le opzioni sono:

- DC/OS: distribuisce un cluster DC/OS.
- Swarm: distribuisce un cluster Docker Swarm.

Quando si è pronti a continuare, fare clic su **OK**.

![Creare una distribuzione](media/acs-portal4.png) <br />

Immettere le seguenti informazioni:

- Conteggio master: numero di master nel cluster.
- Conteggio agenti: per Docker Swarm sarà il numero iniziale di agenti nel set di scalabilità degli agenti. Per DC/OS sarà il numero iniziale di agenti in un set di scalabilità privato. Viene creato anche un set di scalabilità pubblico contenente un numero predeterminato di agenti. Il numero di agenti in questo set di scalabilità pubblico incide sul numero di master creati nel cluster, 1 agente pubblico per 1 master e 2 agenti pubblici per 3 o 5 master.
- Dimensioni macchina virtuale dell'agente: specifica le dimensioni delle macchine virtuali dell'agente.
- Prefisso DNS: nome univoco globale che verrà usato come prefisso delle parti chiave dei nomi di dominio completo per il servizio. 

Quando si è pronti a continuare, fare clic su **OK**.

![Creare una distribuzione](media/acs-portal5.png) <br />

Una volta completata la convalida del servizio, fare clic su **OK**.

![Creare una distribuzione](media/acs-portal6.png) <br />

Fare quindi clic su **Crea** per avviare il processo di distribuzione.

![Creare una distribuzione](media/acs-portal7.png) <br />

Se si è scelto di aggiungere la distribuzione al portale di Azure, si potrà visualizzarne lo stato.

![Creare una distribuzione](media/acs-portal8.png) <br />

Una volta completata la distribuzione, il cluster del servizio contenitore di Azure è pronto per l'uso.

## Creare un servizio usando l'interfaccia della riga di comando di Azure

Per creare un'istanza del servizio contenitore di Azure usando la riga di comando, è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). L'interfaccia della riga di comando di Azure deve essere installata e configurata.

Per distribuire un cluster DC/OS o Docker Swarm, selezionare uno dei modelli di GitHub seguenti. Si noti che i modelli si equivalgono, differiscono unicamente nella selezione dell'agente di orchestrazione predefinito.

* [Modello DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Modello Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Successivamente, assicurarsi che l'interfaccia della riga di comando di Azure sia collegata a una sottoscrizione di Azure. A questo scopo, è possibile eseguire questo comando:

```bash
azure account show
```
Se non viene restituito un account Azure, usare il comando seguente per registrare l'interfaccia della riga di comando in Azure.

```bash
azure login -u user@domain.com
```

Successivamente, configurare gli strumenti dell'interfaccia della riga di comando di Azure per l'uso di Azure Resource Manager.

```bash
azure config mode arm
```

Creare un gruppo di risorse e un cluster del servizio contenitore di Azure con il comando seguente, dove:

- **RESOURCE\_GROUP** è il nome del gruppo di risorse da usare per questo servizio.
- **LOCATION** è l'area di Azure in cui verrà creata la distribuzione del gruppo di risorse e del servizio contenitore di Azure.
- **TEMPLATE\_URI** è il percorso del file di distribuzione. **Nota**: deve essere il file RAW, non un puntatore all'interfaccia utente di GitHub. Per trovare questo URL, selezionare il file azuredeploy.json in GitHub e fare clic sul pulsante RAW:

> Nota: quando si esegue questo comando, la shell richiede i valori dei parametri di distribuzione.
 
```bash
# sample deployment

azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### Fornire i parametri del modello

Questa versione del comando richiede all'utente di definire i parametri in modo interattivo. Per specificare i parametri, ad esempio una stringa in formato JSON, è possibile usare l'opzione `-p`. Ad esempio:

 ```bash
 # sample deployment

azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

In alternativa è possibile fornire un file di parametri in formato JSON usando l'opzione `-e`:

 ```bash
 # sample deployment

azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
 ```

Per visualizzare un file di parametri di esempio denominato `azuredeploy.parameters.json`, cercarlo tra i modelli del servizio contenitore di Azure in GitHub.

## Creare un servizio usando PowerShell

È anche possibile distribuire un cluster del servizio contenitore di Azure con PowerShell. Questo documento si basa sul [modulo Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/) versione 1.0.

Per distribuire un cluster DC/OS o Docker Swarm, selezionare uno dei modelli seguenti. Si noti che i modelli si equivalgono, differiscono unicamente nella selezione dell'agente di orchestrazione predefinito.

* [Modello DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Modello Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Prima di creare un cluster nella sottoscrizione di Azure, verificare che la sessione di PowerShell sia stata registrata in Azure. A tale scopo, usare il comando `Get-AzureRMSubscription`:

```powershell
Get-AzureRmSubscription
```

Se è necessario accedere ad Azure, usare il comando `Login-AzureRMAccount`.

```powershell
Login-AzureRmAccount
```

Se si esegue la distribuzione in un nuovo gruppo di risorse, è necessario creare prima di tutto il gruppo di risorse. Per creare un nuovo gruppo di risorse, usare il comando `New-AzureRmResourceGroup` specificando un'area di destinazione e il nome di un gruppo risorse.

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Dopo aver creato il gruppo di risorse è possibile creare il cluster con il comando seguente. Specificare l'URI del modello per il parametro `-TemplateUri`. Quando si esegue questo comando, PowerShell richiede i valori dei parametri di distribuzione.

```powershell
# sample deployment

New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```

### Fornire i parametri del modello

Se si ha familiarità con PowerShell, è possibile cercare un cmdlet tra i parametri disponibili digitando un segno meno (-) e quindi premendo TAB. Questa stessa funzionalità può essere usata anche con i parametri definiti nel modello. Quando si digita il nome del modello, il cmdlet recupera il modello, analizza i parametri e aggiunge i relativi parametri al comando in modo dinamico. In questo modo è molto semplice specificare i valori dei parametri del modello. Inoltre, se si dimentica un valore per un parametro obbligatorio, PowerShell richiede il valore.

Di seguito viene riportato il comando completo con i parametri inclusi. È possibile specificare i propri valori per i nomi delle risorse.

```powershell
# sample deployment

New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## Passaggi successivi
 
Ora che si ha a disposizione un cluster funzionante, vedere i documenti seguenti per informazioni dettagliate sulla connessione e la gestione.
 
[Connettersi a un cluster del servizio contenitore di Azure](./container-service-connect.md) [Utilizzo del servizio contenitore di Azure e DC/OS](./container-service-mesos-marathon-rest.md) [Utilizzo del servizio contenitore di Azure e Docker Swarm](./container-service-docker-swarm.md)

<!---HONumber=AcomDC_0420_2016-->