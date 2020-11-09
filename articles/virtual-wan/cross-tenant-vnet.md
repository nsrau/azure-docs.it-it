---
title: 'Connettere reti virtuali tra tenant a un hub: PowerShell'
titleSuffix: Azure Virtual WAN
description: Questo articolo consente di connettere reti virtuali tra tenant a un hub virtuale usando PowerShell.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: c49a85c71c9b877be7e143f5caf27dc307fe0c12
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381270"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>Connettere reti virtuali tra tenant a un hub WAN virtuale

Questo articolo consente di usare la rete WAN virtuale per connettere una VNet a un hub virtuale in un tenant diverso. Questa architettura è utile se si dispone di carichi di lavoro client che devono essere connessi alla stessa rete, ma che si trovano in tenant diversi. Come illustrato nel diagramma seguente, ad esempio, è possibile connettere un VNet non-Contoso (il tenant remoto) a un hub virtuale di Contoso (tenant padre).

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="Configurare la configurazione del routing" :::

In questo articolo vengono illustrate le operazioni seguenti:

* Aggiungere un altro tenant come collaboratore nella sottoscrizione di Azure.
* Connettere un VNet tra tenant a un hub virtuale.

I passaggi per questa configurazione vengono eseguiti usando una combinazione delle portale di Azure e di PowerShell. Tuttavia, la funzionalità stessa è disponibile solo in PowerShell e nell'interfaccia della riga di comando.

## <a name="before-you-begin"></a>Prima di iniziare

### <a name="prerequisites"></a>Prerequisiti

Per usare la procedura descritta in questo articolo, è necessario che nell'ambiente sia già stata configurata la configurazione seguente:

* Una rete WAN virtuale e un hub virtuale nella sottoscrizione padre.
* Una rete virtuale configurata in una sottoscrizione in un tenant diverso (remoto).
* Assicurarsi che lo spazio di indirizzi della VNet nel tenant remoto non si sovrappongano a qualsiasi altro spazio degli indirizzi all'interno di altri reti virtuali già connessi all'hub virtuale padre.

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>Assegnare le autorizzazioni

Per consentire alla sottoscrizione padre con l'hub virtuale di modificare e accedere alle reti virtuali nel tenant remoto, è necessario assegnare le autorizzazioni di **collaboratore** alla sottoscrizione padre dalla sottoscrizione del tenant remoto.

1. Aggiungere l'assegnazione di ruolo **collaboratore** all'account padre (quello con l'hub WAN virtuale). Per assegnare questo ruolo, è possibile usare PowerShell o la portale di Azure. Vedere gli articoli seguenti per **aggiungere o rimuovere assegnazioni di ruoli** per i passaggi:

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [Portale](../role-based-access-control/role-assignments-portal.md)

1. Successivamente, aggiungere la sottoscrizione tenant remoto e la sottoscrizione tenant padre alla sessione corrente di PowerShell. Eseguire il comando seguente. Se è stato eseguito l'accesso all'elemento padre, è sufficiente eseguire il comando per il tenant remoto.

   ```azurepowershell-interactive
   Add-AzAccount -SubscriptionId "xxxxx-b34a-4df9-9451-4402dcaecc5b"
   ```

1. Verificare che l'assegnazione di ruolo abbia esito positivo eseguendo l'accesso Azure PowerShell usando le credenziali padre ed eseguendo il comando seguente:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Se le autorizzazioni sono state propagate correttamente all'elemento padre e sono state aggiunte alla sessione, la sottoscrizione di proprietà del tenant remoto viene visualizzata nell'output del comando.

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>Connetti VNet all'hub

Nei passaggi seguenti si passerà tra il contesto delle due sottoscrizioni quando si collega la rete virtuale all'hub virtuale. Sostituire i valori di esempio per riflettere il proprio ambiente.

1. Assicurarsi di trovarsi nel contesto dell'account remoto eseguendo il comando seguente:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[remote ID]"
   ```

1. Creare una variabile locale per archiviare i metadati della rete virtuale che si vuole connettere all'hub.

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[vnet name]" -ResourceGroupName "[resource group name]"
   ```

1. Tornare all'account padre.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[parent ID]"
   ```

1. Connettere il VNet all'hub.

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[parent resource group name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $[local variable name]
   ```

1. È possibile visualizzare la nuova connessione in PowerShell o nel portale di Azure.

   * **PowerShell:** Se la connessione è stata creata correttamente, i metadati della connessione appena creata vengono visualizzati nella console di PowerShell.
   * **Portale di Azure:** Passare a hub virtuale, **connettività-> connessioni di rete virtuale**. È possibile visualizzare il puntatore alla connessione. Per visualizzare la risorsa effettiva, sono necessarie le autorizzazioni appropriate.
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>Risoluzione dei problemi

* Verificare che i metadati in $remote (nella [sezione](#connect)precedente) corrispondano alle informazioni della portale di Azure.
* È possibile verificare le autorizzazioni usando le impostazioni IAM del gruppo di risorse tenant remoto oppure usando Azure PowerShell comandi (Get-AzSubscription).
* Verificare che le virgolette siano incluse tra i nomi dei gruppi di risorse o altre variabili specifiche dell'ambiente, ad esempio "VirtualHub1" o "VirtualNetwork1").

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla rete WAN virtuale, vedere:

* [Domande frequenti](virtual-wan-faq.md) sulla rete WAN virtuale
