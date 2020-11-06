---
title: Impostazioni DNS del firewall di Azure (anteprima)
description: È possibile configurare il firewall di Azure con le impostazioni del server DNS e del proxy DNS.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: efe608437f350a29147cf10989cdb6c17a23196d
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397995"
---
# <a name="azure-firewall-dns-settings-preview"></a>Impostazioni DNS del firewall di Azure (anteprima)

> [!IMPORTANT]
> Le impostazioni DNS del firewall di Azure sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile configurare un server DNS personalizzato e abilitare il proxy DNS per il firewall di Azure. È possibile configurare queste impostazioni quando si distribuisce il firewall o in un secondo momento dalla pagina **impostazioni DNS** .

## <a name="dns-servers"></a>Server DNS

Un server DNS gestisce e risolve i nomi di dominio in indirizzi IP. Per impostazione predefinita, il firewall di Azure usa DNS di Azure per la risoluzione dei nomi. L'impostazione del **server DNS** consente di configurare i server DNS per la risoluzione dei nomi del firewall di Azure. È possibile configurare uno o più server.

> [!NOTE]
> Per i firewall di Azure gestiti con Azure Firewall Manager le impostazioni DNS sono configurate nei criteri del firewall di Azure associati.

### <a name="configure-custom-dns-servers-preview---azure-portal"></a>Configurare server DNS personalizzati (anteprima)-portale di Azure

1. In **Impostazioni** del firewall di Azure selezionare **impostazioni DNS**.
2. In **server DNS** è possibile digitare o aggiungere i server DNS esistenti precedentemente specificati nella rete virtuale.
3. Selezionare **Salva**.
4. Il firewall ora indirizza il traffico DNS ai server DNS specificati per la risoluzione dei nomi.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="Server DNS":::

### <a name="configure-custom-dns-servers-preview---azure-cli"></a>Configurare server DNS personalizzati (anteprima)-interfaccia della riga di comando di Azure

L'esempio seguente aggiorna il firewall di Azure con i server DNS personalizzati usando l'interfaccia della riga di comando di Azure.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> Il comando `az network firewall` richiede l'installazione dell'estensione dell'interfaccia della riga `azure-firewall` di comando di Azure. Può essere installato tramite il comando `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers-preview---azure-powershell"></a>Configurare server DNS personalizzati (anteprima)-Azure PowerShell

L'esempio seguente aggiorna il firewall di Azure con i server DNS personalizzati usando Azure PowerShell.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy-preview"></a>Proxy DNS (anteprima)

È possibile configurare il firewall di Azure in modo che funga da proxy DNS. Un proxy DNS funge da intermediario per le richieste DNS dalle macchine virtuali client a un server DNS. Se si configura un server DNS personalizzato, è necessario abilitare il proxy DNS per evitare la mancata corrispondenza della risoluzione DNS e abilitare il filtro FQDN nelle regole di rete.

Se non si Abilita il proxy DNS, le richieste DNS dal client possono andare a un server DNS in un momento diverso o restituire una risposta diversa rispetto a quella del firewall. Il proxy DNS inserisce il firewall di Azure nel percorso delle richieste del client per evitare l'incoerenza.

Per la configurazione del proxy DNS sono necessari tre passaggi:
1. Abilitare il proxy DNS nelle impostazioni DNS del firewall di Azure.
2. Facoltativamente, configurare il server DNS personalizzato o usare il valore predefinito specificato.
3. Infine, è necessario configurare l'indirizzo IP privato del firewall di Azure come indirizzo DNS personalizzato nelle impostazioni del server DNS della rete virtuale. Ciò garantisce che il traffico DNS venga indirizzato al firewall di Azure.

### <a name="configure-dns-proxy-preview---azure-portal"></a>Configurare il proxy DNS (anteprima)-portale di Azure

Per configurare il proxy DNS, è necessario configurare l'impostazione dei server DNS della rete virtuale per l'uso dell'indirizzo IP privato del firewall. Abilitare quindi il proxy DNS nelle **impostazioni DNS** del firewall di Azure.

#### <a name="configure-virtual-network-dns-servers"></a>Configurare i server DNS della rete virtuale 

1. Selezionare la rete virtuale in cui il traffico DNS verrà instradato attraverso il firewall di Azure.
2. In **Impostazioni** selezionare **Server DNS**.
3. Selezionare **personalizzata** in **server DNS**.
4. Immettere l'indirizzo IP privato del firewall.
5. Selezionare **Salva**.
6. Riavviare le macchine virtuali connesse alla rete virtuale in modo che vengano loro assegnate le nuove impostazioni del server DNS. Le VM continuano a usare le impostazioni DNS correnti finché non vengono riavviate.

#### <a name="enable-dns-proxy-preview"></a>Abilita proxy DNS (anteprima)

1. Selezionare il firewall di Azure.
2. In **Impostazioni** selezionare **impostazioni DNS**.
3. Per impostazione predefinita, il **proxy DNS** è disabilitato. Se abilitata, il firewall è in ascolto sulla porta 53 e invia le richieste DNS ai server DNS configurati.
4. Esaminare la configurazione dei **server DNS** per assicurarsi che le impostazioni siano appropriate per l'ambiente in uso.
5. Selezionare **Salva**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="Proxy DNS":::

### <a name="configure-dns-proxy-preview---azure-cli"></a>Configurare il proxy DNS (anteprima)-interfaccia della riga di comando di Azure

La configurazione delle impostazioni proxy DNS nel firewall di Azure e l'aggiornamento di reti virtuali per l'uso del firewall di Azure come server DNS possono essere eseguite usando l'interfaccia della riga di comando

#### <a name="configure-virtual-network-dns-servers"></a>Configurare i server DNS della rete virtuale

Questo esempio Mostra come configurare VNet per l'uso del firewall di Azure come server DNS.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

#### <a name="enable-dns-proxy-preview"></a>Abilita proxy DNS (anteprima)

Questo esempio Mostra come abilitare la funzionalità proxy DNS nel firewall di Azure.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

### <a name="configure-dns-proxy-preview---azure-powershell"></a>Configurare il proxy DNS (anteprima)-Azure PowerShell

La configurazione delle impostazioni del proxy DNS e l'aggiornamento di reti virtuali per l'uso del firewall di Azure come server DNS possono essere eseguite usando Azure PowerShell.

#### <a name="configure-virtual-network-dns-servers"></a>Configurare i server DNS della rete virtuale

 Questo esempio Mostra come configurare VNet per l'uso del firewall di Azure come server DNS.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

#### <a name="enable-dns-proxy-preview"></a>Abilita proxy DNS (anteprima)

Questo esempio Mostra come abilitare la funzionalità proxy DNS nel firewall di Azure.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Passaggi successivi

[Filtro FQDN nelle regole di rete](fqdn-filtering-network-rules.md)
