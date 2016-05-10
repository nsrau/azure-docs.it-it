<properties
	pageTitle="Creare una VM Linux usando un modello di Azure | Microsoft Azure"
	description="Creare una VM Linux in Azure con un modello di Azure Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/29/2016"
	ms.author="v-livech"/>

# Creare una VM Linux usando un modello di Azure

Questo articolo illustra come distribuire rapidamente una macchina virtuale Linux in Azure usando un modello di Azure. L'articolo richiede un account Azure ([ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)) e [l'interfaccia della riga di comando di Azure](../xplat-cli-install.md) connessa (`azure login`) e in modalità Resource Manager (`azure config mode arm`). È anche possibile distribuire rapidamente una VM Linux usando il [portale di Azure](virtual-machines-linux-quick-create-portal.md) o l'[interfaccia della riga di comando di Azure](virtual-machines-linux-quick-create-cli.md).


## Breve descrizione del comando

Negli esempi di comandi seguenti sostituire i valori compresi tra &lt; e &gt; con i valori dell'ambiente locale.

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## Procedura dettagliata

I modelli consentono di creare VM in Azure con le impostazioni da personalizzare durante l'avvio, ad esempio i nomi utente e i nomi host. Questo articolo è incentrato sull'avvio di una VM Ubuntu con un modello di Azure che crea un gruppo di sicurezza di rete con una sola porta aperta (22 per SSH) e che richiede chiavi SSH per l'accesso.

I modelli di Azure Resource Manager sono file JSON che possono essere usati per semplici attività occasionali, ad esempio l'avvio di una VM Ubuntu come descritto in questo articolo, o per creare complesse configurazioni di Azure di interi ambienti, quale come una distribuzione di test, sviluppo o produzione dalla rete al sistema operativo fino alla distribuzione di stack di applicazioni.

## Creare la VM Linux

L'esempio di codice seguente illustra come chiamare `azure group create` per creare un gruppo di risorse e distribuire una VM Linux protetta con SSH, usando contemporaneamente [questo modello di Azure Resource Manager](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Tenere presente che nell'esempio è necessario usare nomi univoci per l'ambiente locale. Questo esempio usa `quicksecuretemplate` come nome del gruppo di risorse, `securelinux` come nome della VM e `quicksecurelinux` come nome del sottodominio.

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

Output

```bash
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== vlivech@azure
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

È possibile creare un nuovo gruppo di risorse e distribuire una VM usando il parametro `--template-uri` oppure è possibile scaricare o creare un modello in locale e passarlo usando il parametro `--template-file` con un percorso del file modello come argomento. L'interfaccia della riga di comando di Azure richiede i parametri necessari per il modello.

## Passaggi successivi

Dopo aver creato le VM Linux con i modelli, è possibile vedere quali altri framework per app sono disponibili per la distribuzione con i modelli. Eseguire una ricerca nella [raccolta di modelli](https://azure.microsoft.com/documentation/templates/) per scoprire quali framework per app si possono distribuire successivamente.

<!---HONumber=AcomDC_0504_2016-->