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
ms.date: 05/13/2019
ms.author: anavin
ms.openlocfilehash: 26d8ee34c735cab8f1033a9aad897ec0b1bed524
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952692"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Creare, modificare o eliminare un prefisso dell'indirizzo IP pubblico

Informazioni su un prefisso dell'indirizzo IP pubblico e su come crearlo, modificarlo ed eliminarlo. Un prefisso dell'indirizzo IP pubblico è un intervallo contiguo di indirizzi basato sul numero di indirizzi IP pubblici specificati. Gli indirizzi sono assegnati alla sottoscrizione. Quando si crea una risorsa di indirizzo IP pubblico, è possibile assegnare un indirizzo IP pubblico statico dal prefisso e associare l'indirizzo per le macchine virtuali, servizi di bilanciamento del carico o altre risorse, per abilitare la connettività internet. Se non si ha familiarità con i prefissi di indirizzi IP pubblici, vedere [Panoramica del prefisso di indirizzo IP pubblico](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prima di completare i passaggi di qualsiasi sezione di questo articolo, eseguire le attività seguenti:

- Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usa il portale, aprire https://portal.azure.com e accedere con l'account Azure.
- Se si usano i comandi di PowerShell per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/powershell) o tramite PowerShell dal computer in uso. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Questa esercitazione richiede il modulo Azure PowerShell 1.0.0 o versioni successive. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.
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
   |Sottoscrizione|Sì|Deve essere inclusa nella stessa [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) della risorsa cui si vuole associare l'indirizzo IP pubblico.|
   |Gruppo di risorse|Sì|Può trovarsi nello stesso [gruppo di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) della risorsa cui si vuole associare l'indirizzo IP pubblico oppure in un gruppo di risorse diverso.|
   |NOME|Sì|Il nome deve essere univoco all'interno del gruppo di risorse selezionato.|
   |Region|Sì|Deve trovarsi nella stessa [area](https://azure.microsoft.com/regions)degli indirizzi IP pubblici che vengono poi assegnati nell'intervallo.|
   |Dimensioni del prefisso|Sì| Le dimensioni del prefisso necessario. Indirizzi IP A/28 o 16 è il valore predefinito.

**Comandi**

|Tool|Comando|
|---|---|
|Interfaccia della riga di comando|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Creare un indirizzo IP pubblico statico di un prefisso
Dopo aver creato un prefisso, è necessario creare gli indirizzi IP statici di un prefisso. A questo scopo, eseguire i seguenti passaggi.

1. Nella casella che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *prefisso di indirizzo ip pubblico*. Selezionare la voce **prefissi di indirizzi IP pubblici** visualizzata nei risultati della ricerca.
2. Selezionare il prefisso dal quale si vogliono creare indirizzi IP pubblici.
3. Quando viene visualizzato nei risultati della ricerca, selezionarlo e fare clic su **+ Aggiungi indirizzo IP** nella sezione Panoramica.
4. In **Crea indirizzi IP pubblici** immettere o selezionare i valori per le impostazioni seguenti. Poiché un prefisso è per lo SKU Standard, IPv4 e statico, è sufficiente fornire le informazioni seguenti:

   |Impostazione|Obbligatorio?|Dettagli|
   |---|---|---|
   |NOME|Sì|Il nome dell'indirizzo IP pubblico deve essere univoco all'interno del gruppo delle risorse selezionato.|
   |Timeout di inattività (minuti)|N.|Il numero di minuti in cui la connessione TCP o HTTP resta aperta senza affidarsi ai client per l'invio di messaggi Keep-Alive. |
   |Etichetta del nome DNS|N.|Deve essere univoca all'interno dell'area di Azure nella quale viene creato il nome (incluse tutte le sottoscrizioni e tutti i clienti). Azure registra automaticamente il nome e l'indirizzo IP nel proprio DNS, consentendo la connessione a una risorsa con tale nome. Azure accoda un subnet predefinito, ad esempio *location.cloudapp.azure.com* (dove location è il percorso selezionato) del nome fornito, per creare il nome del DNS completo.Per altre informazioni, vedere [Usare DNS di Azure con un indirizzo IP pubblico di Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

In alternativa è possibile usare l'interfaccia della riga di comando e Powershell i comandi seguenti con--public-ip-prefix (CLI) e i parametri - PublicIpPrefix (PS), per creare un indirizzo IP pubblico risorsa di indirizzo. 

|Tool|Comando|
|---|---|
|Interfaccia della riga di comando|[az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=azps-2.0.0)|

## <a name="view-or-delete-a-prefix"></a>Visualizza o elimina un prefisso

1. Nella casella che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *prefisso di indirizzo ip pubblico*. Selezionare la voce **prefissi di indirizzi IP pubblici** visualizzata nei risultati della ricerca.
2. Selezionare il nome del prefisso di indirizzo IP pubblico da visualizzare, eliminare dall'elenco o di cui modificare le impostazioni.
3. Completare una delle seguenti opzioni a seconda che si desideri visualizzare, eliminare o modificare il prefisso dell'indirizzo IP pubblico.
   - **Visualizza**: la sezione **Panoramica** mostra le impostazioni chiave per il prefisso dell'indirizzo IP pubblico, ad esempio prefisso.
   - **Eliminazione**: per eliminare il prefisso dell'indirizzo IP pubblico, selezionare **Elimina** nella sezione **Panoramica**. Se gli indirizzi all'interno del prefisso sono associati a risorse di indirizzo IP pubblico, è innanzitutto necessario eliminare le risorse di indirizzo IP pubblico. Vedere [eliminare un indirizzo IP pubblico](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address).

**Comandi**

|Tool|Comando|
|---|---|
|Interfaccia della riga di comando|[az network public-ip-list](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) per elencare gli indirizzi IP pubblici, [az network public-ip-show](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) per visualizzare le impostazioni; [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) per aggiornare; [az network public-ip delete](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) per eliminare|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) per recuperare un oggetto indirizzo IP pubblico e visualizzarne le impostazioni [Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) per aggiornare le impostazioni. [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) da eliminare|

## <a name="permissions"></a>Autorizzazioni

Per eseguire attività nei prefissi degli indirizzi IP pubblici, l'account deve essere assegnato al ruolo [collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un ruolo [personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le operazioni appropriate elencate nella tabella seguente:

| Azione                                                            | NOME                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Leggere un prefisso di indirizzo IP pubblico                                |
| Microsoft.Network/publicIPPrefixes/write                          | Creare o aggiornare un prefisso di indirizzo IP pubblico                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Eliminare un prefisso di indirizzo IP pubblico                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Creare un prefisso di indirizzo IP pubblico |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sugli scenari e i vantaggi dell'uso di un [prefisso IP pubblico](public-ip-address-prefix.md)
