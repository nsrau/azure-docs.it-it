---
title: Creare, modificare o eliminare un prefisso dell'indirizzo IP pubblico di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su come creare, modificare o eliminare un prefisso dell'indirizzo IP pubblico.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: 4207698c57b907cf60fd860bc409c8f8d5a4c565
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015288"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Creare, modificare o eliminare un prefisso dell'indirizzo IP pubblico

Informazioni su un prefisso dell'indirizzo IP pubblico e su come crearlo, modificarlo ed eliminarlo. Un prefisso dell'indirizzo IP pubblico è un intervallo contiguo di indirizzi basato sul numero di indirizzi IP pubblici specificati. Gli indirizzi sono assegnati alla sottoscrizione. Quando si crea una risorsa di indirizzo IP pubblico, è possibile assegnare un indirizzo IP pubblico statico del prefisso e associarlo alle macchine virtuali, caricare il bilanciamento del carico o altre risorse per abilitare la connettività internet. Se non si ha familiarità con i prefissi di indirizzi IP pubblici, vedere [Panoramica del prefisso di indirizzo IP pubblico](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Prima di iniziare

> [!IMPORTANT]
> Il prefisso IP pubblico si trova in anteprima pubblica in aree limitate. È possibile avere [informazioni su cosa significa essere in anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Il prefisso IP pubblico è attualmente disponibile in: Stati Uniti centro-occidentali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centrali, Europa settentrionale, Europa occidentale e Asia sud-orientale. Per un elenco aggiornato delle aree, consultare [aggiornamenti di Azure](https://azure.microsoft.com/updates/?product=virtual-network).

Prima di completare i passaggi di qualsiasi sezione di questo articolo, eseguire le attività seguenti:

- Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usa il portale, aprire https://aka.ms/publicipprefixportal e accedere con l'account Azure.
- Se si usano i comandi di PowerShell per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/powershell) o tramite PowerShell dal computer in uso. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Questa esercitazione richiede il modulo AzureRm.Network PowerShell 6.3.1 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM.Network` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/AzureRm.Network.6.3.1). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.
- Se si usano i comandi dell'interfaccia della riga di comando di Azure per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/bash) o tramite l'interfaccia della riga di comando dal computer in uso. Questa esercitazione richiede la versione dell'interfaccia della riga di comando di Azure 2.0.41 o versioni successive. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è anche necessario eseguire `az login` per creare una connessione con Azure.

L'account con cui si accede o con cui ci si collega ad Azure deve essere assegnato al ruolo [collaboratore di rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le operazioni appropriate elencate nelle [Autorizzazioni](#permissions).

I prefissi di indirizzi IP pubblici prevedono un addebito. Per informazioni dettagliate, vedere [prezzi](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Creare un prefisso di indirizzo IP pubblico

1. Nell'angolo superiore sinistro del portale selezionare **+ Crea una risorsa**.
2. Immettere *prefisso di indirizzo ip pubblico* nella casella *Cerca nel Marketplace*. Selezionare il **prefisso di indirizzo IP pubblico** visualizzato nei risultati della ricerca.
3. In **prefisso di indirizzo IP pubblico**, selezionare **Creare**.
4. In **Creare prefisso di indirizzo IP pubblico** immettere o selezionare i valori per le seguenti impostazioni, quindi selezionare **Creare**:

   |Impostazione|Obbligatorio?|Dettagli|
   |---|---|---|
   |Sottoscrizione|Yes|Deve essere inclusa nella stessa [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) della risorsa cui si vuole associare l'indirizzo IP pubblico.|
   |Gruppo di risorse|Yes|Può trovarsi nello stesso [gruppo di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) della risorsa cui si vuole associare l'indirizzo IP pubblico oppure in un gruppo di risorse diverso.|
   |NOME|Yes|Il nome deve essere univoco all'interno del gruppo di risorse selezionato.|
   |Region|Yes|Deve trovarsi nella stessa [area](https://azure.microsoft.com/regions)degli indirizzi IP pubblici che vengono poi assegnati nell'intervallo. Il prefisso è attualmente in anteprima in: Stati Uniti centro-occidentali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centrali, Europa settentrionale, Europa occidentale e Asia sud-orientale.|
   |Dimensione prefisso|Yes| Le dimensioni del prefisso necessario. Indirizzi IP A/28 o 16 è il valore predefinito. 

**Comandi**


|Strumento|Comando|
|---|---|
|CLI|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[New-AzureRmPublicIpPrefix](/powershell/module/azurerm.network/new-azurermpublicipprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Creare un indirizzo IP pubblico statico di un prefisso
Dopo aver creato un prefisso, è necessario creare gli indirizzi IP statici di un prefisso. A questo scopo, eseguire i seguenti passaggi.

1. Nella casella che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *prefisso di indirizzo ip pubblico*. Selezionare la voce **prefissi di indirizzi IP pubblici** visualizzata nei risultati della ricerca.
2. Selezionare il prefisso del quale si vogliono creare indirizzi IP pubblici.
3. Quando viene visualizzato nei risultati della ricerca, selezionarlo e fare clic su **+ Aggiungi indirizzo IP** nella sezione Panoramica. Nel caso in cui ciò non fosse visualizzato, verificare che si stia usando il collegamento corretto per l'anteprima: https://aka.ms/publicipprefixportal
4. In **Crea indirizzi IP pubblici** immettere o selezionare i valori per le impostazioni seguenti. Poiché un prefisso è per lo SKU Standard, IPv4 e statico, è sufficiente fornire le informazioni seguenti:

   |Impostazione|Obbligatorio?|Dettagli|
    |---|---|---|
    |NOME|Yes|Il nome dell'indirizzo IP pubblico deve essere univoco all'interno del gruppo delle risorse selezionato.|
   |Timeout di inattività (minuti)|No |Il numero di minuti in cui la connessione TCP o HTTP resta aperta senza affidarsi ai client per l'invio di messaggi Keep-Alive. |
   |Etichetta del nome DNS|No |Deve essere univoca all'interno dell'area di Azure nella quale viene creato il nome (incluse tutte le sottoscrizioni e tutti i clienti). Azure registra automaticamente il nome e l'indirizzo IP nel proprio DNS, consentendo la connessione a una risorsa con tale nome. Azure accoda un subnet predefinito, ad esempio *location.cloudapp.azure.com* (dove location è il percorso selezionato) del nome fornito, per creare il nome del DNS completo.Per altre informazioni, vedere [Usare DNS di Azure con un indirizzo IP pubblico di Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

## <a name="view-or-delete-a-prefix"></a>Visualizza o elimina un prefisso

1. Nella casella che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *prefisso di indirizzo ip pubblico*. Selezionare la voce **prefissi di indirizzi IP pubblici** visualizzata nei risultati della ricerca.
2. Selezionare il nome del prefisso di indirizzo IP pubblico da visualizzare, eliminare dall'elenco o di cui modificare le impostazioni.
3. Completare una delle seguenti opzioni a seconda che si desideri visualizzare, eliminare o modificare il prefisso dell'indirizzo IP pubblico.
    - **Visualizza**: la sezione **Panoramica** mostra le impostazioni chiave per il prefisso dell'indirizzo IP pubblico, ad esempio prefisso.
    - **Elimina**: per eliminare il prefisso dell'indirizzo IP pubblico, selezionare **Elimina** nella sezione **Panoramica**. Se gli indirizzi all'interno del prefisso sono associati a risorse di indirizzo IP pubblico, è innanzitutto necessario eliminare le risorse di indirizzo IP pubblico. Vedere [eliminare un indirizzo IP pubblico](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address).

**Comandi**

|Strumento|Comando|
|---|---|
|CLI|[az network public-ip-list](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) per elencare gli indirizzi IP pubblici, [az network public-ip-show](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) per visualizzare le impostazioni; [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) per aggiornare; [az network public-ip delete](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) per eliminare|
|PowerShell|[Get-AzureRmPublicIpPrefix](/powershell/module/azurerm.network/get-azurermpublicipprefix) per recuperare un oggetto indirizzo IP pubblico e visualizzarne le impostazioni, [Set-AzureRmPublicIpPrefix](/powershell/module/azurerm.network/set-azurermpublicipprefix) per aggiornare le impostazioni; [Remove-AzureRmPublicIpPrefix](/powershell/module/azurerm.network/remove-azurermpublicipprefix) per eliminare|

## <a name="permissions"></a>Autorizzazioni

Per eseguire attività nei prefissi degli indirizzi IP pubblici, l'account deve essere assegnato al ruolo [collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un ruolo [personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le operazioni appropriate elencate nella tabella seguente:

| Azione                                                                   | NOME                                                           |
| ---------                                                                | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Leggere un prefisso di indirizzo IP pubblico                                |
| Microsoft.Network/publicIPPrefixes/write                          | Creare o aggiornare un prefisso di indirizzo IP pubblico                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Eliminare un prefisso di indirizzo IP pubblico                              |
|Microsoft.Network/publicIPPrefixes/join/action | Creare un prefisso di indirizzo IP pubblico |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sugli scenari e i vantaggi dell'uso di un [prefisso IP pubblico](public-ip-address-prefix.md)
