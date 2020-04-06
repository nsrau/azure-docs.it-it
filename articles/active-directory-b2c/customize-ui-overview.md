---
title: Personalizzare l'interfaccia utente
titleSuffix: Azure AD B2C
description: Informazioni su come personalizzare l'interfaccia utente per le applicazioni che usano Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37ddf57057b736cd76a74276e5593a865e7df8cc
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666860"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Personalizzare l'interfaccia utente in Azure Active Directory B2C

La personalizzazione e la personalizzazione dell'interfaccia utente che Azure Active Directory B2C (Azure AD B2C) visualizza ai clienti consentono di offrire un'esperienza utente senza problemi nell'applicazione. Queste esperienze includono l'iscrizione, l'accesso, la modifica del profilo e la reimpostazione della password. In questo articolo vengono presentati i metodi di personalizzazione dell'interfaccia utente per i flussi utente e i criteri personalizzati.

## <a name="ui-customization-in-different-scenarios"></a>Personalizzazione dell'interfaccia utente in scenari diversi

Esistono diversi modi per personalizzare l'interfaccia utente delle esperienze utente dell'applicazione, ognuno appropriato per scenari diversi.

### <a name="user-flows"></a>Flussi degli utenti

Se si [utilizzano flussi utente](user-flow-overview.md), è possibile modificare l'aspetto delle pagine del flusso utente utilizzando *i modelli*di layout di pagina incorporati oppure utilizzando codice HTML e CSS personalizzati. Entrambi i metodi vengono illustrati più avanti in questo articolo.

Usare il portale di [Azure](tutorial-customize-ui.md) per configurare la personalizzazione dell'interfaccia utente per i flussi utente.

> [!TIP]
> Se si desidera modificare solo il logo del banner, l'immagine di sfondo e il colore di sfondo delle pagine del flusso utente, è possibile provare la funzionalità [Di personalizzazione aziendale (anteprima)](#company-branding-preview) descritta più avanti in questo articolo.

### <a name="custom-policies"></a>Criteri personalizzati

Se si utilizzano [criteri personalizzati](custom-policy-overview.md) per fornire l'iscrizione o l'accesso, la reimpostazione della password o la modifica del profilo nell'applicazione, utilizzare i [file di criteri per personalizzare l'interfaccia utente.](custom-policy-ui-customization.md)

Se è necessario fornire contenuto dinamico in base alla decisione del cliente, utilizzare criteri personalizzati che possono modificare il contenuto della [pagina in modo dinamico](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri) in base a un parametro inviato in una stringa di query. Ad esempio, è possibile modificare l'immagine di sfondo nella pagina di iscrizione o di accesso B2C di Azure AD in base a un parametro passato dall'applicazione Web o per dispositivi mobili.

### <a name="javascript"></a>JavaScript

È possibile abilitare il codice JavaScript lato client sia nei [flussi utente](user-flow-javascript-overview.md) che nei [criteri personalizzati.](page-layout.md)

### <a name="sign-in-only-ui-customization"></a>Accedere alla personalizzazione dell'interfaccia utente solo

Se si fornisce solo l'accesso, insieme alla pagina di reimpostazione della password associata e ai messaggi di posta elettronica di verifica, usare gli stessi passaggi di personalizzazione usati per una pagina di accesso di [Azure AD.](../active-directory/fundamentals/customize-branding.md)

Se i clienti tentano di modificare il proprio profilo prima dell'accesso, vengono reindirizzati a una pagina che si personalizza usando la stessa procedura usata per personalizzare la pagina di accesso di Azure AD.

## <a name="page-layout-templates"></a>Modelli di layout di pagina

I flussi utente forniscono diversi modelli predefiniti tra cui è possibile scegliere per conferire alle pagine dell'esperienza utente un aspetto professionale. Questi modelli di layout possono anche e servire come punto di partenza per la propria personalizzazione.

In **Personalizza** nel menu a sinistra selezionare **Layout di pagina** e quindi **Modello**.

![Elenco a discesa Selezione dei modelli nella pagina del flusso utente del portale di AzureTemplate selection drop-down in user flow page of Azure portal](media/customize-ui-overview/template-selection.png)

Selezionare quindi un modello dall'elenco. Di seguito sono riportati alcuni esempi delle pagine di accesso per ogni modello:

| Ocean Blue | Grigio ardesia | Classico |
|:-:|:-:|:-:|
|![Esempio del modello Ocean Blue visualizzato nella pagina di accesso](media/customize-ui-overview/template-ocean-blue.png)|![Esempio di modello Slate Gray visualizzato alla pagina di accesso](media/customize-ui-overview/template-slate-gray.png)|![Esempio del modello classico di cui è stato eseguito il rendering nella pagina di accesso](media/customize-ui-overview/template-classic.png)|

Quando si sceglie un modello, il layout selezionato viene applicato a tutte le pagine nel flusso utente e l'URI per ogni pagina è visibile nel campo **URI della pagina personalizzata**.

## <a name="custom-html-and-css"></a>HTML e CSS personalizzati

Se si desidera progettare il proprio layout di criteri con il codice HTML e CSS personalizzati, è possibile farlo cambiando l'interruttore "Usa contenuto pagina personalizzato" per ciascuno dei nomi di layout presenti nel criterio. Seguire le istruzioni riportate di seguito relative alle configurazioni di layout personalizzate:

Azure AD B2C esegue il codice nel browser del cliente usando un approccio denominato [Condivisione risorse cross-origin (CORS).](https://www.w3.org/TR/cors/)

In fase di esecuzione, il contenuto viene caricato da un URL specificato nel flusso utente o nei criteri personalizzati. Ogni pagina dell'esperienza utente carica il contenuto dall'URL specificato per la pagina. Dopo il caricamento, il contenuto viene caricato dall'URL, viene unito a un frammento HTML inserito da Azure AD B2C, quindi la pagina viene visualizzata al cliente.

Prima di utilizzare i propri file HTML e CSS per personalizzare l'interfaccia utente, consulta le indicazioni seguenti:

- Azure AD B2C **unisce il** contenuto HTML nelle pagine. Non copiare e provare a modificare il contenuto predefinito incluso da Azure AD B2C. È preferibile creare il contenuto HTML da zero e usare il contenuto predefinito come riferimento.
- **JavaScript** può essere incluso nel contenuto personalizzato sia per [i flussi utente](user-flow-javascript-overview.md) che per [i criteri personalizzati.](javascript-samples.md)
- Le versioni di **browser** supportate sono:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Supporto limitato per Internet Explorer 9 e 8
  - Google Chrome 42.0 e versioni successive
  - Mozilla Firefox 38.0 e versioni successive
  - Safari per iOS e macOS, versione 12 e successive
- Non includere **tag modulo** nel codice HTML. I tag modulo interferiscono con le operazioni POST generate dal codice HTML inserito da Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Dove archiviare il contenuto dell'interfaccia utente

Quando si usano i propri file HTML e CSS per personalizzare l'interfaccia utente, è possibile ospitare il contenuto dell'interfaccia utente in qualsiasi endpoint HTTPS disponibile pubblicamente che supporta CORS. Ad esempio, Archiviazione BLOB di [Azure,](../storage/blobs/storage-blobs-introduction.md)server Web, reti CDN, AWS S3 o sistemi di condivisione file.

Il punto importante è che si ospita il contenuto in un endpoint HTTPS disponibile pubblicamente con [CORS abilitato](https://enable-cors.org/server.html). Quando si specifica un URL nel contenuto, è necessario usare un URL assoluto.

> [!NOTE]
> Per informazioni dettagliate sulla creazione di contenuto HTML, sul caricamento di contenuto nell'archiviazione BLOB di Azure e sulla configurazione di CORS, vedere la sezione Procedura dettagliata sul contenuto della [pagina personalizzata](custom-policy-ui-customization.md#custom-page-content-walkthrough) nell'articolo sulla personalizzazione dell'interfaccia utente.

## <a name="get-started-with-custom-html-and-css"></a>Introduzione a HTML e CSS personalizzati

Inizia a usare il tuo codice HTML e CSS nelle tue pagine di esperienza utente seguendo queste linee guida.

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
- Creare o modificare un flusso utente o criteri personalizzati per utilizzare il contenuto creato.

### <a name="html-fragments-from-azure-ad-b2c"></a>Frammenti HTML da Azure AD B2C

La tabella seguente elenca i frammenti HTML che Azure AD B2C unisce nell'elemento `<div id="api"></div>` all'interno del contenuto.

| Pagina inserita | Descrizione del codice HTML |
| ------------- | ------------------- |
| Selezione del provider di identità | Contiene un elenco di pulsanti per i provider di identità che il cliente può scegliere durante la procedura di iscrizione o di accesso. Questi pulsanti includono i provider di identità basati su social network, ad esempio Facebook e Google, o gli account locali (basati su indirizzo di posta elettronica o nome utente). |
| Iscrizione dell'account locale | Contiene un modulo per eseguire l'iscrizione dell'account locale in base a un indirizzo di posta elettronica o a un nome utente. Il modulo può contenere diversi controlli di input, ad esempio caselle per l'immissione di testo, caselle per l'immissione della password, pulsanti di opzione, caselle a discesa a selezione singola e caselle di controllo con selezione multipla. |
| Iscrizione dell'account di social networking | Può essere visualizzata quando si effettua l'iscrizione usando un account esistente di un provider di identità basato su social network, ad esempio Facebook o Google. Viene utilizzato quando è necessario raccogliere informazioni aggiuntive dal cliente utilizzando un modulo di iscrizione. |
| Unificata per l'iscrizione o l'accesso | Gestisce sia l'iscrizione che l'accesso dei clienti, che possono usare provider di identità basati su social network, come Facebook o Google, o account locali. |
| Autenticazione a più fattori | Gli utenti possono verificare il proprio numero di telefono (tramite SMS o chiamata vocale) durante la procedura di iscrizione o di accesso. |
| Errore | Fornisce informazioni sugli errori al cliente. |

## <a name="company-branding-preview"></a>Branding aziendale (anteprima)

È possibile personalizzare le pagine del flusso utente con un logo banner, un'immagine di sfondo e un colore di sfondo utilizzando la [personalizzazione di](../active-directory/fundamentals/customize-branding.md)Azure Active Directory Company .

Per personalizzare le pagine del flusso utente, è innanzitutto necessario configurare la personalizzazione aziendale in Azure Active Directory, quindi abilitarla nei layout di pagina dei flussi utente in Azure AD B2C.

[!INCLUDE [preview note](../../includes/active-directory-b2c-public-preview.md)]

### <a name="configure-company-branding"></a>Configurare la personalizzazione aziendale

Iniziare impostando il logo del banner, l'immagine di sfondo e il colore di sfondo all'interno **del marchio dell'azienda**.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **Gestisci**selezionare **Personalizzazione società**.
1. Seguire i passaggi descritti in Aggiungere la personalizzazione alla pagina di accesso di [Azure Active Directory dell'organizzazione.](../active-directory/fundamentals/customize-branding.md)

Quando si configura la personalizzazione dell'azienda in Azure AD B2C, tenere presenti le informazioni seguenti:Keep these things in mind when you configure company branding in Azure AD B2C:

* La personalizzazione **dell'azienda**in Azure AD B2C è attualmente limitata all'immagine di sfondo, al logo del **banner**e alla personalizzazione del colore di **sfondo.** Le altre proprietà nel riquadro di personalizzazione della società, ad esempio quelle in **Impostazioni avanzate**, non sono *supportate.*
* Nelle pagine del flusso utente, il colore di sfondo viene visualizzato prima del caricamento dell'immagine di sfondo. Ti consigliamo di scegliere un colore di sfondo che corrisponda perfettamente ai colori dell'immagine di sfondo per un'esperienza di caricamento più fluida.
* Il logo del banner viene visualizzato nelle e-mail di verifica inviate agli utenti quando avviano un flusso di utenti di iscrizione.

### <a name="enable-branding-in-user-flow-pages"></a>Abilitare la personalizzazione nelle pagine del flusso utenteEnable branding in user flow pages

Dopo aver configurato il branding aziendale, abilitarlo nei flussi utente.

1. Nel menu a sinistra del portale di Azure selezionare **Azure AD B2C**.
1. In **Criteri**selezionare **Flussi utente (criteri)**.
1. Seleziona il flusso utente per il quale desideri abilitare il branding aziendale. Il branding aziendale non è **supportato** per i tipi di flusso utente *Accedi v1* e *Modifica profilo v1.*
1. In **Personalizza**selezionare **Layout di pagina**e quindi selezionare il layout da personalizzare. Ad esempio, selezionare **Pagina di iscrizione unificata o di accesso**.
1. Per **Versione layout di pagina (anteprima),** scegliere la versione **1.2.0** o successiva.
1. Selezionare **Salva**.

Se desideri personalizzare tutte le pagine nel flusso utente, imposta la versione del layout di pagina per ogni layout di pagina nel flusso utente.

![Selezione del layout di pagina in Azure AD B2C nel portale di AzurePage layout selection in Azure AD B2C in the Azure portal](media/customize-ui-overview/portal-02-page-layout-select.png)

Questo esempio con notato mostra un logo del banner personalizzato e un'immagine di sfondo in una pagina Accedi e accedi al flusso utente che usa il modello Ocean Blue:This annotated example shows a custom banner logo and background image on a *Sign up and sign in* user flow page that uses the Ocean Blue template:

![Pagina di iscrizione/accesso con marchio servita da Azure AD B2C](media/customize-ui-overview/template-ocean-blue-branded.png)

### <a name="use-company-branding-assets-in-custom-html"></a>Usare le risorse di personalizzazione dell'azienda in HTML personalizzato

Per utilizzare le risorse di personalizzazione dell'azienda `<div id="api">` nell'HTML personalizzato, aggiungi i tag seguenti all'esterno del tag:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

L'origine dell'immagine viene sostituita con quella dell'immagine di sfondo e del logo del banner. Come descritto nella sezione [Introduzione all'HTML e CSS personalizzati,](#get-started-with-custom-html-and-css) utilizzare le classi CSS per definire lo stile e posizionare le risorse nella pagina.

## <a name="localize-content"></a>Localizzare il contenuto

Si può localizzare il contenuto HTML abilitando la [personalizzazione della lingua](user-flow-language-customization.md) nel tenant di Azure AD B2C. L'abilitazione di questa funzionalità consente ad Azure `ui-locales` AD B2C di inoltrare il parametro OpenID Connect all'endpoint. Il server di contenuti può usare questo parametro per fornire pagine HTML personalizzate specifiche della lingua.

È possibile eseguire il pull del contenuto da posizioni diverse in base alle impostazioni locali in uso. Nell'endpoint abilitato per CORS, configurare una struttura di cartelle in cui ospitare il contenuto per specifiche lingue. Se si inserisce il valore del carattere jolly `{Culture:RFC5646}` verrà chiamata la lingua corretta.

Ad esempio, l'URI della pagina personalizzata potrebbe essere simile al seguente:For example, your custom page URI might look like:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

È possibile caricare la pagina in francese estraendo il contenuto da:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Esempi

È possibile trovare diversi file di modello di esempio nel repository B2C-AzureBlobStorage-Client in GitHub.You can find several sample template files in the [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) repository on GitHub.

I file HTML e CSS di esempio nei modelli si trovano nella directory [/sample_templates.](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates)

## <a name="next-steps"></a>Passaggi successivi

- Se si usano **flussi utente,** è possibile iniziare a personalizzare l'interfaccia utente con l'esercitazione:

    [Personalizzare l'interfaccia utente delle applicazioni in Azure Active Directory B2C](tutorial-customize-ui.md).
- Se si usano **criteri personalizzati,** è possibile iniziare a personalizzare l'interfaccia utente con l'articolo:

    [Personalizzare l'interfaccia utente dell'applicazione usando criteri personalizzati in Azure Active Directory B2C.](custom-policy-ui-customization.md)
