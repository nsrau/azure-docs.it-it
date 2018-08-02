---
title: Diagnosticare problemi di filtro del traffico di rete di una macchina virtuale | Microsoft Docs
description: Informazioni su come diagnosticare un problema di filtro del traffico di rete di una macchina virtuale visualizzando le regole di sicurezza valide per una macchina virtuale.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: jdial
ms.openlocfilehash: 67b2babcd19268a61794d123f5aa9780af16976b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364013"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnosticare problemi di filtro del traffico di rete di una macchina virtuale

In questo articolo viene illustrato come diagnosticare un problema di filtro del traffico di rete visualizzando le regole di sicurezza del gruppo di sicurezza (NSG, Network Security Group) valide per una macchina virtuale (VM).

I gruppi di sicurezza di rete consentono di controllare i tipi di traffico che scorrono dentro e fuori le VM. È possibile associare un gruppo di sicurezza di rete a una subnet in una rete virtuale di Azure, a un'interfaccia di rete associata a una VM o a entrambe. Le regole di sicurezza applicate a un'interfaccia di rete sono una combinazione tra le regole di sicurezza presenti nel gruppo di sicurezza associato all'interfaccia di rete e quelle della subnet in cui l'interfaccia di rete si trova. Talvolta le regole in gruppi di sicurezza di rete diversi possono essere in conflitto tra loro e influire sulla connettività di rete della VM. È possibile visualizzare tutte le regole di sicurezza effettive degli NSG applicate nelle interfacce di rete della VM. Se non si ha familiarità con le rete virtuali, le interfacce di rete o i concetti di NSG, vedere [Panoramica di Rete virtuale](virtual-networks-overview.md), [Interfaccia di rete](virtual-network-network-interface.md) e [Panoramica dei gruppi di sicurezza di rete](security-overview.md).

## <a name="scenario"></a>Scenario

Si sta cercando di eseguire la connessione a una VM dalla porta 80 da Internet, ma la connessione non riesce. Per determinare perché non è possibile connettersi dalla porta 80 da Internet, è possibile visualizzare le regole di sicurezza valide per un'interfaccia di rete usando il [portale di Azure](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell) o l'[interfaccia della riga di comando di Azure](#diagnose-using-azure-cli).

I passaggi che seguono presuppongono la disponibilità di una VM per visualizzare le relative regole di sicurezza. In mancanza di una VM esistente, distribuire prima una VM [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) con cui completare le attività descritte in questo articolo. Gli esempi di questo articolo sono per una VM denominata *myVM* con un'interfaccia di rete denominata *myVMVMNic*. L'interfaccia di rete e la VM si trovano in un gruppo di risorse denominato *myResourceGroup* nell'area *Stati Uniti orientali*. Modificare i valori nei passaggi come appropriato per la VM per cui si sta diagnosticando il problema.

## <a name="diagnose-using-azure-portal"></a>Diagnosi tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di Azure che abbia le [autorizzazioni necessarie](virtual-network-network-interface.md#permissions).
2. Nella parte superiore del portale di Azure immettere nella casella di ricerca il nome della VM. Quando il nome della VM viene visualizzato nei risultati della ricerca, selezionarlo.
3. In **IMPOSTAZIONI** selezionare **Rete**, come illustrato nell'immagine seguente:

   ![Visualizzare le regole di sicurezza](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Le regole visualizzate nell'immagine precedente sono per un'interfaccia di rete denominata **myVMVMNic**. Sono presenti **Regole porta in ingresso** per l'interfaccia di rete derivanti da due gruppi di sicurezza di rete diversi:
   
   - **mySubnetNSG**: associato alla subnet in cui si trova l'interfaccia di rete.
   - **myVMNSG**: associato all'interfaccia di rete della macchina virtuale denominata **myVMVMNic**.

   È la regola denominata **DenyAllInBound** a impedire la comunicazione con la VM tramite la porta 80, da Internet, come descritto nello [scenario](#scenario). Nella regola è elencato il valore *0.0.0.0/0* per **ORIGINE**, che include anche Internet. Nessun altra regola con priorità più alta (numero più basso) consente le comunicazioni in ingresso dalla porta 80. Per consentire le comunicazioni verso la VM dalla porta 80 da Internet, vedere [Risolvere il problema](#resolve-a-problem). Per altre informazioni sulle regole di sicurezza e su come vengono applicate in Azure, vedere [Gruppi di sicurezza di rete](security-overview.md).

   Nella parte inferiore dell'immagine è anche possibile visualizzare le **Regole porta in uscita**, in cui sono riportate le regole della porta in uscita per l'interfaccia di rete. Anche se l'immagine include solo quattro regole in ingresso per ogni NSG, un NSG può contenerne molte di più. Nell'immagine viene visualizzato **VirtualNetwork** in **ORIGINE** e **DESTINAZIONE** e **AzureLoadBalancer** in  **ORIGINE**. **VirtualNetwork** e **AzureLoadBalancer** sono [tag di servizio](security-overview.md#service-tags). I tag di servizio rappresentano un gruppo di prefissi di indirizzi IP che consentono di ridurre al minimo la complessità della creazione delle regole di sicurezza.

4. Verificare che la VM sia in esecuzione e selezionare **Regole di sicurezza effettive**, come illustrato nell'immagine precedente, per visualizzare le regole di sicurezza valide, illustrate nell'immagine seguente:

   ![Visualizzare le regole di sicurezza valide](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   Le regole elencate sono le stesse illustrate nel passaggio 3, anche se sono presenti diverse schede per il gruppo di sicurezza di rete associato all'interfaccia di rete e alla subnet. Come si può vedere nell'immagine, vengono visualizzate solo le prime 50 regole. Per scaricare un file con estensione csv che contiene tutte le regole, selezionare **Download**.

   Per verificare i prefissi rappresentati da ogni tag di servizio, selezionare una regola, ad esempio la regola denominata **AllowAzureLoadBalancerInbound**. La figura seguente illustra i prefissi per il tag di servizio **AzureLoadBalancer**:

   ![Visualizzare le regole di sicurezza valide](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Anche se il tag di servizio **AzureLoadBalancer** rappresenta solo un prefisso, altri tag di servizio ne rappresentano diversi.

5. I passaggi precedenti hanno illustrato le regole di sicurezza per l'interfaccia di rete denominata **myVMVMNic**, ma è stato possibile visualizzare anche l'interfaccia di rete denominata **myVMVMNic2** in alcune delle immagini precedenti. La VM di questo esempio ha due interfacce di rete collegate. Le regole di sicurezza valide possono essere diverse per ogni interfaccia di rete.

   Per visualizzare le regole per l'interfaccia di rete **myVMVMNic2**, selezionarla. Come illustrato nell'immagine che segue, l'interfaccia di rete ha le stesse regole associate alla subnet dell'interfaccia di rete **myVMVMNic** perché entrambe si trovano nella stessa subnet. Quando si associa un gruppo di sicurezza di rete a una subnet, le regole del gruppo vengono applicate a tutte le interfacce di rete presenti nella subnet.

   ![Visualizzare le regole di sicurezza](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   A differenza dell'interfaccia di rete **myVMVMNic**, l'interfaccia di rete **myVMVMNic2** non ha un gruppo di sicurezza di rete associato. Ogni interfaccia di rete e subnet può avere un NSG associato oppure nessuno. Il gruppo di sicurezza di rete associato a ogni interfaccia di rete o subnet può essere lo stesso o un altro. Lo stesso gruppo di sicurezza di rete può essere associato a un numero qualsiasi di interfacce di rete e subnet.

Anche se le regole di sicurezza valide sono state visualizzate tramite la macchina virtuale, è possibile visualizzarle anche tramite:
- **Una singola interfaccia di rete**: informazioni su come [visualizzare un'interfaccia di rete](virtual-network-network-interface.md#view-network-interface-settings).
- **Un singolo gruppo di sicurezza di rete**: informazioni su come [visualizzare un gruppo di sicurezza di rete](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnosi tramite PowerShell

È possibile eseguire i comandi seguenti in [Azure Cloud Shell](https://shell.azure.com/powershell) oppure eseguendo PowerShell dal computer. Azure Cloud Shell è una shell interattiva gratuita. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Se si esegue PowerShell dal computer, è necessario il modulo *AzureRM* di PowerShell versione 6.0.1 o successiva. Per trovare la versione installata, eseguire `Get-Module -ListAvailable AzureRM` nel computer. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è necessario eseguire anche `Login-AzureRmAccount` per accedere ad Azure con un account con le [autorizzazioni necessarie](virtual-network-network-interface.md#permissions).

Ottenere le regole di sicurezza valide per un'interfaccia di rete con [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup). L'esempio seguente ottiene le regole di sicurezza valide per un'interfaccia di rete denominata *myVMVMNic* che si trova in un gruppo di risorse denominato *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic interface `
  -ResourceGroupName myResourceGroup
```

L'output viene restituito in formato json. Per informazioni sull'output, vedere [Interpretare l'output del comando](#interpret-command-output).
L'output viene restituito solo se un NSG è associato all'interfaccia di rete, alla subnet in cui si trova l'interfaccia di rete o a entrambe. La VM deve essere in esecuzione. Una VM può avere più interfacce di rete a cui sono applicati vari NSG. Per risolvere il problema, eseguire il comando per ogni interfaccia di rete.

Se si verificano ancora problemi di connettività, vedere [Diagnosi aggiuntiva](#additional-diagnosis) e [Considerazioni](#considerations).

Se non si conosce il nome di un'interfaccia di rete, ma si conosce il nome della VM a cui l'interfaccia di rete è collegata, i comandi seguenti restituiscono gli ID di tutte le interfacce di rete collegate alla VM:

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

L'output che si riceve è simile all'esempio seguente:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

Nell'output precedente il nome dell'interfaccia di rete è *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnosi tramite l'interfaccia della riga di comando di Azure

Se si usano i comandi dell'interfaccia della riga di comando di Azure per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/bash) o tramite l'interfaccia della riga di comando dal computer in uso. Questo articolo richiede l'interfaccia della riga di comando di Azure 2.0.32 o versioni successive. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è necessario eseguire anche `az login` e accedere ad Azure con un account dotato delle [autorizzazioni necessarie](virtual-network-network-interface.md#permissions).

Ottenere le regole di sicurezza valide per un'interfaccia di rete con [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg). L'esempio seguente ottiene le regole di sicurezza valide per un'interfaccia di rete denominata *myVMVMNic* che si trova in un gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

L'output viene restituito in formato json. Per informazioni sull'output, vedere [Interpretare l'output del comando](#interpret-command-output).
L'output viene restituito solo se un NSG è associato all'interfaccia di rete, alla subnet in cui si trova l'interfaccia di rete o a entrambe. La VM deve essere in esecuzione. Una VM può avere più interfacce di rete a cui sono applicati vari NSG. Per risolvere il problema, eseguire il comando per ogni interfaccia di rete.

Se si verificano ancora problemi di connettività, vedere [Diagnosi aggiuntiva](#additional-diagnosis) e [Considerazioni](#considerations).

Se non si conosce il nome di un'interfaccia di rete, ma si conosce il nome della VM a cui l'interfaccia di rete è collegata, i comandi seguenti restituiscono gli ID di tutte le interfacce di rete collegate alla VM:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

Nell'output restituito verranno visualizzate informazioni analoghe all'esempio seguente:

```azurecli
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

Nell'output precedente il nome dell'interfaccia di rete è *myVMVMNic interface*.

## <a name="interpret-command-output"></a>Interpretare l'output del comando

Indipendentemente dall'uso di [PowerShell](#diagnose-using-powershell) o dell'[interfaccia della riga di comando di Azure](#diagnose-using-azure-cli) per diagnosticare il problema, l'output restituito contiene le informazioni seguenti:

- **NetworkSecurityGroup**: l'ID del gruppo di sicurezza di rete.
- **Association**: valore che determina se il gruppo di sicurezza di rete è associato a un elemento *NetworkInterface* o *Subnet*. Se un NSG è associato a entrambi, l'output viene restituito con **NetworkSecurityGroup**, **Association** e **EffectiveSecurityRules**, per ogni NSG. Se il gruppo di sicurezza di rete viene associato o se viene eliminata la sua associazione appena prima di eseguire il comando per visualizzare le regole di sicurezza, potrebbe essere necessario attendere qualche secondo prima che la modifica sia effettiva nell'output del comando.
- **EffectiveSecurityRules**: una spiegazione di ogni proprietà dettagliata in [Creare una regola di sicurezza](manage-network-security-group.md#create-a-security-rule). I nomi delle regole preceduti da *defaultSecurityRules/* indicano regole di sicurezza predefinite che esistono in ogni NSG. I nomi delle regole preceduti da *securityRules/* indicano le regole che sono state create. Le regole che specificano un [tag del servizio](security-overview.md#service-tags), come ad esempio **Internet**, **VirtualNetwork** e **AzureLoadBalancer** per le proprietà **destinationAddressPrefix** oppure **sourceAddressPrefix**, hanno anche valori per la proprietà **expandedDestinationAddressPrefix**. La proprietà **expandedDestinationAddressPrefix** include tutti i prefissi di indirizzo rappresentati dal tag di servizio.

Se vengono visualizzate regole duplicate nell'output, il motivo è che un gruppo di sicurezza di rete è associato sia all'interfaccia di rete che alla subnet. Entrambi i gruppi di sicurezza di rete hanno le stesse regole predefinite e potrebbero avere altre regole duplicate, se sono state create le stesse regole personalizzate in entrambi gli NSG.

È la regola denominata **defaultSecurityRules/DenyAllInBound** a impedire la comunicazione in ingresso con la VM tramite la porta 80, da Internet, come descritto nello [scenario](#scenario). Nessun altra regola con priorità più alta (numero più basso) consente le comunicazioni in ingresso dalla porta 80 da Internet.

## <a name="resolve-a-problem"></a>Risolvere il problema

Indipendentemente dall'uso del [portale di Azure](#diagnose-using-azure-portal), di [PowerShell](#diagnose-using-powershell), o dell'[interfaccia della riga di comando di Azure](#diagnose-using-azure-cli) per diagnosticare il problema presentato nello [scenario](#scenario) di questo articolo, la soluzione consiste nel creare una regola di sicurezza di rete con le proprietà seguenti:

| Proprietà                | Valore                                                                              |
|---------                |---------                                                                           |
| Sorgente                  | Qualsiasi                                                                                |
| Intervalli di porte di origine      | Qualsiasi                                                                                |
| Destination             | L'indirizzo IP della VM, un intervallo di indirizzi IP o tutti gli indirizzi nella subnet. |
| Intervalli di porte di destinazione | 80                                                                                 |
| Protocollo                | TCP                                                                                |
| Azione                  | CONSENTI                                                                              |
| Priorità                | 100                                                                                |
| NOME                    | Allow-HTTP-All                                                                     |

Dopo aver creato la regola, la porta 80 può ricevere comunicazioni in ingresso da Internet, perché la priorità della regola è superiore rispetto alla regola di sicurezza predefinita denominata *DenyAllInBound*, che impedisce il traffico. Informazioni su come [creare una regola di sicurezza](manage-network-security-group.md#create-a-security-rule). Se più NSG sono associati sia all'interfaccia di rete che alla subnet, è necessario creare la stessa regola in tutti gli NSG.

Quando Azure elabora il traffico in ingresso, elabora le regole nel gruppo di sicurezza di rete associato alla subnet, se presente, e quindi elabora le regole nel gruppo di sicurezza di rete associato all'interfaccia di rete. Se esiste un NSG associato all'interfaccia di rete e alla subnet, perché il traffico possa raggiungere la VM, la porta deve essere aperta in entrambi gli NSG. Per semplificare la soluzione dei problemi di gestione e comunicazione, è consigliabile associare un NSG a una subnet, anziché a interfacce di rete singole. Se le macchine virtuali all'interno di una subnet necessitano di regole di sicurezza diverse, è possibile rendere le interfacce di rete membri di un gruppo di sicurezza dell'applicazione e specificare un gruppo di sicurezza dell'applicazione come origine e destinazione di una regola di sicurezza. Per altre informazioni, vedere [Gruppi di sicurezza delle applicazioni](security-overview.md#application-security-groups).

Se si verificano ancora problemi di comunicazione, vedere [Considerazioni](#considerations) e [Diagnosi aggiuntiva](#additional-dignosis).

## <a name="considerations"></a>Considerazioni

Durante la risoluzione dei problemi di connettività, tenere presente quanto segue:

* Le regole dei gruppi di sicurezza di rete predefinite bloccano le comunicazioni in ingresso da Internet e consentono solo il traffico di rete in ingresso dalla rete virtuale. Per consentire il traffico in ingresso da Internet, aggiungere regole di sicurezza con una priorità più alta rispetto alle regole predefinite. Per altre informazioni, vedere [Regole di sicurezza predefinite](security-overview.md#default-security-rules), o [Creare una regola di sicurezza](manage-network-security-group.md#create-a-security-rule).
* Se si hanno reti virtuali con peering, per impostazione predefinita il tag **VIRTUAL_NETWORK** si espande automaticamente in modo da includere i prefissi delle reti virtuali con peering. Per risolvere problemi correlati alle reti virtuali con peering, è possibile visualizzare i prefissi nell'elenco **ExpandedAddressPrefix**. Per altre informazioni, vedere [Peering di rete virtuale](virtual-network-peering-overview.md) e [Tag di servizio](security-overview.md#service-tags).
* Le regole di sicurezza valide vengono visualizzate solo per un'interfaccia di rete se è presente un NSG associato all'interfaccia di rete della VM e/o della subnet, e se la VM è in esecuzione.
* Se non esistono NSG associati all'interfaccia di rete o alla subnet e se alla VM è assegnato un [indirizzo IP pubblico](virtual-network-public-ip-address.md), tutte le porte vengono aperte per l'accesso in ingresso e in uscita da qualunque percorso. Se la VM ha un indirizzo IP pubblico, è consigliabile associare un NSG alla subnet dell'interfaccia di rete.

## <a name="additional-diagnosis"></a>Diagnosi aggiuntiva

* Per eseguire un rapido test per determinare se il traffico in ingresso o in uscita da una macchina virtuale è consentito, usare la funzionalità [Verifica flusso IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) di Azure Network Watcher. Il controllo del flusso IP indica se il traffico è consentito o bloccato. Se viene bloccato, la verifica del flusso IP indica quale regola di sicurezza sta bloccando il traffico.
* Se non sono presenti regole di sicurezza che causano un errore di connettività della VM, il problema potrebbe essere dovuto a:
  * Software del firewall in esecuzione all'interno del sistema operativo della VM
  * Route configurate per appliance virtuali o traffico locale. Il traffico Internet può essere reindirizzato alla rete locale tramite il [tunneling forzato](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se si esegue il tunneling forzato del traffico Internet in un'appliance virtuale o in locale, potrebbe non essere possibile connettersi alla VM da Internet. Per informazioni su come diagnosticare i problemi di route che possono impedire il flusso del traffico in uscita dalla VM, vedere [Diagnose a virtual machine network traffic routing problem](diagnose-network-routing-problem.md) (Diagnosticare problemi di routing del traffico di rete di una macchina virtuale).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su tutte le attività, le proprietà e le impostazioni per un [gruppo di sicurezza di rete](manage-network-security-group.md#work-with-network-security-groups) e per le [regole di sicurezza](manage-network-security-group.md#work-with-security-rules).
- Informazioni sulle [regole di sicurezza predefinite](security-overview.md#default-security-rules), i [tag di servizio](security-overview.md#service-tags), e su [come Azure elabora le regole di sicurezza per il traffico in ingresso e in uscita](security-overview.md#network-security-groups) per una VM.
