---
title: Gestire una connessione all'endpoint privato in AzureManage a Private Endpoint connection in Azure
description: Informazioni su come gestire le connessioni agli endpoint privati in AzureLearn how to manage private endpoint connections in Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 62b24b3e2f5c1b89fa7db581ac34cf58381db2a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452973"
---
# <a name="manage-a-private-endpoint-connection"></a>Gestire una connessione endpoint privato
Azure Private Link funziona su un modello di flusso delle chiamate di approvazione in cui il consumer del servizio Private Link può richiedere una connessione al provider di servizi per l'utilizzo del servizio. Il provider di servizi può quindi decidere se consentire o meno al consumer di connettersi. Azure Private Link consente ai provider di servizi di gestire la connessione all'endpoint privato nelle risorse. In questo articolo vengono fornite istruzioni su come gestire le connessioni all'endpoint privato.

![Gestire gli endpoint privati](media/manage-private-endpoint/manage-private-endpoint.png)

Esistono due metodi di approvazione della connessione tra cui un consumer del servizio Private Link può scegliere:There are two connection approval methods that a Private Link service consumer can choose from:
- **Automatico:** se il consumer del servizio dispone delle autorizzazioni RBAC per la risorsa del provider di servizi, il consumer può scegliere il metodo di approvazione automatica. In questo caso, quando la richiesta raggiunge la risorsa del provider di servizi, non è richiesta alcuna azione da parte del provider di servizi e la connessione viene approvata automaticamente. 
- **Manuale:** Al contrario, se il consumer di servizi non dispone delle autorizzazioni RBAC per la risorsa del provider di servizi, il consumer può scegliere il metodo di approvazione manuale. In questo caso, la richiesta di connessione viene visualizzata nelle risorse del servizio come **In sospeso**. Il provider di servizi deve approvare manualmente la richiesta prima di poter stabilire le connessioni. Nei casi manuali, il consumer del servizio può anche specificare un messaggio con la richiesta per fornire più contesto al provider di servizi. Il provider di servizi dispone di opzioni seguenti tra cui scegliere per tutte le connessioni endpoint privati: **Approvato**, **Rifiuta**, **Rimuovi**.

La tabella seguente mostra le varie azioni del provider di servizi e gli stati di connessione risultanti per gli endpoint privati.  Il provider di servizi può inoltre modificare lo stato di connessione della connessione all'endpoint privato in un secondo momento senza l'intervento del consumer. L'azione aggiornerà lo stato dell'endpoint sul lato consumer. 


|Azione provider di servizi   |Stato endpoint privato consumer del servizioService Consumer Private Endpoint State   |Descrizione   |
|---------|---------|---------|
|nessuno    |    In sospeso     |    La connessione viene creata manualmente ed è in attesa di approvazione dal proprietario della risorsa Private Link.       |
|Approvazione    |  Approved       |  La connessione è stata approvata automaticamente o manualmente ed è pronta per essere usata.     |
|Rifiuto     | Rifiutato        | La connessione è stata rifiutata dal proprietario della risorsa di collegamento privato.        |
|Rimuovere    |  Disconnesso       | La connessione è stata rimossa dal proprietario della risorsa collegamento privato, l'endpoint privato diventa informativo e deve essere eliminato per la pulizia.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Gestire le connessioni endpoint privato nelle risorse PaaS di Azure
Portale è il metodo preferito per la gestione delle connessioni endpoint privati nelle risorse di Azure PaaS.Portal is the preferred method for managing private endpoint connections on Azure PaaS resources. Attualmente non è disponibile il supporto di PowerShell/CLI per la gestione delle connessioni nelle risorse di Azure PaaS.Currently, we don't have PowerShell/CLI support for managing connections on Azure PaaS resources.
1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Passare a Centro collegamenti privati.
3. In **Risorse**selezionare il tipo di risorsa che si desidera gestire le connessioni all'endpoint privato.
4. Per ogni tipo di risorsa, è possibile visualizzare il numero di connessioni endpoint private associate. È possibile filtrare le risorse in base alle esigenze.
5. Selezionare le connessioni endpoint privato.  Nelle connessioni elencate selezionare la connessione che si desidera gestire. 
6. È possibile modificare lo stato della connessione selezionando una delle opzioni nella parte superiore.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Gestire le connessioni dell'endpoint privato in un servizio Private Link di proprietà cliente/partnerManage Private Endpoint connections on a customer/partner owned Private Link service

Azure PowerShell e l'interfaccia della riga di comando di Azure sono i metodi preferiti per la gestione delle connessioni agli endpoint privati nei servizi partner Microsoft o nei servizi di proprietà dei clienti. Attualmente, non è disponibile alcun supporto del portale per la gestione delle connessioni in un servizio Private Link.  
 
### <a name="powershell"></a>PowerShell 
  
Usare i comandi di PowerShell seguenti per gestire le connessioni all'endpoint privato.  
#### <a name="get-private-link-connection-states"></a>Ottenere gli stati di connessione private LinkGet Private Link connection states 
Utilizzare `Get-AzPrivateLinkService` il cmdlet per ottenere le connessioni all'endpoint privato e i relativi stati.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Approvare una connessione all'endpoint privato 
 
Utilizzare `Approve-AzPrivateEndpointConnection` il cmdlet per approvare una connessione all'endpoint privato. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Nega connessione endpoint privato 
 
Utilizzare `Deny-AzPrivateEndpointConnection` il cmdlet per rifiutare una connessione all'endpoint privato. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Rimuovi connessione endpoint privato 
 
Utilizzare `Remove-AzPrivateEndpointConnection` il cmdlet per rimuovere una connessione all'endpoint privato. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure 
 
Utilizzare `az network private-link-service update` per la gestione delle connessioni dell'endpoint privato. Lo stato di connessione ```azurecli connection-status``` è specificato nel parametro. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sugli endpoint privatiLearn about Private Endpoints](private-endpoint-overview.md)
 
