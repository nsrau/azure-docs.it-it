---
title: Personalizzare le route definite dall'utente (UDR) nel servizio Azure Kubernetes (AKS)Customize user-defined routes (UDR) in Azure Kubernetes Service (AKS)
description: Informazioni su come definire una route in uscita personalizzata nel servizio Azure Kubernetes (AKS)Learn how to define a custom egress route in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: fa64294939ea487b3123d1db5ef6c8a5f30fcf72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129397"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>Personalizzare l'uscita del cluster con una route definita dall'utente (anteprima)Customize cluster egress with a User-Defined Route (Preview)

L'uscita da un cluster AKS può essere personalizzata per adattarsi a scenari specifici. Per impostazione predefinita, AKS eseguirà il provisioning di un servizio di bilanciamento del carico SKU Standard per l'installazione e l'utilizzo per l'uscita. Tuttavia, la configurazione predefinita potrebbe non soddisfare i requisiti di tutti gli scenari se gli indirizzi IP pubblici non sono consentiti o sono necessari hop aggiuntivi per l'uscita.

Questo articolo illustra come personalizzare la route in uscita di un cluster per supportare scenari di rete personalizzati, ad esempio quelli che non consentono gli indirizzi IP pubblici e richiede che il cluster si esibisca dietro un'appliance virtuale di rete (NVA).

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono self-service e sono offerte in base all'opt-in. Le anteprime vengono fornite così *come sono* e *come disponibili* e sono escluse dal contratto di servizio (SLA) e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dall'assistenza clienti nel *miglior modo possibile.* Pertanto, le funzionalità non sono destinate all'utilizzo in produzione. Per altre informazioni, vedere i seguenti articoli di supporto:
>
> * [Criteri di supporto AKS](support-policies.md)
> * [Domande frequenti relative al supporto tecnico Azure](faq.md)

## <a name="prerequisites"></a>Prerequisiti
* Interfaccia della riga di comando di Azure versione 2.0.81 o successivaAzure CLI version 2.0.81 or greater
* Estensione di anteprima dell'interfaccia della riga di comando di Azure 0.4.28 o successivaAzure CLI Preview extension version version 0.4.28 or greater
* Versione API `2020-01-01` di o superiore

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Installare l'estensione più recente dell'anteprima AKS dell'interfaccia della riga di comando di AzureInstall the latest Azure CLI AKS Preview extension
Per impostare il tipo in uscita di un cluster, è necessaria l'estensione di Azure CLI AKS Preview versione 0.4.18 o successiva. Installare l'estensione di Azure CLI AKS Preview usando il comando az extension add e quindi verificare la presenza di eventuali aggiornamenti disponibili usando il comando di aggiornamento dell'estensione az seguente:Install the Azure CLI AKS Preview extension by using the az extension add command, and then check for any available updates by using the following az extension update command:

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Limitazioni
* Durante l'anteprima, `outboundType` può essere definito solo in fase di creazione del cluster e non può essere aggiornato in seguito.
* Durante l'anteprima, i cluster AKS devono usare CNI di Azure.During preview, `outboundType` AKS clusters should use Azure CNI. Kubenet è configurabile, l'utilizzo richiede associazioni manuali della tabella di route alla subnet AKS.
* `outboundType` L'impostazione richiede cluster `vm-set-type` `VirtualMachineScaleSets` AKS `Standard`con un e di `load-balancer-sku` .
* L'impostazione `outboundType` `UDR` su un valore di richiede una route definita dall'utente con connettività in uscita valida per il cluster.
* L'impostazione `outboundType` `UDR` su un valore implica che l'IP di origine in ingresso instradato al servizio di bilanciamento del carico potrebbe **non corrispondere all'indirizzo** di destinazione in uscita del cluster.

## <a name="overview-of-outbound-types-in-aks"></a>Panoramica dei tipi in uscita in AKS

Un cluster AKS può essere `outboundType` personalizzato con un unico bilanciamento del carico di tipo o routing definito dall'utente.

> [!IMPORTANT]
> Il tipo in uscita influisce solo sul traffico in uscita del cluster. Per ulteriori informazioni, [vedere Configurazione dei controller in ingresso.](ingress-basic.md)

### <a name="outbound-type-of-loadbalancer"></a>Tipo in uscita di loadBalancerOutbound type of loadBalancer

Se `loadBalancer` impostato, AKS completa automaticamente la seguente configurazione. Il servizio di bilanciamento del carico viene usato per l'uscita tramite un IP pubblico assegnato ad AKS. Un tipo `loadBalancer` di supporto in uscita supporta `loadBalancer`i servizi Kubernetes di tipo , che prevedono l'uscita dal servizio di bilanciamento del carico creato dal provider di risorse AKS.

La seguente configurazione viene eseguita da AKS.
   * Viene eseguito il provisioning di un indirizzo IP pubblico per l'uscita del cluster.
   * L'indirizzo IP pubblico viene assegnato alla risorsa di bilanciamento del carico.
   * I pool back-end per il servizio di bilanciamento del carico vengono configurati per i nodi agente nel cluster.

Di seguito è riportata una topologia di rete distribuita `outboundType` `loadBalancer`nei cluster AKS per impostazione predefinita, che utilizzano un oggetto di .

![outboundtype-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Tipo in uscita di userDefinedRoutingOutbound type of userDefinedRouting

> [!NOTE]
> L'utilizzo del tipo in uscita è uno scenario di rete avanzato e richiede una configurazione di rete appropriata.

Se `userDefinedRouting` impostato, AKS non configurerà automaticamente i percorsi in uscita. **L'utente**dovrebbe eseguire le operazioni seguenti.

Il cluster deve essere distribuito in una rete virtuale esistente con una subnet configurata. Una route definita dall'utente valida deve esistere nella subnet con connettività in uscita.

Il provider di risorse AKS distribuirà un servizio di bilanciamento del carico (SLB) standard. Il servizio di bilanciamento del carico non è configurato con alcuna regola e [non comporta un addebito fino a quando non viene inserita una regola.](https://azure.microsoft.com/pricing/details/load-balancer/) AKS **non** eseguirà automaticamente il provisioning di un indirizzo IP pubblico per il front-end SLB. AKS **non** configurerà automaticamente il pool back-end del servizio di bilanciamento del carico.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Distribuire un cluster con tipo in uscita di UDR e Firewall di AzureDeploy a cluster with outbound type of UDR and Azure Firewall

Per illustrare l'applicazione di un cluster con tipo in uscita usando una route definita dall'utente, è possibile configurare un cluster in una rete virtuale con peering con un firewall di Azure.To illustrate the application of a cluster with outbound type using a user-defined route, a cluster can be configured on a virtual network peered with an Azure Firewall.

![Topologia bloccata](media/egress-outboundtype/outboundtype-udr.png)

* L'ingresso è forzato a scorrere attraverso i filtri firewall
   * Una subnet isolata contiene un servizio di bilanciamento del carico interno per il routing nei nodi dell'agenteAn isolated subnet holds an internal load balancer for routing into agent nodes
   * I nodi agente sono isolati in una subnet dedicataAgent nodes are isolated in a dedicated subnet
* Le richieste in uscita vengono avviate dai nodi agente all'indirizzo IP interno del firewall di Azure usando una route definita dall'utenteOutbound requests start from agent nodes to the Azure Firewall internal IP using a user-defined route
   * Le richieste provenienti dai nodi dell'agente AKS seguono un UDR che è stato inserito nella subnet in cui è stato distribuito il cluster AKS.
   * Firewall di Azure in uscita dalla rete virtuale da un front-end IP pubblico
   * L'accesso al piano di controllo AKS è protetto da un gruppo di sicurezza di rete, che ha abilitato l'indirizzo IP front-end del firewall
   * L'accesso a Internet pubblico o ad altri servizi di Azure passa da e verso l'indirizzo IP front-end del firewall

### <a name="set-configuration-via-environment-variables"></a>Impostare la configurazione tramite variabili di ambienteSet configuration via environment variables

Definire un set di variabili di ambiente da utilizzare nelle creazioni di risorse.

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

Successivamente, impostare gli ID sottoscrizione.

```azure-cli
# Get ARM Access Token and Subscription ID - This will be used for AuthN later.

ACCESS_TOKEN=$(az account get-access-token -o tsv --query 'accessToken')

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>Creare una rete virtuale con più subnet

Effettuare il provisioning di una rete virtuale con tre subnet separate, una per il cluster, una per il firewall e una per l'ingresso del servizio.

![Topologia di rete vuota](media/egress-outboundtype/empty-network.png)

Creare un gruppo di risorse per contenere tutte le risorse.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Creare due reti virtuali per ospitare il cluster AKS e il firewall di Azure.Create a two virtual networks to host the AKS cluster and the Azure Firewall. Ognuno avrà la propria subnet. Iniziamo con la rete AKS.

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

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>Creare e configurare un firewall di Azure con un UDRCreate and setup an Azure Firewall with a UDR

È necessario configurare le regole in ingresso e in uscita di Firewall di Azure.Azure Firewall inbound and outbound rules must be configured. Lo scopo principale del firewall è consentire alle organizzazioni di configurare regole granulari per il traffico in ingresso ed uscita in entrata e in uscita dal cluster AKS.

![Firewall e UDR](media/egress-outboundtype/firewall-udr.png)

Creare una risorsa IP pubblica SKU standard che verrà usata come indirizzo front-end di Firewall di Azure.Create a standard SKU public IP resource that will be used as the Azure Firewall frontend address.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registrare l'estensione dell'estensione dell'anteprima per creare un firewall di Azure.Register the preview cli-extension to create an Azure Firewall.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

L'indirizzo IP creato in precedenza può ora essere assegnato al frontend del firewall.
> [!NOTE]
> La configurazione dell'indirizzo IP pubblico in Firewall di Azure potrebbe richiedere alcuni minuti.
> 
> Se gli errori vengono ripetutamente ricevuti nel comando seguente, eliminare il firewall esistente e l'indirizzo IP pubblico ed eseguire il provisioning di IP pubblico e Firewall di Azure tramite il portale contemporaneamente.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Quando il comando precedente ha esito positivo, salvare l'indirizzo IP front-end del firewall per la configurazione in un secondo momento.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Creare un UDR con un hop a Firewall di AzureCreate a UDR with a hop to Azure Firewall

Azure effettua il routing automatico del traffico tra subnet di Azure, reti virtuali e reti locali. Per modificare il routing predefinito di Azure è necessario creare una tabella di route.

Creare una tabella di route vuota da associare a una determinata subnet. La tabella di route definirà l'hop successivo come Firewall di Azure creato in precedenza. A ogni subnet può essere associata una o nessuna tabella di route.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Vedere la [documentazione](../virtual-network/virtual-networks-udr-overview.md#user-defined) della tabella di route della rete virtuale su come eseguire l'override delle route di sistema predefinite di Azure o aggiungere altre route alla tabella di route di una subnet.

## <a name="adding-network-firewall-rules"></a>Aggiunta di regole del firewall di rete

> [!WARNING]
> Di seguito è riportato un esempio di aggiunta di una regola firewall. Tutti gli endpoint in uscita definiti negli endpoint in [uscita necessari](egress.md) devono essere abilitati dalle regole del firewall dell'applicazione affinché i cluster AKS funzionino. Senza questi endpoint abilitati, il cluster non può funzionare.

Di seguito è riportato un esempio di una regola di rete e applicazione. Aggiungiamo una regola di rete che consente qualsiasi protocollo, indirizzo di origine, indirizzo di destinazione e porte di destinazione. Aggiungiamo anche una regola dell'applicazione per **alcuni** degli endpoint richiesti da AKS.

In uno scenario di produzione, è necessario abilitare l'accesso solo agli endpoint necessari per l'applicazione e a quelli definiti in [AKS required egress](egress.md).

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

Per altre informazioni sul servizio Firewall di Azure, vedere [la documentazione](https://docs.microsoft.com/azure/firewall/overview) di Firewall di Azure.See Azure Firewall documentation to learn more about the Azure Firewall service.

## <a name="associate-the-route-table-to-aks"></a>Associare la tabella di route ad AKS

Per associare il cluster al firewall, la subnet dedicata per la subnet del cluster deve fare riferimento alla tabella di route creata in precedenza. L'associazione può essere eseguita inviando un comando alla rete virtuale che contiene sia il cluster che il firewall per aggiornare la tabella di route della subnet del cluster.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Distribuire AKS con tipo di UDR in uscita nella rete esistenteDeploy AKS with outbound type of UDR to the existing network

Ora un cluster AKS può essere distribuito nella configurazione di rete virtuale esistente. Per impostare un tipo di cluster in uscita per il routing definito dall'utente, è necessario fornire una subnet esistente ad AKS.

![aks-deploy](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Creare un'entità servizio con accesso per il provisioning all'interno della rete virtuale esistenteCreate a service principal with access to provision inside the existing virtual network

Un'entità servizio viene usata da AKS per creare risorse cluster. L'entità servizio passata in fase di creazione viene usata per creare risorse AKS sottostanti, ad esempio macchine virtuali, archiviazione e servizi di bilanciamento del carico usati da AKS. Se vengono concesse autorizzazioni troppo poche, non sarà possibile eseguire il provisioning di un cluster AKS.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Sostituire ora `APPID` `PASSWORD` e di seguito con l'appid dell'entità servizio e la password dell'entità servizio generata automaticamente dall'output del comando precedente. Faremo riferimento all'ID risorsa VNET per concedere le autorizzazioni all'entità servizio in modo che AKS possa distribuire le risorse in esso.

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>Distribuire AKSDeploy AKS

Infine, il cluster AKS può essere distribuito nella subnet esistente dedicata al cluster. La subnet di destinazione in cui essere `$SUBNETID`distribuita viene definita con la variabile di ambiente , . Non abbiamo definito `$SUBNETID` la variabile nei passaggi precedenti. Per impostare il valore per l'ID subnet, è possibile utilizzare il comando seguente:

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

Definiremo il tipo in uscita per seguire l'UDR presente nella subnet, consentendo ad AKS di ignorare l'installazione e il provisioning IP per il servizio di bilanciamento del carico che ora può essere strettamente interno.

La funzionalità AKS per [gli intervalli IP autorizzati](api-server-authorized-ip-ranges.md) al server API può essere aggiunta per limitare l'accesso al server API solo all'endpoint pubblico del firewall. La funzionalità degli intervalli IP autorizzati è indicata nel diagramma come gruppo di sicurezza di sicurezza di database che deve essere passato per accedere al piano di controllo. Quando si abilita la funzionalità di intervallo IP autorizzato per limitare l'accesso al server API, gli strumenti di sviluppo devono utilizzare un jumpbox dalla rete virtuale del firewall oppure è necessario aggiungere tutti gli endpoint di sviluppo all'intervallo IP autorizzato.

> [!TIP]
> È possibile aggiungere ulteriori funzionalità alla distribuzione del cluster, ad esempio (Cluster privato)[]. Quando si utilizzano intervalli IP autorizzati, sarà necessario un jumpbox all'interno della rete del cluster per accedere al server API.

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

### <a name="enable-developer-access-to-the-api-server"></a>Abilitare l'accesso dello sviluppatore al server APIEnable developer access to the API server

A causa della configurazione degli intervalli IP autorizzati per il cluster, è necessario aggiungere gli indirizzi IP degli strumenti di sviluppo all'elenco di cluster AKS degli intervalli IP approvati per accedere al server API. Un'altra opzione consiste nel configurare un jumpbox con gli strumenti necessari all'interno di una subnet separata nella rete virtuale del firewall.

Aggiungere un altro indirizzo IP agli intervalli approvati con il seguente comando

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

### <a name="setup-the-internal-load-balancer"></a>Configurare il servizio di bilanciamento del carico interno

AKS ha distribuito un servizio di bilanciamento del carico con il cluster che può essere configurato come servizio di [bilanciamento del carico interno.](internal-lb.md)

Per creare un servizio di bilanciamento del carico interno, creare un manifesto del servizio denominato internal-lb.yaml con il tipo di servizio LoadBalancer e l'annotazione interna al servizio di bilanciamento del carico di azure, come illustrato nell'esempio seguente:To create an internal load balancer, create a service manifest named internal-lb.yaml with the service type LoadBalancer and the azure-load-balancer-internal annotation as shown in the following example:

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

Distribuire il servizio di bilanciamento del carico interno usando l'applicazione kubectl e specificare il nome del manifesto YAML:Deploy the internal load balancer using the kubectl apply and specify the name of your YAML manifest:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Distribuire un servizio Kubernetes

Poiché il tipo di cluster in uscita è impostato come UDR, l'associazione dei nodi dell'agente come pool back-end per il servizio di bilanciamento del carico non viene completata automaticamente da AKS in fase di creazione del cluster. Tuttavia, l'associazione del pool back-end viene gestita dal provider cloud di Kubernetes Azure quando viene distribuito il servizio Kubernetes.However, backend pool association is handled by the Kubernetes Azure cloud provider when the Kubernetes service is deployed.

Distribuire l'applicazione dell'app di voto di Azure `example.yaml`copiando lo yaml riportato di seguito in un file denominato .

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

Distribuire il servizio eseguendo:

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>Aggiungere una regola DNAT a Firewall di AzureAdd a DNAT rule to Azure Firewall

To configure inbound connectivity, a DNAT rule must be written to the Azure Firewall. Per verificare la connettività al cluster, viene definita una regola per l'indirizzo IP pubblico front-end del firewall per instradare all'indirizzo IP interno esposto dal servizio interno.

L'indirizzo di destinazione può essere personalizzato in quanto è la porta sul firewall a cui accedere. L'indirizzo convertito deve essere l'indirizzo IP del servizio di bilanciamento del carico interno. La porta tradotta deve essere la porta esposta per il servizio Kubernetes.

È necessario specificare l'indirizzo IP interno assegnato al servizio di bilanciamento del carico creato dal servizio Kubernetes. Recuperare l'indirizzo eseguendo:

```bash
kubectl get services
```

L'indirizzo IP necessario verrà elencato nella colonna EXTERNAL-IP, simile al seguente.

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
> Quando si elimina il servizio interno Kubernetes, se il servizio di bilanciamento del carico interno non è più utilizzato da alcun servizio, il provider cloud di Azure eliminerà il servizio di bilanciamento del carico interno. Nella distribuzione successiva del servizio verrà distribuito un servizio di bilanciamento del carico se non ne è stato trovato nessuno con la configurazione richiesta.

Per pulire le risorse di Azure, eliminare il gruppo di risorse AKS.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Convalidare la connettività

Passare all'indirizzo IP del front-end di Firewall di Azure in un browser per convalidare la connettività.

Verrà visualizzata un'immagine dell'app di voto di Azure.You should see an image of the Azure voting app.

## <a name="next-steps"></a>Passaggi successivi

Vedere [Panoramica dell'UDR di rete di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).See Azure networking UDR overview .

Informazioni su [come creare, modificare o eliminare una tabella di route.](https://docs.microsoft.com/azure/virtual-network/manage-route-table)
