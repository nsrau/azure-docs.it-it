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

Il servizio contenitore di Azure consente la distribuzione rapida delle soluzioni open source di clustering e orchestrazione dei contenitori più diffuse. Con il servizio contenitore di Azure è possibile distribuire cluster Marathon Mesos e Docker Swarm usando il portale di Azure o modelli di Azure Resource Manager. I cluster vengono distribuiti tramite set di scalabilità di macchine virtuali di Azure e sfruttano i vantaggi delle offerte di rete e di archiviazione di Azure. Per accedere al servizio contenitore di Azure è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Questo documento illustra come distribuire un cluster del servizio contenitore di Azure tramite il [portale di Azure](#creating-a-service-using-the-azure-portal), l'[interfaccia della riga di comando di Azure](#creating-a-service-using-the-azure-cli) e il [modulo di Azure PowerShell](#creating-a-service-using-powershell).

## Creare un servizio usando il portale di Azure

Per distribuire un cluster Mesos o Docker Swarm, selezionare uno dei modelli seguenti in GitHub. Si noti che i modelli si equivalgono, differiscono unicamente nella selezione dell'agente di orchestrazione predefinito.

* [Modello Mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Modello Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Quando si seleziona il pulsante **Distribuisci in Azure** nella pagina di un modello, nel portale di Azure viene visualizzato un modulo simile al seguente: <br />

![Creare una distribuzione con un form](media/create-mesos-params.png) <br />

Compilare il modulo in base a questa guida. Al termine, fare clic su **OK**. <br />

Campo | Descrizione
----------------|-----------
DNSNAMEPREFIX | Deve trattarsi di un valore univoco globale. Questo valore viene usato per creare nomi DNS per ognuna delle parti principali del servizio. Per altre informazioni, vedere più avanti in questo articolo.
AGENTCOUNT | Numero di macchine virtuali che verranno create nel set di scalabilità dell'agente del servizio contenitore di Azure.
AGENTVMSIZE | Dimensione delle macchine virtuali dell'agente. Assicurarsi di selezionare una dimensione che fornisca risorse sufficienti a ospitare i contenitori più grandi.
ADMINUSERNAME | Nome utente che verrà usato per un account in ogni macchina virtuale e in ogni set di scalabilità di macchine virtuali del cluster del servizio contenitore di Azure.
ORCHESTRATORTYPE| Tipo di orchestratore da usare nel cluster del servizio contenitore di Azure.
MASTERCOUNT | Numero di macchine virtuali da configurare come master per il cluster. È possibile selezionare 1, ma questo valore non garantisce la resilienza del cluster ed è consigliato solo a scopo di test. Per un cluster di produzione è consigliabile selezionare 3 o 5.
SSHRSAPUBLICKEY | È necessario usare Secure Shell (SSH) per l'autenticazione nelle macchine virtuali. Qui viene aggiunta la chiave pubblica. Prestare molta attenzione quando si incolla il valore della chiave in questa casella. Alcuni editor possono inserire interruzioni di riga nel contenuto, danneggiando quindi la chiave. Assicurarsi che la chiave sia priva di interruzioni di riga e che includa il prefisso ssh-rsa e il suffisso username@domain. L'aspetto della chiave dovrebbe essere simile al seguente: 'ssh-rsa AAAAB3Nz...SNIPPEDCONTENT...UcyupgH azureuser@linuxvm'. Se è necessario creare una chiave SSH, nel sito della documentazione di Azure sono disponibili indicazioni per [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) e [Linux](../virtual-machines/virtual-machines-linux-ssh-from-linux.md).

Dopo aver impostato i valori appropriati per i parametri, selezionare **OK**. Successivamente, specificare un nome per il gruppo di risorse, selezionare un'area e rivedere e accettare le note legali.

> [AZURE.NOTE] Durante l'anteprima, non è previsto alcun addebito per il servizio contenitore di Azure. Sono previsti unicamente gli addebiti di calcolo standard per macchina virtuale, archiviazione e rete.

![Selezionare il gruppo di risorse](media/resourcegroup.png)

Infine, selezionare **Crea**. Tornare al dashboard. Se non è stata deselezionata la casella di controllo **Aggiungi al dashboard** nel pannello di distribuzione, viene visualizzato un riquadro animato con un aspetto simile a questo:

![Riquadro del modello di distribuzione](media/deploy.png)

Attendere la creazione del cluster. Dopo la creazione del cluster, vengono visualizzati alcuni pannelli con le risorse che costituiscono il cluster del servizio contenitore di Azure.

![Finished](media/final.png)

## Creare un servizio usando l'interfaccia della riga di comando di Azure

Per creare un'istanza del servizio contenitore di Azure usando la riga di comando, è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). L'interfaccia della riga di comando di Azure deve essere installata e configurata.

Per distribuire un cluster Mesos o Docker Swarm, selezionare uno dei modelli seguenti in GitHub. Si noti che i modelli si equivalgono, differiscono unicamente nella selezione dell'agente di orchestrazione predefinito.

* [Modello Mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Modello Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Successivamente, assicurarsi che l'interfaccia della riga di comando di Azure sia collegata a una sottoscrizione di Azure. A questo scopo, è possibile eseguire questo comando:

```bash
Azure account show
```
Se non viene restituito un account Azure, usare il comando seguente per registrare l'interfaccia della riga di comando in Azure.

```bash
azure login -u user@domain.com
```

Successivamente, configurare gli strumenti dell'interfaccia della riga di comando di Azure per l'uso di Azure Resource Manager.

```bash
azure config mode arm
```

Per creare il cluster in un nuovo gruppo di risorse, è necessario creare prima di tutto il gruppo di risorse. Usare il comando seguente, dove `GROUP_NAME` è il nome del gruppo di risorse da creare e `REGION` è l'area in cui creare il gruppo di risorse:

```bash
azure group create GROUP_NAME REGION
```

Dopo aver creato il gruppo di risorse è possibile creare il cluster con questo comando, dove:

- **RESOURCE\_GROUP** è il nome del gruppo di risorse da usare per questo servizio.
- **DEPLOYMENT\_NAME** è il nome della distribuzione.
- **TEMPLATE\_URI** è il percorso del file di distribuzione. Si noti che deve trattarsi del file RAW, *non* di un puntatore all'interfaccia utente di GitHub. Per trovare questo URL, selezionare il file azuredeploy.json in GitHub e fare clic sul pulsante **RAW**.

> [AZURE.NOTE] Quando si esegue questo comando, la shell richiede i valori dei parametri di distribuzione.

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI
```

### Fornire i parametri del modello

Questa versione del comando richiede all'utente di definire i parametri in modo interattivo. Per specificare i parametri, ad esempio una stringa in formato JSON, è possibile usare l'opzione `-p`. Ad esempio:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

In alternativa è possibile fornire un file di parametri in formato JSON usando l'opzione `-e`:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
 ```

Per un file di parametri di esempio denominato `azuredeploy.parameters.json`, cercare tra i modelli del servizio contenitore di Azure in GitHub.

## Creare un servizio usando PowerShell

È anche possibile distribuire un cluster del servizio contenitore di Azure con PowerShell. Questo documento si basa sul [modulo di Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/) versione 1.0.

Per distribuire un cluster Mesos o Docker Swarm, selezionare uno dei modelli seguenti. Si noti che i modelli si equivalgono, differiscono unicamente nella selezione dell'agente di orchestrazione predefinito.

* [Modello Mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Modello Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Prima di creare un cluster nella sottoscrizione di Azure, verificare che la sessione di PowerShell sia stata registrata in Azure. A tale scopo, usare il comando `Get-AzureRMSubscription`:

```powershell
Get-AzureRmSubscription
```

Se è necessario accedere ad Azure, usare il comando `Login-AzureRMAccount`.

```powershell
Login-AzureRmAccount
```

Se si esegue la distribuzione in un nuovo gruppo di risorse, è necessario creare prima di tutto il gruppo di risorse. Per creare un nuovo gruppo di risorse, usare il comando `New-AzureRmResourceGroup` specificando un'area di destinazione e un nome del gruppo risorse.

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Dopo aver creato il gruppo di risorse è possibile creare il cluster con il comando seguente. L'URI del modello verrà specificato per il parametro `-TemplateUri`. Quando si esegue questo comando, PowerShell richiede i valori dei parametri di distribuzione.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```

### Fornire i parametri del modello

Se si ha familiarità con PowerShell, è possibile cercare un cmdlet tra i parametri disponibili digitando un segno meno (-) e quindi premendo TAB. Questa stessa funzionalità può essere usata anche con i parametri definiti nel modello. Quando si digita il nome del modello, il cmdlet recupera il modello, analizza i parametri e aggiunge i relativi parametri al comando in modo dinamico. In questo modo è molto semplice specificare i valori dei parametri del modello. Inoltre, se si dimentica un valore per un parametro obbligatorio, PowerShell richiede il valore.

Di seguito viene riportato il comando completo con i parametri inclusi. È possibile specificare i propri valori per i nomi delle risorse.

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## Passaggi successivi

Ora che si ha a disposizione un cluster funzionante, vedere gli articoli seguenti per informazioni dettagliate sulla connessione e la gestione.

- [Connettersi a un cluster del servizio contenitore di Azure](./container-service-connect.md)
- [Uso del servizio contenitore di Azure e di Mesos](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0413_2016-->