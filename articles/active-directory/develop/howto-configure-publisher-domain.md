---
title: Configurare il dominio di pubblicazione di un'applicazione | Azure
description: Informazioni su come configurare il dominio di pubblicazione di un'applicazione per informare gli utenti in cui vengono inviate le informazioni.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efbf448770bfcf797d6bf01cd3c28dc98023adff
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502279"
---
# <a name="how-to-configure-an-applications-publisher-domain-preview"></a>Procedura: Configurare il dominio di pubblicazione di un'applicazione (anteprima)

Dominio di pubblicazione di un'applicazione viene visualizzata agli utenti nel [richiesta di consenso dell'applicazione](application-consent-experience.md) per informare gli utenti in cui vengono inviate le informazioni. Applicazioni multi-tenant registrate dopo 21 maggio 2019 che non dispongono di un dominio di pubblicazione visualizzato come **non verificati**. Applicazioni multi-tenant sono applicazioni che supportano gli account di fuori di una singola directory dell'organizzazione; ad esempio, supporta tutti gli account Azure AD, o tutti gli account di Azure AD e gli account Microsoft personali.

## <a name="new-applications"></a>Nuove applicazioni

Quando si registra una nuova app, il dominio del server di pubblicazione dell'app può essere impostato su un valore predefinito. Il valore dipende in cui è registrata l'app, in particolare se l'app viene registrata in un tenant e indica se il tenant dispone del tenant di domini verificati.

Se sono presenti domini verificati tenant, il dominio di pubblicazione dell'app predefinita sarà il dominio verificato primario del tenant. Se sono disponibili tenant non verificato domini (che è il caso quando l'applicazione non è registrata in un tenant), dominio di pubblicazione dell'app verrà impostato su null.

La tabella seguente riepiloga il comportamento predefinito del valore del dominio di pubblicazione.  

| Domini verificati tenant | Valore predefinito di dominio di pubblicazione |
|-------------------------|----------------------------|
| Null | Null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>-domain2.com (primario) | domain2.com |

Se non è impostato alcun dominio di pubblicazione di un'applicazione multi-tenant o se è impostato su un dominio che termina con. onmicrosoft.com, richiesta di consenso dell'app mostrerà **non verificati** al posto del dominio di pubblicazione.

## <a name="grandfathered-applications"></a>Applicazioni grandfathered

Se l'app è stata registrata prima del 21 maggio 2019, richiesta di consenso dell'applicazione non visualizzerà **non verificati** se non è stato impostato un dominio di pubblicazione. È consigliabile impostare il server di pubblicazione dominio valore in modo che gli utenti possono visualizzare queste informazioni nella richiesta di consenso dell'app.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Configurare il dominio di pubblicazione nel portale di Azure

Per impostare il dominio dell'app server di pubblicazione, seguire questa procedura.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.

1. Se l'account è presente in più di un tenant di Azure AD:
   1. Selezionare il profilo dal menu nell'angolo superiore destro della pagina e quindi **Cambia directory**.
   1. Modificare la sessione per il tenant di Azure AD in cui si desidera creare l'applicazione.

1. Passare a [Azure Active Directory > registrazioni per l'App](https://go.microsoft.com/fwlink/?linkid=2083908) per trovare e selezionare l'app che si desidera configurare.

   Dopo aver selezionato l'app, si noterà l'app **Panoramica** pagina.

1. Dell'app **Overview** pagina, selezionare la **Branding** sezione.

1. Trovare il **dominio di pubblicazione** campo e selezionare una delle opzioni seguenti:

   - Selezionare **configurare un dominio** se è stato configurato un dominio già.
   - Selezionare **dominio di aggiornamento** se è già stato configurato un dominio.

Se l'app viene registrata in un tenant, si noterà due schede selezionare da: **Selezionare un dominio verificato** e **verificare un nuovo dominio**.

Se l'app non è registrato in un tenant, verrà visualizzata solo l'opzione per verificare un nuovo dominio dell'applicazione.

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

1. Sostituire il segnaposto *{YOUR-APP-ID-HERE}* con l'ID applicazione (client) che corrisponde all'app.

1. Ospitare il file in: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`. Sostituire il segnaposto *{YOUR-DOMAIN-HERE}* corrisponda al dominio verificato.

1. Scegliere il **verificare e salvare dominio** pulsante.

### <a name="to-select-a-verified-domain"></a>Per selezionare un dominio verificato

- Se il tenant dispone di domini verificati, selezionare uno dei domini dal **selezionare un dominio verificato** elenco a discesa.

## <a name="implications-on-the-app-consent-prompt"></a>Implicazioni per l'app richiesta di consenso

Configurazione del dominio di pubblicazione ha un impatto su cosa vedono gli utenti quando viene richiesto il consenso dell'app. Per comprendere completamente i componenti della richiesta di consenso, vedere [informazioni sulle esperienze di consenso dell'applicazione](application-consent-experience.md).

Nella tabella seguente descrive il comportamento per le applicazioni create prima del 21 maggio 2019.

![Richiesta di consenso per le app create prima del 21 maggio 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Il comportamento per le nuove applicazioni create dopo il 21 maggio 2019 varia in base al tipo di applicazione e il dominio del server di pubblicazione. Nella tabella seguente vengono descritte le modifiche si prevedibili con le diverse combinazioni di configurazioni.

![Richiesta di consenso per le app create dopo il 21 maggio 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Implicazioni relative gli URI di reindirizzamento

Le applicazioni che consentono l'accesso degli utenti con qualsiasi account aziendale o dell'istituto di istruzione o account Microsoft personale ([multi-tenant](single-and-multi-tenant-apps.md)) sono soggetti a poche restrizioni quando si specificano URI di reindirizzamento.

### <a name="single-root-domain-restriction"></a>Restrizione di dominio di sola radice

Quando il valore di dominio server di pubblicazione per le app multi-tenant è impostato su null, App sono limitate a condividere un unico dominio principale per l'URI di reindirizzamento. Ad esempio, la seguente combinazione di valori non è consentita perché il dominio radice, contoso.com, non corrisponde a fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Restrizioni di sottodominio

Sono consentiti i sottodomini, ma è necessario registrare in modo esplicito il dominio radice. Ad esempio, mentre gli URI seguenti condividono un unico dominio principale, non è consentita la combinazione.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Tuttavia, se lo sviluppatore aggiunge in modo esplicito il dominio radice, è consentita la combinazione.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Eccezioni

I casi seguenti non sono soggette alle restrizioni di dominio di sola radice:

- App a tenant singolo, o le app che gli account in una singola directory di destinazione
- Uso di localhost come URI di reindirizzamento
- URI di reindirizzamento con schemi personalizzati (diversi da HTTP o HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Configurare il dominio di pubblicazione a livello di codice

Attualmente, non vi è alcun supporto di API REST o PowerShell per configurare a livello di codice di dominio di pubblicazione.
