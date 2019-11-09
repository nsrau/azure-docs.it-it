---
title: Distribuire e configurare il firewall di Azure con l'interfaccia della riga di comando
description: Questo articolo illustra come distribuire e configurare il firewall di Azure usando l'interfaccia della riga di comando di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/29/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: e97783d1a32916cad151f1d0858a8190d0005fd0
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73831973"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Distribuire e configurare il firewall di Azure con l'interfaccia della riga di comando

Il controllo dell'accesso alla rete in uscita è un componente importante di un piano di sicurezza della rete generale. Ad esempio, potrebbe essere utile limitare l'accesso ai siti Web. In alternativa, potrebbe essere utile limitare gli indirizzi IP e le porte in uscita a cui è possibile accedere.

È possibile controllare l'accesso alla rete in uscita da una subnet di Azure con Firewall di Azure. Con Firewall di Azure, è possibile configurare:

* Regole di applicazione che definiscono i nomi di dominio completi (FQDN) accessibili da una subnet. Il nome FQDN può [includere anche istanze di SQL](sql-fqdn-filtering.md).
* Regole di rete che definiscono l'indirizzo di origine, il protocollo, la porta di destinazione e l'indirizzo di destinazione.

Il traffico di rete è sottoposto alle regole del firewall configurate quando si instrada il traffico di rete al firewall come gateway predefinito della subnet.

Per questo articolo, si crea un VNet singolo semplificato con tre subnet per semplificare la distribuzione. Per le distribuzioni di produzione, è consigliabile usare un [modello hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) . Il firewall si trova nel proprio VNet. I server del carico di lavoro si trovano nelle reti virtuali associate all'interno della stessa area con una o più subnet.

* **AzureFirewallSubnet**: in questa subnet si trova il firewall.
* **Workload-SN**: in questa subnet si trova il server del carico di lavoro. Il traffico di rete di questa subnet passa attraverso il firewall.
* **Jump-SN**: in questa subnet si trova il jump server. Il jump server ha un indirizzo IP pubblico a cui è possibile connettersi con Desktop remoto. Da qui è quindi possibile connettersi al server del carico di lavoro (tramite un'altra istanza di Desktop remoto).

![Infrastruttura di rete dell'esercitazione](media/tutorial-firewall-rules-portal/Tutorial_network.png)

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Configurare un ambiente di rete di test
> * Distribuire un firewall
> * Creare una route predefinita
> * Configurare una regola dell'applicazione per consentire l'accesso a www.google.com
> * Configurare una regola di rete per consentire l'accesso a server DNS esterni
> * Testare il firewall

Se si preferisce, è possibile completare questa procedura usando il [portale di Azure](tutorial-firewall-deploy-portal.md) o [Azure PowerShell](deploy-ps.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Per trovare la versione, eseguire **az --version**. Per informazioni sull'installazione o sull'aggiornamento, vedere [Installare l'interfaccia da riga di comando di Azure]( /cli/azure/install-azure-cli).

Installare l'estensione del firewall di Azure:

```azurecli-interactive
az extension add -n azure-firewall
```


## <a name="set-up-the-network"></a>Configurare la rete

In primo luogo, creare un gruppo di risorse per contenere le risorse necessarie per distribuire il firewall. Creare quindi una rete virtuale, le subnet e i server di test.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Il gruppo di risorse contiene tutte le risorse per la distribuzione.

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>Creare una rete virtuale

Questa rete virtuale ha tre subnet.

> [!NOTE]
> La dimensione della subnet AzureFirewallSubnet è /26. Per altre informazioni sulle dimensioni delle subnet, vedere le [domande frequenti su Firewall di Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/26
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare ora le macchine virtuali per il jump server e il server del carico di lavoro e posizionarle nelle subnet appropriate.
Quando richiesto, digitare una password per la macchina virtuale.

Creare la macchina virtuale SRV-Jump.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



Creare una scheda di interfaccia di rete per SRV-lavorare con indirizzi IP del server DNS specifici e nessun indirizzo IP pubblico con cui eseguire il test.

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

Creare ora la macchina virtuale del carico di lavoro.
Quando richiesto, digitare una password per la macchina virtuale.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

## <a name="deploy-the-firewall"></a>Distribuire il firewall

A questo punto, distribuire il firewall nella rete virtuale.

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

Annotare l'indirizzo IP privato. Sarà necessario più avanti per la creazione della route predefinita.

## <a name="create-a-default-route"></a>Creare una route predefinita

Creare una tabella con propagazione di route BGP disabilitata

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

Creare la route.

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

Associare la tabella di route alla subnet

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>Configurare una regola di applicazione

La regola applicazione consente l'accesso in uscita a www.google.com.

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

Firewall di Azure include una raccolta di regole predefinite per i nomi di dominio completi dell'infrastruttura consentiti per impostazione predefinita. Questi nomi di dominio completi sono specifici per la piattaforma e non possono essere usati per altri scopi. Per altre informazioni, vedere [Infrastructure FQDNs](infrastructure-fqdns.md) (FQDN dell'infrastruttura).

## <a name="configure-a-network-rule"></a>Configurare una regola di rete

La regola di rete consente l'accesso in uscita a due indirizzi IP sulla porta 53 (DNS).

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>Testare il firewall

A questo punto testare il firewall per verificare che funzioni come previsto.

1. Prendere nota dell'indirizzo IP privato per la macchina virtuale **SRV-work** :

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. Connettere una sessione di Desktop remoto alla macchina virtuale **Srv-Jump** e accedere. Da qui, aprire una connessione Desktop remoto all'indirizzo IP privato **SRV-lavoro** ed effettuare l'accesso.

3. In **SRV-work**aprire una finestra di PowerShell ed eseguire i comandi seguenti:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Entrambi i comandi devono restituire le risposte, mostrando che le query DNS stanno attraversando il firewall.

1. Eseguire i comandi seguenti:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Le richieste di `www.google.com` dovrebbero avere esito positivo e le richieste di `www.microsoft.com` devono avere esito negativo. Ciò dimostra che le regole del firewall funzionano come previsto.

A questo punto si è verificato che le regole del firewall funzionano:

* È possibile risolvere i nomi DNS con il server DNS esterno configurato.
* È possibile passare al nome di dominio completo consentito ma non agli altri.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile proteggere le risorse del firewall per l'esercitazione successiva o, se non sono più necessarie, eliminare il gruppo di risorse **test-FW-RG** per eliminare tutte le risorse correlate al firewall:

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Monitorare i log di Firewall di Azure](./tutorial-diagnostics.md)
