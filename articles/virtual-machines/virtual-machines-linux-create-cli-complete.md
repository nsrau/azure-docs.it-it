<properties
   pageTitle="Creare una VM Linux da zero con l'interfaccia della riga di comando di Azure | Microsoft Azure"
   description="Creare una VM Linux, archiviazione, rete virtuale e subnet, scheda di rete, IP pubblico, gruppo di sicurezza di rete tutto da zero tramite l'interfaccia della riga di comando di Azure."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="squillace"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/29/2016"
   ms.author="iainfou"/>

# Creare una VM Linux da zero con l'interfaccia della riga di comando di Azure

Per creare una macchina virtuale Linux è necessaria l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md) con modalità Azure Resource Manager attivata (`azure config mode arm`) e uno strumento di analisi JSON, in questo caso [jq](https://stedolan.github.io/jq/).

## Comandi rapidi

Creare il gruppo di risorse

```bash
azure group create TestRG -l westeurope
```

Verificare il gruppo di risorse con il parser JSON

```bash
azure group show TestRG --json | jq '.'
```

Creare l'account di archiviazione

```bash
azure storage account create -g TestRG -l westeurope --kind Storage --sku-name GRS computeteststore
```

Verificare l'account di archiviazione con il parser JSON

```bash
azure storage account show -g TestRG computeteststore --json | jq '.'
```

Creare la rete virtuale

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Creare la subnet

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Verificare la rete virtuale e la subnet con il parser JSON

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Creare un IP pubblico

```bash
azure network public-ip create -g TestRG -n TestLBPIP -l westeurope -d testlb -a static -i 4
```

Creare il servizio di bilanciamento del carico

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Creare un pool di indirizzi IP front-end per il servizio di bilanciamento del carico e associare l'IP pubblico

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Creare il pool di indirizzi IP back-end per il servizio di bilanciamento del carico

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Creare le regole NAT in ingresso SSH per il servizio di bilanciamento del carico

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

Creare le regole NAT in ingresso Web per il servizio di bilanciamento del carico

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Creare il probe di integrità per il servizio di bilanciamento del carico

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

Verificare il servizio di bilanciamento del carico, il pool di indirizzi IP e le regole NAT con il parser JSON

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Creare la prima scheda di rete

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
```

Creare la seconda scheda di rete

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
```

Verificare le schede di rete con il parser JSON

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
azure network nic show TestRG LB-NIC2 --json | jq '.'
```

Creare il gruppo di sicurezza di rete

```bash
azure network nsg create -g TestRG -n TestNSG -l westeurope
```

Aggiungere le regole in ingresso per il gruppo di sicurezza di rete

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow -g TestRG -a TestNSG -n SSHRule
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

Verificare il gruppo di sicurezza di rete e le regole in ingresso con il parser JSON

```bash
azure network nsg show -g TestRG -n TestNSG --json | jq '.'
```

Associare il gruppo di sicurezza di rete alle schede di rete

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

Creare il set di disponibilità

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Creare la prima VM Linux

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Creare la seconda VM Linux

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Verificare tutti gli elementi creati con il parser JSON

```bash
azure vm show -g TestRG -n TestVM1 --json | jq '.'
azure vm show -g TestRG -n TestVM2 --json | jq '.'
```

## Procedura dettagliata

### Introduzione

In questo articolo viene creata una distribuzione con due macchine virtuali Linux e un servizio di bilanciamento del carico. Viene presentata in dettaglio l'intera distribuzione di base, comando per comando, fino a realizzare VM Linux funzionanti e sicure a cui è possibile connettersi da qualsiasi posizione in Internet.

Durante il processo si apprenderanno informazioni sulla gerarchia delle dipendenze fornita dal modello di distribuzione di Azure Resource Manager e sulle potenzialità che offre. Dopo aver appreso come si compila il sistema, si potrà ricompilarlo molto più rapidamente usando comandi dell'interfaccia della riga di comando di Azure più diretti (vedere [questo documento](virtual-machines-linux-quick-create-cli.md) per una distribuzione pressoché uguale con il comando `azure vm quick-create`); in alternativa, è possibile proseguire per comprendere a fondo come progettare e automatizzare le distribuzioni delle applicazioni e dell'intera rete e aggiornarle usando i [modelli di Azure Resource Manager](../resource-group-authoring-templates.md). Una volta appreso come interagiscono le parti della distribuzione, diventa più facile creare modelli per automatizzarle.

Si creerà una semplice rete e un servizio di bilanciamento del carico con un paio di VM utili per lo sviluppo e per semplici calcoli, seguendo una procedura illustrata in dettaglio. Si sarà quindi in grado di passare a distribuzioni e reti più complesse.

## Creare un gruppo di risorse e scegliere i percorsi di distribuzione

I gruppi di risorse di Azure sono entità di distribuzione logiche che contengono dati di configurazione e altri metadati per abilitare la gestione logica delle distribuzioni di risorse.

```bash
azure group create TestRG westeurope
```

Output

```bash                        
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
```

## Creare un account di archiviazione

Tra gli altri scenari saranno necessari account di archiviazione per i dischi della macchina virtuale e per eventuali altri dischi dati che si vuole aggiungere. In breve, si creeranno sempre account di archiviazione quasi subito dopo la creazione di gruppi di risorse.

Viene usato il comando `azure storage account create`, passando il percorso dell'account, il gruppo di risorse che lo controllerà e il tipo di supporto di archiviazione preferito.

```bash
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--kind Storage --sku-name GRS \
computeteststore
```

Output

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
ahmet•~/workspace/keygen» azure group show testrg
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
```

Viene usato lo strumento [jq](https://stedolan.github.io/jq/), ma è possibile usare **jsawk** o la libreria del linguaggio che si preferisce per analizzare JSON, insieme all'opzione dell'interfaccia della riga di comando di Azure `--json` per esaminare il gruppo di risorse con il comando `azure group show`.

```bash
azure group show TestRG --json | jq                                                                                      
```


Output

```bash
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
```

Per esaminare l'account di archiviazione tramite l'interfaccia della riga di comando, è necessario prima di tutto impostare i nomi di account e le chiavi con una variante del comando seguente, sostituendo il nome dell'account di archiviazione usato in questo articolo con uno personalizzato.

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"
```

Sarà quindi possibile visualizzare facilmente le informazioni di archiviazione:

```bash
azure storage container list
```

Output

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## Creare la rete virtuale e la subnet

Sarà necessario creare una rete virtuale di Azure e una subnet in cui è possibile installare le VM.

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Output

```bash
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
```

Anche in questo caso osservare come vengono sviluppate le risorse con l'opzione --json di `azure group show` e **jq**. Ora sono disponibili una risorsa `storageAccounts` e una risorsa `virtualNetworks`.

```bash
azure group show TestRG --json | jq '.'
```

Output

```bash
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
```

Viene quindi creata una subnet all'interno della rete virtuale `TestVnet` in cui verranno distribuite le macchine virtuali. Viene usato il comando `azure network vnet subnet create` insieme alle risorse già create, ovvero il gruppo di risorse `TestRG` e la rete virtuale `TestVNet`, e vengono aggiunti il nome della subnet `FrontEnd` e il prefisso dell'indirizzo della subnet `192.168.1.0/24`, come indicato di seguito.

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Output

```bash
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
```

Dato che la subnet è logicamente all'interno della rete virtuale, le informazioni relative alla subnet vengono cercate con un comando leggermente diverso, `azure network vnet show`, ma viene esaminato comunque l'output JSON restituito con **jq**.

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Output

```bash
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
```

## Creare l'indirizzo IP pubblico (PIP)

Viene ora creato l'indirizzo IP pubblico (PIP) che si assegnerà al servizio di bilanciamento del carico e che consente di connettersi alle macchine virtuali da Internet tramite il comando `azure network public-ip create`. Dato che il valore predefinito è un indirizzo dinamico, si crea una voce DNS denominata nel dominio **cloudapp.azure.com** usando l'opzione `-d testsubdomain`.

```bash
azure network public-ip create -d testsubdomain TestRG TestPIP westeurope
```

Output

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "TestPIP"
+ Creating public ip address "TestPIP"
+ Looking up the public ip "TestPIP"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
data:    Name                            : TestPIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : testsubdomain
data:    FQDN                            : testsubdomain.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

Anche questa è una risorsa di primo livello che può essere visualizzata con `azure group show`.

```bash
azure group show TestRG --json | jq '.'
```

Output

```bash
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
```

Come sempre, è possibile esaminare altri dettagli relativi alla risorsa, incluso il nome di dominio completo (FQDN) del sottodominio, usando il comando `azure network public-ip show` più completo. Si noti che la risorsa indirizzo IP pubblico è stata allocata in modo logico, ma non è ancora stato assegnato un indirizzo specifico. A questo scopo sarà necessario un servizio di bilanciamento del carico, che non è ancora stato creato.

```bash
azure network public-ip show TestRG TestPIP --json | jq '.'
```

Output

```bash
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
```

## Creare il servizio di bilanciamento del carico e i pool di indirizzi IP
La creazione di un servizio di bilanciamento del carico consente di distribuire il traffico tra più macchine virtuali, ad esempio quando si eseguono applicazioni Web. Fornisce anche ridondanza all'applicazione grazie all'esecuzione di più VM che rispondono alle richieste degli utenti in caso di manutenzione o carico elevato.

Il servizio di bilanciamento del carico viene creato con:

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Output

```bash
info:    Executing command network lb create
+ Looking up the load balancer "TestLB"
+ Creating load balancer "TestLB"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB
data:    Name                            : TestLB
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```
Il servizio di bilanciamento del carico è piuttosto vuoto, per cui si creeranno alcuni pool di indirizzi IP. Si creeranno due pool di indirizzi IP per il servizio di bilanciamento del carico, uno per il front-end e uno per il back-end. Il pool di indirizzi IP front-end è quello che sarà visibile pubblicamente e in cui si assegnerà il PIP creato in precedenza. Il pool back-end è quello a cui si connetteranno le VM affinché il traffico possa passare per il servizio di bilanciamento del carico e raggiungerle.

Prima di tutto si creerà il pool di indirizzi IP front-end:

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Output

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "TestLB"
+ Looking up the public ip "TestLBPIP"
+ Updating load balancer "TestLB"
data:    Name                            : TestFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP
info:    network lb frontend-ip create command OK
```

Si noti che è stata usata l'opzione `--public-ip-name` per passare il TestLBPIP creato in precedenza. In questo modo l'indirizzo IP pubblico viene assegnato al servizio di bilanciamento del carico affinché le VM siano raggiungibili su Internet.

Quindi si creerà il secondo pool di indirizzi IP, questa volta per il traffico back-end:

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Output

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : TestBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Per controllare il servizio di bilanciamento del carico è possibile usare `azure network lb show` ed esaminare l'output JSON:

```bash
azure network lb show TestRG TestLB --json | jq '.'
```

Output

```bash
{
  "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## Creare le regole NAT per il servizio di bilanciamento del carico
Affinché il traffico attraversi effettivamente il servizio di bilanciamento del carico, è necessario creare le regole NAT che specificano le azioni in ingresso o in uscita. È possibile specificare il protocollo in uso, quindi eseguire il mapping delle porte esterne alle porte interne desiderate. Per questo ambiente si creeranno alcune regole che consentono il passaggio di SSH attraverso il servizio di bilanciamento del carico fino alle VM. Le porte TCP 4222 e 4223 vengono configurate in modo da puntare alla porta TCP 22 nelle VM, che verranno create successivamente:

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
```

Output

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "TestLB"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default frontend IP configuration "TestFrontEndPool"
+ Updating load balancer "TestLB"
data:    Name                            : VM1-SSH
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Ripetere la procedura per la seconda regola NAT per SSH:

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

Verrà anche creata una regola NAT per la porta TCP 80, associando la regola ai pool di indirizzi IP. Il fatto di non associarla alle VM singolarmente significa che è possibile aggiungere o rimuovere semplicemente le VM dal pool di indirizzi IP e fare in modo che il servizio di bilanciamento del carico regoli automaticamente il flusso del traffico:

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Output

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "TestLB"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "TestLB"
data:    Name                            : WebRule
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
info:    network lb rule create command OK
```

## Creare il probe di integrità per il servizio di bilanciamento del carico

Un probe di integrità verificherà periodicamente le VM che si trovano dietro il servizio di bilanciamento del carico per assicurarsi che siano operative e che rispondano alle richieste, come specificato. In caso contrario ne viene disabilitata l'operatività per fare in modo che gli utenti non vengano indirizzati a esse. È possibile definire controlli personalizzati per il probe di integrità, nonché gli intervalli e i valori di timeout. Per altre informazioni sui probe di integrità, leggere [Probe del servizio di bilanciamento del carico](../load-balancer/load-balancer-custom-probe-overview.md).

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

Output

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : HealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Http
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

In questo caso è stato specificato un intervallo di 15 secondi per i controlli di integrità ed è consentito un massimo di 4 probe mancati (1 minuto) prima che il servizio di bilanciamento del carico consideri l'host come non più funzionante.

## Verificare il servizio di bilanciamento del carico
La configurazione del servizio di bilanciamento del carico è terminata. È stato creato un servizio di bilanciamento del carico, è stato creato un pool di indirizzi IP front-end a cui è stato assegnato un indirizzo IP pubblico, quindi è stato creato un pool di indirizzi IP back-end a cui si connetteranno le VM. In seguito sono state create le regole NAT che consentiranno SSH alle VM per la gestione, oltre a una regola che consente la porta TCP 80 per l'app Web. Infine, per assicurarsi che gli utenti non tentino di accedere a una VM che non funziona più e non fornisce più contenuti, è stato aggiunto un probe di integrità per verificare periodicamente le VM.

Ecco il servizio di bilanciamento del carico risultante:

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Output

```bash
{
  "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM1-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM2-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "WebRule",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "frontendPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/probes/HealthProbe",
      "protocol": "Http",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 4,
      "requestPath": "healthprobe.aspx",
      "provisioningState": "Succeeded",
      "name": "HealthProbe"
    }
  ]
}
```

## Creare una scheda di rete da usare con la VM Linux

Anche le schede di interfaccia di rete sono disponibili a livello di codice, perché può essere necessario applicarvi regole d'uso e averne più di una. Si noti che nel comando `azure network nic create` seguente la scheda di rete viene associata al pool di indirizzi IP back-end del carico e alla regola NAT per consentire il traffico SSH. A questo scopo occorre specificare l'ID della sottoscrizione di Azure al posto di `<GUID>`:

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd -d
```

Output

```bash 
/subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
info:    Executing command network nic create
+ Looking up the subnet "FrontEnd"
+ Looking up the network interface "LB-NIC1"
+ Creating network interface "LB-NIC1"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1
data:    Name                            : LB-NIC1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
data:
info:    network nic create command OK
```

È possibile visualizzare i dettagli esaminando la risorsa direttamente con il comando `azure network nic show`.

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
```

Output

```bash
{
  "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1",
  "name": "LB-NIC1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

A questo punto si creerà la seconda scheda di rete, associandola al pool di indirizzi IP back-end e alla seconda regola NAT per consentire il traffico SSH:

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd -d
```

Output

```bash
 /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH
```

## Creare il gruppo di sicurezza di rete e le regole

Vengono ora creati il gruppo di sicurezza di rete e le regole in ingresso che regolano l'accesso alla scheda di rete.

```bash
azure network nsg create TestRG TestNSG westeurope
```

Si aggiungerà la regola in ingresso per il gruppo di sicurezza di rete per consentire le connessioni in ingresso sulla porta 22 (per supportare SSH):

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow TestRG TestNSG SSHRule
```

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

> [AZURE.NOTE] La regola in ingresso è un filtro per le connessioni di rete in ingresso. In questo esempio il gruppo di sicurezza di rete verrà associato alla scheda di rete virtuale della VM, di conseguenza qualsiasi richiesta per la porta 22 verrà passata alla scheda di rete nella VM. Trattandosi di una regola relativa a una connessione di rete, non a un endpoint come nelle distribuzioni classiche, per aprire una porta è necessario lasciare `--source-port-range` impostato sul valore predefinito '*' per accettare le richieste in ingresso da **tutte** le porte richiedenti, che sono in genere dinamiche.

## Associare alla scheda di rete

Associare il gruppo di sicurezza di rete alle schede di rete:

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
```

```bash
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

## Creare un set di disponibilità
I set di disponibilità agevolano la distribuzione delle VM nei cosiddetti domini di errore e domini di aggiornamento. Si creerà ora un set di disponibilità per le VM:

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

I domini di errore definiscono un gruppo di macchine virtuali che condividono una fonte di alimentazione e uno switch di rete comuni. Per impostazione predefinita, le macchine virtuali configurate nell'ambito di un set di disponibilità vengono suddivise tra un massimo di tre domini di errore. L'idea è che un problema hardware in uno di questi domini di errore non abbia effetto su tutte le VM che eseguono l'app. Azure distribuirà automaticamente le VM tra i domini di errore durante il posizionamento in un set di disponibilità.

I domini di aggiornamento indicano gruppi di macchine virtuali e l'hardware fisico sottostante che possono essere riavviati nello stesso momento. I domini di aggiornamento non vengono necessariamente riavviati in ordine sequenziale durante la manutenzione pianificata, ma ne viene riavviato uno solo alla volta. Anche in questo caso Azure distribuirà automaticamente le VM tra i domini di aggiornamento quando vengono inserite in un sito di disponibilità.

Per altre informazioni, leggere l'articolo relativo alla [gestione della disponibilità delle VM](./virtual-machines-linux-manage-availability.md)

## Creare le VM Linux

Sono state create le risorse di archiviazione e di rete per supportare VM accessibili da Internet. Ora si creeranno tali VM che verranno protette con una chiave SSH senza password. In questo caso, si vuole creare una VM Ubuntu basata sull'LTS più recente. Per trovare informazioni sull'immagini si userà `azure vm image list`, come descritto nell'articolo relativo alla [ricerca di immagini di VM di Azure](virtual-machines-linux-cli-ps-findimage.md). È stata selezionata un'immagine con il comando `azure vm image list westeurope canonical | grep LTS` e in questo caso si userà `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`, ma per l'ultimo campo si passerà `latest`, in modo da ottenere sempre la build più recente in futuro. La stringa usata sarà `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`.

> [AZURE.NOTE] Il passaggio successivo è già noto a chiunque abbia già creato una coppia di chiavi ssh-rsa pubblica e privata in Linux o Mac usando **ssh-keygen -t rsa -b 2048**. Se nella directory `~/.ssh` non sono disponibili coppie di chiavi del certificato, è possibile crearle: <br /> 1. Automaticamente con l'opzione `azure vm create --generate-ssh-keys`. 2. Manualmente usando le [istruzioni per crearle personalmente](virtual-machines-linux-ssh-from-linux.md) <br />. In alternativa, è possibile usare le opzioni `azure vm create --admin-username --admin-password` per usare il metodo meno sicuro basato su nome utente e password per l'autenticazione delle connessioni SSH dopo la creazione della macchina virtuale.

Per creare la VM si dovranno unire tutte le risorse e le informazioni con il comando `azure vm create`.

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Output

```bash
info:    Executing command vm create
+ Looking up the VM "TestVM1"
info:    Verifying the public key SSH file: /home/ifoulds/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_A1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account computeteststore
+ Looking up the availability set "TestAvailSet"
info:    Found an Availability set "TestAvailSet"
+ Looking up the NIC "LB-NIC1"
info:    Found an existing NIC "LB-NIC1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "LB-NIC1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://computeteststore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

È possibile connettersi immediatamente alla VM usando le chiavi SSH predefinite, assicurandosi di specificare la porta appropriata perché si passa per il servizio di bilanciamento del carico (per la prima VM è stata configurata la regola NAT per l'inoltro della porta 4222 alla VM):

```bash
 ssh ops@testlb.westeurope.cloudapp.azure.com -p 4222
```

Output

```bash
The authenticity of host '[testlb.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[testlb.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-58-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Wed Apr 27 23:44:06 UTC 2016

  System load: 0.37              Memory usage: 5%   Processes:       81
  Usage of /:  37.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

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

ops@TestVM1:~$
```

Proseguire e creare la seconda VM nello stesso modo:

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Ora è possibile usare il comando `azure vm show testrg testvm` per esaminare ciò che è stato creato. A questo punto le VM Ubuntu sono in esecuzione dietro un servizio di bilanciamento del carico in Azure a cui è possibile accedere solo con la coppia di chiavi SSH disponibile. Le password sono disabilitate. È possibile installare nginx o httpd e distribuire un'app Web per vedere il traffico passare attraverso il servizio di bilanciamento del carico e dirigersi a entrambe le VM.

```bash
azure vm show TestRG TestVM1
```

Output

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "LB-NIC1"
data:    Id                              :/subscriptions/8fa5cd83-7fbb-431a-af16-4a20dede8802/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/TestVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :TestVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli1cca1d20a1dcf56c-os-1461800591317
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli1cca1d20a1dcf56c-os-1461800591317.vhd
data:
data:    OS Profile:
data:      Computer Name                 :TestVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-20-F8-8B
data:          Provisioning State        :Succeeded
data:          Name                      :LB-NIC1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/availabilitySets/TESTAVAILSET
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://computeteststore.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK
```

## Passaggi successivi

Ora è possibile iniziare con più componenti di rete e VM.

<!---HONumber=AcomDC_0608_2016-->