<properties
	pageTitle="Controllo degli accessi in base al ruolo nel portale di Microsoft Azure"
	description="Descrive le modalità di configurazione e utilizzo del controllo di accesso basato sui ruoli"
	services=""
	documentationCenter=""
	authors="Justinha"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="Ibiza"
	ms.workload="infrastructure-services"
	ms.date="06/29/2015"
	ms.author="justinha"/>

# Controllo degli accessi in base al ruolo nel portale di Microsoft Azure

Il portale di Microsoft Azure offre ora il supporto del Controllo degli accessi in base al ruolo (RBAC), che aiuta le organizzazioni a gestire gli accessi in maniera più semplice e precisa. Questo [post di blog](http://go.microsoft.com/fwlink/?LinkId=511576) include una rapida introduzione a tale funzionalità. In questo argomento vengono descritti i concetti nei dettagli e i casi di utilizzo aggiuntivi.


## RBAC in Azure

Ogni sottoscrizione Azure è associata a un'istanza di Azure Active Directory. Gli utenti e i servizi che accedono alle risorse della sottoscrizione utilizzando il portale di gestione di Microsoft Azure o l'API di Gestione risorse di Azure devono prima eseguire l'autenticazione con tale Azure Active Directory.

![][1]

Il controllo degli accessi in base al ruolo di Azure consente di concedere l'accesso appropriato a utenti, gruppi e servizi di Azure AD, assegnando loro i ruoli a livello di sottoscrizione, di gruppo di risorse o di risorsa indipendente. Il ruolo assegnato definisce il livello di accesso alla risorsa di Azure assegnato agli utenti, ai gruppi e ai servizi.

### Ruolo

Un ruolo è un insieme di azioni che possono essere effettuate sulle risorse di Azure. Un utente o un servizio è autorizzato a svolgere un'azione su una risorsa di Azure se il ruolo assegnato prevede tale azione. Per un elenco dei ruoli predefiniti e delle **relative** proprietà actions e **not actions**, vedere [Ruoli predefiniti](#builtinroles).

### Assegnazione dei ruoli

L'accesso viene concesso agli utenti e ai servizi di Azure AD assegnando loro il ruolo pertinente per una risorsa di Azure.

#### Entità di sicurezza di Azure AD

I ruoli possono essere assegnati ai seguenti tipi di entità di sicurezza di Azure AD:

+ **Utenti**: i ruoli possono essere assegnati a utenti aziendali presenti in Azure AD a cui è associata la sottoscrizione Azure. I ruoli possono essere assegnati anche a utenti di account Microsoft esterni (ad esempio joe@outlook.com), usando l'azione di invito per assegnare l'utente a un ruolo nel portale di Azure. L'assegnazione di un ruolo a un utente di un account Microsoft esterno provoca la creazione di un account guest in Azure AD. Se l'account guest viene disabilitato nella directory, l'utente esterno non potrà utilizzare alcuna risorsa di Azure a cui è autorizzato ad accedere.
+ **Gruppi**: i ruoli possono essere assegnati ai gruppi di sicurezza di Azure AD. Se l'utente diventa un membro che dispone dell'accesso a una risorsa, l'accesso gli viene assegnato automaticamente. Allo stesso modo, se viene rimosso dal gruppo, l'utente perde automaticamente l'accesso alla risorsa. La gestione degli accessi attraverso i gruppi (assegnando i ruoli ai gruppi e aggiungendo gli utenti a tali gruppi) è la prassi consigliata, piuttosto che l'assegnazione diretta dei ruoli agli utenti. Azure RBAC non consente di assegnare ruoli alle liste di distribuzione. La possibilità di assegnare ruoli ai gruppi consente a un'organizzazione di estendere il modello di controllo degli accessi dalla directory locale al cloud. Pertanto, i gruppi di sicurezza già definiti per controllare l'accesso locale possono essere usati per controllare l'accesso alle risorse nel portale di Azure. Per maggiori informazioni sulle diverse opzioni di sincronizzazione degli utenti e dei gruppi da una directory in sede, vedere [Panoramica sull'integrazione della directory](http://technet.microsoft.com/library/jj573653.aspx). Azure AD Premium offre una [funzionalità di delega della gestione dei gruppi](http://msdn.microsoft.com/library/azure/dn641267.aspx) che consente di delegare le attività di creazione e gestione dei gruppi a utenti non amministratori in Azure AD.
+ **Entità del servizio**: le identità del servizio sono rappresentate come entità del servizio nella directory. consentono di eseguire le autenticazioni con Azure AD e possono comunicare in maniera sicura tra loro. I servizi possono ottenere l'accesso alle risorse di Azure assegnando i ruoli all'entità Azure AD che rappresenta il servizio pertinente tramite il modulo Azure per Windows PowerShell.

#### Ambito delle risorse

Non è necessario assegnare l'accesso all'intera sottoscrizione. I ruoli possono essere assegnati sia a gruppi di risorse sia a singole risorse. In Azure, una risorsa eredita le assegnazioni dei ruoli dalle sue risorse principali. Pertanto, se un utente, un gruppo o un servizio è autorizzato ad accedere solo a un gruppo di risorse di sottoscrizione, potrà accedere solo a tale gruppo e alle sue risorse e non agli altri gruppi di risorse della sottoscrizione. Per fare un altro esempio, un gruppo di sicurezza può essere aggiunto al ruolo di lettore per un gruppo di risorse e al ruolo di collaboratore per un database all'interno di tale gruppo.

![][2]

## Coesistenza tra il controllo degli accessi in base al ruolo (RBAC) e i coamministratori delle sottoscrizioni

L'amministratore e i coamministratori delle sottoscrizioni continueranno a disporre di un accesso completo ai portali e alle API di gestione di Azure. Nel modello di controllo di accesso basato sui ruoli, vengono associati a un ruolo di proprietario a livello di sottoscrizione. Tuttavia, il nuovo modello di controllo di accesso basato sui ruoli è supportato solo dal portale di Azure e dalle API di Gestione risorse di Azure. Gli utenti e servizi associati a ruoli RBAC non possono accedere al portale di gestione di Azure e alle API di Gestione servizi di Azure. L'aggiunta di un utente al ruolo di proprietario di una sottoscrizione nel portale di Azure non rende tale utente un coamministratore della sottoscrizione nel portale Azure completo.

Per concedere a un utente l'accesso a una risorsa di Azure che non può ancora essere gestita nel portale di Azure, è necessario aggiungerlo ai coamministratori della sottoscrizione usando il portale di gestione di Azure. Il bus di servizio e i servizi cloud sono due esempi di risorse che non possono essere attualmente gestite con il modello RBAC.

## Autorizzazioni per le operazioni di gestione e per le operazioni di dati

Il controllo degli accessi in base al ruolo è supportato solo per le operazioni di gestione delle risorse di Azure nel portale di Azure e nelle API di Gestione risorse di Azure. Non tutte le operazioni a livello di dati svolte sulle risorse di Azure possono essere autorizzate tramite RBAC. Ad esempio, le operazioni di creazione, lettura, aggiornamento ed eliminazione degli account di archiviazione possono essere gestite tramite RBAC, mentre le stesse operazioni svolte sugli oggetti BLOB e sulle tabelle negli account di archiviazione non possono essere controllate con tale metodo. Allo stesso modo, le operazioni di creazione, lettura, aggiornamento ed eliminazione di un database SQL possono essere gestite tramite RBAC, mentre le stesse operazioni svolte sulle tabelle del database non possono essere controllate con tale metodo.

## Aggiunta e rimozione degli accessi

Nel prossimo esempio viene illustrata la gestione degli accessi da parte di un proprietario di risorse di un'organizzazione. In questo scenario, più persone lavorano a diversi progetti di collaudo e produzione creati utilizzando risorse di Azure. Uno degli obiettivi consiste nel concedere gli accessi nel modo più efficace. In effetti, è necessario permettere agli utenti di accedere a tutte le risorse necessarie, ma non a ulteriori elementi. Un altro obiettivo consiste nello sfruttamento degli investimenti stanziati per i processi e gli strumenti, al fine di utilizzare i gruppi di sicurezza controllati in un'Active Directory in sede. Nelle sezioni successive viene spiegato come impostare l'accesso a tali risorse:

* [Aggiungere un accesso](#add-access)
* [Rimuovere un accesso](#remove-access)
* [Aggiungere o rimuovere l'accesso per utenti esterni](#add-or-remove-access-for-external-user)

### Aggiungere un accesso

Di seguito è riportato un riepilogo dei requisiti di accesso e di come vengono configurati in Azure.

Utente/Gruppo | Requisiti di accesso | Ruolo e ambito dell'accesso
------------- | -------------  | ------------
Tutto il team di Jill Santos | Lettura di tutte le risorse di Azure | Aggiunta del gruppo di AD che rappresenta il team di Jill Santos al ruolo di lettore per la sottoscrizione di Azure
Tutto il team di Jill Santos | Creazione e gestione di tutte le risorse nel gruppo di risorse Test | Aggiunta del gruppo di AD che rappresenta il team di Jill Santos al ruolo di collaboratore per il gruppo di risorse Test
Brock | Creazione e gestione di tutte le risorse nel gruppo di risorse Prod | Aggiunta di Brock al ruolo di collaboratore per il gruppo di risorse Prod


Aggiungere innanzitutto l'accesso in lettura a tutte le risorse della sottoscrizione. Fare clic su **Browse > Everything > Subscriptions**.

![][3]

Fare clic su *nome sottoscrizione* ** > Reader > Add**. Nell'elenco di utenti e gruppi, selezionare o digitare il nome del gruppo di Active Directory.

![][4]

Aggiungere lo stesso team al ruolo di collaboratore del gruppo di risorse Test. Fare clic sul gruppo di risorse per aprire il blade delle proprietà. In **Roles** fare clic su **Contributor > Add** e digitare il nome del team.

![][5]

Per aggiungere Brock al ruolo di collaboratore del gruppo di risorse Prod, fare clic sul gruppo di risorse, selezionare **Contributor > Add** e digitare il nome di Brock.

![][6]

Le assegnazioni dei ruoli possono inoltre essere gestite utilizzando il modulo di Microsoft Azure per Windows PowerShell. Segue un esempio dell'aggiunta di un account di Brock utilizzando il cmdlet New-AzureRoleAssignment al posto del portale:

	PS C:> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Per ulteriori informazioni sull'aggiunta e sulla rimozione degli accessi tramite Windows PowerShell, vedere [Gestione del controllo degli accessi in base al ruolo con Windows PowerShell](role-based-access-control-powershell.md).

### Rimuovere un accesso

Le assegnazioni possono essere rimosse facilmente. Prendiamo l'esempio della rimozione di un utente chiamato Brad Adams dal ruolo di lettore per il gruppo di risorse TestDB. Aprire il blade del gruppo di risorse e fare clic su **Reader > Brad Adams > Remove**.

![][7]

Segue un esempio di come rimuovere Brad Adams usando il cmdlet Remove-AzureRoleAssignment:

	PS C:> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

### Aggiungere o rimuovere l'accesso per utenti esterni

La scheda **Configura** di una directory include le opzioni per il controllo degli accessi per gli utenti esterni. Tali opzioni possono essere modificate unicamente da un amministratore globale di directory all'interno dell'interfaccia utente (non sono disponibili i metodi Windows PowerShell e API) del portale di Azure completo. Per aprire la scheda **Configura** nel portale di Azure, fare clic su **Active Directory**, quindi selezionare il nome della directory.

![][10]

In seguito è possibile modificare le opzioni di controllo degli accessi per gli utenti esterni.

![][8]

Per impostazione predefinita, gli ospiti non possono enumerare i contenuti della directory. Pertanto non possono visualizzare gli utenti e i gruppi presenti nell'area **Elenco membri**. Possono cercare un utente digitandone l'indirizzo e-mail completo e, in seguito, concedere l'accesso. Segue una descrizione delle restrizioni predefinite per gli ospiti:

- Non possono enumerare gli utenti e i gruppi nella directory.
- Possono visualizzare solo alcuni dati di un utente, se ne conoscono l'indirizzo e-mail.
- Possono visualizzare solo alcuni dati di un gruppo, se ne conoscono il nome.

La possibilità di visualizzare alcuni dati di un utente o di un gruppo consente agli ospiti di invitare altre persone a visualizzare alcuni dati delle persone con cui collaborano.

Ora verrà illustrata la procedura di aggiunta dell'accesso per un utente esterno. Verrà aggiunto un utente esterno allo stesso ruolo di lettore per il gruppo di risorse TestDB, per consentire all'utente di eseguire il debug di un errore. Aprire il blade del gruppo di risorse, fare clic su **Reader > Add > Invite** e digitare l'indirizzo e-mail dell'utente da aggiungere.

![][9]

Quando si aggiunge un utente esterno, nella directory viene creato un ospite. In seguito, tale ospite può essere aggiunto o rimosso da un gruppo. Inoltre, l'ospite può essere aggiunto o rimosso singolarmente da un ruolo come con qualsiasi altro utente della directory.

È possibile rimuovere un ospite da qualsiasi ruolo, come con qualsiasi altro utente. La rimozione di un ospite da ruolo su una risorsa non rimuove l'ospite dalla directory.

## Come rilevare le modifiche alle assegnazioni di ruolo

Le modifiche alle assegnazioni di ruolo vengono registrate nei [log di controllo](http://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) in modo analogo ad altri eventi. Il log delle modifiche di assegnazione di ruolo può essere recuperato tramite [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx) o l'[API REST di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn931927.aspx).

Ad esempio, per recuperare l'elenco delle modifiche delle assegnazioni di ruolo per un'intera sottoscrizione, eseguire i seguenti due cmdlet con Azure PowerShell. Il primo effettua il passaggio alla modalità Gestione risorse di Azure.

`Switch-AzureMode -name AzureResourceManager`

`Get-AzureSubscriptionIdLog –DetailedOutput -StartTime '06-15-15' -EndTime '06-29-15'`

Le modifiche delle assegnazioni di ruolo vengono acquisite in eventi in cui ResourceProviderName è `Microsoft.Authorization`. I dettagli effettivi dell'assegnazione vengono acquisiti nei dettagli dell'evento, ovvero entità assegnata, ruolo di assegnazione e ambito di assegnazione. Le modifiche dell'assegnazione di ruolo sono visibili quando i log di controllo vengono visualizzati nel portale, anche se nel portale non vengono visualizzati i dettagli dell'evento. Per visualizzare i dettagli dell'evento, è necessario usare Azure PowerShell.

###Dettagli dell'evento

Di seguito è illustrato un esempio di dettagli dell'evento per una modifica dell'assegnazione di ruolo:

```
Authorization        :
                       Scope     : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-W
                       estUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authoriza
                       tion/roleAssignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78
                       Action    : Microsoft.Authorization/roleAssignments/write
                       Role      : Subscription Admin
                       Condition :
Caller               : William.Hennum@contoso.com
Claims               :
                       aud            : https://management.core.windows.net/
                       iss            : https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/
                       iat            : 1435333533
                       nbf            : 1435333533
                       exp            : 1435337433
                       ver            : 1.0
                       http://schemas.microsoft.com/identity/claims/tenantid: 72f988bf-86f1-41af-91ab-2d7cd011db47
                       http://schemas.microsoft.com/identity/claims/objectidentifier:
                       dda50086-5e3d-4a4b-b8bc-f54771104d89
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn: William.Hennum@contoso.com
                       puid           : 10030000803CDC0B
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier:
                       MJwntjqWaULfl30NJMiDRVSVCWMX5GzmMNU4oqitDXs
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname: William
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname: Hennum
                       name           : William Hennum
                       http://schemas.microsoft.com/claims/authnmethodsreferences: rsa,wia,mfa
                       _claim_names   : {"groups":"src1"}
                       _claim_sources : {"src1":{"endpoint":"https://graph.windows.net/72f988bf-86f1-41af-91ab-2d7cd011
                       db47/users/dda50086-5e3d-4a4b-b8bc-f54771104d89/getMemberObjects"}}
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name: William.Hennum@contoso.com
                       onprem_sid     : S-1-5-21-1721254763-462695806-1538882281-3175325
                       appid          : c44b4083-3bb0-49c1-b47d-974e53cbdf3c
                       appidacr       : 2
                       http://schemas.microsoft.com/identity/claims/scope: user_impersonation
                       http://schemas.microsoft.com/claims/authnclassreference: 1
CorrelationId        : d724ffd0-31a4-4564-941b-f3a5d32ad8a4
Description          :
EventChannels        : Operation
EventDataId          : ed8e79b6-c7d1-4332-adcf-70d37546c5a6
EventName            : BeginRequest
EventSource          : Administrative
EventTimestamp       : 6/26/2015 3:53:34 PM
HttpRequest          :
                       ClientId        : F7272386-295A-4545-96BD-21F0856A43FE
                       Method          : PUT
                       Url             :
                       ClientIpAddress : 23.99.81.159
Id                   : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/provid
                       ers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authorization/roleAss
                       ignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78/events/ed8e79b6-c7d1-4332-adcf-70d37546c5a6/ticks/
                       635709308140011864
Level                : Informational
OperationId          : d724ffd0-31a4-4564-941b-f3a5d32ad8a4
OperationName        : Microsoft.Authorization/roleAssignments/write
Properties           :
                       requestbody    : {"Id":"531f036a-37ff-40c1-9bb9-aa580ebe7e78","Properties":{"PrincipalId":"dda50
                       086-5e3d-4a4b-b8bc-f54771104d89","RoleDefinitionId":"/subscriptions/ff945b8d-441a-41ef-a9db-7bd5
                       fcc99978/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7"
                       ,"Scope":"/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-Wes
                       tUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes"}}
ResourceGroupName    : Default-Storage-WestUS
ResourceProviderName : Microsoft.Authorization
ResourceId           : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/provid
                       ers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authorization/roleAss
                       ignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78
Status               : Started
SubmissionTimestamp  : 6/26/2015 3:53:50 PM
SubscriptionId       : ff945b8d-441a-41ef-a9db-7bd5fcc99978
SubStatus            :`
```

Le informazioni nell'evento vengono interpretate come segue:

| Campo | Valore | Dettagli |
| --- | --- | --- |
| Chiamante |	`William.Hennum@contoso.com` | L'entità che ha effettuato l'assegnazione di ruolo. L'entità può essere un utente, un gruppo o un'entità servizio.
| Metodo HttpRequest: | `PUT` | L'azione eseguita. PUT concede un'assegnazione mentre DELETE elimina un'assegnazione. |
| Proprietà: PrincipalId | `dda50086-5e3d-4a4b-b8bc-f54771104d89` | 	ID oggetto dell'entità assegnata al ruolo. L'entità può essere un utente, un gruppo o un'entità servizio. È possibile determinare il nome e il tipo di entità eseguendo una ricerca in Azure Active Directory tramite Azure PowerShell. |
| Proprietà: RoleDefinitionId |	`/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7	` | Il ruolo assegnato. È possibile determinare il nome visualizzato del ruolo tramite Azure PowerShell. |
| Proprietà: Scope | `/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes` |	La risorsa in cui è stata effettuata l'assegnazione di ruolo. Può trattarsi di una risorsa, un gruppo di risorse o una sottoscrizione. |

###Frammenti di codice di esempio PowerShell

Di seguito è riportato il codice di esempio di Azure PowerShell per eseguire il mapping di PrincipalId a un nome e a un tipo:

```
# Sample - how to resolve a principal
function Get-PrincipalDetails($principalId)
{
    $principalDetails = "" | select Name, Type
    $user = Get-AzureADUser -ObjectId $principalId
    if ($user) {
        $principalDetails.Name = $user.DisplayName
        $principalDetails.Type = "User"
    } else {
        $group = Get-AzureADGroup -ObjectId $principalId
        if ($group) {
            $principalDetails.Name = $group.DisplayName
            $principalDetails.Type = "Group"
        } else {
            $servicePrincipal = Get-AZureADServicePrincipal -objectId $principalId
            if ($servicePrincipal) {
                $principalDetails.Name = $servicePrincipal.DisplayName
                $principalDetails.Type = "Service Principal"
            }
        }
    }

    $principalDetails
}
```

Di seguito è riportato il codice di esempio di Azure PowerShell per eseguire il mapping dell'ambito a un nome di risorsa e un tipo:

```
# Sample - how to resolve a resource
function Get-ResourceDetails($resourceId)
{
    $resourceDetails = "" | select Name, Type
    $resource = Get-AzureResource -Id $resourceId -OutputObjectFormat New
    if ($resource) {
        if ($resource.ResourceName) {
            $resourceDetails.Name = $resource.ResourceName
            $resourceDetails.Type = "Resource"
        } elseif ($resource.ResourceGroupName) {
            $resourceDetails.Name = $resource.ResourceGroupName
            $resourceDetails.Type = "Resource Group"
        } elseif ($resource.SubscriptionId) {
            $resourceDetails.Name = $resource.SubscriptionId
            $resourceDetails.Type = "Subscription"
        }
    }
    $resourceDetails
}
```
Di seguito è riportato il codice di esempio di Azure PowerShell per eseguire il mapping di RoleDefinitionId al nome visualizzato di un ruolo:

```

# Get the name of a role
function Get-AzureRoleDefinitionName($roleDefinitionId)
{
    if (!$Global:_azureRoleDefinitionCache) {
        $Global:_azureRoleDefinitionCache = @{}
        Get-AzureRoleDefinition | % { $Global:_azureRoleDefinitionCache[$_.Id] = $_; }
    }

    if ($Global:_azureRoleDefinitionCache[$roleDefinitionId]) {
        return $Global:_azureRoleDefinitionCache[$roleDefinitionId].Name
    } else {
        return ""
    }
}

```

###Script di esempio di Azure PowerShell

Combinando i precedenti esempi, di seguito è riportato lo script di esempio che recupera gli eventi di assegnazione di ruolo per un determinato intervallo di date e li restituisce sotto forma di tabella:

```
# Sample - how to resolve a principal
function Get-PrincipalDetails($principalId)
{
    $principalDetails = "" | select Name, Type
    $user = Get-AzureADUser -ObjectId $principalId
    if ($user) {
        $principalDetails.Name = $user.DisplayName
        $principalDetails.Type = "User"
    } else {
        $group = Get-AzureADGroup -ObjectId $principalId
        if ($group) {
            $principalDetails.Name = $group.DisplayName
            $principalDetails.Type = "Group"
        } else {
            $servicePrincipal = Get-AZureADServicePrincipal -objectId $principalId
            if ($servicePrincipal) {
                $principalDetails.Name = $servicePrincipal.DisplayName
                $principalDetails.Type = "Service Principal"
            }
        }
    }

    $principalDetails
}
# Sample - how to resolve a resource
function Get-ResourceDetails($resourceId)
{
    $resourceDetails = "" | select Name, Type
    $resource = Get-AzureResource -Id $resourceId -OutputObjectFormat New
    if ($resource) {
        if ($resource.ResourceName) {
            $resourceDetails.Name = $resource.ResourceName
            $resourceDetails.Type = "Resource"
        } elseif ($resource.ResourceGroupName) {
            $resourceDetails.Name = $resource.ResourceGroupName
            $resourceDetails.Type = "Resource Group"
        } elseif ($resource.SubscriptionId) {
            $resourceDetails.Name = $resource.SubscriptionId
            $resourceDetails.Type = "Subscription"
        }
    }
    $resourceDetails
}
# Get the name of a role
function Get-AzureRoleDefinitionName($roleDefinitionId)
{
    if (!$Global:_azureRoleDefinitionCache) {
        $Global:_azureRoleDefinitionCache = @{}
        Get-AzureRoleDefinition | % { $Global:_azureRoleDefinitionCache[$_.Id] = $_; }
    }

    if ($Global:_azureRoleDefinitionCache[$roleDefinitionId]) {
        return $Global:_azureRoleDefinitionCache[$roleDefinitionId].Name
    } else {
        return ""
    }
}
# Sample - output the list of role assignment events
function Get-AzureRBACAuditLog($startDateTime, $endDateTime)
{
    $log = Get-AzureSubscriptionIdLog -DetailedOutput -StartTime $startDateTime -EndTime $endDateTime
    $log = $log | ? { $_.ResourceProviderName -ieq "Microsoft.Authorization" }
    $startEvents = $log | ? { $_.httpRequest -and $_.Status -ieq "Started" }
    $endEvents = @{}
    $log | ? { $_.httpRequest -and $_.Status -ne "Started" } | % { $endEvents[$_.OperationId] = $_ }

    $startEvents | ? { $endEvents.ContainsKey($_.OperationId) } | % {
        $endEvent = $endEvents[$_.OperationId];
        $out = "" | select Timestamp, Caller, Action, PrincipalId, PrincipalName, PrincipalType, RoleName, Scope, ScopeName, ScopeType, RoleDefinitionId
        $out.Timestamp = $endEvent.EventTimestamp
        $out.Caller = $_.Caller
        if ($_.HttpRequest.Method -ieq "PUT") {
            $out.Action = "Granted"
            if ($_.Properties.Content.ContainsKey("requestbody")) {
                $messageBody = ConvertFrom-Json $_.Properties.Content["requestbody"]
            }
        }
        elseif ($_.HttpRequest.Method -ieq "DELETE") {
            $out.Action = "Revoked"
            if ($endEvent.Properties.Content.ContainsKey("responseBody")) {
                $messageBody = ConvertFrom-Json $endEvent.Properties.Content["responseBody"]
            }
        }

        if ($messageBody) {
            $out.PrincipalId = $messageBody.properties.principalId
            $pd = Get-PrincipalDetails $out.PrincipalId
            $out.PrincipalName = $pd.Name
            $out.PrincipalType = $pd.Type
            $out.RoleName = (Get-AzureRoleDefinitionName $messageBody.properties.roleDefinitionId)
            $out.Scope = $messageBody.properties.Scope
            $rd = Get-ResourceDetails $out.Scope
            $out.ScopeName = $rd.Name
            $out.ScopeType = $rd.Type
            $out.RoleDefinitionId = $messageBody.properties.roleDefinitionId
        }

        $out
    }
}

```

Di seguito sono riportati i comandi per eseguire lo script

```
$log = Get-AzureRBACAuditLog '2015-06-26' '2015-06-27'

$log | Format-Table
```

## Problemi noti relativi al controllo degli accessi in base al ruolo

In caso di problemi quando si utilizza la funzionalità di controllo degli accessi in base al ruolo, vedere [Risoluzione dei problemi relativi al controllo degli accessi in base al ruolo](role-based-access-control-troubleshooting.md) per visualizzare tutti i problemi noti in tale ambito.


## Ruoli predefiniti

Il controllo degli accessi in base al ruolo di Azure presenta i seguenti ruoli predefiniti che possono essere assegnati a utenti, gruppi e servizi. I ruoli predefiniti non possono essere modificati. In una futura release di Azure RBAC, sarà possibile definire ruoli personalizzati componendo un set di azioni da un elenco di azioni disponibili che possono essere effettuate sulle risorse di Azure.

Fare clic sul collegamento corrispondente per visualizzare le proprietà **actions** e **not actions** di una definizione del ruolo. La proprietà **actions** specifica le azioni consentite sulle risorse di Azure. Nelle stringhe delle azioni è possibile usare caratteri jolly. La proprietà **not actions** di una definizione del ruolo specifica le azioni che devono essere escluse dalle azioni consentite.


Nome del ruolo | Descrizione
------------- | -------------  
[Collaboratore servizio Gestione API](#api-management-service-contributor) | Consente di gestire il servizio Gestione API, ma non di accedervi.
[Collaboratore componente di Application Insights](#application-insights-component-contributor) | Consente di gestire i componenti di Application Insights, ma non di accedervi.
[Collaboratore BizTalk](#biztalk-contributor) | Consente di gestire i servizi BizTalk, ma non di accedervi.
[Collaboratore database ClearDB MySQL](#cleardb-mysql-db-contributor) | Consente di gestire il database ClearDB MySQL, ma non di accedervi.
[Collaboratore](#contributor) | I collaboratori possono gestire tutto ad eccezione degli accessi.
[Collaboratore Data Factory](#data-factory-contributor) | Consente di gestire data factory, ma non di accedervi.
[Collaboratore account Document DB](#document-db-account-contributor) | Consente di gestire gli account di DocumentDB, ma non di accedervi.
[Collaboratore account Intelligent Systems](#intelligent-systems-account-contributor) | Consente di gestire gli account di Intelligent Systems, ma non di accedervi.
[Collaboratore account NewRelic APM](#newrelic-apm-account-contributor) | Consente di gestire gli account e le applicazioni di New Relic Application Performance Management, ma non di accedervi.
[Proprietario](#owner) | Il proprietario può gestire tutto, compresi gli accessi.
[Lettore](#reader) | I lettori possono visualizzare tutto, ma non possono apportare modifiche.
[Collaboratore cache Redis](#redis-cache-contributor) | Consente di gestire le cache di Redis, ma non di accedervi.
[Collaboratore database SQL](#sql-db-contributor) | Consente di gestire i database SQL, ma non di accedervi. Inoltre, non è possibile gestire i criteri relativi alla sicurezza o i server SQL padre.
[Gestione della sicurezza SQL](#sql-security-manager) | Consente di gestire i criteri di sicurezza dei server e database SQL, ma non di accedervi.
[Collaboratore SQL Server](#sql-server-contributor) | Consente di gestire server e database SQL, ma non di accedervi e non consente di gestire i criteri di sicurezza relativi.
[Collaboratore raccolte di processi dell'unità di pianificazione](#scheduler-job-collections-contributor) | Consente di gestire le raccolte di processi dell'unità di pianificazione, ma non di accedervi.
[Collaboratore servizi di ricerca](#search-service-contributor) | Consente di gestire i servizi di ricerca, ma non di accedervi.
[Collaboratore account di archiviazione](#storage-account-contributor) | Consente di gestire gli account di archiviazione, ma non di accedervi.
[Amministratore accessi utente](#user-access-administrator) | Consente di gestire gli accessi utente alle risorse di Azure.
[Collaboratore macchine virtuali](#virtual-machine-contributor) | Consente di gestire le macchine virtuali, ma non di accedervi e non consente di gestire l'account di archiviazione o la rete virtuale a cui sono collegate.
[Collaboratore reti virtuali](#virtual-network-contributor) | Consente di gestire le reti virtuali, ma non di accedervi.
[Collaboratore piani Web](#web-plan-contributor) | Consente di gestire i piani Web per i siti Web, ma non di accedervi.
[Collaboratore siti Web](#website-contributor) | Consente di gestire i siti Web (non i piani Web), ma non di accedervi.


### Collaboratore servizio Gestione API

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>È in grado di creare e gestire i servizi Gestione API</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Collaboratore componente Application Insights

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Insights/components/*</td>
<td>È in grado di creare e gestire i componenti di Insights</td>
</tr>
<tr>
<td>Microsoft.Insights/webtests/*</td>
<td>È in grado di creare e gestire i test Web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Collaboratore BizTalk

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.BizTalkServices/BizTalk/*</td>
<td>È in grado di creare e gestire i servizi BizTalk</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Collaboratore database ClearDB MySQL

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>successbricks.cleardb/databases/*</td>
<td>È in grado di creare e gestire i database ClearDB MySQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Collaboratore

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>*</td>
<td>È in grado di creare e gestire ogni tipo di risorsa</td>
</tr>
<tr>
<th colspan="2">Non azioni</th>
</tr>
<tr>
<td>Microsoft.Authorization/*/Write</td>
<td>Non è in grado di creare ruoli e assegnazioni di ruoli </td>
</tr>
<tr>
<td>Microsoft.Authorization/*/Delete</td>
<td>Non è in grado di eliminare ruoli e assegnazioni di ruoli</td>
</tr>
</table>

### Collaboratore Data Factory

<table style=width:100%">
<tr>
<td>Microsoft.DataFactory/dataFactories/*</td>
<td>È in grado di creare e gestire data factory</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Collaboratore account Document DB

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.DocumentDb/databaseAccounts/*</td>
<td>È in grado di creare e gestire account DocumentDB</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Collaboratore account Intelligent Systems

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.IntelligentSystems/accounts/*</td>
<td>È in grado di creare e gestire account di Intelligent Systems</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Collaboratore account NewRelic APM

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>NewRelic.APM/accounts/*</td>
<td>È in grado di creare e gestire account di NewRelic Application Performance Management</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Proprietario

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>*</td>
<td>È in grado di creare e gestire ogni tipo di risorsa</td>
</tr>
</table>

### Reader

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>*/lettura</td>
<td>È in grado di leggere ogni tipo di risorsa. È in grado tuttavia di leggere i segreti.</td>
</tr>
</table>

### Collaboratore cache Redis

<table style=width:100%">
<tr>
<td>Microsoft.Cache/redis/*</td>
<td>Creare e gestire le cache Redis</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Collaboratore database SQL

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>È in grado di leggere i server SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/*</td>
<td>È in grado di creare e gestire i database SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>È in grado di leggere le risorse di sottoscrizione</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>È in grado di leggere le risorse di un gruppo di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
<tr>
<th colspan="2">Non azioni</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Non è in grado di gestire i criteri di controllo dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Non è in grado di gestire i criteri di connessione dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Non è in grado di gestire i criteri della maschera dei dati dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Non è in grado di gestire le metriche di sicurezza dei database SQL</td>
</tr>
</table>

### Gestione della sicurezza SQL

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>È in grado di leggere i server SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>È in grado di creare e gestire criteri di controllo di SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/read</td>
<td>È in grado di leggere i database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>È in grado di creare e gestire i criteri di controllo dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>È in grado di creare e gestire i criteri di connessione dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>È in grado di creare e gestire i criteri della maschera dei dati dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>È in grado di creare e gestire le metriche di sicurezza dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Collaboratore SQL Server

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/*</td>
<td>È in grado di creare e gestire i server SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>È in grado di leggere le risorse di sottoscrizione</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>È in grado di leggere le risorse di un gruppo di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
<tr>
<th colspan="2">Non azioni</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>Non è in grado di gestire i criteri di controllo di SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Non è in grado di gestire i criteri di controllo dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Non è in grado di gestire i criteri di connessione dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Non è in grado di gestire i criteri della maschera dei dati dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Non è in grado di gestire le metriche di sicurezza dei database SQL</td>
</tr>
</table>

### Collaboratore raccolte di processi dell'unità di pianificazione

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Scheduler/jobcollections/*</td>
<td>È in grado di creare e gestire raccolte di processi dell'unità di pianificazione</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Collaboratore servizi di ricerca

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Search/searchServices/*</td>
<td>È in grado di creare e gestire i servizi di ricerca</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Collaboratore account di archiviazione

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/*</td>
<td>È in grado di creare e gestire account di archiviazione</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Amministratore accessi utente

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>*/lettura</td>
<td>È in grado di leggere ogni tipo di risorsa</td>
</tr>
<tr>
<td>Microsoft.Authorization/*</td>
<td>È in grado di creare e gestire i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Collaboratore macchine virtuali

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/read</td>
<td>È in grado di leggere gli account di archiviazione</td>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/listKeys/action</td>
<td>È in grado di recuperare le chiavi dell'account di archiviazione</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/read</td>
<td>È in grado di leggere reti virtuali</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/join/action</td>
<td>È in grado di aggiungere reti virtuali</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/read</td>
<td>È in grado di leggere indirizzi IP riservati</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/link/action</td>
<td>È in grado di collegare agli indirizzi IP riservati</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/domainNames/*</td>
<td>È in grado di creare e gestire servizi cloud</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/virtualMachines/*</td>
<td>È in grado di creare e gestire macchine virtuali</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Collaboratore reti virtuali

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/*</td>
<td>È in grado di creare e gestire reti virtuali</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Collaboratore piani Web

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/*</td>
<td>È in grado di creare e gestire piani Web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

### Collaboratore siti Web

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/read</td>
<td>È in grado di leggere piani Web</td>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/join/action</td>
<td>È in grado di aggiungere piani Web</td>
</tr>
<tr>
<td>Microsoft.Web/sites/*</td>
<td>È in grado di creare e gestire siti Web</td>
</tr>
<tr>
<td>Microsoft.Web/certificates/*</td>
<td>È in grado di creare e gestire certificati dei siti Web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>


## Invio di commenti

È possibile inviare commenti sul controllo degli accessi in base al ruolo in Azure facendo clic [qui](http://aka.ms/azurerbacfeedback).


## Passaggi successivi

Di seguito sono riportate altre risorse utili per l'uso del controllo degli accessi in base al ruolo:

+ [Gestione del controllo di accesso basato sui ruoli con Windows PowerShell](role-based-access-control-powershell.md)
+ [Gestione del controllo di accesso basato sui ruoli con XPLAT CLI](role-based-access-control-xplat-cli.md)
+ [Risoluzione dei problemi relativi al controllo di accesso basato sui ruoli](role-based-access-control-troubleshooting.md)
+ [Azure Active Directory Premium e Basic](active-directory-editions.md)
+ [Come vengono associate le sottoscrizioni di Azure ad Azure AD](active-directory-how-subscriptions-associated-directory.md)
+ Per un'introduzione alla gestione self-service dei gruppi di sicurezza, consultare il [blog del team di Active Directory](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx)

<!--Image references-->
[1]: ./media/role-based-access-control-configure/RBACSubAuthDir.png
[2]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
[3]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
[4]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
[5]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
[6]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
[7]: ./media/role-based-access-control-configure/RBACRemoveRole.png
[8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
[9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
[10]: ./media/role-based-access-control-configure/RBACDirConfigTab.png

<!---HONumber=July15_HO3-->