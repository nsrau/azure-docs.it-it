<properties 
   pageTitle="Panoramica di Provider di risorse di rete | Microsoft Azure"
   description="Introduzione al nuovo Provider di risorse di rete in Gestione risorse di Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos" />

# Provider di risorse di rete
Un fattore essenziale per il successo di un'azienda è la possibilità di creare e gestire applicazioni presenti in rete su larga scala in modo agile, flessibile, sicuro e ripetibile. Gestione risorse di Azure (ARM) consente di creare tali applicazioni sotto forma di un'unica raccolta di risorse nei gruppi di risorse. Queste risorse vengono gestite tramite diversi provider di risorse in ARM.

Gestione risorse di Azure si basa su diversi provider di risorse per fornire l'accesso alle risorse. Sono disponibili tre provider di risorse principali: Rete, Archiviazione e Calcolo. In questo documento vengono illustrate le caratteristiche e i vantaggi del Provider di risorse di rete, tra cui:

- **Metadati**: le informazioni alle risorse possono essere aggiunte usando i tag. Questi tag possono essere usati per tenere traccia dell'utilizzo delle risorse tra le sottoscrizioni e i gruppi di risorse.
- **Maggior controllo della rete**: le risorse di rete sono a regime di controllo libero ed è possibile controllarle in modo più granulare. Questo consente una maggiore flessibilità nella gestione delle risorse di rete.
- **Configurazione più veloce**: grazie al regime di controllo libero, è possibile creare e orchestrare in parallelo le risorse di rete. Il tempo di configurazione viene ridotto drasticamente.
- **Controllo degli accessi in base al ruolo**: questa funzionalità fornisce ruoli predefiniti con uno specifico ambito di sicurezza, oltre a consentire la creazione di ruoli personalizzati per una gestione sicura. 
- **Gestione e distribuzione più semplice**: la distribuzione e la gestione delle applicazioni è più semplice perché è possibile creare un intero stack di applicazioni sotto forma di un'unica raccolta di risorse in un gruppo di risorse. Inoltre, la distribuzione risulta più rapida perché è possibile eseguirla semplicemente fornendo un payload JSON del modello.
- **Personalizzazione rapida**: è possibile usare modelli con stile dichiarativo per abilitare la personalizzazione ripetibile e rapida delle distribuzioni. 
- **Personalizzazione ripetibile**: è possibile usare modelli con stile dichiarativo per abilitare la personalizzazione ripetibile e rapida delle distribuzioni.
- **Interfacce di gestione** - è possibile gestire le risorse usando una delle seguenti interfacce:
	- API basata su REST
	- PowerShell
	- .NET SDK
	- Node.JS SDK
	- SDK per Java
	- Interfaccia della riga di comando di Azure
	- Portale di anteprima
	- Linguaggio del modello ARM

## Risorse di rete 
Ora è possibile gestire le risorse di rete in modo indipendente, anziché tutte insieme mediante un'unica risorsa di calcolo (una macchina virtuale). Ciò garantisce una maggiore flessibilità durante la creazione di un'infrastruttura complessa e su larga scala in un gruppo di risorse.

Di seguito è riportata una visualizzazione concettuale di una distribuzione di esempio che interessa un'applicazione multilivello. È possibile gestire in modo indipendente ciascuna risorsa visualizzata, ad esempio schede di rete, indirizzi IP pubblici e VM.

![Modello di risorsa di rete](./media/resource-groups-networking/Figure2.png)

Ogni risorsa prevede un set comune di proprietà e il proprio set di proprietà. Ecco le proprietà comuni:

|Proprietà|Descrizione|Valori di esempio|
|---|---|---|
|**nome**|Nome univoco per le risorse. Ogni tipo di risorsa dispone delle proprie restrizioni di denominazione.|PIP01, VM01, NIC01|
|**Località**|Area di Azure in cui sarà creata la VM.|westus, eastus|
|**id**|Identificazione univoca basata su URI|/subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP|

È possibile controllare le proprietà delle singole risorse nelle sezioni seguenti.

[AZURE.INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## Interfacce di gestione
È possibile gestire le risorse di rete Azure tramite interfacce diverse. Questo documento è dedicato a tali interfacce: API REST e modelli.

### API REST 
Come accennato in precedenza, le risorse di rete possono essere gestite tramite un'ampia gamma di interfacce, tra cui API REST,.NET SDK, Node.JS SDK, Java SDK, PowerShell, l'interfaccia della riga di comando, il portale di Azure e i modelli.

L'API REST è conforme alla specifica del protocollo HTTP 1.1. La struttura generale degli URI dell'API viene presentata di seguito:

	https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

E i parametri tra parentesi graffe rappresentano gli elementi seguenti:

- **subscription-id**: ID sottoscrizione di Azure.
- **resource-provider-namespace**: spazio dei nomi per il provider usato. Il valore per il provider di risorse di rete è *Microsoft.Network*.
- **region-name**: nome dell'area di Azure

Quando si effettuano chiamate all'API REST sono supportati i seguenti metodi HTTP:

- **PUT**: usato per creare una risorsa di un determinato tipo, modificare una proprietà della risorsa o modificare un'associazione tra le risorse. 
- **GET**: usato per recuperare le informazioni per una risorsa con provisioning.
- **DELETE**: usato per eliminare una risorsa esistente.

Richiesta e risposta sono conformi a un formato di payload JSON. Per altre informazioni, vedere [API di gestione delle risorse di Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### Linguaggio del modello ARM
Oltre a gestire le risorse in modo imperativo (tramite API o SDK), è anche possibile usare uno stile di programmazione dichiarativo per compilare e gestire le risorse di rete usando il linguaggio del modello ARM.

Di seguito viene fornita una rappresentazione di un modello:

	{
	  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	  "contentVersion": "<version-number-of-template>",
	  "parameters": { <parameter-definitions-of-template> },
	  "variables": { <variable-definitions-of-template> },
	  "resources": [ { <definition-of-resource-to-deploy> } ],
	  "outputs": { <output-of-template> }    
	}

Il modello è principalmente una descrizione JSON delle risorse e dei valori dell'istanza inseriti tramite i parametri. L'esempio seguente può essere usato per creare una rete virtuale con due subnet.

	{
	    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
	    "contentVersion": "1.0.0.0",
	    "parameters" : {
	      "location": {
	        "type": "String",
	        "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
	        "metadata" : {
	          "Description" : "Deployment location"
	        }
	      },
	      "virtualNetworkName":{
	        "type" : "string",
	        "defaultValue":"myVNET",
	        "metadata" : {
	          "Description" : "VNET name"
	        }
	      },
	      "addressPrefix":{
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/16",
	        "metadata" : {
	          "Description" : "Address prefix"
	        }
	
	      },
	      "subnet1Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-1",
	        "metadata" : {
	          "Description" : "Subnet 1 Name"
	        }
	      },
	      "subnet2Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-2",
	        "metadata" : {
	          "Description" : "Subnet 2 name"
	        }
	      },
	      "subnet1Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/24",
	        "metadata" : {
	          "Description" : "Subnet 1 Prefix"
	        }
	      },
	      "subnet2Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.1.0/24",
	        "metadata" : {
	          "Description" : "Subnet 2 Prefix"
	        }
	      }
	    },
	    "resources": [
	    {
	      "apiVersion": "2015-05-01-preview",
	      "type": "Microsoft.Network/virtualNetworks",
	      "name": "[parameters('virtualNetworkName')]",
	      "location": "[parameters('location')]",
	      "properties": {
	        "addressSpace": {
	          "addressPrefixes": [
	            "[parameters('addressPrefix')]"
	          ]
	        },
	        "subnets": [
	          {
	            "name": "[parameters('subnet1Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet1Prefix')]"
	            }
	          },
	          {
	            "name": "[parameters('subnet2Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet2Prefix')]"
	            }
	          }
	        ]
	      }
	    }
	    ]
	}

Quando si usa un modello è possibile specificare manualmente i valori dei parametri oppure usare un file dei parametri. L'esempio seguente mostra un possibile set di valori dei parametri da usare con il modello precedente:

	{
	  "location": {
	      "value": "East US"
	  },
	  "virtualNetworkName": {
	      "value": "VNET1"
	  },
	  "subnet1Name": {
	      "value": "Subnet1"
	  },
	  "subnet2Name": {
	      "value": "Subnet2"
	  },
	  "addressPrefix": {
	      "value": "192.168.0.0/16"
	  },
	  "subnet1Prefix": {
	      "value": "192.168.1.0/24"
	  },
	  "subnet2Prefix": {
	      "value": "192.168.2.0/24"
	  }
	}


I principali vantaggi associati all'uso dei modelli sono:

- È possibile compilare un'infrastruttura complessa in un gruppo di risorse con uno stile dichiarativo. L'orchestrazione per la creazione delle risorse, inclusa la gestione delle dipendenze, viene gestita da ARM. 
- È possibile creare l'infrastruttura in modo ripetibile in diverse aree e all'interno di una sola area modificando semplicemente i parametri. 
- Lo stile dichiarativo consente tempi più brevi per la compilazione dei modelli e la distribuzione dell'infrastruttura. 

Per i modelli di esempio, vedere i [modelli della guida introduttiva di Azure](https://github.com/Azure/azure-quickstart-templates).

Per altre informazioni sul linguaggio del modello ARM, vedere [Linguaggio del modello di Gestione risorse di Azure](../resource-group-authoring-templates.md).

Il modello di esempio precedente usa la rete virtuale e le risorse della subnet. È possibile usare altre risorse di rete, come indicato di seguito:

### Uso di un modello

È possibile distribuire servizi ad Azure da un modello tramite PowerShell, l'interfaccia della riga di comando di Azure oppure tramite clic per la distribuzione da GitHub. Per distribuire servizi da un modello in GitHub, eseguire i passaggi seguenti:

1. Aprire il file template3 da GitHub. Come esempio, aprire [Rete virtuale con due subnet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Fare clic su **Distribuisci in Azure**, quindi accedere al portale di Azure con le proprie credenziali.
3. Verificare il modello e quindi fare clic su **Salva**.
4. Fare clic su **Modifica parametri** e selezionare una località, ad esempio *West US*, per la rete virtuale e le subnet.
5. Se necessario, modificare i parametri **ADDRESSPREFIX** e **SUBNETPREFIX** e quindi fare clic su **OK**.
6. Fare clic su **Seleziona un gruppo di risorse** e quindi fare clic sul gruppo di risorse a cui si desidera aggiungere la rete virtuale e le subnet. In alternativa, è possibile creare un nuovo gruppo di risorse facendo clic su **Crea nuovo**.
3. Fare clic su **Create**. Si noti il riquadro che visualizza la **distribuzione del modello di provisioning**. Dopo aver eseguito la distribuzione, viene visualizzata una schermata simile alla seguente.

![Distribuzione del modello di esempio](./media/resource-groups-networking/Figure6.png)


## Passaggi successivi

[Linguaggio del modello di Gestione risorse di Azure](../resource-group-authoring-templates.md)

[Rete di Azure: modelli di uso comune](https://github.com/Azure/azure-quickstart-templates)

[Provider di risorse di calcolo](../virtual-machines/virtual-machines-azurerm-versus-azuresm.md)

[Panoramica di Gestione risorse di Microsoft Azure](../resource-group-overview.md)

<!---HONumber=AcomDC_0323_2016-->