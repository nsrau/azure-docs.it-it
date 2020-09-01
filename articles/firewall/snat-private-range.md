---
title: Intervalli di indirizzi IP privati SNAT del firewall di Azure
description: È possibile configurare gli intervalli di indirizzi IP per SNAT.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 08/31/2020
ms.author: victorh
ms.openlocfilehash: 272f5b747efbc3776b1b2ba7c3546ade717c2452
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231368"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Intervalli di indirizzi IP privati SNAT del firewall di Azure

Il firewall di Azure fornisce SNAT automatici per tutto il traffico in uscita agli indirizzi IP pubblici. Per impostazione predefinita, il firewall di Azure non SNAT con le regole di rete quando l'indirizzo IP di destinazione si trova in un intervallo di indirizzi IP privati per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Le regole dell'applicazione vengono sempre applicate usando un [proxy trasparente](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) indipendentemente dall'indirizzo IP di destinazione.

Questa logica funziona correttamente quando si instrada il traffico direttamente a Internet. Tuttavia, se è stato abilitato il [tunneling forzato](forced-tunneling.md), il traffico associato a Internet viene inviato tramite SNAT a uno degli indirizzi IP privati del firewall in AzureFirewallSubnet, nascondendo l'origine dal firewall locale.

Se l'organizzazione usa un intervallo di indirizzi IP pubblici per le reti private, Firewall di Azure invierà il traffico tramite SNAT a uno degli indirizzi IP privati firewall in AzureFirewallSubnet. Tuttavia, è possibile configurare il firewall di Azure in modo che **non** SNAT l'intervallo di indirizzi IP pubblici. Ad esempio, per specificare un singolo indirizzo IP, è possibile specificarlo come segue: `192.168.1.10` . Per specificare un intervallo di indirizzi IP, è possibile specificarlo come segue: `192.168.1.0/24` .

Per configurare il firewall di Azure in modo che non SNAT mai indipendentemente dall'indirizzo IP di destinazione, usare **0.0.0.0/0** come intervallo di indirizzi IP privati. Con questa configurazione, il firewall di Azure non può mai instradare il traffico direttamente a Internet. Per configurare il firewall in modo che sia sempre SNAT indipendentemente dall'indirizzo di destinazione, usare **255.255.255.255/32** come intervallo di indirizzi IP privati.

> [!IMPORTANT]
> Se si desidera specificare gli intervalli di indirizzi IP privati e mantenere gli intervalli di indirizzi IANA RFC 1918 predefiniti, assicurarsi che nell'elenco personalizzato sia ancora incluso l'intervallo IANA RFC 1918. 

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Configurare gli intervalli di indirizzi IP privati SNAT-Azure PowerShell

È possibile usare Azure PowerShell per specificare gli intervalli di indirizzi IP privati per il firewall.

### <a name="new-firewall"></a>Nuovo firewall

Per un nuovo firewall, il Azure PowerShell comando è:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges viene espansa alle impostazioni predefinite correnti nel firewall di Azure mentre gli altri intervalli vengono aggiunti. Per mantenere il valore predefinito di IANAPrivateRanges nella specifica dell'intervallo privato, è necessario che rimanga nella `PrivateRange` specifica, come illustrato negli esempi seguenti.

Per ulteriori informazioni, vedere [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Firewall esistente

Per configurare un firewall esistente, usare i comandi Azure PowerShell seguenti:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Modelli

È possibile aggiungere quanto segue alla `additionalProperties` sezione:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Configurare gli intervalli di indirizzi IP privati SNAT-portale di Azure

È possibile usare la portale di Azure per specificare gli intervalli di indirizzi IP privati per il firewall.

1. Selezionare il gruppo di risorse e quindi selezionare il firewall.
2. Nella pagina **Panoramica** , **intervalli di indirizzi IP privati**, selezionare il valore predefinito **IANA RFC 1918**.

   Verrà visualizzata la pagina **modifica prefissi IP privati** :

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Modificare i prefissi IP privati":::

1. Per impostazione predefinita, è configurato **IANAPrivateRanges** .
2. Modificare gli intervalli di indirizzi IP privati per l'ambiente in uso e quindi selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [tunneling forzato del firewall di Azure](forced-tunneling.md).