---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: 4cb4cdef227c786b12790903037fdc02649592e3
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95990885"
---
## <a name="use-custom-page-content"></a>USA contenuto pagina personalizzata

Con la funzionalità di personalizzazione dell'interfaccia utente della pagina, è possibile definire l'aspetto di qualsiasi criterio personalizzato. È anche possibile mantenere la coerenza visiva e del marchio tra l'applicazione e Azure AD B2C.

### <a name="how-it-works"></a>Funzionamento

Azure AD B2C esegue il codice nel browser del cliente usando la [condivisione di risorse tra le origini (CORS)](https://www.w3.org/TR/cors/). In fase di esecuzione, il contenuto viene caricato da un URL specificato nel flusso utente o in un criterio personalizzato. Ogni pagina nell'esperienza utente carica il proprio contenuto dall'URL specificato per la pagina. Dopo che il contenuto è stato caricato dall'URL, viene unito a un frammento HTML inserito da Azure AD B2C, quindi la pagina viene visualizzata per il cliente.

![Margine contenuto pagina personalizzata](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Contenuto pagina HTML personalizzato

Creare una pagina HTML con una personalizzazione personalizzata per gestire il contenuto della pagina personalizzata. Questa pagina può essere una pagina statica `*.html` o una pagina dinamica, ad esempio .NET, Node.js o php.

Il contenuto della pagina personalizzata può contenere qualsiasi elemento HTML, incluso CSS e JavaScript, ma non può includere elementi non protetti come gli iframe. L'unico elemento obbligatorio è un elemento div con `id` impostato su `api` , ad esempio questo `<div id="api"></div>` nella pagina HTML.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

### <a name="customize-the-default-azure-ad-b2c-pages"></a>Personalizzare le pagine di Azure AD B2C predefinite

Invece di creare il contenuto della pagina personalizzata da zero, è possibile personalizzare Azure AD contenuto della pagina predefinita B2C.

Nella tabella seguente sono elencati i contenuti di pagina predefiniti forniti da Azure AD B2C. Scaricare i file e usarli come punto di partenza per la creazione di pagine personalizzate.

| Pagina predefinita | Descrizione | ID definizione del contenuto<br/>(solo criteri personalizzati) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Pagina di errore**. Questa pagina viene visualizzata quando viene rilevata un'eccezione o un errore. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Pagina autocertificata**. Usare questo file come contenuto di pagina personalizzata per una pagina di iscrizione dell'account di social networking, una pagina di iscrizione dell'account locale, una pagina di accesso dell'account locale, la reimpostazione della password e altro ancora. Il modulo può contenere diversi controlli di input, ad esempio una casella per l'immissione di testo e una per l'immissione della password, un pulsante di opzione, caselle a discesa a selezione singola e caselle di controllo con selezione multipla. | API *. localaccountsignin*, *API. localaccountsignin*, *API. localaccountpasswordreset*, *API. selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Pagina dell'autenticazione a più fattori**. In questa pagina gli utenti possono verificare il proprio numero di telefono (tramite SMS o chiamata vocale) durante la procedura di iscrizione o di accesso. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Pagina di aggiornamento del profilo**. Questa pagina contiene un modulo a cui gli utenti possono accedere per aggiornare il profilo. Questa pagina è simile alla pagina di iscrizione dell'account di social networking, a eccezione dei campi di immissione della password. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Pagina unificata per l'iscrizione o l'accesso**. Questa pagina gestisce il processo di iscrizione e quello di accesso degli utenti. Gli utenti possono usare provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook o Google +, o account locali. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Hosting del contenuto della pagina

Quando si usano i propri file HTML e CSS per personalizzare l'interfaccia utente, ospitare il contenuto dell'interfaccia utente in qualsiasi endpoint HTTPS disponibile pubblicamente che supporti CORS. Ad esempio, [archiviazione BLOB di Azure](../articles/storage/blobs/storage-blobs-introduction.md), [Servizi app Azure](../articles/app-service/index.yml), server Web, CDNs, AWS S3 o sistemi di condivisione di file.

## <a name="guidelines-for-using-custom-page-content"></a>Linee guida per l'utilizzo del contenuto della pagina personalizzata

- Usare un URL assoluto quando si includono risorse esterne come file multimediali, CSS e JavaScript nel file HTML.
- Usando il [layout di pagina](../articles/active-directory-b2c/page-layout.md) 1.2.0 e versioni successive, è possibile aggiungere l' `data-preload="true"` attributo nei tag HTML per controllare l'ordine di caricamento per CSS e JavaScript. Con `data-preload=true` , la pagina viene costruita prima di essere visualizzata all'utente. Questo attributo consente di impedire che la pagina venga "sfarfallio" precaricando il file CSS, senza che venga visualizzato un codice HTML non in stile. Il frammento di codice HTML seguente mostra l'uso del `data-preload` tag.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- È consigliabile iniziare con il contenuto predefinito della pagina e compilarlo.
- È possibile includere JavaScript nel contenuto personalizzato per i [flussi utente](../articles/active-directory-b2c/user-flow-javascript-overview.md) e i [criteri personalizzati](../articles/active-directory-b2c/javascript-samples.md).
- Le versioni di browser supportate sono:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Supporto limitato per Internet Explorer 9 e 8
  - Google Chrome 42.0 e versioni successive
  - Mozilla Firefox 38.0 e versioni successive
  - Safari per iOS e macOS, versione 12 e successive
- A causa delle restrizioni di sicurezza, Azure AD B2C non supporta `frame` `iframe` `form` gli elementi HTML, o.

## <a name="custom-page-content-walkthrough"></a>Procedura dettagliata contenuto pagina personalizzata

Ecco una panoramica del processo:

1. Preparare un percorso per ospitare il contenuto della pagina personalizzata (un endpoint HTTPS accessibile pubblicamente e abilitato per CORS).
1. Scaricare e personalizzare un file di contenuto di pagina predefinito, ad esempio `unified.html` .
1. Pubblicare il contenuto della pagina personalizzata dell'endpoint HTTPS disponibile pubblicamente.
1. Impostare la condivisione di risorse tra le origini (CORS) per l'app Web.
1. Puntare i criteri all'URI del contenuto dei criteri personalizzato.

### <a name="1-create-your-html-content"></a>1. creare il contenuto HTML

Creare un contenuto di pagina personalizzato con il nome del marchio del prodotto nel titolo.

1. Copiare il frammento di codice HTML seguente. Si tratta di un HTML5 ben formato con un elemento vuoto denominato che *\<div id="api"\>\</div\>* si trova all'interno dei *\<body\>* tag. Questo elemento indica il punto in cui deve essere inserito il contenuto di Azure AD B2C.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Incollare il frammento di codice copiato in un editor di testo e quindi salvare il file con il nome *customize-ui.html*.

> [!NOTE]
> Gli elementi del form HTML verranno rimossi a causa di restrizioni di sicurezza se si usa login.microsoftonline.com. Se si desidera utilizzare elementi del form HTML nel contenuto HTML personalizzato, [utilizzare b2clogin.com](../articles/active-directory-b2c/b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. creare un account di archiviazione BLOB di Azure

In questo articolo verrà usato l'archivio BLOB di Azure per ospitare il contenuto. È possibile scegliere di ospitare il contenuto in un server Web, ma è necessario [abilitare la condivisione di risorse tra le origini (CORS) nel server Web](https://enable-cors.org/server.html).

Per ospitare il contenuto HTML nell'archivio BLOB, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu **Hub** selezionare **Nuovo** > **Archiviazione** > **Account di archiviazione**.
1. Selezionare una **sottoscrizione** per l'account di archiviazione.
1. Creare un **gruppo di risorse** o selezionarne uno esistente.
1. Immettere un **nome** unico per l'account di archiviazione.
1. Selezionare la **posizione geografica** dell'account di archiviazione.
1. Per **Modello di distribuzione** è possibile lasciare **Resource Manager**.
1. Per **Prestazioni** è possibile lasciare **Standard**.
1. Modificare **Tipo di Account** in **archiviazione BLOB**.
1. Per **Replica** è possibile lasciare **RA-GRS**.
1. Per **Livello di accesso** è possibile lasciare **Frequente**.
1. Selezionare **Verifica + crea** per creare l'account di archiviazione.
    Al termine della distribuzione, viene visualizzata automaticamente la pagina **account di archiviazione** .

#### <a name="21-create-a-container"></a>2,1 creare un contenitore

Per creare un contenitore pubblico nell'archivio BLOB, seguire questa procedura:

1. In **servizio BLOB** scegliere **BLOB** dal menu a sinistra.
1. Selezionare **+ contenitore**.
1. Per **nome** immettere *radice*. Il nome può essere un nome a scelta, ad esempio *Contoso*, ma in questo esempio viene usata la *radice* per semplicità.
1. Per **livello di accesso pubblico** selezionare **BLOB** e quindi **OK**.
1. Selezionare **radice** per aprire il nuovo contenitore.

#### <a name="22-upload-your-custom-page-content-files"></a>2,2 caricare i file di contenuto della pagina personalizzati

1. Selezionare **Carica**.
1. Selezionare l'icona della cartella accanto a **selezionare un file**.
1. Passare a e selezionare **customize-ui.html**, creato in precedenza nella sezione relativa alla personalizzazione dell'interfaccia utente della pagina.
1. Se si desidera caricare in una sottocartella, espandere **Avanzate** e immettere un nome di cartella in **carica in cartella**.
1. Selezionare **Carica**.
1. Selezionare il BLOB di **customize-ui.html** caricato.
1. A destra della casella di testo **URL** selezionare l'icona **copia negli Appunti** per copiare l'URL negli Appunti.
1. Nel Web browser passare all'URL copiato per verificare che il BLOB caricato sia accessibile. Se è inaccessibile, ad esempio se si verifica un `ResourceNotFound` errore, verificare che il tipo di accesso del contenitore sia impostato su **BLOB**.

### <a name="3-configure-cors"></a>3. configurare CORS

Configurare l'archiviazione BLOB per la condivisione di risorse tra le origini eseguendo i passaggi seguenti:

1. Nel menu selezionare **CORS**.
1. Per **Origini consentite** immettere `https://your-tenant-name.b2clogin.com`. Sostituire `your-tenant-name` con il nome del tenant di Azure AD B2C. Ad esempio: `https://fabrikam.b2clogin.com`. Usare tutte le lettere minuscole quando si immette il nome del tenant.
1. Per **Metodi consentiti** selezionare sia `GET` che `OPTIONS`.
1. Per **Intestazioni consentite** immettere un asterisco (*).
1. Per **Intestazioni esposte** immettere un asterisco (*).
1. Per **Età massima** immettere 200.
1. Selezionare **Salva**.

#### <a name="31-test-cors"></a>3,1 test CORS

Verificare che l'utente sia pronto attenendosi alla procedura seguente:

1. Ripetere il passaggio Configure CORS. Per le **origini consentite**, immettere `https://www.test-cors.org`
1. Passare a [www.test-CORS.org](https://www.test-cors.org/) 
1. Per la casella **URL remoto** incollare l'URL del file HTML. Ad esempio: `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Selezionare **Invia richiesta**.
    Il risultato deve essere `XHR status: 200` . 
    Se si riceve un messaggio d'errore, verificare che le impostazioni CORS siano corrette. Potrebbe anche essere necessario cancellare la cache del browser o aprire una sessione di esplorazione anonima premendo Ctrl+Maiusc+P.