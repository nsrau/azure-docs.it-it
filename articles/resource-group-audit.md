<properties 
	pageTitle="Operazioni di controllo con Gestione risorse | Microsoft Azure" 
	description="Usare il log di controllo in Gestione risorse per esaminare le azioni degli utenti e gli errori. Mostra PowerShell, l'interfaccia della riga di comando di Azure e REST." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="tomfitz"/>

# Operazioni di controllo con Gestione risorse

In caso di problemi durante la distribuzione o nel corso del ciclo di vita della soluzione, è necessario scoprirne le cause. Gestione risorse offre due metodi per scoprire cosa è accaduto e perché. È possibile usare i comandi di distribuzione per recuperare le informazioni relative a distribuzioni e operazioni particolari. In alternativa, si possono usare i log di controllo per recuperare le informazioni relative alle distribuzioni e ad altre azioni eseguite durante il ciclo di vita della soluzione. Questo argomento riguarda in particolare i log di controllo.

Il log di controllo include tutte le azioni eseguite sulle risorse, quindi se un utente dell'organizzazione modifica una risorsa, è possibile identificare l'azione, l'ora e l'utente.

Esistono due importanti limitazioni da tenere presenti quando si lavora con i log di controllo:

1. I log di controllo vengono conservati solo per 90 giorni.
2. È possibile eseguire solo query per un intervallo di 15 giorni o meno.

Si possono recuperare le informazioni dai log di controllo tramite Azure PowerShell, l'interfaccia della riga di comando di Azure, l'API REST o il portale di Azure.

## PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

Per recuperare le voci del log, eseguire il comando **Get- AzureRmLog** (o **Get-AzureResourceGroupLog** per le versioni di PowerShell precedenti alla versione 1.0). Fornire parametri aggiuntivi per filtrare l'elenco di voci.

L'esempio seguente mostra come usare il log di controllo per cercare le azioni eseguite durante il ciclo di vita della soluzione. È possibile vedere quando si è verificata l'azione e chi l'ha richiesta. Le date di inizio e fine vengono specificate in un formato Data.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00

In alternativa, è possibile utilizzare funzioni data per specificare l'intervallo di date come ad esempio gli ultimi 15 giorni.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-15)

A seconda dell'ora di inizio specificata, il comando precedente può restituire un lungo elenco di azioni per quel gruppo di risorse. I risultati possono essere filtrati in base all'elemento da cercare specificando i criteri di ricerca. Ad esempio, se si vuole cercare il motivo per cui un'app Web è stata arrestata, è possibile eseguire il comando seguente e vedere che someone@example.com ha eseguito un'azione di arresto.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-15) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action

    Authorization     :
                        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
                        Action    : Microsoft.Web/sites/stop/action
                        Role      : Subscription Admin
                        Condition :
    Caller            : someone@example.com
    CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
    EventSource       : Administrative
    EventTimestamp    : 8/28/2015 4:08:18 PM
    OperationName     : Microsoft.Web/sites/stop/action
    ResourceGroupName : ExampleGroup
    ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
    Status            : Succeeded
    SubscriptionId    : xxxxx
    SubStatus         : OK

Nell'esempio successivo si cercheranno le azioni non riuscite dopo l'ora di inizio specificata. Per vedere i messaggi di errore, si includerà anche il parametro **DetailedOutput**.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-15) -Status Failed –DetailedOutput
    
Se questo comando restituisce troppe voci e proprietà, è possibile concentrare le attività di controllo recuperando la proprietà **properties**.

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties

    Content
    -------
    {}
    {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
    {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

È anche possibile limitare i risultati esaminando il messaggio di stato.

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :


## Interfaccia della riga di comando di Azure

Per recuperare le voci di log, eseguire il comando **azure group log show**.

    azure group log show ExampleGroup

I risultati possono essere filtrati con un'utilità JSON, ad esempio [jq](http://stedolan.github.io/jq/download/). L'esempio seguente mostra come cercare le operazioni che hanno comportato l'aggiornamento di un file di configurazione Web.

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

È possibile aggiungere il parametro **–-last-deployment** per limitare le voci restituite solo alle operazioni dall'ultima distribuzione.

    azure group log show ExampleGroup --last-deployment

Se l'elenco delle operazioni dall'ultima distribuzione è troppo lungo, è possibile filtrare i risultati solo per le operazioni non riuscite.

    azure group log show tfCopyGroup --last-deployment --json | jq ".[] | select(.status.value == "Failed")"

                                   /Microsoft.Web/Sites/ExampleSite
    data:    SubscriptionId:       <guid>
    data:    EventTimestamp (UTC): Thu Aug 27 2015 13:03:27 GMT-0700 (Pacific Daylight Time)
    data:    OperationName:        Update website
    data:    OperationId:          cb772193-b52c-4134-9013-673afe6a5604
    data:    Status:               Failed
    data:    SubStatus:            Conflict (HTTP Status Code: 409)
    data:    Caller:               someone@example.com
    data:    CorrelationId:        a8c7a2b4-5678-4b1b-a50a-c17230427b1e
    data:    Description:
    data:    HttpRequest:          clientRequestId: <guid>
                                   clientIpAddress: 000.000.000.000
                                   method:          PUT

    data:    Level:                Error
    data:    ResourceGroup:        ExampleGroup
    data:    ResourceProvider:     Azure Web Sites
    data:    EventSource:          Administrative
    data:    Properties:           statusCode:       Conflict
                                   serviceRequestId:
                                   statusMessage:    {"Code":"Conflict","Message":"Website with given name
                                   ExampleSite already exists.","Target":null,"
                                   Details
                                   ":[{"Message":"Website with given name ExampleSite already exists."},
                                   {"Code":"Conflict"},
                                   {"ErrorEntity":{"Code":"Conflict","Message":"Website with given
                                   name ExampleSite already exists.","ExtendedCode
                                   ":"
                                   54001","MessageTemplate":"Website with given name {0} already exists.",
                                   "Parameters":["ExampleSite"],"
                                   InnerErrors":null}}],"Innererror":null}



## API REST

Le operazioni REST per l'utilizzo del log di controllo fanno parte delle [Informazioni di riferimento sulle API REST di Azure Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Per recuperare gli eventi del log di controllo, vedere [Elencare gli eventi di gestione in una sottoscrizione](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## Portale

È anche possibile vedere le operazioni registrate tramite il portale. Selezionare semplicemente il pannello dei log di controllo.

![selezionare i log di controllo](./media/resource-group-audit/select-audit.png)

Visualizzare quindi l'elenco delle operazioni più recenti.

![visualizzare le azioni](./media/resource-group-audit/show-actions.png)

È possibile selezione un'operazione per visualizzarne più dettagli.

## Passaggi successivi

- Per informazioni su come impostare i criteri di sicurezza, vedere [Controllo di accesso basato sul ruolo di Azure](./active-directory/role-based-access-control-configure.md).
- Per informazioni su come concedere l'accesso a un'entità servizio, vedere [Autenticazione di un'entità servizio con Gestione risorse di Azure](resource-group-authenticate-service-principal.md).
- Per informazioni su come agire su una risorsa per tutti gli utenti, vedere [Bloccare le risorse con Gestione risorse di Azure](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0128_2016-->