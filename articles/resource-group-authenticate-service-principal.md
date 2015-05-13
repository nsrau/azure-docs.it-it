<properties
   pageTitle="Autenticazione di un&#39;entità servizio con Gestione risorse di Azure"
   description="Descrive come concedere l&#39;accesso e autenticare un&#39;entità servizio mediante il controllo degli accessi in base al ruolo. Mostra come eseguire queste attività con PowerShell e l&#39;interfaccia della riga di comando di Azure."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz"/>

# Autenticazione di un'entità servizio con Gestione risorse di Azure

Questo argomento mostra come consentire un'entità servizio \(ad esempio un processo, un'applicazione o un servizio automatizzato\) per accedere ad altre risorse nella sottoscrizione. Con Gestione risorse di Azure è possibile usare il controllo degli accessi in base al ruolo per l'esecuzione di azioni consentite in un'entità servizio e l'autenticazione di tale entità. Questo argomento mostra come usare PowerShell e l'interfaccia della riga di comando di Azure per assegnare un ruolo e autenticazione un'entità servizio.


## Concetti
1. Azure Active Directory \(AAD\): servizio di gestione delle identità e degli accessi per il cloud. Per altre informazioni, vedere [Informazioni su Azure Active Directory](./active-directory-whatis.md)
2. Entità servizio: istanza di un'applicazione in una directory.
3. Applicazione AD: record di directory che identifica un'applicazione in AAD. Per altre informazioni, vedere [Nozioni di base sull'autenticazione in Azure AD](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).

## Concedere l'accesso e autenticare un'entità servizio con PowerShell

Se Azure PowerShell non è installato, vedere [Come installare e configurare Azure PowerShell](./powershell-install-configure.md).

Iniziare creando un'entità servizio. Per farlo, è necessario creare un'applicazione nella directory. Questa sezione fornisce spiegazioni su come creare una nuova applicazione nella directory.

1. Creare una nuova applicazione AAD eseguendo il comando **New-AzureADApplication**. Specificare un nome visualizzato per l'applicazione, l'URI a una pagina che descrive l'applicazione \(il collegamento non è verificato\), gli URI che identificano l'applicazione e la password per l'identità dell'applicazione.

        PS C:\> $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

   Viene restituita l'applicazione Azure AD:

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access My
                              Application on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <<Your Application Display Name>>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId":
                            "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application
                             to access <<Your Application Display Name>> on your behalf.",
                            "userConsentDisplayName": "Access <<Your Application Display Name>>",
                            "lang": null
                          }}


>[AZURE.NOTE]La proprietà **ApplicationId** è necessaria per la creazione di entità servizio, le assegnazioni di ruolo e l'acquisizione di token JWT. Salvare l'output o acquisirlo in una variabile.

3. Creare un'entità servizio per l'applicazione.

        PS C:\> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

   È stata creata un'entità servizio nella directory, ma al servizio non sono assegnate autorizzazioni o ambiti. È necessario concedere esplicitamente le autorizzazioni dell'entità servizio per eseguire operazioni in un ambito.

4. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per il parametro **ServicePrincipalName**, fornire il valore **ApplicationId** o **IdentifierUris** usato quando è stata creata l'applicazione. Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Gestione e controllo dell'accesso alle risorse](./resource-group-rbac.md)

        PS C:\> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

5. Recuperare la sottoscrizione in cui è stata creata l'assegnazione di ruolo. La sottoscrizione verrà usata successivamente per ottenere il **TenantId** del tenant in cui si trova l'assegnazione di ruolo dell'entità servizio.

        PS C:\> $subscription = Get-AzureSubscription | where { $_.IsCurrent }

   Se l'assegnazione di ruolo è stata creata in una sottoscrizione diversa da quella attualmente selezionata, è possibile specificare il parametro **SubscriptoinId** o **SubscriptionName** per recuperare una sottoscrizione diversa.

6. Creare un nuovo oggetto **PSCredential** che contenga le credenziali eseguendo il comando **Get-Credential**.

        PS C:\> $creds = Get-Credential

   Verrà richiesto di immettere le credenziali.

   ![][1]

   Per il nome utente, usare **ApplicationId** o **IdentifierUris** specificato durante la creazione dell'applicazione. Per la password, usare quella specificata durante la creazione dell'account.

7. Usare le credenziali immesse come input per il cmdlet **Add-AzureAccount**, che effettuerà l'accesso dell'entità servizio:

        PS C:\> Add-AzureAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId

   A questo punto dovrebbe essere stata eseguita l'autenticazione per l'applicazione AAD creata come entità servizio.


## Concedere l'accesso e autenticare un'entità servizio con l'interfaccia della riga di comando di Azure

Se l'interfaccia della riga di comando di Azure per Mac, Linux e Windows non è installata, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](xplat-cli-install.md)

È necessario disporre già di un'applicazione AD e di un'entità servizio per eseguire questi passaggi. Per informazioni su come configurare un'applicazione AD e un'entità servizio tramite il portale di Azure classico, vedere [Creare una nuova entità servizio di Azure usando il portale di Azure classico](./resource-group-create-service-principal-portal.md).

1. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per il parametro **ServicePrincipalName**, fornire il valore **ApplicationId** o **IdentifierUris** usato quando è stata creata l'applicazione. Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Gestione e controllo dell'accesso alle risorse](./resource-group-rbac.md)

        azure role assignment create --objectId {service-principal-object-id} -o Reader -c /subscriptions/{subscriptionId}/

2. Determinare il **TenantId** del tenant in cui si trova l'assegnazione di ruolo dell'entità servizio elencando gli account e cercando la proprietà **TenantId** nell'output.

        azure account list

3. Accedere usando l'entità servizio come identità. Per il nome utente, usare l'**ApplicationId** usato durante la creazione dell'applicazione. Per la password, usare quella specificata durante la creazione dell'account.

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

  A questo punto dovrebbe essere stata eseguita l'autenticazione per l'applicazione AAD creata come entità servizio.

## Passaggi successivi
Introduzione

- [Panoramica di Gestione risorse di Azure](./resource-group-overview.md)  
- [Uso di Azure PowerShell con Gestione risorse di Azure](./powershell-azure-resource-manager.md)
- [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione delle risorse di Azure](./xplat-cli-azure-resource-manager.md)  
- [Uso del portale di Azure per gestire le risorse di Azure](./resource-group-portal.md)  
  
Creazione e distribuzione delle applicazioni
  
- [Creazione di modelli di Gestione risorse di Azure](./resource-group-authoring-templates.md)  
- [Distribuire un'applicazione con un modello di Gestione risorse di Azure](./resource-group-template-deploy.md)  
- [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](./resource-group-deploy-debug.md)  
- [Funzioni del modello di Gestione risorse di Azure](./resource-group-template-functions.md)  
- [Operazioni avanzate con i modelli](./resource-group-advanced-template.md)  
- [Distribuire le risorse di Azure usando le librerie .NET e un modello](./arm-template-deployment.md)
  
Organizzazione delle risorse
  
- [Uso dei tag per organizzare le risorse di Azure](./resource-group-using-tags.md)  
  
Gestione e controllo dell'accesso
  
- [Gestione e controllo dell'accesso alle risorse](./resource-group-rbac.md)  
- [Creare una nuova entità servizio di Azure usando il portale di Azure](./resource-group-create-service-principal-portal.md)  
  


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!--HONumber=52-->
