<properties
	pageTitle="Operazioni di controllo con Gestione risorse | Microsoft Azure"
	description="Usare il log di controllo in Gestione risorse per esaminare le azioni degli utenti e gli errori. Mostra il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure e REST."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/21/2016"
	ms.author="tomfitz"/>

# Operazioni di controllo con Gestione risorse

Tramite i log di controllo, è possibile determinare:

- le operazioni eseguite sulle risorse nella sottoscrizione;
- chi ha avviato l'operazione (anche se le operazioni avviate da un servizio back-end non restituiscono un utente come chiamante);
- quando si è verificata l'operazione;
- lo stato dell'operazione;
- i valori delle altre proprietà che potrebbero essere utili per esaminare l'operazione.

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Questo argomento riguarda in particolare le operazioni di controllo. Per informazioni sull'uso dei log di controllo per risolvere i problemi relativi a una distribuzione, vedere [Troubleshooting resource group deployments in Azure](resource-manager-troubleshoot-deployments-portal.md) (Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure).

È possibile recuperare le informazioni dai log di controllo tramite il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure, l'API REST di Insights o la [libreria .NET di Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## Visualizzazione dei log di controllo tramite il portale

1. Per visualizzare i log di controllo tramite il portale, selezionare **Sfoglia** e **Log di controllo**.

    ![selezionare i log di controllo](./media/resource-group-audit/select-audit-logs.png)

2. Nel pannello **Log di controllo** verrà visualizzato un riepilogo delle operazioni recenti per tutti i gruppi di risorse nella sottoscrizione. Tale riepilogo include una rappresentazione grafica dell'ora e dello stato delle operazioni, nonché un elenco delle operazioni.

    ![visualizzare le azioni](./media/resource-group-audit/audit-summary.png)

3. Per cercare un tipo di azione specifico, è possibile filtrare le operazioni che vengono visualizzate nel pannello Log di controllo. Selezionare **Filtro** nella parte superiore del pannello.

    ![filtrare i log](./media/resource-group-audit/filter-logs.png)

4. Nel pannello **Filtro** è possibile selezionare molte condizioni diverse per limitare il numero di operazioni visualizzate. Ad esempio, è possibile visualizzare tutte le azioni eseguite da un utente specifico la settimana precedente.

    ![impostare le opzioni di filtro](./media/resource-group-audit/set-filter.png)

Dopo l'aggiornamento della visualizzazione dei log di controllo, verranno visualizzate solo le operazioni che soddisfano la condizione specificata. Tali impostazioni vengono mantenute alla successiva visualizzazione dei log di controllo, quindi potrebbe essere necessario modificare tali valori per ampliare la visualizzazione delle operazioni.

È anche possibile filtrare automaticamente per una risorsa specifica selezionando i log di controllo nel pannello di tale risorsa. Nel portale selezionare la risorsa da controllare, quindi **Log di controllo**.

![controllare la risorsa](./media/resource-group-audit/audit-by-resource.png)

Si noti che il log di controllo viene automaticamente filtrato in base alla risorsa selezionata per la settimana precedente.

![filtrare in base alla risorsa](./media/resource-group-audit/filtered-by-resource.png)

## Visualizzazione dei log di controllo tramite PowerShell

1. Per recuperare le voci di log, eseguire il comando **Get-AzureRmLog**. Offrire parametri aggiuntivi per filtrare l'elenco di voci. Se non si specifica un'ora di inizio e fine, vengono restituite le voci per l'ultima ora. Ad esempio, per recuperare le operazioni per un gruppo di risorse durante l'ultima ora, eseguire:

        Get-AzureRmLog -ResourceGroup ExampleGroup

    L'esempio seguente illustra come usare il log di controllo per esaminare le operazioni eseguite durante un intervallo di tempo specificato. Le date di inizio e fine vengono specificate in un formato Data.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00

    In alternativa, è possibile usare funzioni data per specificare l'intervallo di date, ad esempio gli ultimi 14 giorni.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)

2. A seconda dell'ora di inizio specificata, il comando precedente può restituire un lungo elenco di operazioni per il gruppo di risorse. I risultati possono essere filtrati in base all'elemento da cercare specificando i criteri di ricerca. Ad esempio, se si vuole capire il motivo per cui un'app Web è stata arrestata, è possibile eseguire il comando seguente.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
        
    In questo esempio indica che illustrato che è stata eseguita un'azione di arresto da someone@contoso.com.
        
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

3. È possibile cercare le azioni eseguite da un utente specifico, anche per un gruppo di risorse che non esiste più.

        Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com

## Visualizzazione dei log di controllo tramite l'interfaccia della riga di comando

1. Per recuperare le voci di log, eseguire il comando **azure group log show**.

        azure group log show ExampleGroup

2. I risultati possono essere filtrati con un'utilità JSON, ad esempio [jq](http://stedolan.github.io/jq/download/). L'esempio seguente illustra come cercare le operazioni che hanno aggiornato un file di configurazione Web.

        azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

3. È possibile cercare le azioni per un utente specifico.

        azure group log show ExampleGroup --json | jq ".[] | select(.caller=="someone@contoso.com")"

## Visualizzazione dei log di controllo tramite l'API REST

Le operazioni REST per l'utilizzo del log di controllo fanno parte delle [Informazioni di riferimento sulle API REST di Azure Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Per recuperare gli eventi del log di controllo, vedere [Elencare gli eventi di gestione in una sottoscrizione](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## Passaggi successivi

- I log di controllo di Azure possono essere usati con Power BI per ottenere altre informazioni sulle azioni nella sottoscrizione. Vedere l'articolo relativo alla [visualizzazione e analisi di log di controllo di Azure in Power BI e altri strumenti](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
- Per informazioni su come impostare i criteri di sicurezza, vedere [Controllo di accesso basato sul ruolo di Azure](./active-directory/role-based-access-control-configure.md).
- Per informazioni sui comandi per la risoluzione dei problemi relativi alle distribuzioni, vedere [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse con il portale di Azure](resource-manager-troubleshoot-deployments-portal.md).
- Per informazioni su come impedire operazioni di eliminazione su una risorsa per tutti gli utenti, vedere [Bloccare le risorse con Azure Resource Manager](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0413_2016-->