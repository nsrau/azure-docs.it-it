---
title: 'Azure Active Directory B2C: personalizzazione dell&quot;interfaccia utente con i criteri personalizzati| Documentazione Microsoft'
description: Un argomento sulla personalizzazione dell&quot;interfaccia utente durante l&quot;uso dei criteri personalizzati in Azure AD B2C
services: active-directory-b2c
documentationcenter: 
author: saeeda
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
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 215f9baeda7cd0bcf3fd66893919575647849e7d
ms.contentlocale: it-it
ms.lasthandoff: 05/02/2017

---
# <a name="azure-active-directory-b2c-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C: personalizzazione dell'interfaccia utente in un criterio personalizzato

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dopo aver completato questo articolo, si disporrà di un criterio personalizzato per l'iscrizione e l'accesso con il proprio aspetto e marchio.  Azure AD B2C consente il controllo quasi totale dell'HTML e del CSS presentati all'utente finale.  Quando si usa un criterio personalizzato, la personalizzazione dell'interfaccia utente è configurata in XML anziché usare i controlli nel portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di procedere, è necessario completare [Getting started with custom policies](active-directory-b2c-get-started-custom.md) (Introduzione ai criteri personalizzati).  È necessario disporre di un criterio personalizzato di lavoro per l'iscrizione e l'accesso con account locali.

### <a name="confirming-your-b2c-tenant"></a>Conferma del tenant B2C

Poiché i criteri personalizzati sono ancora in anteprima privata, confermare che il tenant di Azure AD B2C sia abilitato per il caricamento dei criteri personalizzati:

1. Nel [portale di Azure](https://portal.azure.com) [passare al contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e aprire il pannello Azure AD B2C.
1. Fare clic su **Tutti i criteri**.
1. Assicurarsi che **Carica il criterio** sia disponibile.  Se il pulsante è disabilitato, inviare tramite posta elettronica AADB2CPreview@microsoft.com.

## <a name="the-page-ui-customization-feature"></a>Funzionalità di personalizzazione dell'interfaccia utente della pagina

Con la funzionalità di personalizzazione dell'interfaccia utente della pagina, è possibile personalizzare l'aspetto di qualsiasi criterio personalizzato.  Mantenere la coerenza visiva e del marchio tra l'applicazione e Azure AD B2C.

Ecco come funziona: Azure AD B2C esegue il codice nel browser dell'utente e usa un approccio moderno denominato [Condivisione di risorse tra le origini (CORS)](http://www.w3.org/TR/cors/).  In primo luogo, specificare un URL nel criterio personalizzato con contenuto HTML personalizzato.  Azure AD B2C unisce elementi dell'interfaccia utente con il contenuto HTML caricato dall'URL e mostra la pagina agli utenti.

## <a name="creating-your-html5-content"></a>Creazione del contenuto HTML5

Verrà ora creato il contenuto HTML con il nome del marchio del prodotto nel titolo.

1. Fare clic su **Copia** in questo frammento di codice html.  È nel formato corretto HTML5 con un elemento vuoto denominato `<div id="api"></div>` posizionato in qualche punto nella `<body>`. Questo elemento corrisponde al punto in cui viene inserito il contenuto di Azure AD B2C.

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

1. Incollare in un editor di testo e salvare il file con il nome `customize-ui.html`

## <a name="create-a-blob-storage-account"></a>Creare un account di archiviazione BLOB

>[!NOTE]
> In questa guida usiamo l'archiviazione BLOB di Azure per ospitare il contenuto.  È anche possibile ospitare il contenuto in un server Web, ma è necessario [abilitare la Condivisione risorse tra le origini (CORS) sul server Web](https://enable-cors.org/server.html).

Questo codice HTML verrà ora ospitato nell'archiviazione BLOB di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Dal menu Hub scegliere **Nuovo** -> **Archiviazione** -> **Account di archiviazione**.
1. Immettere un **nome** unico per l'account di archiviazione.
1. Il **Modello di distribuzione** può essere lasciato come **Gestione risorse**.
1. Modificare **Tipo di Account** in **archiviazione BLOB**.
1. Le **Prestazioni** possono essere lasciate come **Standard**.
1. La **Replica** può essere lasciata come **RA-GRS**.
1. Il **Livello di accesso** può essere lasciato come **Accesso frequente**.
1. La **Crittografia del servizio di archiviazione** può essere lasciata come **Disabilitata**.
1. Selezionare una **sottoscrizione** per l'account di archiviazione.
1. Creare un **gruppo di risorse**o selezionarne uno esistente.
1. Selezionare la **posizione geografica** dell'account di archiviazione.
1. Fare clic su **Crea** per creare l'account di archiviazione.
1. Attendere il completamento della distribuzione e il pannello dell'account di archiviazione si aprirà automaticamente.

## <a name="create-a-container"></a>Creare un contenitore

Verrà ora creato un contenitore pubblico nell'archiviazione BLOB di Azure.

1. Passare alla scheda a sinistra chiamata **Panoramica**.
1. Fare clic su **+ Contenitore**.
1. Per **Nome** digitare `$root`
1. Impostare **Tipo di accesso** su **BLOB**.
1. Fare clic su '$root' per aprire il nuovo contenitore.
1. Fare clic su **Carica**.
1. Fare clic sull'icona della cartella accanto a `Select a file`.
1. Passare a `customize-ui.html` che è stato creato nella [sezione precedente](#the-page-ui-customization-feature).
1. Fare clic su **Carica**.
1. Selezionare il BLOB che è stato caricato chiamato `customize-ui.html`.
1. Fare clic sul pulsante copia accanto a **URL**.
1. Aprire un browser e passare a questo URL.  Se non è accessibile, assicurarsi che il tipo di accesso del contenitore sia impostato su BLOB.

## <a name="configure-cors"></a>Configurare CORS

Successivamente si configura l'archiviazione BLOB di Azure per la Condivisione di risorse tra le origini (CORS).

>[!NOTE]
>Si desidera provare le funzionalità di personalizzazione dell'interfaccia utente usando il codice HTML e il contenuto CSS?  È stato fornito uno [strumento di supporto semplice](active-directory-b2c-reference-ui-customization-helper-tool.md) che carica e configura il contenuto di esempio nell'account di archiviazione BLOB di Azure.  Se si usa lo strumento, passare a [Modificare il criterio personalizzato di iscrizione o accesso](#modify-your-sign-up-or-sign-in-custom-policy)

1. Nel pannello di archiviazione in impostazioni, aprire **CORS**.
1. Fare clic su **+ Aggiungi**.
1. Impostare `Allowed origins` su `*`.
1. Nell'elenco a discesa denominato `Allowed verbs` selezionare sia `GET` che `OPTIONS`.
1. Impostare `Allowed headers` su `*`.
1. Impostare `Exposed headers` su `*`.
1. Impostare `Maximum age (seconds)` su `200`.
1. Fare clic su **Aggiungi**.

## <a name="testing-cors"></a>Test CORS

Convalidare che si è pronti.

1. Passare a http://test-cors.org/ e incollare l'URL nel campo `Remote URL`.
1. Fare clic su **Invio richiesta**
1. Se si riceve un messaggio d'errore, verificare che le [impostazioni CORS](#configure-cors) siano corrette.  È necessario anche cancellare la cache del browser o provare una sessione di esplorazione in privato `CTRL-SHIFT-P`.

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>Modificare i criteri personalizzati di iscrizione o di accesso

1. Sotto il tag di livello superiore `<TrustFrameworkPolicy>` si dovrebbe trovare il tag `<BuildingBlocks>`.  All'interno del tag `<BuildingBlocks>` aggiungere un tag `ContentDefinitions` tramite la copia di questo esempio.  Sostituire `{your_storage_account}` con il nome del proprio account di archiviazione.

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

1. Nel [portale di Azure](https://portal.azure.com) [passare al contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e aprire il pannello Azure AD B2C.
1. Fare clic su **Tutti i criteri**.
1. Fare clic su **Carica il criterio**.
1. Caricare `SignUpOrSignin.xml` con il tag `ContentDefinitions` che è stato aggiunto.

## <a name="test-the-custom-policy-using-run-now"></a>Testare i criteri personalizzati tramite "Esegui adesso"

1. Aprire il **Pannello Azure AD B2C** e passare a **Tutti i criteri**.
1. Selezionare il criterio personalizzato che è stato caricato e fare clic sul pulsante **Esegui adesso**.
1. Dovrebbe essere possibile iscriversi usando un indirizzo di posta elettronica.

## <a name="next-steps"></a>Passaggi successivi

Questa [guida di riferimento per la personalizzazione dell'interfaccia utente per i criteri predefiniti](active-directory-b2c-reference-ui-customization.md) contiene informazioni aggiuntive sugli elementi dell'interfaccia utente che è possibile personalizzare.  Non esiste alcuna differenza di personalizzazione dell'interfaccia utente tra i criteri predefiniti e quelli personalizzati.

