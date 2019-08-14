---
title: Autenticazione Azure Active Directory (Azure AD)
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come creare una nuova risorsa Reader immersiva con un sottodominio personalizzato e quindi configurare Azure AD nel tenant di Azure.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: e4b792a04b4926fdb56f37c089e73b90cde905d3
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990138"
---
# <a name="use-azure-active-directory-azure-ad-authentication-with-the-immersive-reader-service"></a>Usare l'autenticazione Azure Active Directory (Azure AD) con il servizio di lettura immersiva

Nelle sezioni seguenti si userà l'ambiente Azure Cloud Shell o l'interfaccia della riga di comando di Azure per creare una nuova risorsa Reader immersiva con un sottodominio personalizzato e quindi configurare Azure AD nel tenant di Azure. Dopo aver completato la configurazione iniziale, si chiamerà Azure AD per ottenere un token di accesso, in modo analogo a come verrà fatto quando si usa l'SDK immersive Reader. Se ci si blocca, i collegamenti vengono forniti in ogni sezione con tutte le opzioni disponibili per ogni comando dell'interfaccia della riga di comando di Azure.

## <a name="create-an-immersive-reader-resource-with-a-custom-subdomain"></a>Creare una risorsa Reader immersiva con un sottodominio personalizzato

1. Per iniziare, aprire il [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). [Selezionare quindi una sottoscrizione](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description):

   ```azurecli-interactive
   Select-AzSubscription -SubscriptionName <YOUR_SUBSCRIPTION>
   ```

2. Successivamente, [creare una risorsa Reader immersiva](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) con un sottodominio personalizzato.

   >[!NOTE]
   > Il nome del sottodominio viene usato in immersive Reader SDK quando si avvia il Reader con la funzione launchAsync.

   -SkuName può essere F0 (livello gratuito) o S0 (livello standard, anche gratuito durante l'anteprima pubblica). Il livello S0 ha un limite di frequenza delle chiamate superiore e nessuna quota mensile per il numero di chiamate.

   -Location può essere uno dei seguenti: `eastus`, `westus`, `australiaeast`, `centralindia`, `japaneast`, `northeurope`,`westeurope`

   -CustomSubdomainName deve essere globalmente univoco e non può includere caratteri speciali, ad esempio: ".", "!", ",".


   ```azurecli-interactive
   $resource = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME> -Type ImmersiveReader -SkuName S0 -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>

   // Display the Resource info
   $resource
   ```

   In caso di esito positivo, l' **endpoint** della risorsa dovrebbe mostrare il nome del sottodominio univoco per la risorsa.

   Qui viene acquisito l'oggetto risorsa appena creato in una variabile di **$Resource** , perché verrà usato in un secondo momento quando si concede l'accesso a questa risorsa.


   >[!NOTE]
   > Se si crea una risorsa nella portale di Azure, il nome della risorsa viene usato come sottodominio personalizzato. Per controllare il nome del sottodominio nel portale, passare alla pagina Panoramica risorse e trovare il sottodominio nell'endpoint elencato, ad esempio `https://[SUBDOMAIN].cognitiveservices.azure.com/`. È anche possibile eseguire questa verifica in un secondo momento quando è necessario ottenere il sottodominio per l'integrazione con l'SDK.

   Se la risorsa è stata creata nel portale, è anche possibile [ottenere ora una risorsa esistente](https://docs.microsoft.com/powershell/module/az.cognitiveservices/get-azcognitiveservicesaccount?view=azps-1.8.0) .

   ```azurecli-interactive
   $resource = Get-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME>

   // Display the Resource info
   $resource
   ```

## <a name="assign-a-role-to-a-service-principal"></a>Assegnare un ruolo a un'entità servizio

Ora che si dispone di un sottodominio personalizzato associato alla risorsa, è necessario assegnare un ruolo a un'entità servizio.

1. Prima di tutto, [creare un'applicazione Azure ad](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   >[!NOTE]
   > La password, nota anche come "segreto client", verrà usata per ottenere i token di autenticazione.

   ```azurecli-interactive
   $password = "<YOUR_PASSWORD>"
   $secureStringPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
   $aadApp = New-AzADApplication -DisplayName ImmersiveReaderAAD -IdentifierUris http://ImmersiveReaderAAD -Password $secureStringPassword

   // Display the Azure AD app info
   $aadApp
   ```

   Qui viene acquisito l'oggetto app Azure AD appena creato in una variabile **$aadApp** da usare nel passaggio successivo.

2. Successivamente, è necessario [creare un'entità servizio](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) per l'applicazione Azure ad.

   ```azurecli-interactive
   $principal = New-AzADServicePrincipal -ApplicationId $aadApp.ApplicationId

   // Display the service principal info
   $principal
   ```

   Qui viene acquisito l'oggetto entità servizio appena creato in una variabile **$Principal** da usare nel passaggio successivo.


3. L'ultimo passaggio consiste nell' [assegnare il ruolo "utente servizi cognitivi"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) all'entità servizio (ambito della risorsa). Assegnando un ruolo, si concede all'entità servizio l'accesso a questa risorsa. È possibile concedere all'entità servizio l'accesso a più risorse nella sottoscrizione.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId $principal.Id -Scope $resource.Id -RoleDefinitionName "Cognitive Services User"
   ```

   >[!NOTE]
   > Questo passaggio deve essere eseguito per ogni risorsa Reader immersiva creata. Se, ad esempio, si creano più risorse per aree globali diverse, sarà necessario eseguire questo passaggio per ognuna di queste risorse di area, in modo che l'autenticazione Azure AD funzioni per tutte le risorse. In alternativa, se si crea una nuova risorsa in un momento successivo, sarà necessario eseguire questo passaggio di assegnazione di ruolo anche per la nuova risorsa.


## <a name="obtain-an-azure-ad-token"></a>Ottenere un token di Azure AD

In questo esempio, la password viene usata per autenticare l'entità servizio per ottenere un token di Azure AD.

1. Ottenere il **TenantId**:
   ```azurecli-interactive
   $context = Get-AzContext
   $context.Tenant.Id
   ```

2. Ottenere un token:
   ```azurecli-interactive
   $authority = "https://login.windows.net/" + $context.Tenant.Id
   $resource = "https://cognitiveservices.azure.com/"
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $aadApp.ApplicationId, $password
   $token = $authContext.AcquireTokenAsync($resource, $clientCredential).Result
   $token
   ```

   >[!NOTE]
   > L'SDK immersive Reader usa la proprietà AccessToken del token, ad esempio $token. AccessToken. Per informazioni dettagliate, vedere gli [esempi](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples) di codice e di [riferimento](reference.md) dell'SDK.

In alternativa, l'entità servizio può essere autenticata con un certificato. Oltre a un'entità servizio, le entità utente sono supportate anche con le autorizzazioni delegate tramite un'altra applicazione Azure AD. In questo caso, anziché le password o i certificati, agli utenti viene richiesto di eseguire l'autenticazione a due fattori durante l'acquisizione dei token.

## <a name="next-steps"></a>Passaggi successivi

* Vedere l' [esercitazione su node. js](./tutorial-nodejs.md) per informazioni sulle altre operazioni che è possibile eseguire con l'SDK di immersive Reader usando node. js
* Vedere l' [esercitazione su Python](./tutorial-python.md) per informazioni sulle altre operazioni che è possibile eseguire con l'SDK di immersive Reader con Python
* Vedere l' [esercitazione Swift](./tutorial-ios-picture-immersive-reader.md) per informazioni sulle altre operazioni che è possibile eseguire con l'SDK di immersive Reader con Swift
* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](./reference.md)