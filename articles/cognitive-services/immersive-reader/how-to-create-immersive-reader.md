---
title: Creare una risorsa dello strumento di lettura immersiva
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come creare una nuova risorsa Lettore Immersive con un sottodominio personalizzato e quindi configurare Azure AD nel tenant di Azure.This article show you how to create a new Immersive Reader resource with a custom subdomain and then configure Azure AD in your Azure tenant.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 41efe4592c65ae3cdd85ce1b212554e50691905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330720"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Creare una risorsa Lettore immersivo e configurare l'autenticazione di Azure Active DirectoryCreate an Immersive Reader resource and configure Azure Active Directory authentication

In questo articolo viene fornito uno script che creerà una risorsa Lettore immersivo e verrà configurata l'autenticazione di Azure Active Directory (Azure AD). Ogni volta che viene creata una risorsa Lettore Immersive, con questo script o nel portale, è necessario configurarla anche con autorizzazioni di Azure AD. Questo script vi aiuterà con questo.

Lo script è progettato per creare e configurare tutte le risorse di Lettore Immersive e Azure AD necessarie per l'utente in un unico passaggio. Tuttavia, è anche possibile configurare solo l'autenticazione di Azure AD per una risorsa Lettore Immersive esistente, se ad esempio, ne è già stata creata una nel portale di Azure.However, you can also just configure Azure AD authentication for an existing Immersive Reader resource, if you can one already created one in the Azure portal.

Per alcuni clienti, potrebbe essere necessario creare più risorse di Reader Immersive, per lo sviluppo e la produzione o forse per più aree diverse in cui viene distribuito il servizio. In questi casi, è possibile tornare indietro e usare lo script più volte per creare risorse di Reader Immersive diverse e configurarle con le autorizzazioni di Azure AD.

Lo script è progettato per essere flessibile. Verranno innanzitutto cercate le risorse Immersive Reader e Azure AD esistenti nella sottoscrizione e le creerà solo se non esistono già. Se è la prima volta che crei una risorsa Lettore Immersive, lo script farà tutto il necessario. Se si vuole usarlo solo per configurare Azure AD per una risorsa Lettore immersivo esistente creata nel portale, verrà eseguito anche questo. Può anche essere utilizzato per creare e configurare più risorse di Immersive Reader.

## <a name="set-up-powershell-environment"></a>Configurare l'ambiente PowerShellSet up PowerShell environment

1. Iniziare aprendo [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Verificare che la shell cloud sia impostata su PowerShell `pwsh`nell'elenco a discesa in alto a sinistra o digitando .

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
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

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

1. Eseguire la `Create-ImmersiveReaderResource`funzione , fornendo i parametri in base alle esigenze.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName '<SUBSCRIPTION_NAME>' `
      -ResourceName '<RESOURCE_NAME>' `
      -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' `
      -ResourceSKU '<RESOURCE_SKU>' `
      -ResourceLocation '<RESOURCE_LOCATION>' `
      -ResourceGroupName '<RESOURCE_GROUP_NAME>' `
      -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' `
      -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' `
      -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' `
      -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>'
    ```

    | Parametro | Commenti |
    | --- | --- |
    | SubscriptionName |Nome della sottoscrizione di Azure da usare per la risorsa Lettore Immersivo. Per creare una risorsa, è necessario disporre di una sottoscrizione. |
    | NomeRisorsa |  Deve essere alfanumerico e può contenere '-', purché '-' non sia il primo o l'ultimo carattere. La lunghezza non può superare i 63 caratteri.|
    | Sottodominio delle risorse |È necessario un sottodominio personalizzato per la risorsa Lettore immersivo. Il sottodominio viene utilizzato dall'SDK quando si chiama il servizio Immersive Reader per avviare il lettore. Il sottodominio deve essere univoco a livello globale. Il sottodominio deve essere alfanumerico e può contenere '-', purché '-' non sia il primo o l'ultimo carattere. La lunghezza non può superare i 63 caratteri. Questo parametro è facoltativo se la risorsa esiste già. |
    | RisorsaSKU |Opzioni: `S0`. Visitare la [pagina dei prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) per ulteriori informazioni su ogni SKU disponibile. Questo parametro è facoltativo se la risorsa esiste già. |
    | ResourceLocation |Opzioni: `eastus` `eastus2`, `southcentralus` `westus`, `westus2` `australiaeast`, `southeastasia` `centralindia`, `japaneast` `northeurope`, `uksouth` `westeurope`, , , , , , , . Questo parametro è facoltativo se la risorsa esiste già. |
    | ResourceGroupName |Le risorse vengono create nei gruppi di risorse all'interno delle sottoscrizioni. Specificare il nome di un gruppo di risorse esistente. Se il gruppo di risorse non esiste già, ne verrà creato uno nuovo con questo nome. |
    | ResourceGroupLocation |Se il gruppo di risorse non esiste, è necessario specificare una posizione in cui creare il gruppo. Per trovare un elenco `az account list-locations`di posizioni, eseguire . Utilizzare la proprietà *name* (senza spazi) del risultato restituito. Questo parametro è facoltativo se il gruppo di risorse esiste già. |
    | Nome visualizzato aAADApp |Nome visualizzato dell'applicazione Azure Active Directory. Se non viene trovata un'applicazione Azure AD esistente, verrà creata una nuova applicazione con questo nome. Questo parametro è facoltativo se l'applicazione Azure AD esiste già. |
    | AADAppIdentifierUri |URI per l'app Azure AD. Se non viene trovata un'app Azure AD esistente, ne verrà creata una nuova con questo URI. Ad esempio: `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Password creata che verrà utilizzata in un secondo momento per l'autenticazione durante l'acquisizione di un token per avviare Immersive Reader. La password deve avere una lunghezza di almeno 16 caratteri, contenere almeno 1 carattere speciale e almeno 1 carattere numerico. |

1. Copiare l'output JSON in un file di testo per un utilizzo successivo. L'output sarà simile al seguente.

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
* Guarda il [tutorial Swift](./tutorial-ios-picture-immersive-reader.md) per vedere cos'altro puoi fare con Immersive Reader SDK utilizzando Swift
* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](./reference.md)




