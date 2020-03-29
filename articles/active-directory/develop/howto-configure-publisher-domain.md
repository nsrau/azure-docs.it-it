---
title: Configurare il dominio dell'editore di un'app Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come configurare il dominio dell'editore di un'applicazione per consentire agli utenti di sapere dove vengono inviate le informazioni.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 68040c8ee22454c300296493b6c840eabbca98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697133"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Procedura: configurare il dominio dell'editore di un'applicazioneHow to: Configure an application's publisher domain

Il dominio dell'editore di un'applicazione viene visualizzato agli utenti quando viene richiesta il [consenso dell'applicazione](application-consent-experience.md) per consentire agli utenti di sapere dove vengono inviate le informazioni. Le applicazioni multi-tenant registrate dopo il 21 maggio 2019 che non dispongono di un dominio dell'editore vengono visualizzate come **non verificate.** Le applicazioni multi-tenant sono applicazioni che supportano account esterni a una singola directory organizzativa; ad esempio, supportare tutti gli account Azure AD o tutti gli account Azure AD e gli account Microsoft personali.

## <a name="new-applications"></a>Nuove applicazioni

Quando registri una nuova app, il dominio dell'editore della tua app può essere impostato su un valore predefinito. Il valore dipende da dove è registrata l'app, in particolare se l'app è registrata in un tenant e se il tenant ha domini verificati dal tenant.

Se sono presenti domini verificati dal tenant, per impostazione predefinita il dominio dell'editore dell'app verrà impostato sul dominio verificato primario del tenant. Se non sono presenti domini verificati dal tenant (ovvero quando l'applicazione non è registrata in un tenant), il dominio dell'editore dell'app verrà impostato su null.

Nella tabella seguente viene riepilogato il comportamento predefinito del valore del dominio dell'editore.  

| Domini verificati dal tenant | Valore predefinito del dominio dell'editore |
|-------------------------|----------------------------|
| Null | Null |
| .onmicrosoft.com | .onmicrosoft.com |
| - .onmicrosoft.com<br/>- domain1.com<br/>- domain2.com (principale) | domain2.com |

Se il dominio dell'editore di un'applicazione multi-tenant non è impostato o se è impostato su un dominio che termina con .onmicrosoft.com, la richiesta di consenso dell'app mostrerà **non verificata** al posto del dominio dell'editore.

## <a name="grandfathered-applications"></a>Applicazioni nonno

Se l'app è stata registrata prima del 21 maggio 2019, la richiesta di consenso dell'applicazione non verrà **visualizzata come non verificata** se non è stato impostato un dominio dell'editore. Ti consigliamo di impostare il valore del dominio dell'editore in modo che gli utenti possano visualizzare queste informazioni nella richiesta di consenso dell'app.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Configurare il dominio dell'editore tramite il portale di AzureConfigure publisher domain using the Azure portal

Per impostare il dominio dell'editore dell'app, segui questi passaggi.

1. Accedere al [portale](https://portal.azure.com) di Azure usando un account aziendale o dell'istituto di istruzione o un account Microsoft personale.

1. Se l'account è presente in più di un tenant di Azure AD:
   1. Selezionare il profilo dal menu nell'angolo superiore destro della pagina e quindi fare clic su **Cambia directory**.
   1. Impostare la sessione sul tenant di Azure AD in cui si vuole creare l'applicazione.

1. Passare ad [Azure Active Directory > le registrazioni](https://go.microsoft.com/fwlink/?linkid=2083908) delle app per trovare e selezionare l'app che si vuole configurare.

   Dopo aver selezionato l'app, verrà visualizzata la pagina **Panoramica** dell'app.

1. Nella pagina **Panoramica** dell'app, seleziona la sezione **Personalizzazione.**

1. Individuare il campo **Dominio server** di pubblicazione e selezionare una delle opzioni seguenti:

   - Selezionare **Configura un dominio** se non è già stato configurato un dominio.
   - Selezionare **Aggiorna dominio** se è già stato configurato un dominio.

Se l'app è registrata in un tenant, verranno visualizzate due schede tra cui scegliere: **Selezionare un dominio verificato** e **Verificare un nuovo dominio.**

Se l'app non è registrata in un tenant, verrà visualizzata solo l'opzione per verificare un nuovo dominio per l'applicazione.

### <a name="to-verify-a-new-domain-for-your-app"></a>Per verificare un nuovo dominio per l'app

1. Creare un `microsoft-identity-association.json` file denominato e incollare il frammento di codice JSON seguente.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Sostituire il segnaposto *"YOUR-APP-ID-HERE"* con l'ID dell'applicazione (client) che corrisponde all'app.

1. Ospitare il file `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`in: . Sostituire il segnaposto *"YOUR-DOMAIN-HERE"* in modo che corrisponda al dominio verificato.

1. Fare clic sul pulsante **Verifica e salva dominio.**

### <a name="to-select-a-verified-domain"></a>Per selezionare un dominio verificato

- Se il tenant ha verificato i domini, selezionare uno dei domini nell'elenco a discesa **Seleziona un dominio verificato.**

>[!Note]
> L'intestazione 'Content-Type' prevista `application/json`da restituire è . Si può ottenere un errore come accennato di seguito se si utilizza qualcos'altro come`application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Implicazioni sulla richiesta di consenso dell'app

La configurazione del dominio dell'editore ha un impatto su ciò che gli utenti vedono nella richiesta di consenso dell'app. Per comprendere appieno i componenti della richiesta di consenso, vedere Informazioni sulle esperienze di [consenso dell'applicazione](application-consent-experience.md).

Nella tabella seguente viene descritto il comportamento per le applicazioni create prima del 21 maggio 2019.

![Richiesta di consenso per le app create prima del 21 maggio 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Il comportamento per le nuove applicazioni create dopo il 21 maggio 2019 dipenderà dal dominio dell'editore e dal tipo di applicazione. Nella tabella seguente vengono descritte le modifiche che è necessario visualizzare con le diverse combinazioni di configurazioni.

![Richiesta di consenso per le app create dopo il 21 maggio 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Implicazioni sugli URI di reindirizzamentoImplications on redirect URIs

Le applicazioni che accedono agli utenti con qualsiasi account aziendale o dell'istituto di istruzione o agli account Microsoft personali ([multi-tenant](single-and-multi-tenant-apps.md)) sono soggette a poche restrizioni quando si specificano gli URI di reindirizzamento.

### <a name="single-root-domain-restriction"></a>Restrizione del dominio radice singolo

Quando il valore del dominio dell'editore per le app multi-tenant è impostato su null, le app sono limitate a condividere un singolo dominio radice per gli URI di reindirizzamento. Ad esempio, la seguente combinazione di valori non è consentita perché il dominio radice, contoso.com, non corrisponde a fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Restrizioni dei sottodomini

I sottodomini sono consentiti, ma è necessario registrare in modo esplicito il dominio radice. Ad esempio, mentre gli URI seguenti condividono un singolo dominio radice, la combinazione non è consentita.

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

I seguenti casi non sono soggetti alla restrizione del singolo dominio radice:

- App tenant singole o app destinate ad account in una singola directory
- Utilizzo di localhost come URI di reindirizzamento
- Reindirizzare gli URI con schemi personalizzati (non HTTP o HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Configurare il dominio dell'editore a livello di codiceConfigure publisher domain programmatically

Attualmente, non esiste alcun supporto dell'API REST o PowerShell per configurare il dominio dell'editore a livello di codice.
