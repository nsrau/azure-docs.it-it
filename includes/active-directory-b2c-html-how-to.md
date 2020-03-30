---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: af11283f9e9dbd925ec994dcb1d96393332b90fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116856"
---
## <a name="use-custom-page-content"></a>Utilizzare il contenuto della pagina personalizzata

Con la funzionalità di personalizzazione dell'interfaccia utente della pagina, è possibile definire l'aspetto di qualsiasi criterio personalizzato. È anche possibile mantenere la coerenza visiva e del marchio tra l'applicazione e Azure AD B2C.

### <a name="how-it-works"></a>Funzionamento

Azure AD B2C esegue il codice nel browser del cliente usando [Condivisione risorse tra origini (CORS, Cross-Origin Resource Sharing)](https://www.w3.org/TR/cors/). In fase di esecuzione, il contenuto viene caricato da un URL specificato nel flusso utente o nei criteri personalizzati. Ogni pagina dell'esperienza utente carica il contenuto dall'URL specificato per la pagina. Dopo il caricamento, il contenuto viene caricato dall'URL, viene unito a un frammento HTML inserito da Azure AD B2C, quindi la pagina viene visualizzata al cliente.

![Margine del contenuto della pagina personalizzata](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Contenuto della pagina HTML personalizzata

Crea una pagina HTML con il tuo marchio per pubblicare il contenuto della tua pagina personalizzata. Questa pagina può `*.html` essere una pagina statica o una pagina dinamica come .NET, Node.js o PHP.

Il contenuto della pagina personalizzata può contenere qualsiasi elemento HTML, inclusi CSS e JavaScript, ma non può includere elementi non sicuri come gli iframe. L'unico elemento obbligatorio è `id` un `api`elemento div `<div id="api"></div>` con impostato su , ad esempio questo all'interno della pagina HTML.

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

### <a name="customize-the-default-azure-ad-b2c-pages"></a>Personalizzare le pagine B2C di Azure AD predefiniteCustomize the default Azure AD B2C pages

Anziché creare il contenuto della pagina personalizzata da zero, è possibile personalizzare il contenuto predefinito della pagina di Azure AD B2C.

Nella tabella seguente sono elencati i contenuti predefiniti della pagina forniti da Azure AD B2C. Scaricare i file e utilizzarli come punto di partenza per la creazione di pagine personalizzate.

| Pagina predefinita | Descrizione | ID definizione del contenuto<br/>(solo criteri personalizzati) |
|:-----------------------|:--------|-------------|
| [exception.html (informazioni in lingua inlingua non è](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Pagina di errore**. Questa pagina viene visualizzata quando viene rilevata un'eccezione o un errore. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Pagina auto-asserito**. Usa questo file come contenuto di pagina personalizzato per una pagina di iscrizione a un account social, una pagina di iscrizione all'account locale, una pagina di accesso all'account locale, la reimpostazione della password e altro ancora. Il modulo può contenere diversi controlli di input, ad esempio una casella per l'immissione di testo e una per l'immissione della password, un pulsante di opzione, caselle a discesa a selezione singola e caselle di controllo con selezione multipla. | *api.localaccountsignin*, *api.localaccountsignup*, *api.localaccountpasswordreset*, *api.selfed* |
| [multifattore-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Pagina dell'autenticazione a più fattori**. In questa pagina gli utenti possono verificare il proprio numero di telefono (tramite SMS o chiamata vocale) durante la procedura di iscrizione o di accesso. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Pagina di aggiornamento del profilo**. Questa pagina contiene un modulo a cui gli utenti possono accedere per aggiornare il profilo. Questa pagina è simile alla pagina di iscrizione dell'account di social networking, a eccezione dei campi di immissione della password. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Pagina unificata per l'iscrizione o l'accesso**. Questa pagina gestisce il processo di iscrizione e quello di accesso degli utenti. Gli utenti possono usare provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook o Google +, o account locali. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Hosting del contenuto della pagina

Quando usi i tuoi file HTML e CSS per personalizzare l'interfaccia utente, ospita il contenuto dell'interfaccia utente su qualsiasi endpoint HTTPS disponibile pubblicamente che supporti CORS. Ad esempio, Archiviazione BLOB di [Azure,](../articles/storage/blobs/storage-blobs-introduction.md) [servizi app di Azure,](/azure/app-service/)server Web, reti CDN, AWS S3 o sistemi di condivisione file.

## <a name="guidelines-for-using-custom-page-content"></a>Linee guida per l'utilizzo del contenuto della pagina personalizzata

- Usa un URL assoluto quando includi risorse esterne come file multimediali, CSS e JavaScript nel file HTML.
- Utilizzando la [versione](../articles/active-directory-b2c/page-layout.md) 1.2.0 e successive, `data-preload="true"` potete aggiungere l'attributo nei tag HTML per controllare l'ordine di caricamento per CSS e JavaScript. Con `data-preload=true`, la pagina viene costruita prima di essere visualizzata all'utente. Questo attributo consente di impedire che la pagina venga "sfarfallio" precaricando il file CSS, senza che il codice HTML non in stile venga visualizzato all'utente. Il frammento di codice HTML `data-preload` seguente mostra l'uso del tag.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- È consigliabile iniziare con il contenuto predefinito della pagina e basarsi su di esso.
- È possibile includere JavaScript nel contenuto personalizzato sia per [i flussi utente](../articles/active-directory-b2c/user-flow-javascript-overview.md) che per [i criteri personalizzati.](../articles/active-directory-b2c/javascript-samples.md)
- Le versioni di browser supportate sono:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Supporto limitato per Internet Explorer 9 e 8
  - Google Chrome 42.0 e versioni successive
  - Mozilla Firefox 38.0 e versioni successive
  - Safari per iOS e macOS, versione 12 e successive
- A causa delle restrizioni di sicurezza, Azure `frame`AD `iframe`B2C non supporta , o `form` elementi HTML.

## <a name="custom-page-content-walkthrough"></a>Procedura dettagliata del contenuto della pagina personalizzataCustom page content walkthrough

Ecco una panoramica del processo:

1. Preparare un percorso per ospitare il contenuto della pagina personalizzata (un endpoint HTTPS accessibile pubblicamente e abilitato per CORS).
1. Scaricare e personalizzare un file di `unified.html`contenuto della pagina predefinito, ad esempio .
1. Pubblicare il contenuto della pagina personalizzata nell'endpoint HTTPS disponibile pubblicamente.
1. Impostare la condivisione di risorse tra le origini (CORS) per l'app Web.
1. Puntare i criteri all'URI del contenuto dei criteri personalizzati.

### <a name="1-create-your-html-content"></a>1. Crea il tuo contenuto HTML

Crea un contenuto di pagina personalizzato con il nome del brand del prodotto nel titolo.

1. Copiare il frammento di codice HTML seguente. È ben formato HTML5 con un elemento vuoto chiamato * \<\>\<div\> id "api" /div* che si trova all'interno dei tag * \<body.\> * Questo elemento indica il punto in cui deve essere inserito il contenuto di Azure AD B2C.

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
> Gli elementi del modulo HTML verranno rimossi a causa di restrizioni di sicurezza se si utilizza login.microsoftonline.com. Se si desidera utilizzare elementi modulo HTML nel contenuto HTML personalizzato, [utilizzare b2clogin.com](../articles/active-directory-b2c/b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Creare un account di archiviazione BLOB di Azure

In questo articolo verrà usato l'archivio BLOB di Azure per ospitare il contenuto. È possibile scegliere di ospitare il contenuto in un server Web, ma è necessario [abilitare la condivisione di risorse tra le origini (CORS) nel server Web](https://enable-cors.org/server.html).

Per ospitare il contenuto HTML nell'archivio BLOB, eseguire la procedura seguente:To host your HTML content in Blob storage, perform the following steps:

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Nel menu **Hub** selezionare **Nuovo** > **Archiviazione** > **Account di archiviazione**.
1. Selezionare una **sottoscrizione** per l'account di archiviazione.
1. Creare un gruppo di **risorse** o selezionarne uno esistente.
1. Immettere un **nome** unico per l'account di archiviazione.
1. Selezionare la **posizione geografica** dell'account di archiviazione.
1. Per **Modello di distribuzione** è possibile lasciare **Resource Manager**.
1. Per **Prestazioni** è possibile lasciare **Standard**.
1. Modificare **Tipo di Account** in **archiviazione BLOB**.
1. Per **Replica** è possibile lasciare **RA-GRS**.
1. Per **Livello di accesso** è possibile lasciare **Frequente**.
1. Selezionare **Revisione : crea** per creare l'account di archiviazione.
    Al termine della distribuzione, la pagina **Account di archiviazione** viene aperta automaticamente.

#### <a name="21-create-a-container"></a>2.1 Creare un contenitore

Per creare un contenitore pubblico nell'archiviazione BLOB, eseguire la procedura seguente:To create a public container in Blob storage, perform the following steps:

1. In **Servizio BLOB** nel menu a sinistra selezionare **BLOB**.
1. Selezionare **il contenitore**.
1. In **Nome**immettere *root*. Il nome può essere un nome di propria scelta, ad esempio *contoso*, ma in questo esempio viene utilizzata *la radice* in questo esempio per semplicità.
1. Per **Livello di accesso pubblico**selezionare **BLOB**, quindi **OK**.
1. Selezionare **root** per aprire il nuovo contenitore.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Caricare i file di contenuto della pagina personalizzati

1. Selezionare **Carica**.
1. Selezionare l'icona della cartella accanto a **Selezionare un file**.
1. Passare a e selezionare **customize-ui.html**, creato in precedenza nella sezione Personalizzazione dell'interfaccia utente della pagina.
1. Se si desidera caricare in una sottocartella, espandere **Avanzate** e immettere un nome di cartella in Carica nella **cartella**.
1. Selezionare **Carica**.
1. Selezionare il BLOB **customize-ui.html** caricato.
1. A destra della casella di testo **URL,** selezionare l'icona **Copia negli Appunti** per copiare l'URL negli Appunti.
1. Nel Web browser passare all'URL copiato per verificare che il BLOB caricato sia accessibile. Se non è accessibile, ad `ResourceNotFound` esempio se si verifica un errore, assicurarsi che il tipo di accesso al contenitore sia impostato su **blob**.

### <a name="3-configure-cors"></a>3. Configurare CORS

Configurare l'archiviazione BLOB per la condivisione di risorse tra origini eseguendo la procedura seguente:Configure Blob storage for Cross-Origin Resource Sharing by performing the following steps:

1. Nel menu selezionare **CORS**.
1. Per **Origini consentite** immettere `https://your-tenant-name.b2clogin.com`. Sostituire `your-tenant-name` con il nome del tenant di Azure AD B2C. Ad esempio: `https://fabrikam.b2clogin.com`. Utilizzare tutte le lettere minuscole quando si immette il nome del tenant.
1. Per **Metodi consentiti** selezionare sia `GET` che `OPTIONS`.
1. Per **Intestazioni consentite** immettere un asterisco (*).
1. Per **Intestazioni esposte** immettere un asterisco (*).
1. Per **Età massima** immettere 200.
1. Selezionare **Salva**.

#### <a name="31-test-cors"></a>3.1 Test CORS

Verificare che si è pronti eseguendo la procedura seguente:

1. Ripetere il passaggio di configurazione CORS. Per **Origini consentite**, immettere`https://www.test-cors.org`
1. Passare a [www.test-cors.org](https://www.test-cors.org/) 
1. Nella casella **URL remoto** incollare l'URL del file HTML. Ad esempio, usare `https://your-account.blob.core.windows.net/azure-ad-b2c/unified.html`
1. Selezionare **Invia richiesta**.
    Il risultato `XHR status: 200`dovrebbe essere . 
    Se si riceve un messaggio d'errore, verificare che le impostazioni CORS siano corrette. Potrebbe anche essere necessario cancellare la cache del browser o aprire una sessione di esplorazione anonima premendo Ctrl+Maiusc+P.
