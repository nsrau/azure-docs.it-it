---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 8754504655cdd08c9bf9f89311cb6c5d1057f0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262596"
---
## <a name="authenticate-with-azure-active-directory"></a>Eseguire l'autenticazione con Azure Active Directory

> [!IMPORTANT]
> 1. Attualmente, **solo** l'API Visione artificiale, l'API Viso, l'API Analisi del testo, Lettore immersivo, il riconoscimento moduli, il rilevatore di anomalie e tutti i servizi Bing, ad eccezione di Ricerca personalizzata Bing, supportano l'autenticazione tramite Azure Active Directory (AAD).
> 2. L'autenticazione AAD deve essere sempre usata insieme al nome di sottodominio personalizzato della risorsa di Azure.AAD authentication needs to be always used together with custom subdomain name of your Azure resource. [Gli endpoint regionali](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) non supportano l'autenticazione AAD.

Nelle sezioni precedenti è stato illustrato come eseguire l'autenticazione in Servizi cognitivi di Azure usando una chiave di sottoscrizione a servizio singolo o multiservizio. Sebbene queste chiavi forniscano un percorso rapido e semplice per avviare lo sviluppo, non sono in grado di creare scenari più complessi che richiedono controlli di accesso basati sui ruoli. Diamo un'occhiata a ciò che è necessario per l'autenticazione utilizzando Azure Active Directory (AAD).

Nelle sezioni seguenti si userà l'ambiente Azure Cloud Shell o l'interfaccia della riga di comando di Azure per creare un sottodominio, assegnare ruoli e ottenere un token di connessione per chiamare Servizi cognitivi di Azure.In the following sections, you'll use either the Azure Cloud Shell environment or the Azure CLI to create a subdomain, assign roles, and obtain a bearer token to call the Azure Cognitive Services. Se si rimane bloccati, i collegamenti vengono forniti in ogni sezione con tutte le opzioni disponibili per ogni comando in Azure Cloud Shell/Interfaccia della riga di comando di Azure.If you get stuck, links are provided in each section with all available options for each command in Azure Cloud Shell/Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Creare una risorsa con un sottodominio personalizzatoCreate a resource with a custom subdomain

Il primo passaggio consiste nel creare un sottodominio personalizzato. Se si vuole usare una risorsa di Servizi cognitivi esistente che non ha un nome di sottodominio personalizzato, seguire le istruzioni in Sottodomini personalizzati di [Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) per abilitare il sottodominio personalizzato per la risorsa.

1. Iniziare aprendo Azure Cloud Shell. Quindi [selezionare un abbonamento](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0):

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Successivamente, [creare una risorsa di Servizi cognitivi](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) con un sottodominio personalizzato. Il nome del sottodominio deve essere univoco a livello globale e non può includere caratteri speciali, ad esempio: ".", "!", ",".

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. In caso di esito positivo, **l'endpoint** deve mostrare il nome del sottodominio univoco per la risorsa.


### <a name="assign-a-role-to-a-service-principal"></a>Assegnare un ruolo a un'entità servizio

Ora che si dispone di un sottodominio personalizzato associato alla risorsa, è necessario assegnare un ruolo a un'entità servizio.

> [!NOTE]
> Tenere presente che la propagazione delle assegnazioni di ruolo AAD può richiedere fino a cinque minuti.

1. In primo luogo, registriamo [un'applicazione AAD](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   È necessario il **ApplicationId** nel passaggio successivo.

2. Successivamente, è necessario [creare un'entità servizio](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) per l'applicazione AAD.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Se si registra un'applicazione nel portale di Azure, questo passaggio viene completato.

3. L'ultimo passaggio consiste [nell'assegnare il ruolo "Utente servizi cognitivi"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) all'entità servizio (con ambito alla risorsa). Assegnando un ruolo, si concede l'accesso dell'entità servizio a questa risorsa. È possibile concedere alla stessa entità servizio l'accesso a più risorse nella sottoscrizione.
   >[!NOTE]
   > Viene utilizzato l'ObjectId dell'entità servizio, non l'ObjectId per l'applicazione.
   > Il ACCOUNT_ID sarà l'ID risorsa di Azure dell'account Servizi cognitivi creato. È possibile trovare ID risorsa di Azure da "proprietà" della risorsa nel portale di Azure.You can find Azure resource Id from "properties" of the resource in Azure portal.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Richiesta di esempio

In questo esempio viene usata una password per autenticare l'entità servizio. Il token fornito viene quindi utilizzato per chiamare l'API Visione artificiale.

1. Ottenere il **TenantId**:
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Ottenere un token:Get a token:
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Chiamare l'API Visione artificiale:Call the Computer Vision API:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

In alternativa, l'entità servizio può essere autenticata con un certificato. Oltre all'entità servizio, l'entità utente è supportata anche con autorizzazioni delegate tramite un'altra applicazione AAD. In questo caso, invece di password o certificati, agli utenti verrà richiesta l'autenticazione a due fattori durante l'acquisizione del token.
