---
title: Configurare il dominio di pubblicazione di un'applicazione
titleSuffix: Microsoft identity platform
description: Informazioni su come configurare il dominio del server di pubblicazione di un'applicazione per consentire agli utenti di sapere dove vengono inviate le informazioni.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26ef28be328e01f8edcf898f123db55f262f286c
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803335"
---
# <a name="how-to-configure-an-applications-publisher-domain-preview"></a>Procedura: configurare un dominio di pubblicazione di un'applicazione (anteprima)

Il dominio di pubblicazione di un'applicazione viene visualizzato agli utenti nella [richiesta di consenso dell'applicazione](application-consent-experience.md) per consentire agli utenti di stabilire dove vengono inviate le informazioni. Le applicazioni multi-tenant registrate dopo il 21 maggio 2019 che non hanno un dominio di pubblicazione vengono visualizzate come non **verificate**. Le applicazioni multi-tenant sono applicazioni che supportano account esterni a una singola directory organizzativa; ad esempio, supporta tutti gli account Azure AD o supporta tutti gli account Azure AD e gli account Microsoft personali.

## <a name="new-applications"></a>Nuove applicazioni

Quando si registra una nuova app, il dominio dell'editore dell'app può essere impostato su un valore predefinito. Il valore dipende dalla posizione in cui è registrata l'app, in particolare se l'app viene registrata in un tenant e se il tenant ha domini verificati dal tenant.

Se sono presenti domini verificati dal tenant, per impostazione predefinita il dominio di pubblicazione dell'app verrà impostato sul dominio primario verificato del tenant. Se non sono presenti domini verificati dal tenant, ad esempio quando l'applicazione non è registrata in un tenant, il dominio del server di pubblicazione dell'applicazione verrà impostato su null.

Nella tabella seguente viene riepilogato il comportamento predefinito del valore di dominio del server di pubblicazione.  

| Domini verificati dal tenant | Valore predefinito del dominio di pubblicazione |
|-------------------------|----------------------------|
| Null | Null |
| *. onmicrosoft.com | *. onmicrosoft.com |
| -*. onmicrosoft.com<br/>-domain1.com<br/>-domain2.com (primario) | domain2.com |

Se non è impostato un dominio di pubblicazione di un'applicazione multi-tenant o se è impostato su un dominio che termina con. onmicrosoft.com, la richiesta di consenso dell'app verrà visualizzata non **verificata** al posto del dominio del server di pubblicazione.

## <a name="grandfathered-applications"></a>Applicazioni con nonno

Se l'app è stata registrata prima del 21 maggio 2019, la richiesta di consenso dell'applicazione non verrà **visualizzata se non** è stato impostato un dominio del server di pubblicazione. È consigliabile impostare il valore di dominio del server di pubblicazione in modo che gli utenti possano visualizzare queste informazioni nella richiesta di consenso dell'app.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Configurare il dominio di pubblicazione utilizzando il portale di Azure

Per impostare il dominio del server di pubblicazione dell'applicazione, attenersi alla seguente procedura.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.

1. Se l'account è presente in più di un tenant di Azure AD:
   1. Selezionare il profilo dal menu nell'angolo superiore destro della pagina, quindi **passare alla directory**.
   1. Modificare la sessione nel tenant Azure AD in cui si desidera creare l'applicazione.

1. Passare a [Azure Active Directory > registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) per trovare e selezionare l'app che si vuole configurare.

   Dopo aver selezionato l'app, verrà visualizzata la pagina **Panoramica** dell'app.

1. Dalla pagina **Panoramica** dell'app selezionare la sezione **personalizzazione** .

1. Individuare il campo **Publisher Domain** e selezionare una delle opzioni seguenti:

   - Selezionare **configura un dominio** se non è già stato configurato un dominio.
   - Selezionare **Aggiorna dominio** se è già stato configurato un dominio.

Se l'app è registrata in un tenant, verranno visualizzate due schede da selezionare: **selezionare un dominio verificato** e **verificare un nuovo dominio**.

Se l'app non è registrata in un tenant, verrà visualizzata solo l'opzione per verificare un nuovo dominio per l'applicazione.

### <a name="to-verify-a-new-domain-for-your-app"></a>Per verificare un nuovo dominio per l'app

1. Creare un file denominato `microsoft-identity-association.json` e incollare il frammento di codice JSON seguente.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Sostituire il segnaposto *{Your-App-ID-here}* con l'ID dell'applicazione (client) che corrisponde all'app.

1. Ospitare il file in: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`. Sostituire il segnaposto *{Your-Domain-here}* in modo che corrisponda al dominio verificato.

1. Fare clic sul pulsante **Verifica e salva dominio** .

### <a name="to-select-a-verified-domain"></a>Per selezionare un dominio verificato

- Se il tenant ha verificato domini, selezionare uno dei domini dall'elenco **a discesa selezionare un dominio verificato** .

>[!Note]
> L'intestazione ' Content-Type ' prevista restituita è `application/json`. È possibile che venga ricevuto un errore come indicato di seguito se si usa qualsiasi altra operazione, ad esempio `application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Implicazioni sulla richiesta di consenso dell'app

La configurazione del dominio di pubblicazione ha un effetto sugli elementi visualizzati dagli utenti nella richiesta di consenso dell'app. Per comprendere completamente i componenti della richiesta di consenso, vedere [informazioni sull'esperienza di consenso dell'applicazione](application-consent-experience.md).

Nella tabella seguente viene descritto il comportamento per le applicazioni create prima del 21 maggio 2019.

![Richiesta di consenso per le app create prima del 21 maggio 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Il comportamento per le nuove applicazioni create dopo il 21 maggio 2019 dipenderà dal dominio del server di pubblicazione e dal tipo di applicazione. Nella tabella seguente vengono descritte le modifiche che ci si aspetta di vedere con le diverse combinazioni di configurazioni.

![Richiesta di consenso per le app create dopo il 21 maggio 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Implicazioni sugli URI di Reindirizzamento

Le applicazioni che eseguono l'accesso agli utenti con un account aziendale o dell'Istituto di istruzione o account Microsoft personali ([multi-tenant](single-and-multi-tenant-apps.md)) sono soggette ad alcune restrizioni quando si specificano gli URI di reindirizzamento.

### <a name="single-root-domain-restriction"></a>Restrizione singolo dominio radice

Quando il valore di dominio del server di pubblicazione per le app multi-tenant è impostato su null, le app sono limitate a condividere un singolo dominio radice per gli URI di reindirizzamento. La seguente combinazione di valori, ad esempio, non è consentita perché il dominio radice, contoso.com, non corrisponde a fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Restrizioni del sottodominio

Sono consentiti sottodomini, ma è necessario registrare in modo esplicito il dominio radice. Ad esempio, mentre gli URI seguenti condividono un solo dominio radice, la combinazione non è consentita.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Tuttavia, se lo sviluppatore aggiunge in modo esplicito il dominio radice, la combinazione è consentita.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Eccezioni

I casi seguenti non sono soggetti alla restrizione del dominio radice singolo:

- App a tenant singolo o app destinate ad account in una singola directory
- Uso di localhost come URI di Reindirizzamento
- Reindirizza gli URI con schemi personalizzati (non HTTP o HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Configurare il dominio del server di pubblicazione a livello di codice

Attualmente non è disponibile un'API REST o il supporto di PowerShell per configurare il dominio del server di pubblicazione a livello di codice.
