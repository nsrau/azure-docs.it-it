---
title: Gestire una connessione all'endpoint privato in Azure
description: Informazioni su come gestire le connessioni agli endpoint privati in Azure
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 012b236e997ef9144eaab43862f5f4dd2b324fff
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104640"
---
# <a name="manage-a-private-endpoint-connection"></a>Gestire una connessione all'endpoint privato
Il collegamento privato di Azure funziona in un modello di flusso di chiamate di approvazione, in cui il consumer del servizio di collegamento privato può richiedere una connessione al provider di servizi per l'utilizzo del servizio. Il provider di servizi può quindi decidere se consentire al consumer di connettersi o meno. Il collegamento privato di Azure consente ai provider di servizi di gestire la connessione all'endpoint privato sulle risorse. Questo articolo fornisce istruzioni su come gestire le connessioni agli endpoint privati.

![Gestisci endpoint privati](media/manage-private-endpoint/manage-private-endpoint.png)

Esistono due metodi di approvazione della connessione da cui un consumer del servizio di collegamento privato può scegliere:
- **Automatico**: Se l'utente del servizio dispone di autorizzazioni RBAC per la risorsa del provider di servizi, il consumer può scegliere il metodo di approvazione automatica. In questo caso, quando la richiesta raggiunge la risorsa del provider di servizi, non è richiesta alcuna azione da parte del provider di servizi e la connessione viene approvata automaticamente. 
- **Manuale**: Al contrario, se l'utente del servizio non ha autorizzazioni RBAC per la risorsa del provider di servizi, il consumer può scegliere il metodo di approvazione manuale. In questo caso, la richiesta di connessione viene visualizzata nelle risorse del servizio come **in sospeso**. Il provider di servizi deve approvare manualmente la richiesta prima che sia possibile stabilire le connessioni. Nei casi manuali, il consumer del servizio può inoltre specificare un messaggio con la richiesta per fornire un contesto maggiore al provider di servizi. Il provider di servizi può scegliere tra le opzioni seguenti per tutte le connessioni agli endpoint privati: **Approva**, **rifiuta**, **Rimuovi**.

La tabella seguente illustra le varie azioni del provider di servizi e gli Stati di connessione risultanti per gli endpoint privati.  Il provider di servizi può anche modificare lo stato di connessione della connessione all'endpoint privato in un secondo momento senza l'intervento dell'utente. L'azione aggiornerà lo stato dell'endpoint sul lato consumer. 


|Azione del provider di servizi   |Stato dell'endpoint privato del consumer del servizio   |Descrizione   |
|---------|---------|---------|
|Nessuna    |    In sospeso     |    La connessione viene creata manualmente ed è in attesa di approvazione da parte del proprietario della risorsa di collegamento privato.       |
|Approva    |  Approvata       |  La connessione è stata approvata automaticamente o manualmente ed è pronta per essere usata.     |
|Rifiuta     | Rifiutato        | La connessione è stata rifiutata dal proprietario della risorsa di collegamento privato.        |
|Remove    |  Disconnesso       | La connessione è stata rimossa dal proprietario della risorsa di collegamento privato, l'endpoint privato diventa informativo e deve essere eliminato per la pulizia.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Gestire le connessioni a endpoint privati nelle risorse di Azure PaaS
Il portale è il metodo preferito per la gestione delle connessioni agli endpoint privati nelle risorse di Azure PaaS. Attualmente non è disponibile il supporto per PowerShell/CLI per la gestione delle connessioni nelle risorse di Azure PaaS.
1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Passare a private Link Center.
3. In **risorse**selezionare il tipo di risorsa che si vuole gestire per le connessioni all'endpoint privato.
4. Per ogni tipo di risorsa, è possibile visualizzare il numero di connessioni a endpoint privati associate. È possibile filtrare le risorse in base alle esigenze.
5. Selezionare le connessioni all'endpoint privato.  In connessioni elencate selezionare la connessione che si desidera gestire. 
6. È possibile modificare lo stato della connessione selezionando una delle opzioni nella parte superiore.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Gestire le connessioni a endpoint privati in un servizio di collegamento privato di proprietà di clienti/partner

Azure PowerShell e l'interfaccia della riga di comando di Azure sono i metodi preferiti per la gestione delle connessioni agli endpoint privati nei servizi dei partner Microsoft o dei clienti. Attualmente non è disponibile alcun supporto per il portale per la gestione delle connessioni in un servizio di collegamento privato.  
 
### <a name="powershell"></a>PowerShell 
  
Usare i comandi di PowerShell seguenti per gestire le connessioni a endpoint privati.  
#### <a name="get-private-link-connection-states"></a>Ottenere gli Stati di connessione al collegamento privato 
Usare il `Get-AzPrivateLinkService` cmdlet per ottenere le connessioni all'endpoint privato e i relativi stati.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Approva una connessione all'endpoint privato 
 
Usare il `Approve-AzPrivateEndpointConnection` cmdlet per approvare una connessione all'endpoint privato. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Nega connessione all'endpoint privato 
 
Usare il `Deny-AzPrivateEndpointConnection` cmdlet per rifiutare una connessione all'endpoint privato. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Rimuovere la connessione all'endpoint privato 
 
Usare il `Remove-AzPrivateEndpointConnection` cmdlet per rimuovere una connessione all'endpoint privato. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure 
 
Usare `az network private-link-service update` per la gestione delle connessioni degli endpoint privati. Lo stato della connessione è specificato nel ```azurecli connection-status``` parametro. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sugli endpoint privati](private-endpoint-overview.md)
 
