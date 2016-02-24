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

Il servizio contenitore di Azure consente la distribuzione rapida delle soluzioni open source di clustering e orchestrazione dei contenitori più diffuse. Con il servizio contenitore di Azure, i cluster Marathon Mesos e Docker Swarm possono essere distribuiti usando il portale o modelli di Gestione risorse di Azure. I cluster vengono distribuiti usando set di scalabilità di macchine virtuali di Azure e sfruttano i vantaggi delle offerte di rete e di archiviazione di Azure. Per accedere al servizio contenitore di Azure è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Questo documento illustra i passaggi necessari per la distribuzione di un cluster del servizio contenitore di Azure usando il [portale di Azure](#creating-a-service-using-the-azure-portal), l'[interfaccia della riga di comando di Azure](#creating-a-service-using-the-azure-cli) e il [modulo di Azure PowerShell](#creating-a-service-using-powershell).
   
## Creare un servizio usando il portale di Azure
 
Selezionare uno dei modelli seguenti per distribuire un cluster Mesos o Docker Swarm. **Nota**: i modelli si equivalgono, differiscono unicamente nella selezione dell'agente di orchestrazione predefinito.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm)
 
Ogni pagina del modello ha un pulsante per la distribuzione in Azure. Facendo clic su questo pulsante viene avviato il portale di Azure con un modulo simile al seguente. <br />

![Creare una distribuzione](media/create-mesos-params.png) <br />

Compilare il modulo in base a questa guida. Al termine, fare clic su OK. <br />

Campo | Descrizione
----------------|-----------
DNSNAMEPREFIX | Deve trattarsi di un valore univoco globale. Questo valore viene usato per creare nomi DNS per ognuna delle parti principali del servizio. Altre informazioni sono riportate di seguito.
AGENTCOUNT | Numero di macchine virtuali che verranno create nel set di scalabilità dell'agente di ACS.
AGENTVMSIZE | Specifica la dimensione delle macchine virtuali dell'agente. Assicurarsi di selezionare una dimensione che fornisca risorse sufficienti a ospitare i contenitori più grandi.
ADMINUSERNAME | Nome utente che verrà usato per un account in ogni macchina virtuale e in ogni set di scalabilità di macchine virtuali del cluster ACS.
ORCHESTRATORTYPE| Selezionare l'agente di orchestrazione da usare nel cluster ACS.
MASTERCOUNT | Numero di macchine virtuali da configurare come master per il cluster. È possibile selezionare 1, ma questo valore non garantisce la resilienza del cluster ed è consigliato solo a scopo di test. Il numero consigliato per un cluster di produzione è 3 o 5. 
SSHRSAPUBLICKEY | È necessario usare SSH per l'autenticazione nelle macchine virtuali. Qui viene aggiunta la chiave pubblica. Prestare molta attenzione quando si incolla il valore della chiave in questa casella. Alcuni editor possono inserire interruzioni di riga nel contenuto, danneggiando quindi la chiave. Assicurarsi che la chiave sia priva di interruzioni di riga e che includa il prefisso ssh-rsa e il suffisso username@domain. L'aspetto della chiave dovrebbe essere simile al seguente: 'ssh-rsa AAAAB3Nz...SNIPPEDCONTENT...UcyupgH azureuser@linuxvm'. Se è necessario creare una chiave SSH, nel sito della documentazione di Azure sono disponibili indicazioni per [Windows](../virtual-machines/virtual-machines-windows-use-ssh-key.md) e [Linux](../virtual-machines/virtual-machines-linux-use-ssh-key.md).
  
Dopo aver impostato i valori appropriati per i parametri, fare clic su OK. Successivamente, specificare un nome per il gruppo di risorse, selezionare un'area e rivedere e accettare le note legali.

> Durante l'anteprima non è previsto alcun addebito per il servizio contenitore di Azure, solo addebiti di calcolo standard per macchine virtuali, risorse di archiviazione, rete e così via.
 
![Selezionare il gruppo di risorse](media/resourcegroup.png)
 
Infine, fare clic su "Crea". Verrà visualizzato nuovamente il dashboard. Se non è stata deselezionata l'opzione "Aggiungi al dashboard" nel pannello di distribuzione, viene visualizzato un riquadro animato con un aspetto simile a questo:

![distribuzione](media/deploy.png)
 
Attendere la creazione del cluster. Al termine, verranno visualizzati alcuni pannelli con le risorse che costituiscono il cluster ACS.
 
![Finished](media/final.png)

## Creare un servizio usando l'interfaccia della riga di comando di Azure

Per creare un'istanza del servizio contenitore di Azure usando la riga di comando, è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una versione di valutazione gratuita. È necessario che l'interfaccia della riga di comando di Azure sia installata e configurata.
 
Selezionare uno dei modelli seguenti per distribuire un cluster Mesos o Docker Swarm. **Nota**: i modelli si equivalgono, differiscono unicamente nella selezione dell'agente di orchestrazione predefinito.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm)
 
Successivamente, assicurarsi che l'interfaccia della riga di comando di Azure sia collegata a una sottoscrizione di Azure. A tale scopo, usare il comando seguente.

```bash
Azure account show
```
Se non viene restituito un account Azure, usare quanto indicato di seguito per registrare l'interfaccia della riga di comando in Azure.
 
```bash
azure login -u user@domain.com
```

Successivamente, configurare gli strumenti dell'interfaccia della riga di comando di Azure per l'uso di Gestione risorse di Azure.
 
```bash
azure config mode arm
```
 
Per creare il cluster in un nuovo gruppo di risorse, è necessario creare prima di tutto il gruppo di risorse. Usare questo comando, dove `GROUP_NAME` è il nome del gruppo di risorse da creare e `REGION` è l'area in cui creare il gruppo di risorse.
 
```bash
azure group create GROUP_NAME REGION
```
 
Dopo aver creato il gruppo di risorse è possibile creare il cluster con questo comando, dove:

- **RESOURCE\_GROUP** è il nome del gruppo di risorse da usare per questo servizio.
- **DEPLOYMENT\_NAME** è il nome della distribuzione.
- **TEMPLATE\_URI** è il percorso del file di distribuzione. **Nota**: deve trattarsi del file RAW, non di un puntatore all'interfaccia utente di GitHub. Per trovare questo URL, selezionare il file azuredeploy.json in GitHub e fare clic sul pulsante RAW:

> Nota: quando si esegue questo comando, la shell richiede i valori dei parametri di distribuzione.
 
```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI
```
 
### Fornire i parametri del modello
 
Questa versione del comando richiede all'utente di definire i parametri in modo interattivo. Per specificare parametri come stringa in formato JSON, è possibile usare l'opzione `-p`. Ad esempio:
 
 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

In alternativa è possibile fornire un file di parametri in formato JSON usando l'opzione `-e`:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON'
 ```
 
Un file di parametri di esempio denominato `azuredeploy.parameters.json` è disponibile con i modelli ACS in GitHub.
 
## Creare un servizio usando PowerShell

Per la distribuzione di un cluster ACS è possibile usare anche PowerShell. Questo documento si basa sul [modulo di Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/) 1.0 e versioni successive.

Selezionare uno dei modelli seguenti per distribuire un cluster Mesos o Docker Swarm. **Nota**: i modelli si equivalgono, differiscono unicamente nella selezione dell'agente di orchestrazione predefinito.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm)

Prima di creare un cluster nella sottoscrizione di Azure, verificare che la sessione di PowerShell sia stata registrata in Azure. Questa operazione può essere eseguita con il comando `Get-AzureRMSubscription`.

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
 
### Provide template parameters
 
If you are familiar with PowerShell, you know that you can cycle through the available parameters for a cmdlet by typing a minus sign (-) and then pressing the TAB key. This same functionality also works with parameters that you define in your template. As soon as you type the template name, the cmdlet fetches the template, parses the parameters, and adds the template parameters to the command dynamically. This makes it very easy to specify the template parameter values. And, if you forget a required parameter value, PowerShell prompts you for the value.
 
Below is the full command with parameters included. You can provide your own values for the names of the resources.

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE\_GROUP\_NAME-TemplateURI TEMPLATE\_URI -adminuser value1 -adminpassword value2 ....
```
 
## Passaggi successivi
 
Ora che si ha a disposizione un cluster funzionante, vedere i documenti seguenti per informazioni dettagliate sulla connessione e la gestione.
 
- [Connettersi al cluster ACS](./container-service-connect.md)
- [Uso di ACS e Mesos](./container-service-mesos-marathon-rest.md)

 

<!----HONumber=AcomDC_0218_2016-->