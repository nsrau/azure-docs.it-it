---
title: Strategie di autenticazione del servizio di analisi del Marketplace | Azure Marketplace
description: Strategie di autenticazione del servizio di analisi supportate in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: e24dcdc2de94fe73f6bb83dcc03bbd1139503784
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120756"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Strategie di autenticazione del servizio di analisi del Marketplace

Il servizio di analisi del Marketplace supporta due strategie di autenticazione:

* [Token di sicurezza di Azure AD](../../active-directory/develop/access-tokens.md)
* [Identità gestite](../../active-directory/managed-identities-azure-resources/overview.md) 

Verrà spiegato quando e come usare le diverse strategie di autenticazione per inviare in modo sicuro contatori personalizzati usando il servizio di analisi del Marketplace.

## <a name="using-the-azure-ad-security-token"></a>Utilizzo del token di sicurezza Azure AD

I tipi di offerta applicabili sono SaaS transazionale e applicazioni Azure con tipo di piano di applicazione gestito.  

Inviare contatori personalizzati usando un ID applicazione predefinito Azure AD predefinito per l'autenticazione.

Per le offerte SaaS, questa è l'unica opzione disponibile. Si tratta di un passaggio obbligatorio per la pubblicazione di qualsiasi offerta SaaS, come descritto in [registrare un'applicazione SaaS](./pc-saas-registration.md).

Per le applicazioni Azure con piano dell'applicazione gestita è consigliabile usare questa strategia nei casi seguenti:

* Si dispone già di un meccanismo per comunicare con i servizi back-end e si desidera estendere questo meccanismo per generare contatori personalizzati da un servizio centrale.
* La logica dei contatori personalizzati è complessa.  Eseguire questa logica in una posizione centrale, invece delle risorse dell'applicazione gestita.

Dopo aver registrato l'applicazione, è possibile richiedere a livello di codice un token di sicurezza di Azure AD. Si presuppone che il server di pubblicazione usi questo token ed esegua una richiesta per risolverlo.

Per altre informazioni su questi token, vedere [Token di accesso di Azure Active Directory](../../active-directory/develop/access-tokens.md).

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Ottenere un token basato sull'app Azure AD

#### <a name="http-method"></a>Metodo HTTP

**POST**

#### <a name="request-url"></a>*Request URL (URL richiesta)*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*Parametro URI*

|  **Nome parametro** |  **Obbligatorio**  |  **Descrizione**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | ID tenant dell'applicazione Azure AD registrata.   |
| | | |

#### <a name="request-header"></a>*Intestazione della richiesta*

|  **Nome dell'intestazione**    |  **Obbligatorio**  |  **Descrizione**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | Tipo di contenuto associato alla richiesta. Il valore predefinito è `application/x-www-form-urlencoded`.  |
| | | |

#### <a name="request-body"></a>*Corpo della richiesta*

|  **Nome proprietà**  |  **Obbligatorio**  |  **Descrizione**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | Tipo di concessione. Usare `client_credentials`. |
|  `Client_id`        |   True         | Identificatore del client/app associato all'app di Azure AD.|
|  `client_secret`    |   True         | Segreto associato all'app Azure AD.  |
|  `Resource`         |   True         | Risorsa di destinazione per cui è richiesto il token. Usare `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`. |
| | | |

#### <a name="response"></a>*Risposta*

|  **Nome**    |  **Tipo**  |  **Descrizione**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | La richiesta ha avuto esito positivo.  |
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

## <a name="using-the-azure-managed-identities-token"></a>Uso del token delle identità gestite di Azure

Il tipo di offerta applicabile è applicazioni Azure con tipo di piano dell'applicazione gestita.

L'uso di questo approccio consente all'identità delle risorse distribuite di eseguire l'autenticazione per l'invio di eventi di utilizzo dei contatori personalizzati.  È possibile incorporare il codice che genera i dati sull'utilizzo entro i limiti della distribuzione.

>[!Note]
>Il server di pubblicazione deve assicurarsi che le risorse che emettono l'utilizzo siano bloccate, quindi non verranno manomesse.

L'applicazione gestita può contenere diversi tipi di risorse, dalle macchine virtuali alle funzioni di Azure.  Per altre informazioni su come eseguire l'autenticazione usando identità gestite per diversi servizi, vedere [come usare le identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md#how-can-i-use-managed-identities-for-azure-resources).

Ad esempio, seguire questa procedura per eseguire l'autenticazione usando una macchina virtuale Windows,

1. Verificare che l'identità gestita sia configurata utilizzando uno dei metodi seguenti:
    * [Interfaccia utente del portale di Azure](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
    * [CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
    * [PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
    * [Modello di Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
    * [Rest](../../active-directory/managed-identities-azure-resources/qs-configure-rest-vm.md#system-assigned-managed-identity))
    * [Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

1. Ottenere un token di accesso per l'ID dell'applicazione del servizio di analisi del Marketplace (`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`) usando l'identità di sistema, eseguire RDP nella macchina virtuale, aprire la console PowerShell ed eseguire il comando seguente

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. Ottenere l'ID dell'app gestita dalla proprietà "ManagedBy" dei gruppi di risorse correnti

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 
    
    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Il servizio di analisi del Marketplace richiede di generare report sull'utilizzo su un `resourceID` e `resourceUsageId` se si tratta di un'applicazione gestita.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. Usare l'[API del servizio di analisi Marketplace](./marketplace-metering-service-apis.md) per generare dati sull'utilizzo.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'offerta di applicazione Azure](./create-new-azure-apps-offer.md)
* [Creare un'offerta SaaS transazionale](./offer-creation-checklist.md)