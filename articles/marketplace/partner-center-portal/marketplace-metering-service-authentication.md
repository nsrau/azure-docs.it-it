---
title: Strategie di autenticazione del servizio di misurazione del Marketplace | Azure Marketplace
description: Strategie di autenticazione del servizio di misurazione supportate in Azure Marketplace.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/03/2020
ms.openlocfilehash: 31b9d4d57e38adcd079082a4f32770c4cbc8fbb3
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736201"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Strategie di autenticazione del servizio di misurazione del Marketplace

Il servizio di misurazione del Marketplace supporta due strategie di autenticazione:

* [Token di sicurezza Azure AD](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
* [identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 

Si spiegheranno quando e come usare le diverse strategie di autenticazione per inviare in modo sicuro contatori personalizzati usando il servizio di misurazione del Marketplace.

## <a name="using-the-azure-ad-security-token"></a>Uso del token di sicurezza Azure AD

I tipi di offerta applicabili sono applicazioni SaaS e Azure con tipo di piano di applicazioni gestite.  

Inviare contatori personalizzati usando un ID applicazione fisso predefinito per l'autenticazione.

Per le offerte SaaS, Azure AD è l'unica opzione disponibile.

Per le applicazioni Azure con piano di applicazione gestito, è consigliabile usare questa strategia nei casi seguenti:

* Si dispone già di un meccanismo per comunicare con i servizi back-end e si desidera estendere questo meccanismo per creare contatori personalizzati da un servizio centrale.
* La logica di contatori personalizzati è complessa.  Eseguire questa logica in una posizione centrale, anziché le risorse dell'applicazione gestita.

Dopo aver registrato l'applicazione, è possibile richiedere a livello di codice un token di sicurezza Azure AD. Il server di pubblicazione deve usare questo token ed effettuare una richiesta per risolverlo.

Per ulteriori informazioni su questi token, vedere [Azure Active Directory token di accesso](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Ottenere un token basato sull'app Azure AD

#### <a name="http-method"></a>Metodo HTTP

**Inserisci**

#### <a name="request-url"></a>*URL richiesta*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*Parametro URI*

|  **Nome parametro** |  **Richiesto**  |  **Descrizione**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | ID tenant dell'applicazione Azure AD registrata.   |
| | | |

#### <a name="request-header"></a>*Intestazione della richiesta*

|  **Nome dell'intestazione**    |  **Richiesto**  |  **Descrizione**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | Tipo di contenuto associato alla richiesta. Il valore predefinito è `application/x-www-form-urlencoded`.  |
| | | |

#### <a name="request-body"></a>*Corpo della richiesta*

|  **Nome proprietà**  |  **Richiesto**  |  **Descrizione**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | Tipo di concessione. Il valore predefinito è `client_credentials`. |
|  `Client_id`        |   True         | Identificatore del client/app associato all'app di Azure AD.|
|  `client_secret`    |   True         | Password associata all'app Azure AD.  |
|  `Resource`         |   True         | Risorsa di destinazione per cui è richiesto il token. Il valore predefinito è `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`.  |
| | | |

#### <a name="response"></a>*Risposta*

|  **Nome**    |  **Tipo**  |  **Descrizione**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | La richiesta è riuscita.  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

Token di risposta di esempio:

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Uso del token di identità gestito di Azure

Il tipo di offerta applicabile è applicazioni Azure con tipo di piano di applicazione gestito.

L'uso di questo approccio consente all'identità delle risorse distribuite di eseguire l'autenticazione per l'invio di eventi di utilizzo di contatori personalizzati.  È possibile incorporare il codice che genera l'utilizzo entro i limiti della distribuzione.

>[!Note]
>Il server di pubblicazione deve assicurarsi che le risorse che emettono l'utilizzo siano bloccate, quindi non verranno manomesse.

L'applicazione gestita può contenere diversi tipi di risorse, dalle macchine virtuali alle funzioni di Azure.  Per altre informazioni su come eseguire l'autenticazione usando identità gestite per diversi servizi, vedere [come usare le identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-can-i-use-managed-identities-for-azure-resources).

Ad esempio, attenersi alla procedura seguente per eseguire l'autenticazione tramite una macchina virtuale Windows,

1. Verificare che l'identità gestita sia configurata utilizzando uno dei metodi seguenti:
    * [Interfaccia utente di portale di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)
    * [CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
    * [PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
    * [Modello di Azure Resource Manager](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
    * [REST](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-rest-vm#system-assigned-managed-identity)
    * [Azure SDK](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

1. Ottenere un token di accesso per l'ID dell'applicazione del servizio`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`di controllo del Marketplace () usando l'identità di sistema, RDP per la macchina virtuale, aprire la console di PowerShell ed eseguire il comando seguente.

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. Ottenere l'ID app gestita dalla proprietà' ManagedBy ' dei gruppi di risorse correnti

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 
    
    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Il servizio di misurazione del Marketplace richiede l'utilizzo di `resourceID`un, `resourceUsageId` e se un'applicazione gestita.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. Usare l' [API del servizio di misurazione del Marketplace](https://review.docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis?branch=pr-en-us-101847) per emettere l'utilizzo.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'offerta per un'applicazione di Azure](./create-new-azure-apps-offer.md)