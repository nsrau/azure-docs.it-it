<properties
   pageTitle="Gestione e controllo dell'accesso alle risorse"
   description="Usare il controllo di accesso basato sui ruoli (RBAC) per gestire le autorizzazioni utente per le risorse di distribuzione in Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Gestione e controllo dell'accesso alle risorse

Con Gestione risorse di Azure, è possibile assicurarsi che gli utenti dell'organizzazione dispongano di autorizzazioni appropriate per gestire o accedere alle risorse. Gestione risorse sfrutta il controllo di accesso in base ai ruoli (RBAC), per cui è possibile applicare facilmente i criteri di sicurezza a singole risorse o gruppi di risorse. Ad esempio, è possibile concedere a un utente l'accesso a una macchina virtuale specifica in una sottoscrizione o consentire a un utente di gestire tutti i siti Web in una sottoscrizione, ma non altre risorse.

## Concetti

Esistono alcuni concetti fondamentali sul controllo di accesso in base ai ruoli:

1. Entità: l'entità che ha concesso l'autorizzazione, ad esempio un utente, un gruppo di sicurezza o un'applicazione.
2. Ruolo: il set di azioni consentite
3. Ambito: il livello a cui viene applicato un ruolo, ad esempio la sottoscrizione, il gruppo di risorse o la risorsa.
3. Assegnazione dei ruoli: il processo di aggiunta di un'entità a un ruolo e l'impostazione dell'ambito.

## Esempi di ruolo
Per comprendere i concetti di controllo di accesso in base ai ruoli, vengono riportati alcuni esempi di definizioni di ruolo comuni:

| Ruolo | Azioni consentite |
| ------- | ----------------- |
| Lettore | **/read (Leggere qualsiasi elemento) | | Proprietario | * (Leggere/scrivere qualsiasi elemento) |

Per assegnare il ruolo **Lettore** all'**Utente A** per il gruppo di risorse denominato **ExampleGroup** e il ruolo **Proprietario** all'**Utente B** per l'intera sottoscrizione, verrà assegnato quanto segue:

| Ruolo | Entità | Ambito |
| --------|-------------|---------- |
| Lettore | Utente A | /subscriptions/{IDsottoscrizione}/resourceGroups/examplegroup |
| Proprietario | Utente B | /subscriptions/{IDsottoscrizione} |

## Scenari di esempio

In questo argomento, verrà illustrato come eseguire gli scenari comuni seguenti tramite Azure PowerShell, l'interfaccia della riga di comando di Azure per Mac, Linux e Windows e API REST.

1. Visualizzare i ruoli disponibili in una sottoscrizione e le azioni consentite per tali ruoli.
2. Concedere le autorizzazioni di Lettore ai membri di un gruppo nella sottoscrizione.
3. Concedere le autorizzazioni di Collaboratore a un'applicazione per consentire all'applicazione la gestione delle risorse all'interno del gruppo di risorse.
4. Concedere le autorizzazioni di Proprietario per un particolare sito Web a un singolo utente.
5. Elencare i registri di controllo del gruppo di risorse.


## Come usare PowerShell per gestire l'accesso
Se la versione più recente di Azure PowerShell non è ancora installata, vedere [Installare e configurare Azure PowerShell](../powershell-install-configure.md). Aprire la console di Azure PowerShell.

1. Accedere all'account Azure con le proprie credenziali. Il comando restituisce le informazioni relative all'account.

        PS C:\> Add-AzureAccount
          
        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

2. Se si hanno più sottoscrizioni, specificare l'ID sottoscrizione che si desidera usare per la distribuzione.

        PS C:\> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Passare al modulo Gestione risorse di Azure.

        PS C:\> Switch-AzureMode AzureResourceManager

### Visualizzare i ruoli disponibili
Per visualizzare tutti i ruoli disponibili per la sottoscrizione, eseguire il comando **Get-AzureRoleDefinition**.

    PS C:\> Get-AzureRoleDefinition

    Name                          Id                            Actions                  NotActions
    ----                          --                            -------                  ----------
    API Management Service Con... /subscriptions/####... {Microsoft.ApiManagement/S...   {}
    Application Insights Compo... /subscriptions/####... {Microsoft.Insights/compon...   {}
    ...

### Concedere l'autorizzazione di Lettore a un gruppo per la sottoscrizione.
1. Esaminare la definizione di ruolo **Lettore**, fornendo il nome del ruolo quando si esegue il comando **Get-AzureRoleDefinition**. Verificare che le operazioni consentite siano quelle che si intende assegnare.

        PS C:\> Get-AzureRoleDefinition Reader
   
        Name            Id                            Actions           NotActions
        ----            --                            -------           ----------
        Reader          /subscriptions/####...        {*/read}          {}

2. Ottenere il gruppo di sicurezza richiesto eseguendo il comando **Get-AzureADGroup**. Specificare il nome effettivo del gruppo nella sottoscrizione. Di seguito è riportato ExampleAuditorGroup.

        PS C:\> $group = Get-AzureAdGroup -SearchString ExampleAuditorGroup

3. Creare l'assegnazione dei ruoli per il gruppo di sicurezza del revisore. Al completamento del comando, viene restituita l'assegnazione del nuovo ruolo.

        PS C:\> New-AzureRoleAssignment -ObjectId $group.Id -Scope /subscriptions/{subscriptionId}/ -RoleDefinitionName Reader

        Mail               :
        RoleAssignmentId   : /subscriptions/####/providers/Microsoft.Authorization/roleAssignments/####
        DisplayName        : Auditors
        RoleDefinitionName : Reader
        Actions            : {*/read}
        NotActions         : {}
        Scope              : /subscriptions/####
        ObjectId           : ####

###Concedere l'autorizzazione di Collaboratore a un'applicazione per un gruppo di risorse.
1. Esaminare la definizione di ruolo **Collaboratore** fornendo il nome del ruolo quando si esegue il comando **Get-AzureRoleDefinition**. Verificare che le operazioni consentite siano quelle che si intende assegnare.

        PS C:\> Get-AzureRoleDefinition Contributor

2. Ottenere l'ID dell'oggetto entità servizio eseguendo il comando **Get-AzureADServicePrincipal** e specificando il nome dell'applicazione nella sottoscrizione. Di seguito è riportato ExampleApplication.

        PS C:\> $service = Get-AzureADServicePrincipal -SearchString ExampleApplicationName

3. Creare le assegnazioni dei ruoli per l'entità servizio eseguendo il comando **New-AzureRoleAssignment**.

        PS C:\> New-AzureRoleAssignment -ObjectId $service.Id -ResourceGroupName ExampleGroupName -RoleDefinitionName Contributor

Per altre informazioni sulla configurazione di un'applicazione Azure Active Directory e su un'entità servizio, vedere [Autenticazione di un'entità servizio con Gestione risorse di Azure](../resource-group-authenticate-service-principal.md).

###Concedere le autorizzazioni di Proprietario a un utente per una risorsa.
1. Esaminare la definizione di ruolo **Proprietario**, fornendo il nome del ruolo quando si esegue il comando **Get-AzureRoleDefinition**. Verificare che le operazioni consentite siano quelle che si intende assegnare.

        PS C:\> Get-AzureRoleDefinition Owner

2. Creare le assegnazioni dei ruoli per l'utente.

        PS C:\> New-AzureRoleAssignment -UserPrincipalName "someone@example.com" -ResourceGroupName {groupName} -ResourceType "Microsoft.Web/sites" -ResourceName "mysite" -RoleDefinitionName Owner


###Elencare i registri di controllo del gruppo di risorse.
Per ottenere il registro di controllo per un gruppo di risorse, eseguire il comando **Get-AzureResourceGroupLog**.

      PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleGroupName

## Come usare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows

Se l'interfaccia della riga di comando per Mac, Linux e Windows non è installata oppure se è non stato configurato l'account dell'organizzazione per l'uso con l'interfaccia della riga di comando di Azure, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).

1. Accedere all'account Azure con le proprie credenziali. Il comando restituisce il risultato dell'accesso dell'utente.

        azure login

        ...
        info:    login command OK

2. Se si hanno più sottoscrizioni, specificare l'ID sottoscrizione che si desidera usare per la distribuzione.

        azure account set <YourSubscriptionNameOrId>

3. Passare al modulo Gestione risorse di Azure. Si riceverà la conferma della nuova modalità.

        azure config mode arm
        
        info:     New mode is arm

### Visualizzare i ruoli disponibili
Consente di visualizzare tutti i ruoli disponibili per la sottoscrizione. Restituisce un elenco di definizioni di ruolo.

    azure role list

### Concedere l'autorizzazione di Lettore a un gruppo per la sottoscrizione.
1. Ottenere la definizione di ruolo per il ruolo **Lettore**. Verificare che le operazioni consentite siano quelle che si intende assegnare.

        azure role show Reader
        
        info:    Executing command role show
        + Getting role definitions
        data:    Name    Actions  NotActions
        data:    ------  -------  ----------
        data:    Reader  */read
        info:    role show command OK

2. Ottenere il gruppo di sicurezza richiesto e il relativo valore objectId eseguendo una ricerca del gruppo in base al nome. Nell'esempio seguente viene riportato ExampleAuditorGroup.

        azure ad group show --search ExampleAuditorGroup
        
        info:    Executing command ad group show
        + Getting group list
        data:    Display Name:      ExampleAuditorGroup
        data:    ObjectId:          1c272299-9729-462a-8d52-7efe5ece0c5c
        data:    Security Enabled:  true
        data:    Mail Enabled:
        data:
        info:    ad group show command OK

3. Creare l'assegnazione dei ruoli per il gruppo di sicurezza.

        azure role assignment create --objectId {group-object-id} -o Reader -c /subscriptions/{subscriptionId}/
        
        info:    Executing command role assignment create
        + Getting role definition id
        + Creating role assignment
        info:    role assignment create command OK


### Concedere l'autorizzazione di Collaboratore a un'applicazione per un gruppo di risorse.
1. Ottenere la definizione di ruolo per il ruolo **Collaboratore**. Verificare che le operazioni consentite siano quelle che si intende assegnare.

        azure role show Contributor

2. Ottenere il valore ObjectId per l'applicazione. Specificare il nome dell'applicazione da recuperare.

        azure ad sp show --search ExampleApplicationName

2. Creare l'assegnazione dei ruoli per il l'applicazione.

        azure role assignment create --ObjectId {service-principal-object-id} -o Contributor -c /subscriptions/{subscriptionId}/


###Concedere le autorizzazioni di Proprietario a un utente per un determinato sito Web.
1. Ottenere la definizione di ruolo per il ruolo **Proprietario**. Verificare che le operazioni consentite siano quelle che si intende assegnare.

        azure role show Owner

2. Creare l'assegnazione dei ruoli per l'utente.

        azure role assignment create --mail "someone@example.com" -o Owner -c /subscriptions/{subscriptionId}/resourceGroups/{groupName}/providers/Microsoft.Web/sites/{mySiteName}


###Elencare i registri di controllo del gruppo di risorse.
Per ottenere il registro di controllo per un gruppo di risorse, eseguire il comando **azure group log show** e specificare il nome del gruppo di risorse desiderato.

         azure group log show ExampleGroupName


## Come usare l'API REST
Per gestire il controllo di accesso in base ai ruoli tramite l'API REST di Gestione risorse di Azure, è necessario impostare le intestazioni e parametri comuni (inclusi i token di autenticazione) quando si inviano le richieste. Per informazioni vedere [Parametri e intestazioni comuni](https://msdn.microsoft.com/library/azure/dn906885.aspx).
   
Per individuare le versioni di api supportate, eseguire:

      GET https://management.azure.com/providers/Microsoft.Authorization?api-version=2015-01-01

È possibile usare la versione `2014-10-01-preview` per questo argomento.

###Creare un'assegnazione dei ruoli
Ottenere le definizioni di ruolo disponibili.

    GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-10-01-preview

Creare l'assegnazione dei ruoli.

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview
    {
      "properties": {
          "roleDefinitionId": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
          "principalId": "{principal-object-id}",
          "scope": "/subscriptions/{subscription-id}"
       }
    }


###Eliminare un'assegnazione dei ruoli

      Delete  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview


## Passaggi successivi

- [Controllo di accesso in base al ruolo nel portale di Microsoft Azure](../role-based-access-control-configure.md)
- [Creare una nuova entità servizio di Azure usando il portale di Azure classico](../resource-group-create-service-principal-portal.md)
- [Autenticazione di un'entità servizio con Gestione risorse di Azure](../resource-group-authenticate-service-principal.md)

 

<!---HONumber=August15_HO6-->