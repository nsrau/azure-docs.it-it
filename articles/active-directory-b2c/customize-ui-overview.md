---
title: Personalizzare l'interfaccia utente
titleSuffix: Azure AD B2C
description: Informazioni su come personalizzare l'interfaccia utente per le applicazioni che usano Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d737d010f323a5d5b230091ad07ba530d25d6e51
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949407"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Personalizzare l'interfaccia utente in Azure Active Directory B2C

La personalizzazione e la personalizzazione dell'interfaccia utente che Azure Active Directory B2C (Azure AD B2C) Visualizza ai clienti contribuisce a offrire un'esperienza utente uniforme nell'applicazione. Queste esperienze includono l'iscrizione, l'accesso, la modifica del profilo e la reimpostazione della password. Questo articolo presenta i metodi di personalizzazione dell'interfaccia utente per i flussi utente e i criteri personalizzati.

## <a name="ui-customization-in-different-scenarios"></a>Personalizzazione dell'interfaccia utente in diversi scenari

Esistono diversi modi per personalizzare l'interfaccia utente dell'applicazione, ognuno dei quali è adatto a scenari diversi.

### <a name="user-flows"></a>Flussi degli utenti

Se si usano i [flussi utente](active-directory-b2c-reference-policies.md), è possibile modificare l'aspetto delle pagine del flusso utente usando *modelli di layout di pagina*predefiniti oppure usando il codice HTML e CSS. Entrambi i metodi sono illustrati più avanti in questo articolo.

Usare il [portale di Azure](tutorial-customize-ui.md) per configurare la personalizzazione dell'interfaccia utente per i flussi utente.

### <a name="custom-policies"></a>Criteri personalizzati

Se si usano [criteri personalizzati](active-directory-b2c-overview-custom.md) per fornire l'iscrizione o l'accesso, la reimpostazione della password o la modifica del profilo nell'applicazione, usare [i file dei criteri per personalizzare l'interfaccia utente](active-directory-b2c-ui-customization-custom.md).

Se è necessario fornire contenuti dinamici in base alla decisione di un cliente, usare criteri personalizzati che possono [modificare il contenuto della pagina in modo dinamico](active-directory-b2c-ui-customization-custom-dynamic.md) in base a un parametro inviato in una stringa di query. È ad esempio possibile modificare l'immagine di sfondo nella pagina Azure AD B2C iscrizione o accesso in base a un parametro passato dall'applicazione Web o per dispositivi mobili.

### <a name="javascript"></a>JavaScript

È possibile abilitare il codice JavaScript lato client sia nei [flussi utente](user-flow-javascript-overview.md) che nei [criteri personalizzati](page-layout.md).

### <a name="sign-in-only-ui-customization"></a>Personalizzazione dell'interfaccia utente di accesso solo

Se si fornisce solo l'accesso, insieme alla pagina di reimpostazione della password e ai messaggi di posta elettronica di verifica associati, usare gli stessi passaggi di personalizzazione usati per una [pagina di accesso Azure ad](../active-directory/fundamentals/customize-branding.md).

Se i clienti tentano di modificare il profilo prima dell'accesso, vengono reindirizzati a una pagina che viene personalizzata usando la stessa procedura usata per personalizzare la pagina di accesso Azure AD.

## <a name="page-layout-templates"></a>Modelli di layout di pagina

I flussi utente forniscono diversi modelli predefiniti che è possibile scegliere per offrire alle pagine dell'esperienza utente un aspetto professionale. Questi modelli di layout possono anche fungere da punto di partenza per la personalizzazione.

In **Personalizza** nel menu a sinistra selezionare **layout di pagina** e quindi selezionare **modello**.

![Elenco a discesa Selezione modello nella pagina flusso utente di portale di Azure](media/customize-ui-overview/template-selection.png)

Selezionare quindi un modello nell'elenco. Di seguito sono riportati alcuni esempi di pagine di accesso per ogni modello:

| Blu oceano | Grigio ardesia | Classico |
|:-:|:-:|:-:|
|![Esempio di modello blu oceano di cui è stato eseguito il rendering nella pagina di accesso per l'iscrizione](media/customize-ui-overview/template-ocean-blue.png)|![Esempio del modello grigio dello Slate visualizzato nella pagina di accesso per l'iscrizione](media/customize-ui-overview/template-slate-gray.png)|![Esempio del modello classico di cui è stato eseguito il rendering nella pagina di accesso per l'iscrizione](media/customize-ui-overview/template-classic.png)|

Quando si sceglie un modello, il layout selezionato viene applicato a tutte le pagine nel flusso utente e l'URI per ogni pagina è visibile nel campo **URI della pagina personalizzata**.

## <a name="custom-html-and-css"></a>HTML personalizzato e CSS

Azure AD B2C esegue il codice nel browser del cliente usando un approccio denominato [condivisione risorse tra le origini (CORS)](https://www.w3.org/TR/cors/).

In fase di esecuzione, il contenuto viene caricato da un URL specificato nel flusso utente o in un criterio personalizzato. Ogni pagina nell'esperienza utente carica il proprio contenuto dall'URL specificato per la pagina. Dopo che il contenuto è stato caricato dall'URL, viene unito a un frammento HTML inserito da Azure AD B2C, quindi la pagina viene visualizzata per il cliente.

Prima di usare i file HTML e CSS per personalizzare l'interfaccia utente, vedere le indicazioni seguenti:

- Azure AD B2C **unisce** contenuto HTML alle pagine. Non copiare e provare a modificare il contenuto predefinito incluso da Azure AD B2C. È preferibile creare il contenuto HTML da zero e usare il contenuto predefinito come riferimento.
- **JavaScript** può essere incluso nel contenuto personalizzato per i [flussi utente](user-flow-javascript-overview.md) e i [criteri personalizzati](javascript-samples.md).
- Le **versioni** supportate del browser sono:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Supporto limitato per Internet Explorer 9 e 8
  - Google Chrome 42.0 e versioni successive
  - Mozilla Firefox 38.0 e versioni successive
- Non includere **tag form** nel codice HTML. I tag form interferiscono con le operazioni POST generate dal codice HTML inserito da Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Dove archiviare il contenuto dell'interfaccia utente

Quando si usano i propri file HTML e CSS per personalizzare l'interfaccia utente, è possibile ospitare il contenuto dell'interfaccia utente in qualsiasi endpoint HTTPS disponibile pubblicamente che supporti CORS. Ad esempio, [archiviazione BLOB di Azure](../storage/blobs/storage-blobs-introduction.md), server Web, CDNS, AWS S3 o sistemi di condivisione di file.

L'aspetto importante è che il contenuto sia ospitato in un endpoint HTTPS disponibile pubblicamente con CORS abilitato. Quando si specifica un URL nel contenuto, è necessario usare un URL assoluto.

## <a name="get-started-with-custom-html-and-css"></a>Introduzione a HTML e CSS personalizzati

Per iniziare a usare il codice HTML e CSS nelle pagine dell'esperienza utente, attenersi alle indicazioni riportate di seguito.

- Creare contenuto HTML ben formato con un elemento `<div id="api"></div>` vuoto che si trova in un punto all'interno di `<body>`. Questo elemento corrisponde al punto in cui viene inserito il contenuto di Azure AD B2C. L'esempio seguente mostra una pagina minima:

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- Usare CSS per applicare uno stile agli elementi dell'interfaccia utente inseriti nella pagina da Azure AD B2C. L'esempio seguente illustra un semplice file CSS che include anche le impostazioni per gli elementi HTML inseriti all'iscrizione:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

- Ospitare il contenuto in un endpoint HTTPS in cui è consentita la condivisione CORS. Entrambi i metodi di richiesta GET e OPTIONS devono essere abilitati quando si configura CORS.
- Creare o modificare un flusso utente o un criterio personalizzato per usare il contenuto creato.

### <a name="html-fragments-from-azure-ad-b2c"></a>Frammenti HTML da Azure AD B2C

La tabella seguente elenca i frammenti HTML che Azure AD B2C unisce nell'elemento `<div id="api"></div>` all'interno del contenuto.

| Pagina inserita | Descrizione del codice HTML |
| ------------- | ------------------- |
| Selezione del provider di identità | Contiene un elenco di pulsanti per i provider di identità che il cliente può scegliere durante la procedura di iscrizione o di accesso. Questi pulsanti includono i provider di identità basati su social network, ad esempio Facebook e Google, o gli account locali (basati su indirizzo di posta elettronica o nome utente). |
| Iscrizione dell'account locale | Contiene un modulo per eseguire l'iscrizione dell'account locale in base a un indirizzo di posta elettronica o a un nome utente. Il modulo può contenere diversi controlli di input, ad esempio caselle per l'immissione di testo, caselle per l'immissione della password, pulsanti di opzione, caselle a discesa a selezione singola e caselle di controllo con selezione multipla. |
| Iscrizione dell'account di social networking | Può essere visualizzata quando si effettua l'iscrizione usando un account esistente di un provider di identità basato su social network, ad esempio Facebook o Google. Viene usato quando è necessario raccogliere informazioni aggiuntive dal cliente usando un modulo di iscrizione. |
| Unificata per l'iscrizione o l'accesso | Gestisce sia l'iscrizione che l'accesso dei clienti, che possono usare provider di identità basati su social network, come Facebook o Google, o account locali. |
| Multi-Factor Authentication | Gli utenti possono verificare il proprio numero di telefono (tramite SMS o chiamata vocale) durante la procedura di iscrizione o di accesso. |
| Errore | Fornisce informazioni sugli errori al cliente. |

## <a name="localize-content"></a>Localizzare il contenuto

Si può localizzare il contenuto HTML abilitando la [personalizzazione della lingua](active-directory-b2c-reference-language-customization.md) nel tenant di Azure AD B2C. L'abilitazione di questa funzionalità consente Azure AD B2C di inviare il parametro OpenID Connect `ui-locales` all'endpoint. Il server di contenuti può usare questo parametro per fornire pagine HTML personalizzate specifiche della lingua.

È possibile eseguire il pull del contenuto da posizioni diverse in base alle impostazioni locali in uso. Nell'endpoint abilitato per CORS, configurare una struttura di cartelle in cui ospitare il contenuto per specifiche lingue. Se si inserisce il valore del carattere jolly `{Culture:RFC5646}` verrà chiamata la lingua corretta.

Ad esempio, l'URI della pagina personalizzata potrebbe essere simile al seguente:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

È possibile caricare la pagina in francese eseguendo il pull del contenuto da:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>esempi

È possibile trovare diversi file di modello di esempio nel repository [B2C-AzureBlobStorage-client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) su GitHub.

I file HTML e CSS di esempio nei modelli si trovano nella directory [/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates) .

## <a name="next-steps"></a>Passaggi successivi

- Se si usano i **flussi utente**, è possibile iniziare a personalizzare l'interfaccia utente con l'esercitazione:

    [Personalizzare l'interfaccia utente delle applicazioni in Azure Active Directory B2C](tutorial-customize-ui.md).
- Se si usano **criteri personalizzati**, è possibile iniziare a personalizzare l'interfaccia utente con l'articolo:

    [Personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
