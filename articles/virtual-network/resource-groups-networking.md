<properties 
   pageTitle="Panoramica di Provider di risorse di rete | Microsoft Azure"
   description="Introduzione al nuovo Provider di risorse di rete in Gestione risorse di Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2015"
   ms.author="telmos" />

# Provider di risorse di rete
Un fattore essenziale per il successo di un'azienda è la possibilità di creare e gestire applicazioni presenti in rete su larga scala in modo agile, flessibile, sicuro e ripetibile. Gestione risorse di Azure (ARM) consente di creare tali applicazioni sotto forma di un'unica raccolta di risorse nei gruppi di risorse. Queste risorse vengono gestite tramite diversi provider di risorse in ARM.

Gestione risorse di Azure (ARM) consente di creare tali applicazioni e la raccolta associata di risorse di rete sotto forma di un'unica raccolta di risorse in un gruppo di risorse. L'applicazione e le risorse di rete vengono eseguite come una singola unità in un gruppo di risorse ARM.

È possibile gestire le risorse di rete usando una delle seguenti interfacce di gestione:

- API basata su REST
- PowerShell
- .NET SDK
- Node.JS SDK
- SDK per Java
- Interfaccia della riga di comando di Azure
- Portale di Azure
- Linguaggio del modello ARM

L'introduzione dei provider di risorse di rete offre i vantaggi seguenti:

- **Metadati**: le informazioni alle risorse possono essere aggiunte usando i tag. Questi tag possono essere usati per tenere traccia dell'utilizzo delle risorse tra le sottoscrizioni e i gruppi di risorse.
- **Maggior controllo della rete**: le risorse di rete sono a regime di controllo libero ed è possibile controllarle in modo più granulare. Questo consente una maggiore flessibilità nella gestione delle risorse di rete.
- **Configurazione più veloce**: grazie al regime di controllo libero, è possibile creare e orchestrare in parallelo le risorse di rete. Il tempo di configurazione viene ridotto drasticamente.
- **Controllo degli accessi in base al ruolo**: questa funzionalità fornisce ruoli predefiniti con uno specifico ambito di sicurezza, oltre a consentire la creazione di ruoli personalizzati per una gestione sicura. 
- **Gestione e distribuzione più semplice**: la distribuzione e la gestione delle applicazioni è più semplice perché è possibile creare un intero stack di applicazioni sotto forma di un'unica raccolta di risorse in un gruppo di risorse. Inoltre, la distribuzione risulta più rapida perché è possibile eseguirla semplicemente fornendo un payload JSON del modello.
- **Personalizzazione rapida**: è possibile usare modelli con stile dichiarativo per abilitare la personalizzazione ripetibile e rapida delle distribuzioni. 
- **Personalizzazione ripetibile**: è possibile usare modelli con stile dichiarativo per abilitare la personalizzazione ripetibile e rapida delle distribuzioni.

## Risorse di rete 
Ora è possibile gestire le risorse di rete in modo indipendente, anziché tutte insieme mediante un'unica risorsa di calcolo (una macchina virtuale). Ciò garantisce una maggiore flessibilità durante la creazione di un'infrastruttura complessa e su larga scala in un gruppo di risorse.
 
Il diagramma seguente illustra una panoramica generale del modello di risorse di rete e delle relative associazioni. Le risorse di livello superiore sono visualizzate con un contorno blu. Oltre alle risorse di livello superiore, è possibile visualizzare le risorse figlio (con il contorno grigio). È possibile gestire singolarmente ogni risorsa.

![Modello di risorsa di rete](./media/resource-groups-networking/Figure1.png)

Di seguito è riportata una visualizzazione concettuale di una distribuzione di esempio che interessa un'applicazione multilivello. Tutte le risorse di rete sono visualizzate con un contorno blu.

![Modello di risorsa di rete](./media/resource-groups-networking/Figure2.png)

## API REST 
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

## Linguaggio del modello ARM
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

## NIC
La scheda di interfaccia di rete, o NIC, rappresenta un'interfaccia di rete che è possibile associare a una macchina virtuale (VM). Una macchina virtuale può avere una o più schede di interfaccia di rete.

![Scheda di interfaccia di rete in una macchina virtuale singola](./media/resource-groups-networking/Figure3.png)

Le proprietà principali di una risorsa NIC includono:

- Impostazioni IP
- Nome DNS interno
- Server DNS

Una scheda di interfaccia di rete può essere associata anche alle seguenti risorse di rete:

- Gruppo di sicurezza di rete 
- Bilanciamento del carico

## Rete virtuale e subnet
Le reti virtuali e le subnet consentono di definire un limite di sicurezza per i carichi di lavoro in esecuzione in Azure. Una rete virtuale è caratterizzata da uno spazio di indirizzi, noto anche come blocco CIDR.

Una subnet è una risorsa figlio di una rete virtuale e consente di definire i segmenti degli spazi di indirizzi all'interno di un blocco CIDR, usando i prefissi degli indirizzi IP. Le macchine virtuali che eseguono diversi carichi di lavoro operano in sostanza in un limite della subnet.

![Scheda di interfaccia di rete in una macchina virtuale singola](./media/resource-groups-networking/Figure4.png)

Le proprietà principali di una risorsa della rete virtuale includono:

- Spazio di indirizzi IP (blocco CIDR) 
- Nome della rete virtuale
- subnet
- Server DNS

Una rete virtuale può essere associata anche alle risorse di rete seguenti:

- Gateway VPN

Le proprietà principali di una subnet includono:

- Prefisso dell'indirizzo IP
- Nome della subnet

Una subnet può essere associata anche alle risorse di rete seguenti:

- NSG

## Bilanciamento del carico
Un bilanciamento del carico viene usato per ridimensionare le applicazioni. Gli scenari di distribuzione più comuni riguardano applicazioni in esecuzione in più istanze della macchina virtuale. Le istanze della macchina virtuale sono gestite dal bilanciamento del carico che consente di distribuire il traffico di rete alle varie istanze.

![Scheda di interfaccia di rete in una macchina virtuale singola](./media/resource-groups-networking/Figure5.png)

I servizi di bilanciamento del carico contengono le risorse figlio seguenti:

- **Configurazione IP front-end**: un bilanciamento del carico può includere uno o più indirizzi IP front-end, anche noti come IP virtuali (VIP). Questi indirizzi IP vengono usati come ingresso per il traffico. 
- **Pool di indirizzi back-end**: indirizzi IP associati alle schede di interfaccia di rete della macchina virtuale a cui viene distribuito il carico.
- **Regole di bilanciamento del carico**: una proprietà della regola esegue il mapping di una specifica combinazione di IP e porte front-end a un set di combinazioni di indirizzi IP e porte back-end. Con una singola definizione di una risorsa di bilanciamento del carico è possibile definire più regole di bilanciamento carico, ciascuna delle quali riflette una combinazione di IP e porte front-end e di IP e porte back-end associata alle macchine virtuali. 
- **Probe**: le probe consentono di tenere traccia dell'integrità delle istanze della macchina virtuale. Se la probe di integrità non riesce, l'istanza della macchina virtuale viene esclusa automaticamente dalla rotazione.
- **Regole NAT in ingresso**: regole NAT che definiscono il traffico in ingresso che attraversa l'IP front-end e viene distribuito all'IP back-end.

## Gateway applicazione

Il servizio Gateway applicazione fornisce una soluzione di bilanciamento del carico HTTP gestita da Azure basata sul bilanciamento del carico di livello 7. Il bilanciamento del carico applicazioni consente di usare le regole di routing per il traffico di rete basato su HTTP.

I gateway applicazione contengono le risorse figlio seguenti:

- **Pool di server back-end** - L’elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici. 
- **Impostazioni del pool di server back-end** - Ogni pool ha impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
- **Porta front-end** - Questa porta è la porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
- **Listener:** - il listener ha una porta front-end, un protocollo (Http o Https, con applicazione della distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL). 
- **Regola** - La regola associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico. È attualmente supportata solo la regola di base. La regola di base è una distribuzione del carico di tipo round robin.


## IP pubblico
Una risorsa di indirizzo IP pubblico fornisce un indirizzo IP pubblico riservato o dinamico. L'indirizzo IP pubblico può essere assegnato a un bilanciamento del carico, a NAT o associato a un indirizzo IP privato in una scheda di interfaccia di rete di una macchina virtuale.

Le proprietà principali di una risorsa IP pubblica includono:

- **Metodo di allocazione IP**: riservato o dinamico. 

## Gruppo di sicurezza di rete
Una risorsa del gruppo di sicurezza di rete consente di creare un limite di sicurezza per i carichi di lavoro, implementando le regole di accesso consentito e negato. Queste regole possono essere applicate a livello della scheda di interfaccia di rete (livello dell'istanza della macchina virtuale) oppure a livello della subnet (gruppo di macchine virtuali).

Le proprietà principali di una risorsa del gruppo di sicurezza di rete includono:

- **Regola di sicurezza**: in un gruppo di sicurezza di rete possono essere definite più regole di sicurezza. Ogni regola può consentire o negare diversi tipi di traffico.

## Regola di sicurezza
Una regola di sicurezza è una risorsa figlio di un gruppo di sicurezza di rete.

Le proprietà principali di una regola di sicurezza includono:

- **Protocollo**: protocollo di rete a cui si applica questa regola.
- **Intervallo di porte di origine**: porta di origine o intervallo di porte compreso tra 0 e 65535. È possibile usare un carattere jolly per includere tutte le porte. 
- **Intervallo di porte di destinazione**: porta di destinazione o intervallo di porte compreso tra 0 e 65535. È possibile usare un carattere jolly per includere tutte le porte.
- **Prefisso dell'indirizzo di origine**: intervallo di indirizzi IP di origine. 
- **Prefisso dell'indirizzo di destinazione**: intervallo di indirizzi IP di destinazione.
- **Accesso**: opzioni *Consenti* o *Nega* per il traffico.
- **Priorità**: valore compreso tra 100 e 4096. Il numero di priorità deve essere univoco per ogni regola nella raccolta delle regole di sicurezza. Più basso è il numero di priorità, maggiore sarà la priorità della regola.
- **Direzione**: specifica se la direzione della regola applicata al traffico è *In ingresso* o *In uscita*. 

## Gateway VPN 
Una risorsa del gateway VPN consente di creare una connessione sicura tra i data center locali e Azure. Una risorsa del gateway VPN può essere configurata in tre modi diversi:
 
- **Da punto a sito**: per accedere in modo sicuro alle risorse di Azure ospitate in una rete virtuale usando un client VPN da qualsiasi computer. 
- **Connessione multisito**: per connettersi in modo sicuro dai data center locali alle risorse in esecuzione in una rete virtuale. 
- **Connessione tra reti virtuali**: per stabilire una connessione sicura tra le reti virtuali di Azure nella stessa area o in aree diverse per compilare i carichi di lavoro con ridondanza geografica.

Le proprietà principali di un gateway VPN includono:
 
- **Tipo di gateway**: gateway indirizzato in modo dinamico o statico. 
- **Prefisso del pool di indirizzi del client VPN**: indirizzi IP da assegnare ai client che si connettono in una configurazione da punto a sito.



## Profilo di Gestione traffico
Gestione traffico e la relativa risorsa endpoint figlio consentono la distribuzione del traffico agli endpoint all'interno e all'esterno di Azure. Questa distribuzione del traffico è regolata dai criteri. Gestione traffico consente anche il monitoraggio dell'integrità dell'endpoint e la corretta deviazione del traffico in base all'integrità di un endpoint.

Le proprietà principali di un profilo di Gestione traffico includono:

- **Metodo di routing del traffico**: i valori possibili sono *Prestazioni*, *Valore ponderato* e *Priorità*.
- **Configurazione DNS**: FQDN per il profilo.
- **Protocollo**: protocollo di monitoraggio. I valori possibili sono *HTTP* e *HTTPS*.
- **Porta**: porta di monitoraggio. 
- **Percorso**: percorso di monitoraggio.
- **Endpoint**: contenitore per le risorse endpoint.

## Endpoint 
Un endpoint è una risorsa figlio di un profilo di Gestione traffico. Rappresenta un endpoint di servizio o Web al quale viene distribuito il traffico in base ai criteri configurati nella risorsa del profilo di Gestione traffico.

Le proprietà principali di un endpoint includono:
 
- **Tipo**: tipo di endpoint. I valori possibili sono *Endpoint di Azure*, *Endpoint esterno* ed *Endpoint annidato*. 
- **ID risorsa di destinazione**: indirizzo IP pubblico di un endpoint di servizio o Web. Può trattarsi di un endpoint di Azure o esterno.
- **Peso**: peso dell'endpoint usato nella gestione del traffico. 
- **Priorità**: priorità dell'endpoint, usata per definire un'azione di failover. 

## DNS di Azure

DNS di Azure è un servizio di hosting per i domini DNS, che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure.

Le proprietà principali di DNS di Azure includono:

- ** Zone DNS**: informazioni sulla zona del dominio per ospitare i record DNS di un particolare dominio.
- **Set di record DNS**: insieme di record di un tipo specifico. I tipi supportati sono A, AAAA, CNAME, MX, NS, SOA, SRV e TXT.


## Uso di un modello

È possibile distribuire servizi ad Azure da un modello tramite PowerShell, l'interfaccia della riga di comando di Azure oppure tramite clic per la distribuzione da GitHub. Per distribuire servizi da un modello in GitHub, eseguire i passaggi seguenti:

1. Aprire il file template3 da GitHub. Come esempio, aprire [Rete virtuale con due subnet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Fare clic su **Distribuisci in Azure**, quindi accedere al portale di Azure con le proprie credenziali.
3. Verificare il modello e quindi fare clic su **Salva**.
4. Fare clic su **Modifica parametri** e selezionare una località, ad esempio *West US*, per la rete virtuale e le subnet.
5. Se necessario, modificare i parametri **ADDRESSPREFIX** e **SUBNETPREFIX** e quindi fare clic su **OK**.
6. Fare clic su **Seleziona un gruppo di risorse** e quindi fare clic sul gruppo di risorse a cui si desidera aggiungere la rete virtuale e le subnet. In alternativa, è possibile creare un nuovo gruppo di risorse facendo clic su **Crea nuovo**.
3. Fare clic su **Create**. Si noti il riquadro che visualizza la **distribuzione del modello di provisioning**. Dopo aver eseguito la distribuzione, viene visualizzata una schermata simile alla seguente.

![Distribuzione del modello di esempio](./media/resource-groups-networking/Figure6.png)


## Vedere anche

[Riferimento all'API per le funzionalità di rete di Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Riferimento ad Azure PowerShell per le funzionalità di rete](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Linguaggio del modello di Gestione risorse di Azure](../resource-group-authoring-templates.md)

[Rete di Azure: modelli di uso comune](https://github.com/Azure/azure-quickstart-templates)

[Provider di risorse di calcolo](../virtual-machines-azurerm-versus-azuresm)

[Panoramica di Gestione risorse di Azure](../resource-group-overview)

[Controllo degli accessi in base al ruolo in Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Uso dei tag in Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[Distribuzioni modello](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=Sept15_HO3-->