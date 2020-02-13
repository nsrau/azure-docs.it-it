---
title: Creare una risorsa dello strumento di lettura immersiva
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
ms.openlocfilehash: 187989153ae32704df8a7ff061e19fe35206e0e8
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162515"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Creare una risorsa Reader immersiva e configurare l'autenticazione Azure Active Directory

In questo articolo viene fornito uno script che creerà una risorsa Reader immersiva e configurerà l'autenticazione Azure Active Directory (Azure AD). Ogni volta che viene creata una risorsa di lettura immersiva, sia con questo script che nel portale, deve essere configurata anche con Azure AD autorizzazioni. Questo script consentirà di risolvere il problema.

Lo script è progettato per creare e configurare tutte le risorse di Reader immersive e Azure AD necessarie in un unico passaggio. Tuttavia, è anche possibile configurare solo Azure AD l'autenticazione per una risorsa di lettura immersiva esistente, se ad esempio è già stata creata una nella portale di Azure.

Per alcuni clienti, potrebbe essere necessario creare più risorse di Reader immersive, per lo sviluppo e la produzione o per più aree diverse in cui il servizio è distribuito. In questi casi, è possibile tornare indietro e usare lo script più volte per creare risorse di lettura Immersive diverse e configurarle con le autorizzazioni Azure AD.

Lo script è progettato per essere flessibile. In questo modo, si cercherà prima di tutto un reader immersivo e Azure AD risorse nella sottoscrizione e li si creerà solo se necessario, se non sono già esistenti. Se è la prima volta che si crea una risorsa di lettura immersiva, lo script eseguirà tutte le operazioni necessarie. Se si vuole usarlo solo per configurare Azure AD per una risorsa Reader immersiva esistente creata nel portale, verrà eseguita anche questa operazione. Può anche essere usato per creare e configurare più risorse di Reader immersive.

## <a name="set-up-powershell-environment"></a>Configurare l'ambiente PowerShell

1. Per iniziare, aprire il [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Verificare che cloud Shell sia impostato su PowerShell nell'elenco a discesa in alto a sinistra o digitando `pwsh`.

1. Copiare e incollare il frammento di codice seguente nella shell.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret
    )
    {
        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully"
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. Eseguire la funzione `Create-ImmersiveReaderResource`, specificando i parametri in base alle esigenze.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName <SUBSCRIPTION_NAME> `
      -ResourceName <RESOURCE_NAME> `
      -ResourceSubdomain <RESOURCE_SUBDOMAIN> `
      -ResourceSKU <RESOURCE_SKU> `
      -ResourceLocation <RESOURCE_LOCATION> `
      -ResourceGroupName <RESOURCE_GROUP_NAME> `
      -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
      -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
      -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
      -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
    ```

    | Parametro | Commenti |
    | --- | --- |
    | SubscriptionName |Nome della sottoscrizione di Azure da usare per la risorsa Reader immersiva. Per creare una risorsa, è necessario disporre di una sottoscrizione. |
    | NomeRisorsa |  Deve essere alfanumerico e può contenere '-', purché'-' non sia il primo o l'ultimo carattere. La lunghezza non può superare i 63 caratteri.|
    | ResourceSubdomain |Per la risorsa Reader immersivo è necessario un sottodominio personalizzato. Il sottodominio viene usato dall'SDK quando si chiama il servizio di lettura immersiva per avviare il lettore. Il sottodominio deve essere globalmente univoco. Il sottodominio deve essere alfanumerico e può contenere '-', purché'-' non sia il primo o l'ultimo carattere. La lunghezza non può superare i 63 caratteri. Questo parametro è facoltativo se la risorsa esiste già. |
    | ResourceSKU |Opzioni: `S0`. Visitare la [pagina dei prezzi di servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) per altre informazioni su ogni SKU disponibile. Questo parametro è facoltativo se la risorsa esiste già. |
    | ResourceLocation |Opzioni: `eastus`, `eastus2`, `southcentralus`, `westus`, `westus2`, `australiaeast`, `southeastasia`, `centralindia`, `japaneast`, `northeurope`, `uksouth``westeurope`. Questo parametro è facoltativo se la risorsa esiste già. |
    | ResourceGroupName |Le risorse vengono create in gruppi di risorse all'interno delle sottoscrizioni. Specificare il nome di un gruppo di risorse esistente. Se il gruppo di risorse non esiste già, ne verrà creato uno nuovo con questo nome. |
    | ResourceGroupLocation |Se il gruppo di risorse non esiste, è necessario specificare un percorso in cui creare il gruppo. Per trovare un elenco di percorsi, eseguire `az account list-locations`. Usare la proprietà *Name* (senza spazi) del risultato restituito. Questo parametro è facoltativo se il gruppo di risorse esiste già. |
    | AADAppDisplayName |Nome visualizzato dell'applicazione Azure Active Directory. Se non viene trovata un'applicazione Azure AD esistente, ne verrà creato uno nuovo con questo nome. Questo parametro è facoltativo se l'applicazione Azure AD esiste già. |
    | AADAppIdentifierUri |URI per l'app Azure AD. Se non viene trovata alcuna app Azure AD esistente, ne verrà creata una nuova con questo URI. Ad esempio: `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Password creata che verrà usata in seguito per eseguire l'autenticazione quando si acquisisce un token per avviare il lettore immersivo. La password deve avere una lunghezza di almeno 16 caratteri, contenere almeno un carattere speciale e contenere almeno un carattere numerico. |

1. Copiare l'output JSON in un file di testo per usarlo in seguito. L'output sarà simile al seguente.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare l'[esercitazione per Node.js](./quickstart-nodejs.md) per scoprire quali altre attività è possibile eseguire con Immersive Reader SDK usando Node.js
* Visualizzare l'[esercitazione per Python](./tutorial-python.md) per scoprire quali altre attività è possibile eseguire con Immersive Reader SDK usando Python
* Vedere l' [esercitazione Swift](./tutorial-ios-picture-immersive-reader.md) per informazioni sulle altre operazioni che è possibile eseguire con l'SDK di immersive Reader con Swift
* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](./reference.md)




