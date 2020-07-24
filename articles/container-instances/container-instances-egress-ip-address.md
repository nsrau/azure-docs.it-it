---
title: Configurare l'indirizzo IP in uscita statico
description: Configurare il firewall di Azure e le route definite dall'utente per i carichi di lavoro di istanze di contenitore di Azure che usano l'indirizzo IP pubblico del firewall per il traffico in ingresso e in uscita
ms.topic: article
ms.date: 07/16/2020
author: dlepow
ms.author: danlep
ms.openlocfilehash: d748e3e6239ba913afc5b8aadd7e85dcd1027c04
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023705"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>Configurare un singolo indirizzo IP pubblico per il traffico in uscita e in ingresso verso un gruppo di contenitori

La configurazione di un [gruppo di contenitori](container-instances-container-groups.md) con un indirizzo IP esterno consente ai client esterni di usare l'indirizzo IP per accedere a un contenitore nel gruppo. Ad esempio, un browser può accedere a un'app Web in esecuzione in un contenitore. Attualmente, tuttavia, un gruppo di contenitori utilizza un indirizzo IP diverso per il traffico in uscita. Questo indirizzo IP in uscita non viene esposto a livello di programmazione, rendendo più complesso il monitoraggio e la configurazione delle regole del firewall del client del gruppo di contenitori.

Questo articolo illustra la procedura per configurare un gruppo di contenitori in una [rete virtuale](container-instances-virtual-network-concepts.md) integrata con il [firewall di Azure](../firewall/overview.md). Impostando una route definita dall'utente per il gruppo di contenitori e le regole del firewall, è possibile indirizzare e identificare il traffico da e verso il gruppo di contenitori. Il traffico in ingresso e in uscita del gruppo di contenitori usa l'indirizzo IP pubblico del firewall. Un singolo indirizzo IP in uscita può essere usato da più gruppi di contenitori distribuiti nella subnet della rete virtuale delegata alle istanze di contenitore di Azure.

In questo articolo si usa l'interfaccia della riga di comando di Azure per creare le risorse per questo scenario:

* Gruppi di contenitori distribuiti in una subnet delegata [nella rete virtuale](container-instances-vnet.md) 
* Un firewall di Azure distribuito in rete con un indirizzo IP pubblico statico
* Una route definita dall'utente nella subnet dei gruppi di contenitori
* Una regola NAT per il traffico in ingresso del firewall e una regola dell'applicazione per l'uscita

È quindi possibile convalidare il traffico in ingresso e in uscita da gruppi di contenitori di esempio tramite il firewall.

## <a name="deploy-aci-in-a-virtual-network"></a>Distribuire ACI in una rete virtuale

In un caso tipico, è possibile che si disponga già di una rete virtuale di Azure in cui distribuire un gruppo di contenitori. A scopo dimostrativo, i comandi seguenti creano una rete virtuale e una subnet quando viene creato il gruppo di contenitori. La subnet viene delegata alle istanze di contenitore di Azure. 

Il gruppo di contenitori esegue una piccola app Web dall' `aci-helloworld` immagine. Come illustrato negli altri articoli della documentazione, questa immagine crea un pacchetto di un'app Web di piccole dimensioni scritta in Node.js che funge da pagina HTML statica.

Se necessario, creare prima di tutto un gruppo di risorse di Azure con il comando [AZ Group create][az-group-create] . Ad esempio:

```azurecli
az group create --name myResourceGroup --location eastus
```

Per semplificare gli esempi di comando seguenti, usare una variabile di ambiente per il nome del gruppo di risorse:

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

Creare il gruppo di contenitori con il comando [AZ container create][az-container-create] :

```azurecli
az container create \
  --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

> [!TIP]
> Modificare il valore di `--subnet address-prefix` per lo spazio di indirizzi IP necessario nella subnet. La subnet più piccola supportata è/29, che fornisce otto indirizzi IP. Alcuni indirizzi IP sono riservati per l'uso da parte di Azure.

Per l'uso in un passaggio successivo, ottenere l'indirizzo IP privato del gruppo di contenitori eseguendo il comando [AZ container Show] [AZ-container-Show]:

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>Distribuire il firewall di Azure in rete

Nelle sezioni seguenti usare l'interfaccia della riga di comando di Azure per distribuire un firewall di Azure nella rete virtuale. Per informazioni di base, vedere [esercitazione: distribuire e configurare il firewall di Azure usando il portale di Azure](../firewall/deploy-cli.md).

Per prima cosa, usare [AZ Network VNET subnet create][az-network-vnet-subnet-create] per aggiungere una subnet denominata AzureFirewallSubnet per il firewall. AzureFirewallSubnet è il nome *obbligatorio* della subnet.

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

Usare i seguenti [comandi dell'interfaccia](../firewall/deploy-cli.md) della riga di comando di Azure per creare un firewall nella subnet.

Se non è già installato, aggiungere l'estensione del firewall all'interfaccia della riga di comando di Azure usando il comando [AZ Extension Add][az-extension-add] :

```azurecli
az extension add --name azure-firewall
```

Creare le risorse del firewall:

```azurecli
az network firewall create \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus

az network public-ip create \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --allocation-method static \
  --sku standard
    
az network firewall ip-config create \
  --firewall-name myFirewall \
  --name FW-config \
  --public-ip-address fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet
```

Aggiornare la configurazione del firewall usando il comando [AZ Network Firewall Update][az-network-firewall-update] :

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

Ottenere l'indirizzo IP privato del firewall usando il comando [AZ network firewall IP-Config list][az-network-firewall-ip-config-list] . Questo indirizzo IP privato viene usato in un comando successivo.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
Ottenere l'indirizzo IP pubblico del firewall usando il comando [AZ Network Public-IP Show][az-network-public-ip-show] . Questo indirizzo IP pubblico viene usato in un comando successivo.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>Definire la route definita dall'utente nella subnet ACI

Definire una route definita dall'uso nella subnet ACI per deviare il traffico al firewall di Azure. Per ulteriori informazioni, vedere [instradare il traffico di rete](../virtual-network/tutorial-create-route-table-cli.md). 

### <a name="create-route-table"></a>Creare una tabella di route

Eseguire innanzitutto il comando [AZ Network Route-Table Create][az-network-route-table-create] seguente per creare la tabella di route. Creare la tabella di route nella stessa area della rete virtuale.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>Crea Route

Eseguire il comando [AZ Network-Route-Table Route create][az-network-route-table-route-create] per creare una route nella tabella di route. Per instradare il traffico al firewall, impostare il tipo di hop successivo su `VirtualAppliance` e passare l'indirizzo IP privato del firewall come indirizzo hop successivo.

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>Associare la tabella di route alla subnet ACI

Eseguire il comando [AZ Network VNET subnet Update][az-network-vnet-subnet-update] per associare la tabella di route alla subnet delegata alle istanze di contenitore di Azure.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>Configurare le regole nel firewall

Per impostazione predefinita, il firewall di Azure nega (blocca) il traffico in ingresso e in uscita. 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>Configurare la regola NAT nel firewall per la subnet ACI

Creare una [regola NAT](../firewall/rule-processing.md) nel firewall per tradurre e filtrare il traffico Internet in ingresso verso il contenitore dell'applicazione avviato in precedenza nella rete. Per informazioni dettagliate, vedere [filtrare il traffico Internet in ingresso con il firewall di Azure DNAT](../firewall/tutorial-firewall-dnat.md)

Creare una regola e una raccolta NAT usando il comando [AZ Network Firewall NAT-Rule create][az-network-firewall-nat-rule-create] :

```azurecli
az network firewall nat-rule create \
  --firewall-name myFirewall \
  --collection-name myNATCollection \
  --action dnat \
  --name myRule \
  --protocols TCP \
  --source-addresses '*' \
  --destination-addresses $FW_PUBLIC_IP \
  --destination-ports 80 \
  --resource-group $RESOURCE_GROUP_NAME \
  --translated-address $ACI_PRIVATE_IP \
  --translated-port 80 \
  --priority 200
```

Aggiungere le regole NAT necessarie per filtrare il traffico verso altri indirizzi IP nella subnet. Ad esempio, altri gruppi di contenitori nella subnet possono esporre indirizzi IP per il traffico in ingresso o altri indirizzi IP interni possono essere assegnati al gruppo di contenitori dopo un riavvio.

### <a name="create-outbound-application-rule-on-the-firewall"></a>Creare una regola dell'applicazione in uscita nel firewall

Eseguire il comando [AZ Network Firewall Application-Rule create][az-network-firewall-application-rule-create] seguente per creare una regola in uscita nel firewall. Questa regola di esempio consente l'accesso dalla subnet delegata alle istanze di contenitore di Azure al nome di dominio completo `checkip.dyndns.org` . L'accesso HTTP al sito viene usato in un passaggio successivo per confermare l'indirizzo IP in uscita dalle istanze di contenitore di Azure.

```azurecli
az network firewall application-rule create \
  --collection-name myAppCollection \
  --firewall-name myFirewall \
  --name Allow-CheckIP \
  --protocols Http=80 Https=443 \
  --resource-group $RESOURCE_GROUP_NAME \
  --target-fqdns checkip.dyndns.org \
  --source-addresses 10.0.0.0/24 \
  --priority 200 \
  --action Allow
```

## <a name="test-container-group-access-through-the-firewall"></a>Testare l'accesso al gruppo di contenitori tramite il firewall

Le sezioni seguenti verificano che la subnet delegata alle istanze di contenitore di Azure sia configurata correttamente dietro il firewall di Azure. I passaggi precedenti indirizzavano il traffico in ingresso alla subnet e il traffico in uscita dalla subnet attraverso il firewall.

### <a name="test-ingress-to-a-container-group"></a>Testare il traffico in ingresso in un gruppo di contenitori

Testare l'accesso in ingresso ai *appcontainer* in esecuzione nella rete virtuale passando all'indirizzo IP pubblico del firewall. In precedenza, l'indirizzo IP pubblico è stato archiviato nella variabile $FW _PUBLIC_IP:

```bash
echo $FW_PUBLIC_IP
```

L'output è simile a:

```console
52.142.18.133
```

Se la regola NAT nel firewall è configurata correttamente, quando si immette l'indirizzo IP pubblico del firewall nel browser vengono visualizzati gli elementi seguenti:

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="Passare all'indirizzo IP pubblico del firewall":::

### <a name="test-egress-from-a-container-group"></a>Testare l'uscita da un gruppo di contenitori


Distribuire il contenitore di esempio seguente nella rete virtuale. Quando viene eseguito, invia una singola richiesta HTTP a `http://checkip.dyndns.org` , che Visualizza l'indirizzo IP del mittente, ovvero l'indirizzo IP in uscita. Se la regola dell'applicazione nel firewall è configurata correttamente, viene restituito l'indirizzo IP pubblico del firewall.

```azurecli
az container create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress \
  --image mcr.microsoft.com/azuredocs/aci-tutorial-sidecar \
  --command-line "curl -s http://checkip.dyndns.org" \
  --restart-policy OnFailure \
  --vnet aci-vnet \
  --subnet aci-subnet
```

Visualizzare i log dei contenitori per verificare che l'indirizzo IP sia uguale all'indirizzo IP pubblico del firewall.

```azurecli
az container logs \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress 
```

L'output è simile a:

```console
<html><head><title>Current IP Check</title></head><body>Current IP Address: 52.142.18.133</body></html>
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si configurano i gruppi di contenitori in una rete virtuale dietro un firewall di Azure. Sono state configurate una route definita dall'utente e regole di applicazione e NAT sul firewall. Questa configurazione consente di configurare un singolo indirizzo IP statico per l'ingresso e l'uscita dalle istanze di contenitore di Azure.

Per altre informazioni sulla gestione del traffico e sulla protezione delle risorse di Azure, vedere la documentazione del [firewall di Azure](../firewall/index.yml) .



[az-group-create]: /cli/azure/group#az-group-create
[az-container-create]: /cli/azure/container#az-container-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-update]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az-network-public-ip-show
[az-network-route-table-create]:/cli/azure/network/route-table/#az-network-route-table-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[az-network-firewall-ip-config-list]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-update
[az-container-exec]: /cli/azure/container#az-container-exec
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-open-port]: /cli/azure/vm#az-vm-open-port
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[az-network-firewall-application-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/application-rule#ext-azure-firewall-az-network-firewall-application-rule-create
[az-network-firewall-nat-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/nat-rule#ext-azure-firewall-az-network-firewall-nat-rule-create






