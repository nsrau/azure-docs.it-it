---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: fb3795ff807a87e9bac4d95400f5e446c68d1e4d
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73897512"
---
## <a name="authenticate-with-azure-active-directory"></a>Eseguire l'autenticazione con Azure Active Directory

> [!IMPORTANT]
> 1. Attualmente, **solo** il API Visione artificiale, API Viso, API analisi del testo, il lettore immersivo, il riconoscitore del modulo, il rilevatore di anomalie e tutti i servizi Bing tranne ricerca personalizzata Bing supportano l'autenticazione con Azure Active Directory (AAD).
> 2. L'autenticazione di AAD deve essere sempre usata insieme al nome del sottodominio personalizzato della risorsa di Azure. Gli [endpoint internazionali](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) non supportano l'autenticazione AAD.

Nelle sezioni precedenti è stato illustrato come eseguire l'autenticazione in Servizi cognitivi di Azure usando una chiave di sottoscrizione a servizio singolo o multiservizio. Sebbene queste chiavi forniscano un percorso rapido e semplice per iniziare lo sviluppo, si riferiscono a scenari più complessi che richiedono controlli degli accessi in base al ruolo. Verranno ora esaminati gli elementi necessari per l'autenticazione con Azure Active Directory (AAD).

Nelle sezioni seguenti verrà usato l'ambiente Azure Cloud Shell o l'interfaccia della riga di comando di Azure per creare un sottodominio, assegnare i ruoli e ottenere un bearer token per chiamare i servizi cognitivi di Azure. Se ci si blocca, i collegamenti vengono forniti in ogni sezione con tutte le opzioni disponibili per ogni comando nell'interfaccia della riga di comando Azure Cloud Shell/Azure.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Creare una risorsa con un sottodominio personalizzato

Il primo passaggio consiste nel creare un sottodominio personalizzato. Se si vuole usare una risorsa di servizi cognitivi esistente che non dispone di un nome di sottodominio personalizzato, seguire le istruzioni in [sottodomini personalizzati di servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) per abilitare il sottodominio personalizzato per la risorsa.

1. Per iniziare, aprire il Azure Cloud Shell. [selezionare quindi una sottoscrizione](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description):

   ```azurecli-interactive
   Select-AzureSubscription -SubscriptionName <YOUR_SUBCRIPTION>
   ```

2. Successivamente, [creare una risorsa Servizi cognitivi](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) con un sottodominio personalizzato. Il nome del sottodominio deve essere globalmente univoco e non può includere caratteri speciali, ad esempio: ".", "!", ",".

   ```azurecli-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. In caso di esito positivo, l' **endpoint** dovrebbe mostrare il nome del sottodominio univoco per la risorsa.


### <a name="assign-a-role-to-a-service-principal"></a>Assegnare un ruolo a un'entità servizio

Ora che è stato associato un sottodominio personalizzato alla risorsa, sarà necessario assegnare un ruolo a un'entità servizio.

> [!NOTE]
> Tenere presente che le assegnazioni di ruolo AAD possono richiedere fino a cinque minuti per la propagazione.

1. Prima di tutto, registrare un' [applicazione AAD](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   ```azurecli-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   Il **ApplicationID** sarà necessario nel passaggio successivo.

2. Successivamente, è necessario [creare un'entità servizio](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) per l'applicazione AAD.

   ```azurecli-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Se si registra un'applicazione nel portale di Azure, questo passaggio viene completato.

3. L'ultimo passaggio consiste nell' [assegnare il ruolo "utente servizi cognitivi"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) all'entità servizio (ambito della risorsa). Assegnando un ruolo, si concede all'entità servizio l'accesso a questa risorsa. È possibile concedere all'entità servizio l'accesso a più risorse nella sottoscrizione.
   >[!NOTE]
   > Viene usato il valore ObjectId dell'entità servizio, non il valore ObjectId per l'applicazione.
   > Il ACCOUNT_ID sarà l'ID risorsa di Azure dell'account di servizi cognitivi creato. È possibile trovare l'ID risorsa di Azure da "Properties" della risorsa in portale di Azure.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Richiesta di esempio

In questo esempio viene usata una password per autenticare l'entità servizio. Il token fornito viene quindi utilizzato per chiamare il API Visione artificiale.

1. Ottenere il **TenantId**:
   ```azurecli-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Ottenere un token:
   ```azurecli-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Chiamare la API Visione artificiale:
   ```azurecli-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

In alternativa, l'entità servizio può essere autenticata con un certificato. Oltre all'entità servizio, l'entità utente è supportata anche con le autorizzazioni delegate tramite un'altra applicazione di AAD. In questo caso, anziché le password o i certificati, agli utenti viene richiesto di eseguire l'autenticazione a due fattori durante l'acquisizione del token.
