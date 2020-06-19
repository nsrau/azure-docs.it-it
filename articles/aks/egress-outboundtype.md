---
title: Personalizzare le route definite dall'utente nel servizio Azure Kubernetes
description: Informazioni su come definire una route in uscita personalizzata nel servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: babfd70a6a9732113531be13073af212a6820557
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83677897"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>Personalizzare l'uscita del cluster con una route definita dall'utente (anteprima)

È possibile personalizzare l'uscita da un cluster del servizio Azure Kubernetes per adattarla a scenari specifici. Per impostazione predefinita, il servizio Azure Kubernetes effettuerà il provisioning di un Load Balancer dello SKU Standard che verrà configurato e usato per l'uscita. La configurazione predefinita potrebbe, però, non soddisfare i requisiti di tutti gli scenari se gli indirizzi IP pubblici non sono consentiti o se per l'uscita sono necessari hop aggiuntivi.

Questo articolo illustra in dettaglio come personalizzare la route in uscita di un cluster in modo da supportare scenari di rete personalizzati, ad esempio quelli che non consentono indirizzi IP pubblici e in cui il cluster deve trovarsi dietro un'appliance virtuale di rete.

> [!IMPORTANT]
> Le funzionalità di anteprima del servizio Azure Kubernetes sono self-service ed è possibile scegliere se usufruirne o meno. Le anteprime vengono fornite *così come sono* e *come disponibili* e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime del servizio Azure Kubernetes sono parzialmente coperte dal supporto clienti secondo il principio del *massimo sforzo*. Di conseguenza, le funzionalità non sono destinate all'uso in produzione. Per altre informazioni, vedere gli articoli di supporto seguenti:
>
> * [Criteri di supporto del servizio Azure Kubernetes](support-policies.md)
> * [Domande frequenti relative al supporto tecnico Azure](faq.md)

## <a name="prerequisites"></a>Prerequisiti
* Interfaccia della riga di comando di Azure 2.0.81 o versione successiva
* Estensione del servizio Azure Kubernetes per l'interfaccia della riga di comando di Azure (anteprima) 0.4.28 o versione successiva
* Versione API `2020-01-01` o successiva

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Installare la versione più recente dell'estensione del servizio Azure Kubernetes per l'interfaccia della riga di comando di Azure (anteprima)
Per impostare il tipo in uscita di un cluster, è necessaria l'estensione del servizio Azure Kubernetes per l'interfaccia della riga di comando di Azure (anteprima) 0.4.18 o versione successiva. Installare l'estensione del servizio Azure Kubernetes per l'interfaccia della riga di comando di Azure (anteprima) eseguendo il comando az extension add e quindi verificare la disponibilità di eventuali aggiornamenti eseguendo il comando az extension update:

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Limitazioni
* Nella versione di anteprima è possibile definire `outboundType` solo durante la creazione del cluster e non è possibile aggiornarlo in seguito.
* Nella versione di anteprima i cluster del servizio Azure Kubernetes `outboundType` devono usare Azure CNI. Kubenet è configurabile. Per usarlo è necessario definire associazioni manuali della tabella di route alla subnet del servizio Azure Kubernetes.
* Per impostare `outboundType`, sono necessari cluster del servizio Azure Kubernetes con `vm-set-type` impostato su `VirtualMachineScaleSets` e `load-balancer-sku` impostato su `Standard`.
* Per impostare `outboundType` sul valore `UDR`, è necessaria una route definita dall'utente con connettività in uscita valida per il cluster.
* L'impostazione di `outboundType` sul valore `UDR` implica che l'indirizzo IP di origine in ingresso indirizzato al servizio di bilanciamento del carico potrebbe **non corrispondere** all'indirizzo di destinazione in uscita del cluster.

## <a name="overview-of-outbound-types-in-aks"></a>Panoramica dei tipi in uscita nel servizio Azure Kubernetes

Un cluster del servizio Azure Kubernetes può essere personalizzato con un `outboundType` univoco di tipo bilanciamento del carico o un routing definito dall'utente.

> [!IMPORTANT]
> Il tipo in uscita influisce solo sul traffico in uscita del cluster. Per altre informazioni, vedere [Configurazione dei controller in ingresso](ingress-basic.md).

### <a name="outbound-type-of-loadbalancer"></a>Tipo in uscita loadBalancer

Se `loadBalancer` è impostato, il servizio Azure Kubernetes completa automaticamente la configurazione seguente. Il di bilanciamento del carico viene usato per l'uscita tramite un indirizzo IP pubblico assegnato dal servizio Azure Kubernetes. Il tipo in uscita `loadBalancer` supporta i servizi Kubernetes di tipo `loadBalancer`, che prevedono l'uscita dal bilanciamento del carico creato dal provider di risorse del servizio Azure Kubernetes.

La configurazione seguente viene completata dal servizio Azure Kubernetes.
   * Viene effettuato il provisioning di un indirizzo IP pubblico per l'uscita del cluster.
   * L'indirizzo IP pubblico viene assegnato alla risorsa del bilanciamento del carico.
   * Per i nodi agente nel cluster vengono impostati pool back-end per il bilanciamento del carico.

Di seguito è illustrata una topologia di rete distribuita nei cluster del servizio Azure Kubernetes per impostazione predefinita, in cui `outboundType` è impostato su `loadBalancer`.

![outboundtype-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Tipo in uscita userDefinedRouting

> [!NOTE]
> L'uso di un tipo in uscita rappresenta uno scenario di rete avanzato e richiede una configurazione di rete appropriata.

Se `userDefinedRouting` è impostato, il servizio Azure Kubernetes non configurerà automaticamente i percorsi in uscita. È previsto che le operazioni seguenti vengano eseguite dall'**utente**.

Il cluster del servizio Azure Kubernetes deve essere distribuito in una rete virtuale esistente con una subnet configurata. Quando si usa l'architettura Load Balancer Standard, è necessario indicare l'uscita in modo esplicito. A questo scopo, è necessario inviare richieste in uscita a un'appliance, ad esempio un firewall, un gateway, un ambiente locale oppure consentire che l'uscita sia indicata da un indirizzo IP pubblico assegnato a Load Balancer Standard o a un nodo specifico.

Il provider di risorse del servizio Azure Kubernetes distribuirà un'istanza di Load Balancer Standard. La configurazione del bilanciamento del carico non prevede regole e [non sono previsti addebiti finché non viene aggiunta una regola](https://azure.microsoft.com/pricing/details/load-balancer/). Il servizio Azure Kubernetes **non** effettua automaticamente il provisioning di un indirizzo IP pubblico per il front-end di Load Balancer Standard. Il servizio Azure Kubernetes **non** configura automaticamente il pool back-end del bilanciamento del carico.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Distribuire un cluster con tipo di uscita con route definita dall'utente e Firewall di Azure

Per illustrare l'applicazione di un cluster in cui il tipo in uscita usa una route definita dall'utente, è possibile configurare un cluster in una rete virtuale con peering con un'istanza di Firewall di Azure.

![Topologia bloccata](media/egress-outboundtype/outboundtype-udr.png)

* Il traffico in ingresso deve necessariamente passare attraverso i filtri del firewall
   * Una subnet isolata include un bilanciamento del carico interno per il routing nei nodi agente
   * I nodi agente sono isolati in una subnet dedicata
* Le richieste in uscita partono dai nodi agente e raggiungono l'indirizzo IP interno di Firewall di Azure usando una route definita dall'utente
   * Le richieste provenienti dai nodi agente del servizio Azure Kubernetes seguono una route definita dall'utente che è stata inserita nella subnet in cui è stato distribuito il cluster del servizio Azure Kubernetes.
   * Uscite di Firewall di Azure dalla rete virtuale da un front-end IP pubblico
   * L'accesso al piano di controllo del servizio Azure Kubernetes è protetto da un gruppo di sicurezza di rete, che ha abilitato l'indirizzo IP front-end del firewall
   * L'accesso alla rete Internet pubblica o ad altri servizi di Azure viene trasmesso da e verso l'indirizzo IP front-end del firewall

### <a name="set-configuration-via-environment-variables"></a>Impostare la configurazione tramite variabili di ambiente

Definire un set di variabili di ambiente da usare durante la creazione di risorse.

```bash
PREFIX="contosofin"
RG="${PREFIX}-rg"
LOC="eastus"
NAME="${PREFIX}outboundudr"
AKS_NAME="${PREFIX}aks"
VNET_NAME="${PREFIX}vnet"
AKSSUBNET_NAME="${PREFIX}akssubnet"
SVCSUBNET_NAME="${PREFIX}svcsubnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}fw"
FWPUBLICIP_NAME="${PREFIX}fwpublicip"
FWIPCONFIG_NAME="${PREFIX}fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}fwrt"
FWROUTE_NAME="${PREFIX}fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}fwinternet"
DEVSUBNET_NAME="${PREFIX}dev"
```

Impostare, quindi, gli ID sottoscrizione.

```azure-cli

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>Creare una rete virtuale con più subnet

Effettuare il provisioning di una rete virtuale con tre subnet separate, una per il cluster, una per il firewall e una per l'ingresso del servizio.

![Topologia di rete vuota](media/egress-outboundtype/empty-network.png)

Creare un gruppo di risorse che conterrà tutte le risorse.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Creare due reti virtuali per ospitare il cluster del servizio Azure Kubernetes e l'istanza di Firewall di Azure. Ognuna avrà una propria subnet. Si inizierà con la rete del servizio Azure Kubernetes.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --address-prefixes 100.64.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 100.64.1.0/24

# Dedicated subnet for K8s services

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $SVCSUBNET_NAME \
    --address-prefix 100.64.2.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 100.64.3.0/24
```

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>Creare e configurare un'istanza di Firewall di Azure con una route definita dall'utente

È necessario configurare le regole in ingresso e in uscita di Firewall di Azure. Il firewall viene usato principalmente per consentire alle organizzazioni di configurare regole granulari di traffico in ingresso e in uscita all'interno e all'esterno del cluster del servizio Azure Kubernetes.

![Firewall e route definita dall'utente](media/egress-outboundtype/firewall-udr.png)

Creare una risorsa IP pubblico con SKU standard che verrà usata come indirizzo front-end di Firewall di Azure.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registrare l'estensione dell'interfaccia della riga di comando di anteprima per creare un'istanza di Firewall di Azure.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

È ora possibile assegnare l'indirizzo IP creato in precedenza al front-end del firewall.
> [!NOTE]
> La configurazione dell'indirizzo IP pubblico per Firewall di Azure può richiedere alcuni minuti.
> 
> Se il comando seguente restituisce ripetutamente errori, eliminare il firewall e l'IP pubblico esistenti ed effettuare contemporaneamente il provisioning dell'IP pubblico e di Firewall di Azure tramite il portale.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Dopo aver eseguito il comando precedente, salvare l'indirizzo IP del front-end del firewall per eseguire la configurazione in un secondo momento.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Creare una route definita dall'utente con un hop a Firewall di Azure

Azure effettua il routing automatico del traffico tra subnet di Azure, reti virtuali e reti locali. Per modificare il routing predefinito di Azure è necessario creare una tabella di route.

Creare una tabella di route vuota da associare a una determinata subnet. Firewall di Azure creato in precedenza verrà impostato come hop successivo nella tabella di route. A ogni subnet può essere associata una o nessuna tabella di route.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Vedere la [documentazione relativa alle tabelle di route della rete virtuale](../virtual-network/virtual-networks-udr-overview.md#user-defined) per informazioni su come eseguire l'override delle route di sistema predefinite di Azure o su come aggiungere altre route alla tabella di route di una subnet.

## <a name="adding-network-firewall-rules"></a>Aggiunta di regole del firewall della rete

> [!WARNING]
> Di seguito è illustrato un esempio di aggiunta di una regola del firewall. Per consentire il corretto funzionamento dei cluster del servizio Azure Kubernetes, tutti gli endpoint in uscita definiti negli [endpoint in uscita richiesti](egress.md) devono essere abilitati da regole del firewall dell'applicazione. Se questi endpoint non sono abilitati, il cluster non può funzionare.

Di seguito è illustrato un esempio di una regola di rete e di una regola dell'applicazione. Viene aggiunta una regola di rete che consente qualsiasi protocollo, indirizzo di origine, indirizzo di destinazione e porte di destinazione. Viene aggiunta anche una regola dell'applicazione per **alcuni** degli endpoint richiesti dal servizio Azure Kubernetes.

In uno scenario di produzione è necessario abilitare l'accesso solo agli endpoint richiesti per l'applicazione e a quelli definiti [in uscita richiesti dal servizio Azure Kubernetes](egress.md).

```
# Add Network FW Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'netrules' --protocols 'Any' --source-addresses '*' --destination-addresses '*' --destination-ports '*' --action allow --priority 100

# Add Application FW Rules
# IMPORTANT: Add AKS required egress endpoints

az network firewall application-rule create -g $RG -f $FWNAME \
    --collection-name 'AKS_Global_Required' \
    --action allow \
    --priority 100 \
    -n 'required' \
    --source-addresses '*' \
    --protocols 'http=80' 'https=443' \
    --target-fqdns \
        'aksrepos.azurecr.io' \
        '*blob.core.windows.net' \
        'mcr.microsoft.com' \
        '*cdn.mscr.io' \
        '*.data.mcr.microsoft.com' \
        'management.azure.com' \
        'login.microsoftonline.com' \
        'ntp.ubuntu.com' \
        'packages.microsoft.com' \
        'acs-mirror.azureedge.net'
```

Per altre informazioni sul servizio Firewall di Azure, vedere la [documentazione di Firewall di Azure](https://docs.microsoft.com/azure/firewall/overview).

## <a name="associate-the-route-table-to-aks"></a>Associare la tabella di route al servizio Azure Kubernetes

Per associare il cluster al firewall, la subnet dedicata per la subnet del cluster deve fare riferimento alla tabella di route creata in precedenza. È possibile eseguire l'associazione inviando un comando alla rete virtuale che contiene sia il cluster che il firewall per aggiornare la tabella di route della subnet del cluster.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Distribuire il servizio Azure Kubernetes con il tipo in uscita con route definita dall'utente nella rete esistente

È ora possibile distribuire un cluster del servizio Azure Kubernetes nella configurazione di rete virtuale esistente. Per impostare un tipo in uscita del cluster sul routing definito dall'utente, è necessario fornire una subnet esistente al servizio Azure Kubernetes.

![aks-deploy](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Creare un'entità servizio con accesso per effettuare il provisioning all'interno della rete virtuale esistente

Il servizio Azure Kubernetes usa un'entità servizio per creare le risorse cluster. L'entità servizio passata in fase di creazione viene usata per creare risorse del servizio Azure Kubernetes sottostanti, ad esempio macchine virtuali, archiviazione e servizi di bilanciamento del carico usati dal servizio Azure Kubernetes. Se le autorizzazioni concesse sono insufficienti, non sarà possibile effettuare il provisioning di un cluster del servizio Azure Kubernetes.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

A questo punto, sostituire `APPID` e `PASSWORD` nell'esempio seguente con i valori di ID app e password dell'entità servizio generati automaticamente dall'output del comando precedente. Verrà fatto riferimento all'ID risorsa della rete virtuale per concedere le autorizzazioni all'entità servizio in modo che il servizio Azure Kubernetes possa distribuirvi le risorse.

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>Distribuire il servizio Azure Kubernetes

È infine possibile distribuire il cluster del servizio Azure Kubernetes nella subnet esistente dedicata al cluster. Per definire la subnet di destinazione in cui eseguire la distribuzione, si usa la variabile di ambiente `$SUBNETID`. La variabile `$SUBNETID` non è stata definita nei passaggi precedenti. Per impostare il valore per l'ID subnet, è possibile usare il comando seguente:

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

Verrà definito il tipo in uscita in modo che sia conforme alla route definita dall'utente presente nella subnet, in modo da consentire al servizio Azure Kubernetes di evitare la configurazione e il provisioning IP per il bilanciamento del carico che ora può essere rigorosamente interno.

È possibile aggiungere la funzionalità del servizio Azure Kubernetes per [intervalli IP autorizzati del server API](api-server-authorized-ip-ranges.md) per limitare l'accesso al server API solo all'endpoint pubblico del firewall. La funzionalità degli intervalli IP autorizzati corrisponde nel diagramma al gruppo di sicurezza di rete che deve essere passato per accedere al piano di controllo. Quando si abilita la funzionalità degli intervalli IP autorizzati per limitare l'accesso al server API, gli strumenti di sviluppo devono usare un JumpBox dalla rete virtuale del firewall oppure è necessario aggiungere tutti gli endpoint sviluppatore all'intervallo di indirizzi IP autorizzati.

> [!TIP]
> È possibile aggiungere funzionalità aggiuntive alla distribuzione del cluster, ad esempio (Cluster privato) []. Quando si usano intervalli IP autorizzati, per accedere al server API è necessario un JumpBox all'interno della rete del cluster.

```azure-cli
az aks create -g $RG -n $AKS_NAME -l $LOC \
  --node-count 3 \
  --network-plugin azure --generate-ssh-keys \
  --service-cidr 192.168.0.0/16 \
  --dns-service-ip 192.168.0.10 \
  --docker-bridge-address 172.22.0.1/29 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --load-balancer-sku standard \
  --outbound-type userDefinedRouting \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
  ```

### <a name="enable-developer-access-to-the-api-server"></a>Consentire agli sviluppatori di accedere al server API

In seguito alla configurazione degli intervalli IP autorizzati per il cluster, per accedere al server API è necessario aggiungere gli indirizzi IP degli strumenti per sviluppatori all'elenco di intervalli IP approvati del cluster del servizio Azure Kubernetes. Un'altra opzione consiste nel configurare un JumpBox con gli strumenti necessari all'interno di una subnet separata nella rete virtuale di Firewall.

Aggiungere un altro indirizzo IP agli intervalli approvati con il comando seguente

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Usare il comando [az aks get-credentials][az-aks-get-credentials] per configurare `kubectl` per la connessione al cluster Kubernetes appena creato. 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="setup-the-internal-load-balancer"></a>Configurare il bilanciamento del carico interno

Il servizio Azure Kubernetes ha distribuito con il cluster un bilanciamento del carico che può essere configurato come [bilanciamento del carico interno](internal-lb.md).

Per creare un bilanciamento del carico interno, creare un manifesto del servizio denominato internal-lb.yaml con tipo di servizio LoadBalancer e l'annotazione azure-load-balancer-internal come illustrato nell'esempio seguente:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Distribuire il bilanciamento del carico interno usando il comando kubectl apply e specificare il nome del manifesto YAML:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Distribuire un servizio Kubernetes

Dal momento che il tipo in uscita del cluster è impostato come route definita dall'utente, l'associazione dei nodi agente come pool back-end per il bilanciamento del carico non viene completata automaticamente dal servizio Azure Kubernetes durante la creazione del cluster. L'associazione del pool back-end viene però gestita dal provider di servizi cloud di Azure Kubernetes durante la distribuzione del servizio Kubernetes.

Distribuire l'applicazione Azure Voting copiando il codice YAML seguente in un file denominato `example.yaml`.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Per distribuire il servizio, eseguire:

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>Aggiungere una regola DNAT a Firewall di Azure

Per configurare la connettività in ingresso, è necessario scrivere una regola DNAT in Firewall di Azure. Per testare la connettività al cluster, viene definita una regola per l'indirizzo IP pubblico del front-end del firewall da indirizzare all'IP interno esposto dal servizio interno.

È possibile personalizzare l'indirizzo di destinazione perché corrisponde alla porta del firewall a cui accedere. L'indirizzo convertito deve essere l'indirizzo IP del bilanciamento del carico interno. La porta convertita deve essere la porta esposta per il servizio Kubernetes.

Sarà necessario specificare l'indirizzo IP interno assegnato al bilanciamento del carico creato dal servizio Kubernetes. Per recuperare l'indirizzo, eseguire:

```bash
kubectl get services
```

L'indirizzo IP necessario sarà indicato nella colonna EXTERNAL-IP e sarà simile a quello illustrato di seguito.

```bash
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
azure-vote-back    ClusterIP      192.168.92.209   <none>        6379/TCP       23m
azure-vote-front   LoadBalancer   192.168.19.183   100.64.2.5    80:32106/TCP   23m
kubernetes         ClusterIP      192.168.0.1      <none>        443/TCP        4d3h
```

```azure-cli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address <INSERT IP OF K8s SERVICE>
```

## <a name="clean-up-resources"></a>Pulire le risorse

> [!NOTE]
> Quando si elimina il servizio interno Kubernetes, il provider di servizi cloud di Azure eliminerà il bilanciamento del carico interno se non è più usato da alcun servizio. Alla successiva distribuzione del servizio, verrà distribuito un bilanciamento del carico se non ne viene trovato uno con la configurazione richiesta.

Per pulire le risorse di Azure, eliminare il gruppo di risorse del servizio Azure Kubernetes.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Convalidare la connettività

In un browser accedere all'indirizzo IP front-end di Firewall di Azure per convalidare la connettività.

Verrà visualizzata un'immagine dell'app Azure Voting.

## <a name="next-steps"></a>Passaggi successivi

Vedere la [panoramica sulle route definite dall'utente per le reti Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Vedere le informazioni su [come creare, modificare o eliminare una tabella di route](https://docs.microsoft.com/azure/virtual-network/manage-route-table).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
