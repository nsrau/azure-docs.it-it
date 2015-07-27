<properties
	pageTitle="Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure | Microsoft Azure"
	description="Informazioni sull'uso dell'interfaccia della riga di comando per Mac, Linux e Windows per gestire le risorse di Azure mediante la modalità arm dell'interfaccia della riga di comando di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="danlep"/>

# Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure

Questo argomento illustra come usare l'interfaccia della riga di comando di Azure in modalità **arm** per creare, gestire ed eliminare servizi nella riga di comando di computer Mac, Linux e Windows. È possibile eseguire le stesse attività usando le diverse librerie di Azure SDK con PowerShell e tramite il portale di Azure.

Gestione risorse di Azure consente di creare un gruppo di risorse, ovvero macchine virtuali, siti Web, database e così via, come una singola unità distribuibile. È quindi possibile distribuire, aggiornare o eliminare tutte le risorse per l'applicazione mediante un'unica operazione coordinata. È possibile descrivere le risorse del gruppo in un modello JSON per la distribuzione e quindi usare tale modello per ambienti diversi, ad esempio di testing, gestione temporanea e produzione.

## Approccio imperativo e dichiarativo

Come avviene con la [modalità di gestione servizi (**asm**)](../virtual-machines-command-line-tools.md), la modalità **arm** dell'interfaccia della riga di comando di Azure offre comandi che consentono di creare risorse in modo imperativo nella riga di comando. Ad esempio, se si digita `azure group create <groupname> <location>`, si chiede ad Azure di creare un gruppo di risorse, mentre con `azure group deployment create <resourcegroup> <deploymentname>` si indica ad Azure di creare una distribuzione di un numero qualsiasi di elementi e di inserirli in un gruppo. Poiché per ogni tipo di risorsa sono previsti comandi imperativi, è possibile concatenarli per creare distribuzioni piuttosto complesse.

L'uso di _modelli_ che descrivono un gruppo di risorse costituisce tuttavia un approccio dichiarativo molto più potente che consente di automatizzare distribuzioni complesse indipendentemente dal numero di risorse e quasi per qualunque scopo. Quando si usano modelli, l'unico comando imperativo è per distribuire uno di essi. Per informazioni generali su modelli, risorse e gruppi di risorse, vedere l'articolo relativo alla [panoramica dei gruppi di risorse di Azure](resource-groups-overview).

> [AZURE.NOTE]Oltre alle opzioni specifiche dei comandi riportate di seguito e nella riga di comando, vi sono tre opzioni che possono essere usate per visualizzare output dettagliato come opzioni di richiesta e codici di stato. Il parametro -v fornisce output dettagliato, mentre il parametro -vv fornisce un output con un dettaglio ancor maggiore. L'opzione --json restituirà come output il risultato in formato json non elaborato ed è molto utile per gli scenari di script.
>
> L'uso con l'opzione --json è molto comune e costituisce un aspetto importante per il recupero e la comprensione dei risultati delle operazioni dell'interfaccia della riga di comando di Azure che restituiscono informazioni sulle risorse, lo stato e i log e per l'applicazione di modelli. È possibile installare gli strumenti parser JSON come **jq** o **jsawk** oppure usare la libreria del linguaggio preferito.

##Requisiti per l'uso

I requisiti di impostazione per l'uso della modalità **arm** con l'interfaccia della riga di comando di Azure sono i seguenti:

- Un account Azure ([per ottenere una versione di valutazione gratuita, fare clic qui](http://azure.microsoft.com/pricing/free-trial/))
- [Installazione dell'interfaccia della riga di comando di Azure](../xplat-cli-install.md)
- [Configurazione dell'interfaccia della riga di comando di Azure](../xplat-cli-connect.md) per l'uso di un'identità di Azure Active Directory o di un'entità servizio

Dopo aver ottenuto un account e aver installato l'interfaccia della riga di comando di Azure, è necessario eseguire queste operazioni:

- Passare alla modalità **arm** digitando `azure config mode arm`.
- Accedere all'account Azure digitando `azure login` e usando la propria identità aziendale o dell'istituto di istruzione in risposta ai messaggi di richiesta.

A questo punto digitare `azure` per visualizzare un elenco dei comandi di primo livello descritti nelle sezioni seguenti.

## azure account - Gestione delle informazioni relative all'account e le impostazioni di pubblicazione
Le informazioni relative alla sottoscrizione di Azure vengono usate dallo strumento per connettersi all'account dell'utente. Tali informazioni possono essere ottenute dal portale di Azure in un file di impostazioni di pubblicazione come illustrato di seguito. È possibile importare il file di impostazioni di pubblicazione come impostazione di configurazione locale persistente che lo strumento userà per le operazioni successive. Sarà necessario importare le impostazioni di pubblicazione una sola volta.

**Elencare le sottoscrizioni importate**

	account list [options]

**Visualizzare i dettagli relativi a una sottoscrizione**

	account show [options] [subscriptionNameOrId]

**Impostare la sottoscrizione corrente**

	account set [options] <subscriptionNameOrId>

**Rimuovere una sottoscrizione o un ambiente oppure cancellare tutte le informazioni dell'account o dell'ambiente archiviate**

	account clear [options]

**Comandi per gestire l'ambiente dell'account**

	account env list [options]
	account env show [options] [environment]
	account env add [options] [environment]
	account env set [options] [environment]
	account env delete [options] [environment]

## azure ad - Comandi per visualizzare gli oggetti Active Directory

**Comandi per visualizzare le applicazioni di Active Directory**

	ad app create [options]
	ad app delete [options] <object-id>

**Comandi per visualizzare i gruppi di Active Directory**

	ad group list [options]
	ad group show [options]

**Comandi per fornire le informazioni relative a un membro o a un sottogruppo di Active Directory**

	ad group member list [options] [objectId]

**Comandi per visualizzare le entità servizio di Active Directory**

	ad sp list [options]
	ad sp show [options]
	ad sp create [options] <application-id>
	ad sp delete [options] <object-id>

**Comandi per visualizzare gli utenti di Active Directory**

	ad user list [options]
	ad user show [options]

## azure availset - Comandi per gestire i set di disponibilità

**Creare un set di disponibilità all'interno di un gruppo di risorse**

	availset create [options] <resource-group> <name> <location> [tags]

**Elencare i set di disponibilità all'interno di un gruppo di risorse**

	availset list [options] <resource-group>

**Ottenere un set di disponibilità all'interno di un gruppo di risorse**

	availset show [options] <resource-group> <name>

**Eliminare un set di disponibilità all'interno di un gruppo di risorse**

	availset delete [options] <resource-group> <name>

## azure config - Comandi per gestire le impostazioni locali

**Elencare le impostazioni di configurazione dell'interfaccia della riga di comando di Azure**

	config list [options]

**Eliminare un'impostazione di configurazione**

	config delete [options] <name>

**Aggiornare un'impostazione di configurazione**

	config set <name> <value>

**Impostare la modalità operativa dell'interfaccia della riga di comando di Azure su `arm` o `asm`**

	config mode [options] <modename>


## azure feature - Comandi per gestire le funzionalità dell'account

**Elencare tutte le funzionalità disponibili per la sottoscrizione**

	feature list [options]

**Visualizzare una funzionalità**

	feature show [options] <providerName> <featureName>

**Registrare una funzionalità in anteprima di un provider di risorse**

	feature register [options] <providerName> <featureName>

## azure group - Comandi per gestire i gruppi di risorse

**Creare un nuovo gruppo di risorse**

	group create [options] <name> <location>

**Impostare i tag per un gruppo di risorse**

	group set [options] <name> <tags>

**Eliminare un gruppo di risorse**

	group delete [options] <name>

**Elencare i gruppi di risorse per la sottoscrizione**

	group list [options]

**Visualizzare un gruppo di risorse per la sottoscrizione**

	group show [options] <name>

**Comandi per gestire i log dei gruppi di risorse**

	group log show [options] [name]

**Comandi per gestire la distribuzione in un gruppo di risorse**

	group deployment create [options] [resource-group] [name]
	group deployment list [options] <resource-group> [state]
	group deployment show [options] <resource-group> [deployment-name]
	group deployment stop [options] <resource-group> [deployment-name]

**Comandi per gestire il modello di gruppo di risorse locale o della raccolta**

	group template list [options]
	group template show [options] <name>
	group template download [options] [name] [file]
	group template validate [options] <resource-group>

## azure insights - Comandi relativi al monitoraggio di Insights (eventi, regole di avviso, impostazioni di scalabilità automatica, metriche)

**Recuperare i log delle operazioni per una sottoscrizione, un ID correlazione, un gruppo di risorse, una risorsa o un provider di risorse**

	insights logs list [options]

## azure location - Comandi per ottenere le posizioni disponibili per tutti i tipi di risorse

**Elencare le posizioni disponibili**

	location list [options]

## azure network - Comandi per gestire le risorse di rete

**Comandi per gestire le reti virtuali**

	network vnet create [options] <resource-group> <name> <location>
Consente di creare una nuova rete virtuale. Nell'esempio seguente viene creata una rete virtuale denominata newvnet per il gruppo di risorse myresourcegroup nell'area westus.


	azure network vnet create myresourcegroup newvnet "west us"
	info:    Executing command network vnet create
	+ Looking up virtual network "newvnet"
	+ Creating virtual network "newvnet"
	 Loading virtual network state
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
	data:    Name:                 newvnet
	data:    Type:                 Microsoft.Network/virtualNetworks
	data:    Location:             westus
	data:    Tags:
	data:    Provisioning state:   Succeeded
	data:    Address prefixes:
	data:     10.0.0.0/8
	data:    DNS servers:
	data:    Subnets:
	data:
	info:    network vnet create command OK


Opzioni dei parametri:

 	-h, --help                                 output usage information
 	-v, --verbose                              use verbose output
	--json                                     use json output
 	-g, --resource-group <resource-group>      the name of the resource group
 	-n, --name <name>                          the name of the virtual network
 	-l, --location <location>                  the location
 	-a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network
      For example -a 10.0.0.0/24,10.0.1.0/24.
      Default value is 10.0.0.0/8

	-d, --dns-servers <dns-servers>            the comma separated list of DNS servers IP addresses
 	-t, --tags <tags>                          the tags set on this virtual network.
      Can be multiple. In the format of "name=value".
      Name is required and value is optional.
      For example, -t tag1=value1;tag2
	 -s, --subscription <subscription>          the subscription identifier
<BR>

	network vnet set [options] <resource-group> <name>

Aggiorna la configurazione di una rete virtuale all'interno di un gruppo di risorse.

	azure network vnet set myresourcegroup newvnet

	info:    Executing command network vnet set
	+ Looking up virtual network "newvnet"
	+ Updating virtual network "newvnet"
	+ Loading virtual network state
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
	data:    Name:                 newvnet
	data:    Type:                 Microsoft.Network/virtualNetworks
	data:    Location:             westus
	data:    Tags:
	data:    Provisioning state:   Succeeded
	data:    Address prefixes:
	data:     10.0.0.0/8
	data:    DNS servers:
	data:    Subnets:
	data:
	info:    network vnet set command OK

Opzioni dei parametri:

	   -h, --help                                 output usage information
	   -v, --verbose                              use verbose output
	   --json                                     use json output
	   -g, --resource-group <resource-group>      the name of the resource group
	   -n, --name <name>                          the name of the virtual network
	   -a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network.
        For example -a 10.0.0.0/24,10.0.1.0/24.
        This list will be appended to the current list of address prefixes.
        The address prefixes in this list should not overlap between them.
        The address prefixes in this list should not overlap with existing address prefixes in the vnet.

	   -d, --dns-servers [dns-servers]            the comma separated list of DNS servers IP addresses.
        This list will be appended to the current list of DNS server IP addresses.

	   -t, --tags <tags>                          the tags set on this virtual network.
        Can be multiple. In the format of "name=value".
        Name is required and value is optional. For example, -t tag1=value1;tag2.
        This list will be appended to the current list of tags

	   --no-tags                                  remove all existing tags
	   -s, --subscription <subscription>          the subscription identifier
<BR>

	network vnet list [options] <resource-group>

Il comando consente di elencare tutte le reti virtuali in un gruppo di risorse.


	C:>azure network vnet list myresourcegroup

	info:    Executing command network vnet list
	+ Listing virtual networks
		data:    ID
       Name      Location  Address prefixes  DNS servers
	data:    -------------------------------------------------------------------
	------  --------  --------  ----------------  -----------
	data:    /subscriptions/###############################/resourceGroups/
	wvnet   newvnet   westus    10.0.0.0/8
	info:    network vnet list command OK

Opzioni dei parametri:


      -h, --help                             output usage information
      -v, --verbose                          use verbose output
      --json                                 use json output
      -g, --resource-group <resource-group>  the name of the resource group
      -s, --subscription <subscription>      the subscription identifier

<BR>

	network vnet show [options] <resource-group> <name>
Il comando visualizza le proprietà della rete virtuale in un gruppo di risorse.

	azure network vnet show -g myresourcegroup -n newvnet

	info:    Executing command network vnet show
	+ Looking up virtual network "newvnet"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
	data:    Name:                 newvnet
	data:    Type:                 Microsoft.Network/virtualNetworks
	data:    Location:             westus
	data:    Tags:
	data:    Provisioning state:   Succeeded
	data:    Address prefixes:
	data:     10.0.0.0/8
	data:    DNS servers:
	data:    Subnets:
	data:
	info:    network vnet show command OK
<BR>

	network vnet delete [options] <resource-group> <name>
Il comando rimuove una rete virtuale.

	azure network vnet delete myresourcegroup newvnetX

	info:    Executing command network vnet delete
	+ Looking up virtual network "newvnetX"
	Delete virtual network newvnetX? [y/n] y
	+ Deleting virtual network "newvnetX"
	info:    network vnet delete command OK

Opzioni dei parametri:

     -h, --help                             output usage information
     -v, --verbose                          use verbose output
     --json                                 use json output
     -g, --resource-group <resource-group>  the name of the resource group
     -n, --name <name>                      the name of the virtual network
     -q, --quiet                            quiet mode, do not ask for delete confirmation
     -s, --subscription <subscription>      the subscription identifier


**Comandi per gestire le subnet delle reti virtuali**

	network vnet subnet create [options] <resource-group> <vnet-name> <name>
Il comando consente di aggiungere un'altra subnet a una rete virtuale esistente.

	azure network vnet subnet create -g myresourcegroup --vnet-name newvnet -n subnet --address-prefix 10.0.1.0/24

	info:    Executing command network vnet subnet create
	+ Looking up the subnet "subnet"
	+ Creating subnet "subnet"
	+ Looking up the subnet "subnet"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
	data:    Name:                      subnet
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet create command OK

Opzioni dei parametri:

     -h, --help                                                       output usage information
     -v, --verbose                                                    use verbose output
		 --json                                                           use json output
	 -g, --resource-group <resource-group>                            the name of the resource group
	 -e, --vnet-name <vnet-name>                                      the name of the virtual network
     -n, --name <name>                                                the name of the subnet
     -a, --address-prefix <address-prefix>                            the address prefix
     -w, --network-security-group-id <network-security-group-id>      the network security group identifier.
           e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
     -o, --network-security-group-name <network-security-group-name>  the network security group name
     -s, --subscription <subscription>                                the subscription identifier

<BR>

	network vnet subnet set [options] <resource-group> <vnet-name> <name>

Imposta una subnet specifica della rete virtuale all'interno di un gruppo di risorse.


	C:>azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet set
	+ Looking up the subnet "subnet1"
	+ Setting subnet "subnet1"
	+ Looking up the subnet "subnet1"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
	data:    Name:                      subnet1
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet set command OK
<BR>

	network vnet subnet list [options] <resource-group> <vnet-name>

Elenca tutte le subnet per una rete virtuale specifica all'interno di un gruppo di risorse.

	azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet set
	+ Looking up the subnet "subnet1"
	+ Setting subnet "subnet1"
	+ Looking up the subnet "subnet1"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
	data:    Name:                      subnet1
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet set command OK
<BR>

	network vnet subnet show [options] <resource-group> <vnet-name> <name>
Visualizza le proprietà della subnet della rete virtuale

	azure network vnet subnet show -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet show
	+ Looking up the subnet "subnet1"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft
	.Network/virtualNetworks/newvnet/subnets/subnet1
	data:    Name:                      subnet1
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet show command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-e, --vnet-name <vnet-name>            the name of the virtual network
	-n, --name <name>                      the name of the subnet
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network vnet subnet delete [options] <resource-group> <vnet-name> <subnet-name>
Rimuove una subnet da una rete virtuale esistente.

	azure network vnet subnet delete -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet delete
	+ Looking up the subnet "subnet1"
	Delete subnet "subnet1"? [y/n] y
	+ Deleting subnet "subnet1"
	info:    network vnet subnet delete command OK

Opzioni dei parametri:

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
 	-g, --resource-group <resource-group>  the name of the resource group
 	-e, --vnet-name <vnet-name>            the name of the virtual network
 	-n, --name <name>                      the subnet name
 	-s, --subscription <subscription>      the subscription identifier
 	-q, --quiet                            quiet mode, do not ask for delete confirmation

**Comandi per gestire il bilanciamento del carico**

	network lb create [options] <resource-group> <name> <location>
Crea un set di bilanciamento del carico.

	azure network lb create -g myresourcegroup -n mylb -l westus

	info:    Executing command network lb create
	+ Looking up the load balancer "mylb"
	+ Creating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
	data:    Name:                         mylb
	data:    Type:                         Microsoft.Network/loadBalancers
	data:    Location:                     westus
	data:    Provisioning state:           Succeeded
	info:    network lb create command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the load balancer
	-l, --location <location>              the location
	-t, --tags <tags>                      the list of tags.
     Can be multiple. In the format of "name=value".
     Name is required and value is optional. For example, -t tag1=value1;tag2
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb list [options] <resource-group>
Elenca le risorse di bilanciamento del carico all'interno di un gruppo di risorse.

	azure network lb list myresourcegroup

	info:    Executing command network lb list
	+ Getting the load balancers
	data:    Name  Location
	data:    ----  --------
	data:    mylb  westus
	info:    network lb list command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb show [options] <resource-group> <name>

Visualizza le informazioni di un servizio/dispositivo di bilanciamento del carico specifico all'interno di un gruppo di risorse.

	azure network lb show myresourcegroup mylb -v

	info:    Executing command network lb show
	verbose: Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
	data:    Name:                         mylb
	data:    Type:                         Microsoft.Network/loadBalancers
	data:    Location:                     westus
	data:    Provisioning state:           Succeeded
	info:    network lb show command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb delete [options] <resource-group> <name>

Elimina le risorse di bilanciamento del carico.

	azure network lb delete  myresourcegroup mylb

	info:    Executing command network lb delete
	+ Looking up the load balancer "mylb"
	Delete load balancer "mylb"? [y/n] y
	+ Deleting load balancer "mylb"
	info:    network lb delete command OK

Opzioni dei parametri:

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
 	-g, --resource-group <resource-group>  the name of the resource group
 	-n, --name <name>                      the name of the load balancer
 	-q, --quiet                            quiet mode, do not ask for delete confirmation
 	-s, --subscription <subscription>      the subscription identifier

**Comandi per gestire i probe di un servizio/dispositivo di bilanciamento del carico**

	network lb probe create [options] <resource-group> <lb-name> <name>

Crea la configurazione di probe per lo stato di integrità nel servizio/dispositivo di bilanciamento del carico. Tenere presente che, per eseguire questo comando, il servizio/dispositivo di bilanciamento del carico richiede una risorsa IP front-end. Vedere il comando "azure network frontend-ip" per assegnare un indirizzo IP per il bilanciamento del carico.

	azure network lb probe create -g myresourcegroup --lb-name mylb -n mylbprobe --protocol tcp --port 80 -i 300

	info:    Executing command network lb probe create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	info:    network lb probe create command OK

Opzioni dei parametri:

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the probe
	-p, --protocol <protocol>              the probe protocol
	-o, --port <port>                      the probe port
	-f, --path <path>                      the probe path
	-i, --interval <interval>              the probe interval in seconds
	-c, --count <count>                    the number of probes
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb probe set [options] <resource-group> <lb-name> <name>

Aggiorna un probe esistente del servizio/dispositivo di bilanciamento del carico con nuovi valori.

	azure network lb probe set -g myresourcegroup -l mylb -n mylbprobe -p mylbprobe1 -p TCP -o 443 -i 300

	info:    Executing command network lb probe set
		+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	info:    network lb probe set command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the probe
	-e, --new-probe-name <new-probe-name>  the new name of the probe
	-p, --protocol <protocol>              the new value for probe protocol
	-o, --port <port>                      the new value for probe port
	-f, --path <path>                      the new value for probe path
	-i, --interval <interval>              the new value for probe interval in seconds
	-c, --count <count>                    the new value for number of probes
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb probe list [options] <resource-group> <lb-name>

Elenca le proprietà di probe per un set di bilanciamento del carico.

	C:>azure network lb probe list -g myresourcegroup -l mylb

	info:    Executing command network lb probe list
	+ Looking up the load balancer "mylb"
	data:    Name       Protocol  Port  Path  Interval  Count
	data:    ---------  --------  ----  ----  --------  -----
	data:    mylbprobe  Tcp       443         300       2
	info:    network lb probe list command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier


	network lb probe delete [options] <resource-group> <lb-name> <name>
Rimuove il probe creato per il servizio/dispositivo di bilanciamento del carico.

	azure network lb probe delete -g myresourcegroup -l mylb -n mylbprobe

	info:    Executing command network lb probe delete
	+ Looking up the load balancer "mylb"
	Delete a probe "mylbprobe?" [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb probe delete command OK

**Comandi per gestire le configurazioni IP front-end di un servizio/dispositivo di bilanciamento del carico**

	network lb frontend-ip create [options] <resource-group> <lb-name> <name>
Crea una configurazione IP front-end per un set di bilanciamento del carico esistente.

	azure network lb frontend-ip create -g myresourcegroup --lb-name mylb -n myfrontendip -o Dynamic -e subnet -m newvnet

	info:    Executing command network lb frontend-ip create
	+ Looking up the load balancer "mylb"
	+ Looking up the subnet "subnet"
	+ Creating frontend IP configuration "myfrontendip"
	+ Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/Myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
	/frontendIPConfigurations/myfrontendip
	data:    Name:                         myfrontendip
	data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
	data:    Provisioning state:           Succeeded
	data:    Private IP allocation method: Dynamic
	data:    Private IP address:           10.0.1.4
	data:    Subnet:                       id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
	data:    Public IP address:
	data:    Inbound NAT rules
	data:    Outbound NAT rules
	data:    Load balancing rules
	data:
	info:    network lb frontend-ip create command OK

<BR>

	network lb frontend-ip set [options] <resource-group> <lb-name> <name>

Consente di aggiornare una configurazione esistente di un elemento IP front-end. Il comando seguente aggiunge un elemento IP pubblico denominato mypubip5 a un elemento IP front-end di bilanciamento del carico esistente denominato myfrontendip.

	azure network lb frontend-ip set -g myresourcegroup --lb-name mylb -n myfrontendip -i mypubip5

	info:    Executing command network lb frontend-ip set
	+ Looking up the load balancer "mylb"
	+ Looking up the public ip "mypubip5"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Name:                         myfrontendip
	data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
	data:    Provisioning state:           Succeeded
	data:    Private IP allocation method: Dynamic
	data:    Private IP address:
	data:    Subnet:
	data:    Public IP address:            id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypubip5
	data:    Inbound NAT rules
	data:    Outbound NAT rules
	data:    Load balancing rules
	data:
	info:    network lb frontend-ip set command OK

Opzioni dei parametri:

	-h, --help                                                         output usage information
	-v, --verbose                                                      use verbose output
	--json                                                             use json output
	-g, --resource-group <resource-group>                              the name of the resource group
	-l, --lb-name <lb-name>                                            the name of the load balancer
	-n, --name <name>                                                  the name of the frontend ip configuration
	-a, --private-ip-address <private-ip-address>                      the private ip address
	-o, --private-ip-allocation-method <private-ip-allocation-method>  the private ip allocation method [Static, Dynamic]
	-u, --public-ip-id <public-ip-id>                                  the public ip identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
	-i, --public-ip-name <public-ip-name>                              the public ip name.
	This public ip must exist in the same resource group as the lb.
	Please use public-ip-id if that is not the case.
	-b, --subnet-id <subnet-id>                                        the subnet id.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/VirtualNetworks/<vnet-name>/subnets/<subnet-name>
	-e, --subnet-name <subnet-name>                                    the subnet name
	-m, --vnet-name <vnet-name>                                        the virtual network name.
	This virtual network must exist in the same resource group as the lb.
	Please use subnet-id if that is not the case.
	-s, --subscription <subscription>                                  the subscription identifier

<BR>

	network lb frontend-ip list [options] <resource-group> <lb-name>

Elenca tutte le risorse IP front-end configurate per il servizio/dispositivo di bilanciamento del carico.

	azure network lb frontend-ip list -g myresourcegroup -l mylb

	info:    Executing command network lb frontend-ip list
	+ Looking up the load balancer "mylb"
	data:    Name         Provisioning state  Private IP allocation method  Subnet
	data:    -----------  ------------------  ----------------------------  ------
	data:    myprivateip  Succeeded           Dynamic
	info:    network lb frontend-ip list command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb frontend-ip delete [options] <resource-group> <lb-name> <name>
Elimina l'oggetto IP front-end associato al servizio/dispositivo di bilanciamento del carico

	network lb frontend-ip delete -g myresourcegroup -l mylb -n myfrontendip
	info:    Executing command network lb frontend-ip delete
	+ Looking up the load balancer "mylb"
	Delete frontend ip configuration "myfrontendip"? [y/n] y
	+ Updating load balancer "mylb"

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the frontend ip configuration
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**Comandi per gestire i pool di indirizzi back-end di un servizio/dispositivo di bilanciamento del carico**

	network lb address-pool create [options] <resource-group> <lb-name> <name>

Creare un pool di indirizzi back-end per un servizio/dispositivo di bilanciamento del carico.

	azure network lb address-pool create -g myresourcegroup --lb-name mylb -n myaddresspool

	info:    Executing command network lb address-pool create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourgroup/providers/Microso.Network/loadBalancers/mylb/backendAddressPools/myaddresspool
	data:    Name:                      myaddresspool
	data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
	data:    Provisioning state:        Succeeded
	data:    Backend IP configurations:
	data:    Load balancing rules:
	data:
	info:    network lb address-pool create command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb address-pool add [options] <resource-group> <lb-name> <name>

Un intervallo pool di indirizzi back-end consente a un servizio/dispositivo di bilanciamento del carico di determinare le risorse a cui instradare il traffico di rete in ingresso dal relativo endpoint mediante Gestione risorse di Azure. Dopo aver creato e denominato l'intervallo pool di indirizzi back-end (vedere il comando "azure network lb address-pool create"), è necessario aggiungere gli endpoint che ora sono definiti da una risorsa denominata "network interfaces" (ovvero interfacce di rete).

Per configurare l'intervallo di indirizzi back-end, sarà necessaria almeno un'interfaccia di rete ("network interface"). Per altre informazioni, vedere la riga di comando "azure network lb nic".

Nell'esempio seguente è stata usata un'interfaccia di rete "nic1" creata in precedenza per creare l'intervallo pool di indirizzi back-end.

	azure network lb address-pool add -g myresourcegroup -l mylb -n mybackendpool -a nic1

	info:    Executing command network lb address-pool add
	+ Looking up the load balancer "mylb"
	+ Getting network interfaces
	+ Updating network interface "nic1"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Name:                      mybackendpool
	data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
	data:    Provisioning state:        Succeeded
	data:    Backend IP configurations:
	data:     id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/networkInterfaces/nic1/ipConfigurations/NIC-config
	data:    Load balancing rules:
	data:
	info:    network lb address-pool add command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-i, --vm-id <vm-id>                    the virtual machine identifier.
	e.g. "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>,/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>"
	-m, --vm-name <vm-name>                the name of the virtual machine
	-d, --nic-id <nic-id>                  the network interface identifier.
	e.g. ""/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkInterfaces/<nic-name>"
	-a, --nic-name <nic-name>              the name of the network interface
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb address-pool remove [options] <resource-group> <lb-name> <name>

Rimuove un'interfaccia di rete dall'intervallo pool di indirizzi IP back-end.

	azure network lb address-pool remove -g myresourcegroup -l mylb -n mybackendpool -a nic1

	info:    Executing command network lb address-pool remove
	+ Looking up the load balancer "mylb"
	+ Getting network interfaces
	+ Updating network interface "nic1"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Name:                      mybackendpool
	data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
	data:    Provisioning state:        Succeeded
	data:    Backend IP configurations:
	data:    Load balancing rules:
	data:
	info:    network lb address-pool remove command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-i, --vm-id <vm-id>                    the virtual machine identifier.
	e.g. "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>,/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>"
	-m, --vm-name <vm-name>                the name of the virtual machine
	-d, --nic-id <nic-id>                  the network interface identifier.
	e.g. ""/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkInterfaces/<nic-name>"
	-a, --nic-name <nic-name>              the name of the network interface
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb address-pool list [options] <resource-group> <lb-name>

Elenca l'intervallo pool di indirizzi IP back-end per un gruppo di risorse specifico.

	azure network lb address-pool list -g myresourcegroup -l mylb

	info:    Executing command network lb address-pool list
	+ Looking up the load balancer "mylb"
	data:    Name           Provisioning state
	data:    -------------  ------------------
	data:    mybackendpool  Succeeded
	info:    network lb address-pool list command OK

Opzioni dei parametri:

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
 	-g, --resource-group <resource-group>  the name of the resource group
 	-l, --lb-name <lb-name>                the name of the load balancer
 	-s, --subscription <subscription>      the subscription identifier

<BR> network lb address-pool delete [opzioni] <resource-group> <lb-name> <name>

Rimuove la risorsa dell'intervallo pool di indirizzi IP back-end dal servizio/dispositivo di bilanciamento del carico.

	azure network lb address-pool delete -g myresourcegroup -l mylb -n mybackendpool

	info:    Executing command network lb address-pool delete
	+ Looking up the load balancer "mylb"
	Delete backend address pool "mybackendpool"? [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb address-pool delete command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**Comandi per gestire le regole di bilanciamento del carico**

	network lb rule create [options] <resource-group> <lb-name> <name>
Crea regole di bilanciamento del carico.

È possibile creare una regola di bilanciamento del carico configurando l'endpoint front-end per il servizio/dispositivo di bilanciamento del carico e l'intervallo pool di indirizzi back-end a cui giungerà il traffico di rete in ingresso. Le impostazioni includono anche le porte per l'endpoint IP front-end e le porte per l'intervallo pool di indirizzi back-end.

L'esempio seguente illustra come creare una regola di bilanciamento del carico, con l'endpoint front-end in ascolto sulla porta TCP 80 e l'invio del traffico di rete con bilanciamento del carico alla porta 8080 per l'intervallo pool di indirizzi back-end.

	azure network lb rule create -g myresourcegroup -l mylb -n mylbrule -p tcp -f 80 -b 8080 -i 10


	info:    Executing command network lb rule create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Loading rule state
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mylbrule
	data:    Name:                      mylbrule
	data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Protocol:                  Tcp
	data:    Frontend port:             80
	data:    Backend port:              8080
	data:    Enable floating IP:        false
	data:    Idle timeout in minutes:   10
	data:    Probes
	data:
	info:    network lb rule create command OK

<BR>

	network lb rule set [options] <resource-group> <lb-name> <name>

Aggiorna una regola di sistema di bilanciamento del carico esistente impostata in un gruppo di risorse specifico. Nell'esempio seguente il nome della regola è stato modificato da mylbrule a mynewlbrule.

	azure network lb rule set -g myresourcegroup -l mylb -n mylbrule -r mynewlbrule -p tcp -f 80 -b 8080 -i 10 -t myfrontendip -o mybackendpool

	info:    Executing command network lb rule set
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Loading rule state
	data:    Id:                        /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mynewlbrule
	data:    Name:                      mynewlbrule
	data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Protocol:                  Tcp
	data:    Frontend port:             80
	data:    Backend port:              8080
	data:    Enable floating IP:        false
	data:    Idle timeout in minutes:   10
	data:    Probes
	data:
	info:    network lb rule set command OK

Opzioni dei parametri:

	-h, --help                                         output usage information
	-v, --verbose                                      use verbose output
	--json                                             use json output
	-g, --resource-group <resource-group>              the name of the resource group
	-l, --lb-name <lb-name>                            the name of the load balancer
	-n, --name <name>                                  the name of the rule
	-r, --new-rule-name <new-rule-name>                new rule name
	-p, --protocol <protocol>                          the rule protocol
	-f, --frontend-port <frontend-port>                the frontend port
	-b, --backend-port <backend-port>                  the backend port
	-e, --enable-floating-ip <enable-floating-ip>      enable floating point ip
	-i, --idle-timeout <idle-timeout>                  the idle timeout in minutes
	-a, --probe-name [probe-name]                      the name of the probe defined in the same load balancer
	-t, --frontend-ip-name <frontend-ip-name>          the name of the frontend ip configuration in the same load balancer
	-o, --backend-address-pool <backend-address-pool>  name of the backend address pool defined in the same load balancer
	-s, --subscription <subscription>                  the subscription identifier


	network lb rule list [options] <resource-group> <lb-name>

Elenca tutte le regole di bilanciamento del carico configurate per un servizio/dispositivo di bilanciamento del carico in un gruppo di risorse specifico.

	azure network lb rule list -g myresourcegroup -l mylb

	info:    Executing command network lb rule list
	+ Looking up the load balancer "mylb"
	data:    Name         Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend address pool  Probe data

	data:    mynewlbrule  Succeeded           Tcp       80             8080          false               10                       /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	info:    network lb rule list command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier

	network lb rule delete [options] <resource-group> <lb-name> <name>

Elimina una regola di bilanciamento del carico.

	azure network lb rule delete -g myresourcegroup -l mylb -n mynewlbrule

	info:    Executing command network lb rule delete
	+ Looking up the load balancer "mylb"
	Delete load balancing rule mynewlbrule? [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb rule delete command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the rule
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**Comandi per gestire le regole NAT in ingresso per il bilanciamento del carico**

	network lb inbound-nat-rule create [options] <resource-group> <lb-name> <name>
Crea una regola NAT in ingresso per il bilanciamento del carico.

Nell'esempio seguente è stata creata una regola NAT dall'oggetto IP front-end (definito in precedenza; per altre informazioni, vedere "azure network frontend-ip") con una porta di ascolto in ingresso e una porta in uscita che verrà usata dal servizio/dispositivo di bilanciamento del carico per inviare il traffico di rete.


	azure network lb inbound-nat-rule create -g myresourcegroup -l mylb -n myinboundnat -p tcp -f 80 -b 8080 -i myfrontendip

	info:    Executing command network lb inbound-nat-rule create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
	data:    Name:                      myinboundnat
	data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend IP configuration
	data:    Protocol                   Tcp
	data:    Frontend port              80
	data:    Backend port               8080
	data:    Enable floating IP         false
	info:    network lb inbound-nat-rule create command OK

Opzioni dei parametri:

	-h, --help                                     output usage information
	-v, --verbose                                  use verbose output
	--json                                         use json output
	-g, --resource-group <resource-group>          the name of the resource group
	-l, --lb-name <lb-name>                        the name of the load balancer
	-n, --name <name>                              the name of the inbound NAT rule
	-p, --protocol <protocol>                      the rule protocol [tcp,udp]
	-f, --frontend-port <frontend-port>            the frontend port [0-65535]
	-b, --backend-port <backend-port>              the backend port [0-65535]
	-e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
	-i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
	-m, --vm-id <vm-id>                            the VM id.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
	-a, --vm-name <vm-name>                        the VM name.This VM must exist in the same resource group as the lb.
	Please use vm-id if that is not the case.
	this parameter will be ignored if --vm-id is specified
	-s, --subscription <subscription>              the subscription identifier
<BR>

	network lb inbound-nat-rule set [options] <resource-group> <lb-name> <name>
Aggiorna una regola NAT in ingresso esistente. Nell'esempio seguente la porta di ascolto in ingresso è stata modificata da 80 a 81.

	azure network lb inbound-nat-rule set -g group-1 -l mylb -n myinboundnat -p tcp -f 81 -b 8080 -i myfrontendip

	info:    Executing command network lb inbound-nat-rule set
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
	data:    Name:                      myinboundnat
	data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend IP configuration
	data:    Protocol                   Tcp
	data:    Frontend port              81
	data:    Backend port               8080
	data:    Enable floating IP         false
	info:    network lb inbound-nat-rule set command OK

Opzioni dei parametri:

	-h, --help                                     output usage information
	-v, --verbose                                  use verbose output
	--json                                         use json output
	-g, --resource-group <resource-group>          the name of the resource group
	-l, --lb-name <lb-name>                        the name of the load balancer
	-n, --name <name>                              the name of the inbound NAT rule
	-p, --protocol <protocol>                      the rule protocol [tcp,udp]
	-f, --frontend-port <frontend-port>            the frontend port [0-65535]
	-b, --backend-port <backend-port>              the backend port [0-65535]
	-e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
	-i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
	-m, --vm-id [vm-id]                            the VM id.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
	-a, --vm-name <vm-name>                        the VM name.
	This virtual machine must exist in the same resource group as the lb.
	Please use vm-id if that is not the case
	-s, --subscription <subscription>              the subscription identifier
<BR>

	network lb inbound-nat-rule list [options] <resource-group> <lb-name>

Elenca tutte le regole NAT in ingresso per il servizio/dispositivo di bilanciamento del carico.

	azure network lb inbound-nat-rule list -g myresourcegroup -l mylb

	info:    Executing command network lb inbound-nat-rule list
	+ Looking up the load balancer "mylb"
	data:    Name          Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend IP configuration
	data:    ------------  ------------------  --------  -------------  ------------  ------------------  -----------------------  ---
	---------------------
	data:    myinboundnat  Succeeded           Tcp       81             8080          false               4

	info:    network lb inbound-nat-rule list command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb inbound-nat-rule delete [options] <resource-group> <lb-name> <name>

Elimina una regola NAT per il servizio/dispositivo di bilanciamento del carico in un gruppo di risorse specifico.

	azure network lb inbound-nat-rule delete -g myresourcegroup -l mylb -n myinboundnat

	info:    Executing command network lb inbound-nat-rule delete
	+ Looking up the load balancer "mylb"
	Delete inbound NAT rule "myinboundnat?" [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb inbound-nat-rule delete command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the inbound NAT rule
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**Comandi per gestire gli indirizzi IP pubblici**

	network public-ip create [options] <resource-group> <name> <location>
Crea una risorsa IP pubblico. La risorsa verrà creata e associata a un nome di dominio.

	azure network public-ip create -g myresourcegroup -n mytestpublicip1 -l eastus -d azureclitest -a "Dynamic"
	info:    Executing command network public-ip create
	+ Looking up the public ip "mytestpublicip1"
	+ Creating public ip address "mytestpublicip1"
	+ Looking up the public ip "mytestpublicip1"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
	data:    Name:                 mytestpublicip1
	data:    Type:                 Microsoft.Network/publicIPAddresses
	data:    Location:             eastus
	data:    Provisioning state:   Succeeded
	data:    Allocation method:    Dynamic
	data:    Idle timeout:         4
	data:    Domain name label:    azureclitest
	data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
	info:    network public-ip create command OK


Opzioni dei parametri: -h, --help output usage information -v, --verbose use verbose output --json use json output -g, --resource-group <resource-group> the name of the resource group -n, --name <name> the name of the public ip -l, --location <location> the location -d, --domain-name-label <domain-name-label> the domain name label. This set DNS to <domain-name-label>.<location>.cloudapp.azure.com -a, --allocation-method <allocation-method> the allocation method [Static][Dynamic] -i, --idletimeout <idletimeout> the idle timeout in minutes -f, --reverse-fqdn <reverse-fqdn> the reverse fqdn -t, --tags <tags> the list of tags. Can be multiple. In the format of "name=value". Name is required and value is optional. For example, -t tag1=value1;tag2 -s, --subscription <subscription> the subscription identifier <br>

	network public-ip set [options] <resource-group> <name>
Aggiorna le proprietà di una risorsa IP pubblico esistente. Nell'esempio seguente l'impostazione dell'indirizzo IP pubblico è stata modificata da Dynamic a Static.

	azure network public-ip set -g group-1 -n mytestpublicip1 -d azureclitest -a "Static"
	info:    Executing command network public-ip set
	+ Looking up the public ip "mytestpublicip1"
	+ Updating public ip address "mytestpublicip1"
	+ Looking up the public ip "mytestpublicip1"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
	data:    Name:                 mytestpublicip1
	data:    Type:                 Microsoft.Network/publicIPAddresses
	data:    Location:             eastus
	data:    Provisioning state:   Succeeded
	data:    Allocation method:    Static
	data:    Idle timeout:         4
	data:    IP Address:           (static IP address)
	data:    Domain name label:    azureclitest
	data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
	info:    network public-ip set command OK

Opzioni dei parametri:

	-h, --help                                   output usage information
	-v, --verbose                                use verbose output
	--json                                       use json output
	-g, --resource-group <resource-group>        the name of the resource group
	-n, --name <name>                            the name of the public ip
	-d, --domain-name-label [domain-name-label]  the domain name label.
	This set DNS to <domain-name-label>.<location>.cloudapp.azure.com
	-a, --allocation-method <allocation-method>  the allocation method [Static][Dynamic]
	-i, --idletimeout <idletimeout>              the idle timeout in minutes
	-f, --reverse-fqdn [reverse-fqdn]            the reverse fqdn
	-t, --tags <tags>                            the list of tags.
	Can be multiple. In the format of "name=value".
	Name is required and value is optional.
	For example, -t tag1=value1;tag2
	--no-tags                                    remove all existing tags
	-s, --subscription <subscription>            the subscription identifier

<br> network public-ip list [opzioni] <resource-group> Elenca tutte le risorse IP pubblico all'interno di un gruppo di risorse.

	azure network public-ip list -g myresourcegroup

	info:    Executing command network public-ip list
	+ Getting the public ip addresses
	data:    Name             Location  Allocation  IP Address    Idle timeout  DNS Name
	data:    ---------------  --------  ----------  ------------  ------------  -------------------------------------------
	data:    mypubip5         westus    Dynamic                   4             "domain name".westus.cloudapp.azure.com
	data:    myPublicIP       eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
	data:    mytestpublicip   eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
	data:    mytestpublicip1  eastus   Static (Static IP address) 4             azureclitest.eastus.cloudapp.azure.com

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-s, --subscription <subscription>      the subscription identifier
<BR> network public-ip show [opzioni] <resource-group> <name> Visualizza le proprietà di una risorsa IP pubblico all'interno di un gruppo di risorse.

	azure network public-ip show -g myresourcegroup -n mytestpublicip

	info:    Executing command network public-ip show
	+ Looking up the public ip "mytestpublicip1"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip
	data:    Name:                 mytestpublicip
	data:    Type:                 Microsoft.Network/publicIPAddresses
	data:    Location:             eastus
	data:    Provisioning state:   Succeeded
	data:    Allocation method:    Static
	data:    Idle timeout:         4
	data:    IP Address:           (static IP address)
	data:    Domain name label:    azureclitest
	data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
	info:    network public-ip show command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the public IP
	-s, --subscription <subscription>      the subscription identifier


	network public-ip delete [options] <resource-group> <name>

Elimina una risorsa IP pubblico.

	azure network public-ip delete -g group-1 -n mypublicipname
	info:    Executing command network public-ip delete
	+ Looking up the public ip "mypublicipname"
	Delete public ip address "mypublicipname"? [y/n] y
	+ Deleting public ip address "mypublicipname"
	info:    network public-ip delete command OK

Opzioni dei parametri:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the public IP
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier


**Comandi per gestire le interfacce di rete**

	network nic create [options] <resource-group> <name> <location>
Crea una risorsa denominata interfaccia di rete (NIC) che può essere usata per i servizi/dispositivi di bilanciamento del carico o per l'associazione a una macchina virtuale.

	azure network nic create -g myresourcegroup -l eastus -n testnic1 --subnet-name subnet-1 --subnet-vnet-name myvnet

	info:    Executing command network nic create
	+ Looking up the network interface "testnic1"
	+ Looking up the subnet "subnet-1"
	+ Creating network interface "testnic1"
	+ Looking up the network interface "testnic1"
	data:    Id:                     /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/networkInterfaces/testnic1
	data:    Name:                   testnic1
	data:    Type:                   Microsoft.Network/networkInterfaces
	data:    Location:               eastus
	data:    Provisioning state:     Succeeded
	data:    IP configurations:
	data:       Name:                         NIC-config
	data:       Provisioning state:           Succeeded
	data:       Private IP address:           10.0.0.5
	data:       Private IP Allocation Method: Dynamic
	data:       Subnet:                       /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/virtualNetworks/myVNET/subnets/Subnet-1

Opzioni dei parametri:

	-h, --help                                                       output usage information
	-v, --verbose                                                    use verbose output
	--json                                                           use json output
	-g, --resource-group <resource-group>                            the name of the resource group
	-n, --name <name>                                                the name of the network interface
	-l, --location <location>                                        the location
	-w, --network-security-group-id <network-security-group-id>      the network security group identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
	-o, --network-security-group-name <network-security-group-name>  the network security group name.
	This network security group must exist in the same resource group as the nic.
	Please use network-security-group-id if that is not the case.
	-i, --public-ip-id <public-ip-id>                                the public IP identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
	-p, --public-ip-name <public-ip-name>                            the public IP name.
	This public ip must exist in the same resource group as the nic.
	Please use public-ip-id if that is not the case.
	-a, --private-ip-address <private-ip-address>                    the private IP address
	-u, --subnet-id <subnet-id>                                      the subnet identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>/subnets/<subnet-name>
	--subnet-name <subnet-name>                                  the subnet name
	-m, --subnet-vnet-name <subnet-vnet-name>                        the vnet name under which subnet-name exists
	-t, --tags <tags>                                                the comma seperated list of tags.
	Can be multiple. In the format of "name=value".
	Name is required and value is optional.
	For example, -t tag1=value1;tag2
	-s, --subscription <subscription>                                the subscription identifier
	data:
	info:    network nic create command OK

<BR>

	network nic set [options] <resource-group> <name>

	network nic list [options] <resource-group>
	network nic show [options] <resource-group> <name>
	network nic delete [options] <resource-group> <name>

**Comandi per gestire i gruppi di sicurezza di rete**

	network nsg create [options] <resource-group> <name> <location>
	network nsg set [options] <resource-group> <name>
	network nsg list [options] <resource-group>
	network nsg show [options] <resource-group> <name>
	network nsg delete [options] <resource-group> <name>

**Comandi per gestire le regole dei gruppi di sicurezza di rete**

	network nsg rule create [options] <resource-group> <nsg-name> <name>
	network nsg rule set [options] <resource-group> <nsg-name> <name>
	network nsg rule list [options] <resource-group> <nsg-name>
	network nsg rule show [options] <resource-group> <nsg-name> <name>
	network nsg rule delete [options] <resource-group> <nsg-name> <name>

**Comandi per gestire il profilo di gestione traffico**

	network traffic-manager profile create [options] <resource-group> <name>
	network traffic-manager profile set [options] <resource-group> <name>
	network traffic-manager profile list [options] <resource-group>
	network traffic-manager profile show [options] <resource-group> <name>
	network traffic-manager profile delete [options] <resource-group> <name>
	network traffic-manager profile is-dns-available [options] <resource-group> <relative-dns-name>

**Comandi per gestire gli endpoint di gestione traffico**

	network traffic-manager profile endpoint create [options] <resource-group> <profile-name> <name> <endpoint-location>
	network traffic-manager profile endpoint set [options] <resource-group> <profile-name> <name>
	network traffic-manager profile endpoint delete [options] <resource-group> <profile-name> <name>

**Comandi per gestire i gateway di rete virtuale**

	network gateway list [options] <resource-group>

## azure provider - Comandi per gestire le registrazioni dei provider di risorse

**Elencare i provider attualmente registrati in Gestione risorse di Azure**

	provider list [options]

**Visualizzare i dettagli relativi allo spazio dei nomi del provider richiesto**

	provider show [options] <namespace>

**Registrare il provider nella sottoscrizione**

	provider register [options] <namespace>

**Annullare la registrazione del provider nella sottoscrizione**

	provider unregister [options] <namespace>

## azure resource - Comandi per gestire le risorse

**Creare una risorsa in un gruppo di risorse**

	resource create [options] <resource-group> <name> <resource-type> <location> <api-version>

**Aggiornare una risorsa in un gruppo di risorse senza modelli o parametri**

	resource set [options] <resource-group> <name> <resource-type> <properties> <api-version>

**Elencare le risorse**

	resource list [options] [resource-group]

**Ottenere una risorsa all'interno di un gruppo di risorse o una sottoscrizione**

	resource show [options] <resource-group> <name> <resource-type> <api-version>

**Eliminare una risorsa in un gruppo di risorse**

	resource delete [options] <resource-group> <name> <resource-type> <api-version>

## azure role - Comandi per gestire i ruoli di Azure

**Ottenere tutte le definizioni di ruolo disponibili**

	role list [options]

**Ottenere una definizione di ruolo disponibile**

	role show [options] [name]

**Comandi per gestire l'assegnazione del ruolo**

	role assignment create [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
	role assignment list [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
	role assignment delete [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]

## azure storage - Comandi per gestire gli oggetti di archiviazione

**Comandi per gestire gli account di archiviazione**

	storage account list [options]
	storage account show [options] <name>
	storage account create [options] <name>
	storage account set [options] <name>
	storage account delete [options] <name>

**Comandi per gestire le chiavi degli account di archiviazione**

	storage account keys list [options] <name>
	storage account keys renew [options] <name>

**Comandi per visualizzare la stringa di connessione archiviazione**

	storage account connectionstring show [options] <name>

**Comandi per gestire i contenitori di archiviazione**

	storage container list [options] [prefix]
	storage container show [options] [container]
	storage container create [options] [container]
	storage container delete [options] [container]
	storage container set [options] [container]

**Comandi per gestire le firme di accesso condiviso del contenitore di archiviazione**

	storage container sas create [options] [container] [permissions] [expiry]

**Comandi per gestire i criteri di accesso archiviati del contenitore di archiviazione**

	storage container policy create [options] [container] [name]
	storage container policy show [options] [container] [name]
	storage container policy list [options] [container]
	storage container policy set [options] [container] [name]
	storage container policy delete [options] [container] [name]

**Comandi per gestire i BLOB di archiviazione**

	storage blob list [options] [container] [prefix]
	storage blob show [options] [container] [blob]
	storage blob delete [options] [container] [blob]
	storage blob upload [options] [file] [container] [blob]
	storage blob download [options] [container] [blob] [destination]

**Comandi per gestire le operazioni di copia BLOB**

	storage blob copy start [options] [sourceUri] [destContainer]
	storage blob copy show [options] [container] [blob]
	storage blob copy stop [options] [container] [blob] [copyid]

**Comandi per gestire la firma di accesso condiviso del BLOB di archiviazione**

	storage blob sas create [options] [container] [blob] [permissions] [expiry]

**Comandi per gestire le condivisioni file di archiviazione**

	storage share create [options] [share]
	storage share show [options] [share]
	storage share delete [options] [share]
	storage share list [options] [prefix]

**Comandi per gestire i file di archiviazione**

	storage file list [options] [share] [path]
	storage file delete [options] [share] [path]
	storage file upload [options] [source] [share] [path]
	storage file download [options] [share] [path] [destination]

**Comandi per gestire la directory dei file di archiviazione**

	storage directory create [options] [share] [path]
	storage directory delete [options] [share] [path]

**Comandi per gestire le code di archiviazione**

	storage queue create [options] [queue]
	storage queue list [options] [prefix]
	storage queue show [options] [queue]
	storage queue delete [options] [queue]

**Comandi per gestire le firme di accesso condiviso della coda di archiviazione**

	storage queue sas create [options] [queue] [permissions] [expiry]

**Comandi per gestire i criteri di accesso archiviati della coda di archiviazione**

	storage queue policy create [options] [queue] [name]
	storage queue policy show [options] [queue] [name]
	storage queue policy list [options] [queue]
	storage queue policy set [options] [queue] [name]
	storage queue policy delete [options] [queue] [name]

**Comandi per gestire le proprietà di registrazione dell'archiviazione**

	storage logging show [options]
	storage logging set [options]

**Comandi per gestire le proprietà delle metriche di archiviazione**

	storage metrics show [options]
	storage metrics set [options]

**Comandi per gestire le tabelle di archiviazione**

	storage table create [options] [table]
	storage table list [options] [prefix]
	storage table show [options] [table]
	storage table delete [options] [table]

**Comandi per gestire le firme di accesso condiviso della tabella di archiviazione**

	storage table sas create [options] [table] [permissions] [expiry]

**Comandi per gestire i criteri di accesso condiviso della tabella di archiviazione**

	storage table policy create [options] [table] [name]
	storage table policy show [options] [table] [name]
	storage table policy list [options] [table]
	storage table policy set [options] [table] [name]
	storage table policy delete [options] [table] [name]

## azure tag - Comandi per gestire il tag di Gestione risorse

**Aggiungere un tag**

	tag create [options] <name> <value>

**Rimuovere un intero tag o un valore di tag**

	tag delete [options] <name> <value>

**Elencare le informazioni di tag**

	tag list [options]

**Ottenere un tag**

	tag show [options] [name]

## azure vm - Comandi per gestire le macchine virtuali di Azure

**Creare una macchina virtuale**

	vm create [options] <resource-group> <name> <location> <os-type>

**Creare una macchina virtuale con le risorse predefinite**

	vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>

**Elencare le macchine virtuali all'interno di un gruppo di risorse**

	vm list [options] <resource-group>

**Ottenere una macchina virtuale all'interno di un gruppo di risorse**

	vm show [options] <resource-group> <name>

**Eliminare una macchina virtuale all'interno di un gruppo di risorse**

	vm delete [options] <resource-group> <name>

**Arrestare una macchina virtuale all'interno di un gruppo di risorse**

	vm stop [options] <resource-group> <name>

**Riavviare una macchina virtuale all'interno di un gruppo di risorse**

	vm restart [options] <resource-group> <name>

**Avviare una macchina virtuale all'interno di un gruppo di risorse**

	vm start [options] <resource-group> <name>

**Arrestare una macchina virtuale all'interno di un gruppo di risorse e rilasciare le risorse di calcolo**

	vm deallocate [options] <resource-group> <name>

**Elencare le dimensioni delle macchine virtuali disponibili**

	vm sizes [options]

**Acquisire la macchina virtuale come immagine del sistema operativo o immagine di macchina virtuale**

	vm capture [options] <resource-group> <name> <vhd-name-prefix>

**Impostare la macchina virtuale sullo stato generalizzato**

	vm generalize [options] <resource-group> <name>

**Ottenere la visualizzazione dell'istanza della macchina virtuale**

	vm get-instance-view [options] <resource-group> <name>

**Consente di ripristinare le impostazioni di Accesso desktop remoto o SSH in una macchina virtuale e di reimpostare la password per l'account con autorità di amministratore o sudo**

	vm reset-access [options] <resource-group> <name>

**Aggiornare la macchina virtuale con nuovi dati**

	vm set [options] <resource-group> <name>

**Comandi per gestire i dischi dati delle macchine virtuali**

	vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]
	vm disk detach [options] <resource-group> <vm-name> <lun>
	vm disk attach [options] <resource-group> <vm-name> [vhd-url]

**Comandi per gestire le estensioni delle risorse delle macchine virtuali**

	vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>
	vm extension get [options] <resource-group> <vm-name>

**Comandi per gestire la macchina virtuale Docker**

	vm docker create [options] <resource-group> <name> <location> <os-type>

**Comandi per gestire le immagini delle macchine virtuali**

	vm image list-publishers [options] <location>
	vm image list-offers [options] <location> <publisher>
	vm image list-skus [options] <location> <publisher> <offer>
	vm image list [options] <location> <publisher> [offer] [sku]
 

<!---HONumber=July15_HO3-->