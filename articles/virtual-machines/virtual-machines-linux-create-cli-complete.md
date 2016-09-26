
<properties
   pageTitle="Creare un ambiente Linux completo tramite l'interfaccia della riga di comando di Azure | Microsoft Azure"
   description="Usare l'interfaccia della riga di comando di Azure per creare da zero una risorsa di archiviazione, una VM di Linux, una rete virtuale con subnet, il bilanciamento del carico, una scheda di interfaccia di rete, un IP pubblico e un gruppo di sicurezza di rete."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>  

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="iainfou"/>

# Creare un ambiente Linux completo mediante l'interfaccia della riga di comando di Azure

In questo articolo viene spiegato come creare una semplice rete con un servizio di bilanciamento del carico e un paio di VM utili per lo sviluppo e per calcoli semplici. Viene presentato in dettaglio l'intero processo, comando dopo comando, fino a creare due VM Linux funzionanti e sicure a cui è possibile connettersi da qualsiasi posizione in Internet. Si potrà quindi passare a reti e ambienti più complessi.

Durante il processo si apprenderanno informazioni sulla gerarchia delle dipendenze fornita dal modello di distribuzione di Resource Manager e sulle potenzialità che offre. Dopo aver osservato come viene realizzato il sistema, è possibile ricrearlo molto più rapidamente utilizzando i [modelli di Azure Resource Manager](../resource-group-authoring-templates.md). In più, dopo aver scoperto come interagiscono le parti dell'ambiente, diventa più facile creare modelli per automatizzarle.

Nell'ambiente sono presenti:

- Due VM all'interno di un set di disponibilità.
- Un servizio di bilanciamento del carico con una regola di bilanciamento del carico sulla porta 80.
- Regole del gruppo di sicurezza di rete (NSG) per proteggere la VM dal traffico indesiderato.

![Panoramica sull'ambiente di base](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

Per creare questo ambiente personalizzato, è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure](../xplat-cli-install.md) in modalità Resource Manager (`azure config mode arm`). È inoltre necessario uno strumento di analisi JSON. In questo esempio viene utilizzato [jq](https://stedolan.github.io/jq/).

## Comandi rapidi
È possibile usare i seguenti comandi rapidi per creare un ambiente personalizzato. Per ulteriori informazioni sulla funzione di ogni comando durante la creazione dell'ambiente, leggere i [passaggi della procedura dettagliata qui di seguito](#detailed-walkthrough).

Creare il gruppo di risorse:

```bash
azure group create TestRG -l westeurope
```

Verificare il gruppo di risorse utilizzando il parser JSON:

```bash
azure group show TestRG --json | jq '.'
```

Creare l'account di archiviazione:

```bash
azure storage account create -g TestRG -l westeurope --kind Storage --sku-name GRS computeteststore
```

Verificare l'account di archiviazione utilizzando il parser JSON:

```bash
azure storage account show -g TestRG computeteststore --json | jq '.'
```

Creare la rete virtuale:

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Creare la subnet:

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Verificare la rete virtuale e la subnet utilizzando il parser JSON:


```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Creare un IP pubblico:

```bash
azure network public-ip create -g TestRG -n TestLBPIP -l westeurope -d testlb -a static -i 4
```

Creare il servizio di bilanciamento del carico.

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Creare un pool di indirizzi IP front-end per il servizio di bilanciamento del carico e associare l'IP pubblico:

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Creare il pool di indirizzi IP back-end per il servizio di bilanciamento del carico:

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Creare le regole NAT in ingresso SSH per il servizio di bilanciamento del carico:

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

Creare le regole NAT in ingresso Web per il servizio di bilanciamento del carico:

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Creare il probe di integrità per il servizio di bilanciamento del carico:

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "tcp" -i 15 -c 4
```

Verificare il servizio di bilanciamento del carico, il pool di indirizzi IP e le regole NAT utilizzando il parser JSON:

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Creare la prima scheda di interfaccia di rete (NIC):

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
```

Creare la seconda scheda di rete:

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
```

Verificare le schede di interfaccia di rete utilizzando il parser JSON:

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
azure network nic show TestRG LB-NIC2 --json | jq '.'
```

Creare il gruppo di sicurezza di rete:

```bash
azure network nsg create -g TestRG -n TestNSG -l westeurope
```

Aggiungere le regole in ingresso per il gruppo di sicurezza di rete:

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow -g TestRG -a TestNSG -n SSHRule
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

Verificare il gruppo di sicurezza di rete e le regole in ingresso utilizzando il parser JSON:

```bash
azure network nsg show -g TestRG -n TestNSG --json | jq '.'
```

Associare il gruppo di sicurezza di rete alle schede di rete:

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

Creare il set di disponibilità:

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Creare la prima VM Linux:

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
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Creare la seconda VM Linux:

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
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Utilizzare il parser JSON per verificare tutto ciò che è stato creato:

```bash
azure vm show -g TestRG -n TestVM1 --json | jq '.'
azure vm show -g TestRG -n TestVM2 --json | jq '.'
```

Esportare l'ambiente creato su un modello per ricreare rapidamente nuove istanze:

```bash
azure resource export TestRG
```

## Procedura dettagliata
La procedura dettagliata seguente illustra il funzionamento di ciascun comando durante la creazione dell'ambiente. Questi concetti torneranno utili in fase di creazione degli ambienti personalizzati per lo sviluppo o per la produzione.

## Creare i gruppi di risorse e scegliere i percorsi di distribuzione

I gruppi di risorse di Azure sono entità di distribuzione logiche che contengono informazioni di configurazione e altri metadati per abilitare la gestione logica delle distribuzioni di risorse.

```bash
azure group create TestRG westeurope
```

Output:

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

Sono necessari account di archiviazione per i dischi della macchina virtuale e per eventuali altri dischi dati che si desidera aggiungere. Si creano account di archiviazione quasi subito dopo la creazione di gruppi di risorse.

Viene usato il comando `azure storage account create`, passando il percorso dell'account, il gruppo di risorse che lo controlla e il tipo di supporto di archiviazione desiderato.

```bash
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--kind Storage --sku-name GRS \
computeteststore
```

Output:

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

Per esaminare il gruppo di risorse con il comando `azure group show`, usare lo strumento [jq](https://stedolan.github.io/jq/) insieme all'opzione `--json` dell'interfaccia della riga di comando di Azure. È possibile utilizzare **jsawk** o qualsiasi altra libreria di linguaggio per l'analisi del JSON.

```bash
azure group show TestRG --json | jq                                                                                      
```


Output:

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

Per esaminare l'account di archiviazione usando l'interfaccia della riga di comando, è necessario innanzitutto impostare le chiavi e i nomi dell'account usando una variante del comando seguente. Sostituire il nome dell'account di archiviazione nell'esempio seguente con un nome a scelta:

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"
```

Dopodiché è possibile visualizzare facilmente le informazioni di archiviazione:

```bash
azure storage container list
```

Output:

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## Creare una rete virtuale e una subnet

Dopodiché è necessario creare una rete virtuale che funziona in Azure e una subnet in cui poter installare le VM.

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Output:

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

Anche in questo caso osservare come vengono realizzate le risorse mediante l'opzione --json di `azure group show` e **jq**. Ora sono disponibili una risorsa `storageAccounts` e una risorsa `virtualNetworks`.

```bash
azure group show TestRG --json | jq '.'
```

Output:

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

Viene quindi creata una subnet all'interno della rete virtuale `TestVnet` in cui vengono distribuite le VM. Usare il comando `azure network vnet subnet create` insieme alle risorse create in precedenza: il gruppo di risorse `TestRG` e la rete virtuale `TestVNet`. Aggiungere il nome della subnet `FrontEnd` e il prefisso dell'indirizzo subnet `192.168.1.0/24`, come descritto di seguito:

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Output:

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

Dato che la subnet è logicamente all'interno della rete virtuale, le informazioni relative alla subnet vengono cercate con un comando leggermente diverso. Il comando usato è `azure network vnet show`, ma si continua a esaminare l'output JSON mediante lo strumento **jq**.

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Output:

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

## Creare un indirizzo IP pubblico (PIP)

A questo punto, creare l'indirizzo IP pubblico (PIP) che viene assegnato al bilanciamento del carico. Consente di connettersi alle VM da Internet tramite il comando `azure network public-ip create`. Dato che l'indirizzo predefinito è dinamico, si crea una voce DNS denominata nel dominio **cloudapp.azure.com** usando l'opzione `-d testsubdomain`.

```bash
azure network public-ip create -d testsubdomain TestRG TestPIP westeurope
```

Output:

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

Anche l'indirizzo IP pubblico è una risorsa di primo livello che può essere visualizzata con `azure group show`.

```bash
azure group show TestRG --json | jq '.'
```

Output:

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

È possibile esaminare altri dettagli relativi alla risorsa, incluso il nome di dominio completo (FQDN) del sottodominio, usando il comando `azure network public-ip show` completo. La risorsa indirizzo IP pubblico è stata allocata in modo logico, ma non è ancora stato assegnato un indirizzo specifico. Per ottenere un indirizzo IP, sarà necessario un servizio di bilanciamento del carico, che non è ancora stato creato.

```bash
azure network public-ip show TestRG TestPIP --json | jq '.'
```

Output:

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

## Creare un servizio di bilanciamento del carico e i pool di indirizzi IP
Quando si crea un bilanciamento del carico, questo consente di distribuire il traffico tra più VM. Fornisce anche ridondanza all'applicazione grazie all'esecuzione di più VM che rispondono alle richieste degli utenti in caso di manutenzione o carichi elevati.

Il servizio di bilanciamento del carico viene creato con:

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Output:

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
Il servizio di bilanciamento del carico è piuttosto vuoto, per cui si creeranno alcuni pool di indirizzi IP. Si creeranno due pool di indirizzi IP per il servizio di bilanciamento del carico, uno per il front-end e uno per il back-end. Il pool di IP front-end è visibile pubblicamente. Rappresenta anche il percorso a cui si assegnano i PIP creati in precedenza. Dopodiché, si usa il pool di back-end come un percorso per le VM a cui connettersi. In questo modo, il traffico può fluire attraverso il bilanciamento del carico verso le VM.

Prima di tutto si creerà il pool di indirizzi IP front-end:

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Output:

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

Si noti che è stata usata l'opzione `--public-ip-name` per passare il TestLBPIP creato in precedenza. L'assegnazione dell'indirizzo IP pubblico al servizio di bilanciamento del carico consente di raggiungere le VM su Internet.

Quindi si creerà il secondo pool di indirizzi IP, questa volta per il traffico back-end:

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Output:

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : TestBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Per vedere il servizio di bilanciamento del carico è possibile usare `azure network lb show` ed esaminare l'output JSON:

```bash
azure network lb show TestRG TestLB --json | jq '.'
```

Output:

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
Affinché il traffico attraversi il servizio di bilanciamento del carico, è necessario creare le regole NAT che specificano le azioni in ingresso o in uscita. È possibile specificare il protocollo da usare, quindi eseguire il mapping delle porte esterne alle porte interne desiderate. Per questo ambiente si creeranno alcune regole che consentono il passaggio di SSH attraverso il servizio di bilanciamento del carico fino alle VM. Le porte TCP 4222 e 4223 vengono configurate in modo da puntare alla porta TCP 22 nelle VM, che vengono create successivamente:

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
```

Output:

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

Verrà anche creata una regola NAT per la porta TCP 80, associando la regola ai pool di indirizzi IP. Se la regola viene collegata al pool IP, anziché essere collegata singolarmente alle VM, è possibile aggiungere o rimuovere semplicemente le VM dal pool IP. A quel punto, il bilanciamento del carico regola automaticamente il flusso del traffico:

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Output:

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

## Creare un probe di integrità per il servizio di bilanciamento del carico

Un probe di integrità verifica periodicamente le VM che si trovano dietro il servizio di bilanciamento del carico per assicurarsi che siano operative e che rispondano alle richieste, come specificato. In caso contrario ne viene disabilitata l'operatività per fare in modo che gli utenti non vengano indirizzati a esse. È possibile definire controlli personalizzati per il probe di integrità, nonché gli intervalli e i valori di timeout. Per altre informazioni sui probe di integrità, vedere [Probe di integrità del servizio di bilanciamento del carico](../load-balancer/load-balancer-custom-probe-overview.md).

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "tcp" -i 15 -c 4
```

Output:

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : HealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

In questo caso è stato specificato un intervallo di 15 secondi per i controlli di integrità. È consentito un massimo di quattro probe mancati (un minuto) prima che il bilanciamento del carico consideri l'host come non più funzionante.

## Verificare il bilanciamento del carico
La configurazione del bilanciamento del carico è completa. Ecco i passaggi effettuati:

1. Per prima cosa si è creato un bilanciamento del carico.
2. Poi si è creato un pool IP front-end a cui è stato assegnato un indirizzo IP pubblico.
3. Successivamente è stato creato un pool IP back-end a cui possono connettersi le VM.
4. Dopodiché, sono state create le regole NAT che consentono SSH alle VM per la gestione, oltre a una regola che consente la porta TCP 80 per l'app Web.
5. Infine è stato aggiunto un probe di integrità per verificare periodicamente lo stato delle VM. Questo probe di integrità garantisce agli utenti di accedere solo alle VM non più funzionanti o con contenuti non più disponibili.

Ecco il bilanciamento del carico risultante:

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Output:

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
      "name": "HealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/probes/HealthProbe"
    }
  ]
}
```

## Creare una scheda di interfaccia di rete da usare con la VM di Linux

 Le schede di interfaccia di rete sono disponibili a livello di programmazione in quanto è possibile applicare delle regole di utilizzo. È inoltre possibile averne più di una. Nel comando `azure network nic create` seguente la scheda di interfaccia di rete viene associata al pool di indirizzi IP back-end del carico e alla regola NAT per consentire il traffico SSH. A questo scopo occorre specificare l'ID della sottoscrizione di Azure al posto di `<GUID>`:

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
     -d /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
     -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
```

Output:

```bash
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

È possibile visualizzare i dettagli esaminando la risorsa direttamente. La risorsa viene esaminata con il comando `azure network nic show`:

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
```

Output:

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

Ora è possibile creare la seconda scheda di interfaccia di rete, collegandola di nuovo al pool IP back-end. Questa volta la seconda regola NAT consente il traffico SSH:

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d  /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
    -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH
```

## Creare un gruppo di sicurezza di rete e le regole corrispondenti

Vengono ora creati il gruppo di sicurezza di rete e le regole in ingresso che regolano l'accesso alla scheda di interfaccia di rete.

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

> [AZURE.NOTE] La regola in ingresso è un filtro per le connessioni di rete in ingresso. In questo esempio il gruppo di sicurezza di rete viene associato alla scheda di rete virtuale delle VM, di conseguenza qualsiasi richiesta per la porta 22 viene passata alla scheda di rete nella VM. Questa regola è relativa alla connessione di rete anziché all'endpoint, come invece sarebbe nelle distribuzioni classiche. Per aprire una porta, è necessario lasciare `--source-port-range` impostato su '*' (valore predefinito) per accettare le richieste in ingresso da **qualsiasi** porta richiedente. Le porte sono solitamente dinamiche.

## Associare alla scheda di rete

Associare il gruppo di sicurezza di rete alle schede di rete:

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
```

```bash
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

## Creare un set di disponibilità
I set di disponibilità agevolano la distribuzione delle VM nei domini di errore e domini di aggiornamento. Si creerà ora un set di disponibilità per le VM:

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

I domini di errore definiscono un gruppo di macchine virtuali che condividono una fonte di alimentazione e uno switch di rete comuni. Per impostazione predefinita, le macchine virtuali configurate nell'ambito di un set di disponibilità vengono suddivise tra un massimo di tre domini di errore. L'idea è che un problema hardware in uno di questi domini di errore non abbia effetto su tutte le VM che eseguono l'app. Azure distribuisce automaticamente le VM tra i domini di errore durante il posizionamento in un set di disponibilità.

I domini di aggiornamento indicano gruppi di macchine virtuali e l'hardware fisico sottostante che possono essere riavviati nello stesso momento. I domini di aggiornamento non vengono necessariamente riavviati in ordine sequenziale durante la manutenzione pianificata, ma ne viene riavviato uno solo alla volta. D nuovo, Azure distribuisce automaticamente le VM tra i domini di aggiornamento durante il posizionamento in un sito di disponibilità.

Per altre informazioni, leggere l'articolo relativo alla [gestione della disponibilità delle VM](./virtual-machines-linux-manage-availability.md).

## Creare le VM di Linux

Sono state create le risorse di archiviazione e di rete per supportare le VM accessibili tramite Internet. Ora è possibile creare quelle VM e proteggerle con una chiave SSH priva di password. In questo caso, si vuole creare una VM Ubuntu basata sull'LTS più recente. Per trovare le informazioni sull'immagine si usa `azure vm image list`, come descritto nell'articolo relativo alla [ricerca di immagini di VM di Azure](virtual-machines-linux-cli-ps-findimage.md).

È stata selezionata un'immagine utilizzando il comando `azure vm image list westeurope canonical | grep LTS`. In questo caso, si usa `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Per l'ultimo campo si passa a `latest` così che in futuro si ottenga sempre la build più recente. La stringa usata è `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`.

Il passaggio successivo è già noto a chiunque abbia già creato una coppia di chiavi ssh-rsa pubblica e privata in Linux o Mac usando **ssh-keygen -t rsa -b 2048**. Se non si dispone di alcuna coppia di chiavi del certificato nella directory `~/.ssh`, è possibile crearle:

- Automaticamente, utilizzando l'opzione `azure vm create --generate-ssh-keys`.
- Manualmente, usando [le istruzioni per crearle autonomamente](virtual-machines-linux-mac-create-ssh-keys.md).

In alternativa, è possibile utilizzare il metodo con password amministratore per autenticare le connessioni SSH dopo aver creato la VM. Di solito, questo metodo risulta essere meno sicuro.

Per creare la VM si dovranno unire tutte le risorse e le informazioni con il comando `azure vm create`:

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
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Output:

```bash
info:    Executing command vm create
+ Looking up the VM "TestVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
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

È possibile connettersi immediatamente alla VM usando le chiavi SSH predefinite. Assicurarsi di specificare la porta appropriata poiché si passa tramite il bilanciamento del carico. Per la prima VM, impostare la regola NAT per l'inoltro della porta 4222 alla VM:

```bash
 ssh ops@testlb.westeurope.cloudapp.azure.com -p 4222
```

Output:

```bash
The authenticity of host '[testlb.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[testlb.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

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
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Ora è possibile usare il comando `azure vm show testrg testvm` per esaminare ciò che è stato creato. A questo punto le VM Ubuntu sono in esecuzione dietro al bilanciamento del carico in Azure a cui è possibile accedere solo con la coppia di chiavi SSH, dal momento che le password sono disabilitate. È possibile installare nginx o httpd, distribuire un'app Web e vedere il traffico passare attraverso il servizio di bilanciamento del carico e dirigersi a entrambe le VM.

```bash
azure vm show TestRG TestVM1
```

Output:

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "LB-NIC1"
data:    Id                              :/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/TestVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :TestVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
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
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LB-NIC1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/TESTAVAILSET
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://computeteststore.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## Esportare l'ambiente come modello
Dopo aver creato questo ambiente, è possibile creare un ambiente di sviluppo aggiuntivo utilizzando gli stessi parametri oppure creare un ambiente di produzione compatibile? Azure Resource Manager utilizza i modelli JSON che definiscono tutti i parametri per l'ambiente. È possibile creare interi ambienti facendo riferimento a questo modello JSON. È possibile [creare modelli JSON manualmente](../resource-group-authoring-templates.md) o semplicemente esportare un ambiente esistente per creare il modello JSON desiderato.

```bash
azure group export TestRG
```

Questo comando crea il file `TestRG.json` nella directory di lavoro corrente. Quando si crea un ambiente da questo modello, vengono richiesti i nomi di tutte le risorse, tra cui quelli del servizio di bilanciamento del carico, delle interfacce di rete o delle VM. È possibile popolare questi nomi nel file del modello aggiungendo i parametri `-p` o `--includeParameterDefaultValue` al comando `azure group export` descritto in precedenza. Modificare il modello JSON per specificare i nomi delle risorse, o [creare un file parameters.json](../resource-group-authoring-templates.md#parameters) nel quale vengono specificati i nomi delle risorse.

Per creare un ambiente in base al modello:

```bash
azure group deployment create -f TestRG.json -g NewRGFromTemplate
```

Se lo si desidera, è possibile consultare [ulteriori informazioni su come eseguire la distribuzione partendo dai modelli](../resource-group-template-deploy-cli.md). Ulteriori informazioni su come aggiornare gli ambienti in modo incrementale, utilizzare il file di parametri e accedere ai modelli da un unico percorso di archiviazione.

## Passaggi successivi

Ora è possibile iniziare a utilizzare più componenti di rete e VM. È possibile utilizzare questo ambiente di esempio per compilare l'applicazione utilizzando i componenti principali presentati qui.

<!---HONumber=AcomDC_0914_2016-->