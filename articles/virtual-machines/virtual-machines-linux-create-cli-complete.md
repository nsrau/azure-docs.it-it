<properties
   pageTitle="Creare una VM Linux da zero con l'interfaccia della riga di comando di Azure | Microsoft Azure"
   description="Creare una VM Linux, archiviazione, rete virtuale e subnet, scheda di rete, IP pubblico, gruppo di sicurezza di rete tutto da zero tramite l'interfaccia della riga di comando di Azure."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="vlivech"
   manager="squillace"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/10/2016"
   ms.author="v-livech"/>

# Creare una VM Linux da zero con l'interfaccia della riga di comando di Azure


## Obiettivo

- Distribuire un gruppo di risorse
- Distribuire un account di archiviazione
- Distribuire una rete virtuale e una subnet
- Configurare un gruppo di sicurezza di rete e regole in ingresso
- Assegnare un indirizzo IP pubblico alla scheda di rete
- Assegnare il gruppo di sicurezza di rete alla scheda di rete
- Distribuire una VM Ubuntu 14.04 LTS

## Prerequisiti

- Account Azure
  - [Ottenere una versione di valutazione gratuita.](https://azure.microsoft.com/pricing/free-trial/) 
  - [Portale di Azure](https://portal.azure.com)
- Strumento di analisi JSON: questo esempio usa [jq](https://stedolan.github.io/jq/)


## Introduzione

Questo articolo consente di creare una distribuzione simile alla distribuzione di un servizio cloud con una VM Linux all'interno di una subnet di rete virtuale. Illustra in dettaglio l'intera distribuzione di base, comando per comando, fino ad avere una VM Linux funzionante e sicura a cui è possibile connettersi da qualsiasi posizione in Internet.

Durante il processo si apprenderanno informazioni sulla gerarchia delle dipendenze fornita dal modello di distribuzione di Azure Resource Manager e sulle potenzialità che offre. Dopo aver visto come si compila il sistema, si potrà ricompilarlo molto più rapidamente usando i comandi dell'interfaccia della riga di comando di Azure più diretti (vedere [questo documento](virtual-machines-linux-quick-create-cli.md) per una distribuzione pressoché uguale con il comando `azure vm quick-create`); in alternativa, è possibile proseguire per comprendere a fondo come progettare e automatizzare le distribuzioni delle applicazioni e dell'intera rete e aggiornarle usando i [modelli di Azure Resource Manager](../resource-group-authoring-templates.md). Una volta appreso come interagiscono le parti della distribuzione, diventa più facile creare modelli per automatizzarle.

Si creerà una semplice rete con una VM utile per lo sviluppo e per semplici calcoli, seguendo una procedura illustrata in dettaglio. Si sarà quindi in grado di passare a distribuzioni e reti più complesse.

## Comandi rapidi

_Questa sezione di comandi rapidi include diversi esempi che è possibile sostituire con impostazioni personalizzate o modificare in base alle esigenze._

```bash
# Create the Resource Group
azure group create TestRG westeurope

# Create the Storage Account
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--type GRS \
computeteststore

# Verify the RG using the JSON parser
azure group show testrg --json | jq '.'

# Create the Virtual Network
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope

# Verify the RG
azure group show testrg --json | jq '.'

# Create the Subnet
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

# Verify the VNet and Subnet
azure network vnet show testrg testvnet --json | jq '.'

# Create the NIC
azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd

# Verify the NIC
azure network nic show testrg testnic --json | jq '.'

# Create the NSG
azure network nsg create testrg testnsg westeurope

# Add an inbound rule for the NSG
azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule

# Creat the Public facing NIC
azure network public-ip create -d testsubdomain testrg testpip westeurope

# Verify the NIC
azure network public-ip show testrg testpip --json | jq '.'

# Associate the Public IP to the NIC
azure network nic set --public-ip-name testpip testrg testnic

# Bind the NSG to the NIC
azure network nic set --network-security-group-name testnsg testrg testnic

# Create the Linux VM
azure vm create \            
    --resource-group testrg \
    --name testvm \
    --location westeurope \
    --os-type linux \
    --nic-name testnic \
    --vnet-name testvnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
    
# Verify everything built
azure vm show testrg testvm 

```


## Procedura dettagliata

### Creare un gruppo di risorse e scegliere i percorsi di distribuzione 

I gruppi di risorse di Azure sono entità di distribuzione logiche che contengono dati di configurazione e altri metadati per abilitare la gestione logica delle distribuzioni di risorse.

    azure group create TestRG westeurope                        
    info:    Executing command group create
    + Getting resource group TestRG
    + Creating resource group TestRG
    info:    Created resource group TestRG
    data:    Id:                  /subscriptions/<yoursub>/resourceGroups/TestRG
    data:    Name:                TestRG
    data:    Location:            westeurope
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

### Creare un account di archiviazione 

Tra gli altri scenari saranno necessari account di archiviazione per i dischi della VM e per eventuali altri dischi dati che si vuole aggiungere. In breve, si creeranno sempre account di archiviazione quasi subito dopo la creazione di gruppi di risorse.

Qui si userà il comando `azure storage account create`, passando il percorso dell'account, il gruppo di risorse che lo controllerà e il tipo di supporto di archiviazione preferito.

    azure storage account create \  
    --location westeurope \
    --resource-group TestRG \
    --type GRS \
    computeteststore
    info:    Executing command storage account create
    + Creating storage account
    info:    storage account create command OK
    rasquill•~/workspace/keygen» azure group show testrg 
    info:    Executing command group show
    + Listing resource groups
    + Listing resources for the group
    data:    Id:                  /subscriptions/<guid>/resourceGroups/TestRG
    data:    Name:                TestRG
    data:    Location:            westeurope
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:    Resources:
    data:
    data:      Id      : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore
    data:      Name    : computeteststore
    data:      Type    : storageAccounts
    data:      Location: westeurope
    data:      Tags    :
    data:
    data:    Permissions:
    data:      Actions: *
    data:      NotActions:
    data:
    info:    group show command OK
    
Si userà lo strumento [jq](https://stedolan.github.io/jq/), ma è possibile usare **jsawk** o qualsiasi libreria del linguaggio preferita per analizzare JSON, insieme all'opzione dell'interfaccia della riga di comando di Azure `--json` per esaminare il gruppo di risorse con il comando `azure group show`.

    azure group show testrg --json | jq '.'                                                                                        
    {
      "tags": {},
      "id": "/subscriptions/<guid>/resourceGroups/TestRG",
      "name": "TestRG",
      "provisioningState": "Succeeded",
      "location": "westeurope",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
          "name": "computeteststore",
          "type": "storageAccounts",
          "location": "westeurope",
          "tags": null
        }
      ],
      "permissions": [
        {
          "actions": [
            "*"
          ],
          "notActions": []
        }
      ]
    }

Per esaminare l'account di archiviazione tramite l'interfaccia della riga di comando, è necessario prima di tutto impostare i nomi di account e le chiavi con una variante del comando seguente, sostituendo il nome dell'account di archiviazione usato in questo articolo con uno personalizzato.

        AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"

Sarà quindi possibile visualizzare facilmente le informazioni di archiviazione:

        azure storage container list 
        info:    Executing command storage container list
        + Getting storage containers
        data:    Name  Public-Access  Last-Modified
        data:    ----  -------------  -----------------------------
        data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
        info:    storage container list command OK

### Creare la rete virtuale e la subnet 

Sarà necessario creare una rete virtuale di Azure e una subnet in cui è possibile installare la VM.

    azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
    info:    Executing command network vnet create
    + Looking up virtual network "TestVNet"
    + Creating virtual network "TestVNet"
    + Loading virtual network state
    data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
    data:    Name                            : TestVNet
    data:    Type                            : Microsoft.Network/virtualNetworks
    data:    Location                        : westeurope
    data:    ProvisioningState               : Succeeded
    data:    Address prefixes:
    data:      192.168.0.0/16
    info:    network vnet create command OK

Anche in questo caso si vedrà come si sviluppano le risorse con l'opzione --json di `azure group show` e **jq**. Ora sono disponibili una risorsa `storageAccounts` e una risorsa `virtualNetworks`.

    azure group show testrg --json | jq '.'
    {
      "tags": {},
      "id": "/subscriptions/<guid>/resourceGroups/TestRG",
      "name": "TestRG",
      "provisioningState": "Succeeded",
      "location": "westeurope",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
          "name": "TestVNet",
          "type": "virtualNetworks",
          "location": "westeurope",
          "tags": null
        },
        {
          "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
          "name": "computeteststore",
          "type": "storageAccounts",
          "location": "westeurope",
          "tags": null
        }
      ],
      "permissions": [
        {
          "actions": [
            "*"
          ],
          "notActions": []
        }
      ]
    }

Si passerà quindi alla creazione di una subnet all'interno della rete virtuale `TestVnet` in cui verrà distribuita la VM. Si userà il comando `azure network vnet subnet create` insieme alle risorse già create: il gruppo di risorse `TestRG`, la rete virtuale `TestVNet` e si aggiungerà il nome della subnet `FrontEnd` e il prefisso dell'indirizzo della subnet `192.168.1.0/24`, come indicato di seguito.

    azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
    info:    Executing command network vnet subnet create
    + Looking up the subnet "FrontEnd"
    + Creating subnet "FrontEnd"
    + Looking up the subnet "FrontEnd"
    data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:    Type                            : Microsoft.Network/virtualNetworks/subnets
    data:    ProvisioningState               : Succeeded
    data:    Name                            : FrontEnd
    data:    Address prefix                  : 192.168.1.0/24
    data:
    info:    network vnet subnet create command OK
  
Poiché la subnet è logicamente all'interno della rete virtuale, si cercheranno le informazioni relative alla subnet con un comando leggermente diverso: `azure network vnet show`, ma si esaminerà comunque l'output JSON restituito con **jq**.

    azure network vnet show testrg testvnet --json | jq '.'
    {
      "subnets": [
        {
          "ipConfigurations": [],
          "addressPrefix": "192.168.1.0/24",
          "provisioningState": "Succeeded",
          "name": "FrontEnd",
          "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
          "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
        }
      ],
      "tags": {},
      "addressSpace": {
        "addressPrefixes": [
          "192.168.0.0/16"
        ]
      },
      "dhcpOptions": {
        "dnsServers": []
      },
      "provisioningState": "Succeeded",
      "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
      "name": "TestVNet",
      "location": "westeurope"
    }

### Creare una scheda di rete da usare con la VM Linux

Anche le schede di interfaccia di rete sono disponibili a livello di codice, perché può essere necessario applicarvi regole d'uso e averne più di una.

        azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd
        info:    Executing command network nic create
        + Looking up the network interface "TestNIC"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC"
        + Looking up the network interface "TestNIC"
        data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
        data:    Name                            : TestNIC
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westeurope
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.101
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

Poiché la risorsa scheda di rete è associata a una macchina virtuale e a un gruppo di sicurezza di rete, è possibile visualizzarla come una risorsa di primo livello quando si esamina il gruppo di risorse `TestRG`:

        azure group show testrg --json | jq '.'
        {
        "tags": {},
        "id": "/subscriptions/guid/resourceGroups/TestRG",
        "name": "TestRG",
        "provisioningState": "Succeeded",
        "location": "westeurope",
        "properties": {
            "provisioningState": "Succeeded"
        },
        "resources": [
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
            "name": "TestNIC",
            "type": "networkInterfaces",
            "location": "westeurope",
            "tags": null
            },
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
            "name": "TestVNet",
            "type": "virtualNetworks",
            "location": "westeurope",
            "tags": null
            },
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
            "name": "computeteststore",
            "type": "storageAccounts",
            "location": "westeurope",
            "tags": null
            }
        ],
        "permissions": [
            {
            "actions": [
                "*"
            ],
            "notActions": []
            }
        ]
        }

È possibile visualizzare i dettagli esaminando di risorsa direttamente con il comando `azure network nic show`.

        azure network nic show testrg testnic --json | jq '.'
        {
        "ipConfigurations": [
            {
            "loadBalancerBackendAddressPools": [],
            "loadBalancerInboundNatRules": [],
            "privateIpAddress": "192.168.1.101",
            "privateIpAllocationMethod": "Static",
            "subnet": {
                "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
            },
            "provisioningState": "Succeeded",
            "name": "NIC-config",
            "etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/NIC-config"
            }
        ],
        "tags": {},
        "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
        },
        "enableIPForwarding": false,
        "provisioningState": "Succeeded",
        "etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
        "name": "TestNIC",
        "location": "westeurope"
        }

### Creare il gruppo di sicurezza di rete e le regole

Ora si creerà il gruppo di sicurezza di rete e le regole in ingresso che regolano l'accesso alla scheda di rete.

        azure network nsg create testrg testnsg westeurope

Aggiungere la regola in ingresso per il gruppo di sicurezza di rete per consentire le connessioni in ingresso sulla porta 22 (per supportare SSH):

        azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule

> [AZURE.NOTE] La regola in ingresso è un filtro per le connessioni di rete in ingresso. In questo esempio il gruppo di sicurezza di rete verrà associato alla scheda di rete virtuale della VM, di conseguenza qualsiasi richiesta per la porta 22 verrà passata alla scheda di rete nella VM. Poiché si tratta di una regola per l'apertura di una porta per una connessione di rete, non un endpoint come nelle distribuzioni classiche, è necessario lasciare `--source-port-range` impostato sul valore predefinito '*' per accettare le richieste in ingresso da **tutte** le porte richiedenti, che sono in genere dinamiche.

### Creare l'indirizzo IP pubblico (PIP)

Si creerà ora l'indirizzo IP pubblico (PIP) che consente di connettersi alla VM da Internet tramite il comando `azure network public-ip create`. Poiché il valore predefinito è un indirizzo dinamico, si crea una voce DNS denominata nel dominio **cloudapp.azure.com** usando l'opzione `-d testsubdomain`.

        azure network public-ip create -d testsubdomain testrg testpip westeurope
        info:    Executing command network public-ip create
        + Looking up the public ip "testpip"
        + Creating public ip address "testpip"
        + Looking up the public ip "testpip"
        data:    Id                              : /subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip
        data:    Name                            : testpip
        data:    Type                            : Microsoft.Network/publicIPAddresses
        data:    Location                        : westeurope
        data:    Provisioning state              : Succeeded
        data:    Allocation method               : Dynamic
        data:    Idle timeout                    : 4
        data:    Domain name label               : testsubdomain
        data:    FQDN                            : testsubdomain.westeurope.cloudapp.azure.com
        info:    network public-ip create command OK

Anche questa è una risorsa di primo livello che può essere visualizzata con `azure group show`.

        azure group show testrg --json | jq '.'
        {
        "tags": {},
        "id": "/subscriptions/guid/resourceGroups/TestRG",
        "name": "TestRG",
        "provisioningState": "Succeeded",
        "location": "westeurope",
        "properties": {
            "provisioningState": "Succeeded"
        },
        "resources": [
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
            "name": "TestNIC",
            "type": "networkInterfaces",
            "location": "westeurope",
            "tags": null
            },
            {
            "id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
            "name": "testpip",
            "type": "publicIPAddresses",
            "location": "westeurope",
            "tags": null
            },
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
            "name": "TestVNet",
            "type": "virtualNetworks",
            "location": "westeurope",
            "tags": null
            },
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
            "name": "computeteststore",
            "type": "storageAccounts",
            "location": "westeurope",
            "tags": null
            }
        ],
        "permissions": [
            {
            "actions": [
                "*"
            ],
            "notActions": []
            }
        ]
        }

Come sempre, è possibile esaminare altri dettagli relativi alla risorsa, incluso il nome di dominio completo (FQDN) del sottodominio, usando il comando `azure network public-ip show` più completo. Si noti che la risorsa indirizzo IP pubblico è stata allocata in modo logico, ma non è ancora stato assegnato un indirizzo specifico. A questo scopo sarà necessaria una VM, che non è ancora stata creata.

        azure network public-ip show testrg testpip --json | jq '.'
        {
        "tags": {},
        "publicIpAllocationMethod": "Dynamic",
        "dnsSettings": {
            "domainNameLabel": "testsubdomain",
            "fqdn": "testsubdomain.westeurope.cloudapp.azure.com"
        },
        "idleTimeoutInMinutes": 4,
        "provisioningState": "Succeeded",
        "etag": "W/"c63154b3-1130-49b9-a887-877d74d5ebc5"",
        "id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
        "name": "testpip",
        "location": "westeurope"
        }

### Associare l'indirizzo IP pubblico e il gruppo di sicurezza di rete alla scheda di rete

        azure network nic set --public-ip-name testpip testrg testnic

Associare il gruppo di sicurezza di rete alla scheda di rete:

        azure network nic set --network-security-group-name testnsg testrg testnic

### Creare la VM Linux

Sono state create le risorse di archiviazione e di rete per supportare una VM accessibile da Internet. Ora si creerà la VM che verrà protetta con una chiave SSH senza password. In questo caso, si vuole creare una VM Ubuntu basata sull'LTS più recente. È necessario trovare prima di tutto informazioni sulle immagini usando `azure vm image list`, come descritto nell'articolo relativo alla [ricerca di immagini di VM di Azure](virtual-machines-linux-cli-ps-findimage.md). È stata selezionata un'immagine con il comando `azure vm image list westeurope canonical | grep LTS` e in questo caso si userà `canonical:UbuntuServer:14.04.3-LTS:14.04.201509080`, ma per l'ultimo campo si passerà `latest`, in modo che in futuro si avrà sempre la build più recente. La stringa usata sarà `canonical:UbuntuServer:14.04.3-LTS:latest`.

> [AZURE.NOTE] Il passaggio successivo è familiare a chiunque abbia già creato una coppia di chiavi ssh-rsa pubblica e privata in Linux o Mac usando **ssh-keygen -t rsa -b 2048**. Se nella directory `~/.ssh` non sono disponibili coppie di chiavi del certificato, è possibile crearle: <br /> 1. Automaticamente tramite l'opzione `azure vm create --generate-ssh-keys`. 2. Manualmente usando [le istruzioni per crearle personalmente](virtual-machines-linux-ssh-from-linux.md) <br />. In alternativa, è possibile usare le opzioni `azure vm create --admin-username --admin-password` per usare il metodo meno sicuro basato su nome utente e password per l'autenticazione delle connessioni SSH dopo la creazione della VM.

Per creare la VM si dovranno unire tutte le risorse e le informazioni con il comando `azure vm create`.

        azure vm create \            
        --resource-group testrg \
        --name testvm \
        --location westeurope \
        --os-type linux \
        --nic-name testnic \
        --vnet-name testvnet \
        --vnet-subnet-name FrontEnd \
        --storage-account-name computeteststore \
        --image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
        --ssh-publickey-file ~/.ssh/id_rsa.pub \
        --admin-username ops
        info:    Executing command vm create
        + Looking up the VM "testvm"
        info:    Verifying the public key SSH file: /Users/user/.ssh/id_rsa.pub
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account computeteststore
        + Looking up the NIC "testnic"
        info:    Found an existing NIC "testnic"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "testnic"
        info:    This NIC IP configuration has a public ip already configured "/subscriptions/guid/resourcegroups/testrg/providers/microsoft.network/publicipaddresses/testpip", any public ip parameters if provided, will be ignored.
        + Creating VM "testvm"
        info:    vm create command OK

È possibile connettersi immediatamente alla VM usando le chiavi SSH predefinite.

        ssh ops@testsubdomain.westeurope.cloudapp.azure.com           
        The authenticity of host 'testsubdomain.westeurope.cloudapp.azure.com (XX.XXX.XX.XXX)' can't be established.
        RSA key fingerprint is b6:a4:7g:4b:cb:cd:76:87:63:2d:84:83:ac:12:2d:cd.
        Are you sure you want to continue connecting (yes/no)? yes
        Warning: Permanently added 'testsubdomain.westeurope.cloudapp.azure.com,XX.XXX.XX.XXX' (RSA) to the list of known hosts.
        Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)
        
        * Documentation:  https://help.ubuntu.com/
        
        System information as of Mon Sep 28 18:45:02 UTC 2015
        
        System load: 0.64              Memory usage: 5%   Processes:       81
        Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0
        
        Graph this data and manage this system at:
            https://landscape.canonical.com/
        
        Get cloud support with Ubuntu Advantage Cloud Guest:
            http://www.ubuntu.com/business/services/cloud
        
        0 packages can be updated.
        0 updates are security updates.
        
        
        
        The programs included with the Ubuntu system are free software;
        the exact distribution terms for each program are described in the
        individual files in /usr/share/doc/*/copyright.
        
        Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
        applicable law.
        
        ops@testvm:~$

Ora è possibile usare il comando `azure vm show testrg testvm` per esaminare cosa è stato creato. A questo punto, è disponibile una VM Ubuntu in esecuzione in Azure a cui è possibile accedere solo con la coppia di chiavi SSH disponibile. Le password sono disabilitate.

        azure vm show testrg testvm 
        info:    Executing command vm show
        + Looking up the VM "testvm"
        + Looking up the NIC "testnic"
        + Looking up the public ip "testpip"
        data:    Id                              :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm
        data:    ProvisioningState               :Succeeded
        data:    Name                            :testvm
        data:    Location                        :westeurope
        data:    FQDN                            :testsubdomain.westeurope.cloudapp.azure.com
        data:    Type                            :Microsoft.Compute/virtualMachines
        data:
        data:    Hardware Profile:
        data:      Size                          :Standard_A1
        data:
        data:    Storage Profile:
        data:      Image reference:
        data:        Publisher                   :canonical
        data:        Offer                       :UbuntuServer
        data:        Sku                         :14.04.3-LTS
        data:        Version                     :latest
        data:
        data:      OS Disk:
        data:        OSType                      :Linux
        data:        Name                        :cli4eecdddc349d6015-os-1443465824206
        data:        Caching                     :ReadWrite
        data:        CreateOption                :FromImage
        data:        Vhd:
        data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli4eecdddc349d6015-os-1443465824206.vhd
        data:
        data:    OS Profile:
        data:      Computer Name                 :testvm
        data:      User Name                     :ops
        data:      Linux Configuration:
        data:        Disable Password Auth       :true
        data:        SSH Public Keys:
        data:          Public Key #1:
        data:            Path                    :/home/ops/.ssh/authorized_keys
        data:            Key                     :MIIBrTCCAZigAwIBAgIBATALBgkqhkiG9w0BAQUwADAiGA8yMDE1MDkyODE4MzM0
        <snip>
        data:
        data:    Network Profile:
        data:      Network Interfaces:
        data:        Network Interface #1:
        data:          Id                        :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
        data:          Primary                   :true
        data:          MAC Address               :00-0D-3A-21-8E-AE
        data:          Provisioning State        :Succeeded
        data:          Name                      :testnic
        data:          Location                  :westeurope
        data:            Private IP alloc-method :Dynamic
        data:            Private IP address      :192.168.1.101
        data:            Public IP address       :40.115.48.189
        data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
        data:
        data:    Diagnostics Instance View:
        info:    vm show command OK

### Passaggi successivi

Ora è possibile iniziare con più componenti di rete e VM.

<!---HONumber=AcomDC_0330_2016-->