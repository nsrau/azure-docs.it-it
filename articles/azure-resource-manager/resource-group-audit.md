---
title: "Visualizzare i log attività di Azure per monitorare le risorse | Documentazione Microsoft"
description: "Usare i log attività per esaminare le azioni degli utenti e gli errori. Mostra il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure e REST."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: 9f90bc80c146c6c2da04aacbc110f7d389c0baa2
ms.contentlocale: it-it
ms.lasthandoff: 01/24/2017

---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Visualizzare i log attività per controllare le azioni sulle risorse
Con i log attività è possibile determinare:

* le operazioni eseguite sulle risorse nella sottoscrizione
* chi ha avviato l'operazione (anche se le operazioni avviate da un servizio back-end non restituiscono un utente come chiamante);
* quando si è verificata l'operazione;
* lo stato dell'operazione;
* i valori delle altre proprietà che potrebbero essere utili per esaminare l'operazione.

[!INCLUDE [resource-manager-audit-limitations](../../includes/resource-manager-audit-limitations.md)]

È possibile recuperare le informazioni dai log attività tramite il portale, PowerShell, l'interfaccia della riga di comando di Azure, l'API REST di Insights o la [libreria .NET di Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="portal"></a>Portale
1. Per visualizzare i log attività dal portale, selezionare **Monitoraggio**.
   
    ![Selezionare i log di attività](./media/resource-group-audit/select-monitor.png)

   In alternativa, per filtrare automaticamente il log attività per una determinata risorsa o uno specifico gruppo di risorse, selezionare **Log attività** nel pannello di tale risorsa. Si noti che il log attività viene automaticamente filtrato in base alla risorsa selezionata.
   
    ![filtrare in base alla risorsa](./media/resource-group-audit/filtered-by-resource.png)
2. Nel pannello **Log attività** viene visualizzato un riepilogo delle operazioni recenti.
   
    ![visualizzare le azioni](./media/resource-group-audit/audit-summary.png)
3. Per limitare il numero di operazioni visualizzate, selezionare condizioni diverse. Ad esempio, l'immagine seguente mostra i campi **Intervallo di tempo** ed **Evento avviato da** modificati per poter visualizzare le azioni eseguite da un determinato utente o applicazione il mese scorso. Selezionare **Applica** per visualizzare i risultati della query.
   
    ![impostare le opzioni di filtro](./media/resource-group-audit/set-filter.png)

4. Se è necessario eseguire ancora la query in un secondo momento, selezionare **Salva** e assegnare un nome alla query.
   
    ![Salvare la query](./media/resource-group-audit/save-query.png)
5. Per eseguire rapidamente una query, è possibile selezionare una query predefinita, ad esempio "distribuzioni non riuscite".

    ![Selezionare la query](./media/resource-group-audit/select-quick-query.png)

   La query selezionata imposta automaticamente i valori di filtro necessari.

    ![Visualizzare gli errori di distribuzione](./media/resource-group-audit/view-failed-deployment.png)   

6. Selezionare una delle operazioni per visualizzare un riepilogo dell'evento.

    ![Visualizzare l'operazione](./media/resource-group-audit/view-operation.png)  

## <a name="powershell"></a>PowerShell
1. Per recuperare le voci di log, eseguire il comando **Get-AzureRmLog** . Offrire parametri aggiuntivi per filtrare l'elenco di voci. Se non si specifica un'ora di inizio e fine, vengono restituite le voci per l'ultima ora. Ad esempio, per recuperare le operazioni per un gruppo di risorse durante l'ultima ora, eseguire:

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup
  ```
   
    L'esempio seguente illustra come usare il log attività per cercare le operazioni eseguite durante un intervallo di tempo specificato. Le date di inizio e fine vengono specificate in un formato Data.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
  ```

    In alternativa, è possibile usare funzioni data per specificare l'intervallo di date, ad esempio gli ultimi 14 giorni.
   
  ```powershell 
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

2. A seconda dell'ora di inizio specificata, il comando precedente può restituire un lungo elenco di operazioni per il gruppo di risorse. I risultati possono essere filtrati in base all'elemento da cercare specificando i criteri di ricerca. Se si vuole capire il motivo per cui un'app Web è stata arrestata, ad esempio, è possibile eseguire questo comando:

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
  ```

    In questo esempio indica che è stata eseguita un'azione di arresto da someone@contoso.com. 

  ```powershell 
  Authorization     :
  Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Action    : Microsoft.Web/sites/stop/action
  Role      : Subscription Admin
  Condition :
  Caller            : someone@contoso.com
  CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
  EventSource       : Administrative
  EventTimestamp    : 8/28/2015 4:08:18 PM
  OperationName     : Microsoft.Web/sites/stop/action
  ResourceGroupName : ExampleGroup
  ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Status            : Succeeded
  SubscriptionId    : xxxxx
  SubStatus         : OK
  ```

3. È possibile cercare le azioni eseguite da un utente specifico, anche per un gruppo di risorse che non esiste più.

  ```powershell 
  Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

4. È possibile filtrare le operazioni non riuscite.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed
  ```

5. È possibile concentrarsi su un errore esaminando il messaggio di stato per tale voce.
   
        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
   
    Che restituisce:
   
        code           message                                                                        
        ----           -------                                                                        
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. 


## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
* Per recuperare le voci di log, eseguire il comando **azure group log show** .

  ```azurecli
  azure group log show ExampleGroup --json
  ```


## <a name="rest-api"></a>API REST
Le operazioni REST per l'uso del log attività fanno parte delle [Informazioni di riferimento sulle API REST di Azure Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Per recuperare gli eventi del log attività, vedere [Elencare gli eventi di gestione in una sottoscrizione](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## <a name="next-steps"></a>Passaggi successivi
* I log attività di Azure possono essere usati con Power BI per ottenere altre informazioni sulle azioni nella sottoscrizione. Vedere [View and analyze Azure Activity Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)(Visualizzare e analizzare i log attività di Azure in Power BI e altri strumenti).
* Per informazioni su come impostare i criteri di sicurezza, vedere [Controllo degli accessi in base al ruolo in Azure](../active-directory/role-based-access-control-configure.md).
* Per informazioni sui comandi per visualizzare le operazioni di distribuzione, vedere [Visualizzare le operazioni di distribuzione](resource-manager-deployment-operations.md).
* Per informazioni su come impedire operazioni di eliminazione su una risorsa per tutti gli utenti, vedere [Bloccare le risorse con Azure Resource Manager](resource-group-lock-resources.md).


