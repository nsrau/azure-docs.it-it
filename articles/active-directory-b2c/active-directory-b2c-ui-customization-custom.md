---
title: 'Azure Active Directory B2C: personalizzare un''interfaccia utente usando criteri personalizzati | Microsoft Docs'
description: Informazioni sulla personalizzazione di un'interfaccia utente con l'uso di criteri personalizzati in Azure AD B2C.
services: active-directory-b2c
documentationcenter: 
author: SaeedAkhter-MSFT
manager: krassk
editor: gsacavdm
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeeda
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: cede72e742bbea7b1d51d42609743ea574af75ed
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017

---
# <a name="azure-active-directory-b2c-configure-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C: configurare la personalizzazione dell'interfaccia utente in un criterio personalizzato

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dopo aver completato questo articolo si avrà un criterio personalizzato per l'iscrizione e l'accesso con il proprio marchio e aspetto. Con Azure Active Directory B2C (Azure AD B2C) si ottiene il controllo quasi completo del contenuto HTML e CSS presentato agli utenti. Quando si usa un criterio personalizzato, si configura la personalizzazione dell'interfaccia utente in XML anziché usare i controlli nel portale di Azure. 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, completare [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md). È necessario disporre di un criterio personalizzato di lavoro per l'iscrizione e l'accesso con account locali.

## <a name="page-ui-customization"></a>Personalizzazione dell'interfaccia utente della pagina

Con la funzionalità di personalizzazione dell'interfaccia utente della pagina, è possibile definire l'aspetto di qualsiasi criterio personalizzato. È anche possibile mantenere la coerenza visiva e del marchio tra l'applicazione e Azure AD B2C.

Ecco come funziona: Azure AD B2C esegue il codice nel browser del cliente e usa un approccio moderno denominato [Condivisione di risorse tra le origini (CORS)](http://www.w3.org/TR/cors/). In primo luogo, specificare un URL nel criterio personalizzato con contenuto HTML personalizzato. Azure AD B2C unisce elementi dell'interfaccia utente con il contenuto HTML caricato dall'URL e quindi visualizza la pagina al cliente.

## <a name="create-your-html5-content"></a>Creare il contenuto HTML5

Creare il contenuto HTML con il nome del marchio del prodotto nel titolo.

1. Copiare il frammento di codice HTML seguente. Si tratta di codice HTML5 ben formato con un elemento vuoto denominato *\<div id="api"\>\</div\>* posizionato tra i tag *\<body\>*. Questo elemento indica il punto in cui deve essere inserito il contenuto di Azure AD B2C.

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

   >[!NOTE]
   >Per motivi di sicurezza non è attualmente possibile usare JavaScript per la personalizzazione.

2. Incollare il frammento di codice copiato in un editor di testo e quindi salvare il file con il nome *customize-ui.html*.

## <a name="create-an-azure-blob-storage-account"></a>Creare un account di archiviazione BLOB di Azure

>[!NOTE]
> In questo articolo verrà usato l'archivio BLOB di Azure per ospitare il contenuto. È possibile scegliere di ospitare il contenuto in un server Web, ma è necessario [abilitare la condivisione di risorse tra le origini (CORS) nel server Web](https://enable-cors.org/server.html).

Per ospitare il contenuto HTML nell'archivio Blob, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu **Hub** selezionare **Nuovo** > **Archiviazione** > **Account di archiviazione**.
3. Immettere un **nome** unico per l'account di archiviazione.
4. Per **Modello di distribuzione** è possibile lasciare **Resource Manager**.
5. Modificare **Tipo di Account** in **archiviazione BLOB**.
6. Per **Prestazioni** è possibile lasciare **Standard**.
7. Per **Replica** è possibile lasciare **RA-GRS**.
8. Per **Livello di accesso** è possibile lasciare **Frequente**.
9. Per **Crittografia del servizio di archiviazione** è possibile lasciare **Disabilitata**.
10. Selezionare una **sottoscrizione** per l'account di archiviazione.
11. Creare un **gruppo di risorse** o selezionarne uno esistente.
12. Selezionare la **posizione geografica** dell'account di archiviazione.
13. Fare clic su **Crea** per creare l'account di archiviazione.  
    Quando la distribuzione è terminata, il pannello dell'**account di archiviazione** si aprirà automaticamente.

## <a name="create-a-container"></a>Creare un contenitore

Per creare un contenitore pubblico nell'archivio BLOB seguire questa procedura:

1. Fare clic sulla scheda **Panoramica**.
2. Fare clic su **Contenitore**.
3. Per **Nome** digitare **$root**.
4. Impostare **Tipo di accesso** su **BLOB**.
5. Fare clic su **$root** per aprire il nuovo contenitore.
6. Fare clic su **Carica**.
7. Fare clic sull'icona della cartella accanto a **Selezionare un file**.
8. Passare al file **customize-ui.html** creato in precedenza nella sezione [Personalizzazione dell'interfaccia utente della pagina](#the-page-ui-customization-feature).
9. Fare clic su **Carica**.
10. Selezionare il BLOB customize-ui.html caricato.
11. Accanto a **URL** fare clic su **Copia**.
12. Incollare l'URL copiato in un browser e andare al sito. Se il sito non è accessibile, assicurarsi che il tipo di accesso del contenitore sia impostato su **BLOB**.

## <a name="configure-cors"></a>Configurare CORS

Configurare l'archivio BLOB per la condivisione di risorse tra le origini (CORS) seguendo questa procedura:

>[!NOTE]
>Si desidera provare le funzionalità di personalizzazione dell'interfaccia utente usando il codice HTML e il contenuto CSS? È stato fornito uno [strumento di supporto semplice](active-directory-b2c-reference-ui-customization-helper-tool.md) che carica e configura il contenuto di esempio nell'account di archiviazione BLOB. Se si usa lo strumento, passare a [Modificare i criteri personalizzati di iscrizione o di accesso](#modify-your-sign-up-or-sign-in-custom-policy).

1. Nel pannello **Archiviazione** aprire **CORS** in **Impostazioni**.
2. Fare clic su **Aggiungi**.
3. Per **Origini consentite** digitare un asterisco (\*).
4. Nell'elenco a discesa **Verbi consentiti** selezionare **GET** e **OPZIONI**.
5. Per **Intestazioni consentite** digitare un asterisco (\*).
6. Per **Intestazioni esposte** digitare un asterisco (\*).
7. Per **Tempo trascorso massimo (secondi)**, digitare **200**.
8. Fare clic su **Aggiungi**.

## <a name="test-cors"></a>Testare CORS

Verificare che tutte le operazioni preliminari siano state completate seguendo questa procedura:

1. Passare al sito Web [test-cors.org](http://test-cors.org/) e quindi incollare l'URL nella casella **Remote URL** (URL remoto).
2. Fare clic su **Send Request** (Invia richiesta).  
    Se si riceve un messaggio d'errore, verificare che le [impostazioni CORS](#configure-cors) siano corrette. Potrebbe anche essere necessario cancellare la cache del browser o aprire una sessione di esplorazione anonima premendo Ctrl+Maiusc+P.

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>Modificare i criteri personalizzati di iscrizione o di accesso

Sotto il tag *\<TrustFrameworkPolicy\>* di primo livello dovrebbe essere presente il tag *\<BuildingBlocks\>*. All'interno dei tag *\<BuildingBlocks\>* aggiungere un tag *\<ContentDefinitions\>* copiando l'esempio seguente. Sostituire *your_storage_account* con il nome del proprio account di archiviazione.

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>Caricare il criterio personalizzato aggiornato

1. Nel [portale di Azure](https://portal.azure.com) [passare al contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e quindi aprire il pannello **Azure AD B2C**.
2. Fare clic su **Tutti i criteri**.
3. Fare clic su **Carica il criterio**.
4. Caricare `SignUpOrSignin.xml` con il tag *\<ContentDefinitions\>* aggiunto in precedenza.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testare i criteri personalizzati tramite **Esegui adesso**

1. Nel pannello **Azure AD B2C** passare a **Tutti i criteri**.
2. Selezionare il criterio personalizzato che è stato caricato e fare clic sul pulsante **Esegui adesso**.
3. Dovrebbe essere possibile iscriversi usando un indirizzo di posta elettronica.

## <a name="reference"></a>Riferimento

È possibile trovare modelli di esempio per la personalizzazione dell'interfaccia utente qui:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

La cartella sample_templates/wingtip contiene i file HTML seguenti:

| Modello HTML5 | Descrizione |
|----------------|-------------|
| *phonefactor.html* | Usare questo file come modello per una pagina di autenticazione a più fattori. |
| *resetpassword.html* | Usare questo file come modello per una pagina Password dimenticata. |
| *selfasserted.html* | Usare questo file come modello per una pagina di iscrizione all'account di social networking, una pagina di iscrizione dell'account locale o una pagina di accesso dell'account locale. |
| *unified.html* | Usare questo file come modello per una pagina unificata per l'iscrizione o l'accesso. |
| *updateprofile.html* | Usare questo file come modello per una pagina di aggiornamento del profilo. |

Nella sezione [Modificare i criteri personalizzati di iscrizione o di accesso](#modify-your-sign-up-or-sign-in-custom-policy) è stata configurata la definizione del contenuto per `api.idpselections`. Il set completo di ID di definizione del contenuto riconosciuti dal framework dell'esperienza di gestione delle identità di AD B2C e le relative descrizioni sono disponibili nella tabella seguente:

| ID definizione del contenuto | Descrizione | 
|-----------------------|-------------|
| *api.error* | **Pagina di errore**. Questa pagina viene visualizzata quando viene rilevata un'eccezione o un errore. |
| *api.idpselections* | **Pagina di selezione del provider di identità**. Questa pagina contiene un elenco dei provider di identità che l'utente può scegliere durante la procedura di accesso. Si tratta di provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook e Google+, o account locali. |
| *api.idpselections.signup* | **Selezione del provider di identità per l'iscrizione**. Questa pagina contiene un elenco dei provider di identità che l'utente può scegliere durante la procedura di iscrizione. Si tratta di provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook e Google+, o account locali. |
| *api.localaccountpasswordreset* | **Pagina Password dimenticata**. Questa pagina contiene un modulo che l'utente deve compilare per avviare la reimpostazione della password.  |
| *api.localaccountsignin* | **Pagina di accesso dell'account locale**. Questa pagina contiene un modulo per eseguire l'accesso con un account locale basato su indirizzo di posta elettronica o nome utente. Il modulo può contenere una casella di input di testo e una casella di immissione della password. |
| *api.localaccountsignup* | **Pagina di iscrizione dell'account locale**. Questa pagina contiene un modulo per eseguire l'iscrizione a un account locale basato su indirizzo di posta elettronica o nome utente. Il modulo può contenere diversi controlli di input, ad esempio una casella per l'immissione di testo, una casella per l'immissione della password, un pulsante di opzione, caselle a discesa a selezione singola e caselle di controllo con selezione multipla. |
| *api.phonefactor* | **Pagina dell'autenticazione a più fattori**. In questa pagina gli utenti possono verificare il proprio numero di telefono (tramite SMS o chiamata vocale) durante la procedura di iscrizione o di accesso. |
| *api.selfasserted* | **Pagina di iscrizione dell'account locale**. Questa pagina contiene un modulo di iscrizione che l'utente deve compilare per effettuare l'iscrizione usando un account esistente di un provider di identità basato su social network, ad esempio Facebook o Google+. Questa pagina è simile alla pagina di iscrizione dell'account di social networking illustrata in precedenza, a eccezione dei campi di immissione della password. |
| *api.selfasserted.profileupdate* | **Pagina di aggiornamento del profilo**. Questa pagina contiene un modulo che gli utenti possono usare per aggiornare il profilo. Questa pagina è simile alla pagina di iscrizione dell'account di social networking, a eccezione dei campi di immissione della password. |
| *api.signuporsignin* | **Pagina unificata per l'iscrizione o l'accesso**. Questa pagina consente di gestire sia l'iscrizione che l'accesso degli utenti, che possono usare provider di identità aziendali, provider di identità basati su social network come Facebook o Google+ o account locali.  |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli elementi dell'interfaccia utente che è possibile personalizzare, vedere la [guida di riferimento per la personalizzazione dell'interfaccia utente per i criteri predefiniti](active-directory-b2c-reference-ui-customization.md).

