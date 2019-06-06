---
title: Esempi di JavaScript - Azure Active Directory B2C | Microsoft Docs
description: Informazioni sull'uso di JavaScript in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9e19df7c50ca9d2c57ab385a567f4911b200c5e2
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510897"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Esempi JavaScript da usare in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

È possibile aggiungere codice lato server JavaScript personalizzato alle applicazioni Azure Active Directory (Azure AD) B2C. Per abilitare JavaScript per le applicazioni, è necessario aggiungere un elemento per il [criteri personalizzati](active-directory-b2c-overview-custom.md), selezionare una [contratto pagina](page-contract.md)e usare [b2clogin.com](b2clogin.md) nelle richieste. Questo articolo descrive come modificare i criteri personalizzati per abilitare l'esecuzione dello script.

> [!NOTE]
> Se si desidera abilitare JavaScript per i flussi utente, vedere [JavaScript e pagina Contratto di versioni in Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Prerequisiti

Selezionare un contratto di pagina per gli elementi dell'interfaccia utente dell'applicazione. Se si prevede di usare JavaScript, è necessario definire una versione del contratto di pagina per tutte le definizioni del contenuto nei criteri personalizzati.

## <a name="add-the-scriptexecution-element"></a>Aggiungere l'elemento ScriptExecution

Per abilitare l'esecuzione di script, aggiungere l'elemento **ScriptExecution** all'elemento [RelyingParty](relyingparty.md).

1. Aprire il file dei criteri personalizzati. Ad esempio, *SignUpOrSignin.xml*.
2. Aggiungere l'elemento **ScriptExecution** all'elemento **UserJourneyBehaviors** di **RelyingParty**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Salvare e caricare il file.

## <a name="guidelines-for-using-javascript"></a>Linee guida per l'uso di JavaScript

Seguire queste linee guida per personalizzare l'interfaccia dell'applicazione tramite JavaScript:

- Non associare un evento clic in elementi HTML `<a>`.
- Non definire una dipendenza dal codice o da commenti di Azure AD B2C.
- Non modificare l'ordine o la gerarchia di elementi HTML di Azure AD B2C. Usare un criterio di Azure AD B2C per controllare l'ordine degli elementi dell'interfaccia utente.
- È possibile chiamare qualsiasi servizio RESTful tenendo presenti queste considerazioni:
    - Potrebbe essere necessario impostare CORS del servizio RESTful in modo da consentire le chiamate HTTP lato client.
    - Assicurarsi che il servizio RESTful sia sicuro e usi solo il protocollo HTTPS.
    - Non usare JavaScript direttamente per chiamare endpoint di Azure AD B2C.
- È possibile incorporare JavaScript o è possibile eseguire il collegamento a file JavaScript esterni. Quando si usa un file JavaScript esterno, assicurarsi di usare l'URL assoluto e non un URL relativo.
- Framework JavaScript:
    - Azure AD B2C usa una versione specifica di jQuery. Non includere un'altra versione di jQuery. L'uso di più versioni nella stessa pagina può provocare problemi.
    - L'uso di RequireJS non è supportato.
    - La maggior parte dei framework JavaScript non è supportata da Azure AD B2C.
- Le impostazioni di Azure AD B2C possono essere lette chiamando oggetti `window.SETTINGS` e `window.CONTENT`, ad esempio la lingua dell'interfaccia utente corrente. Non modificare il valore di questi oggetti.
- Per personalizzare il messaggio di errore di Azure AD B2C, usare la localizzazione in un criterio.
- Se è possibile ottenere il risultato desiderato usando un criterio, questo è in genere l'approccio consigliato.

## <a name="javascript-samples"></a>Esempi JavaScript

### <a name="show-or-hide-a-password"></a>Mostrare o nascondere una password

Un modo comune per aiutare i clienti a effettuare correttamente l'iscrizione consiste nel permettere loro di visualizzare la password immessa. Questa opzione permette agli utenti di effettuare l'iscrizione consentendo loro di visualizzare e correggere la password in tutta semplicità, se necessario. Qualsiasi campo per la digitazione della password contiene una casella di controllo con etichetta **Mostra password**.  Questa casella di controllo permette agli utenti di visualizzare la password in testo normale. Includere questo frammento di codice nel modello di iscrizione o di accesso per una pagina autocertificata:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Aggiungere le condizioni per l'utilizzo

Includere il codice seguente nella pagina in cui si vuole aggiungere una casella di controllo **Condizioni per l'utilizzo**. Questa casella di controllo è in genere necessaria nelle pagine di iscrizione dell'account locale e di iscrizione dell'account di social networking.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

Nel codice sostituire `termsOfUseUrl` con il collegamento alle condizioni per l'utilizzo. Per la directory, creare un nuovo attributo utente denominato **termsOfUse** e quindi includere **termsOfUse** come attributo utente.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come personalizzare l'interfaccia utente delle applicazioni sono disponibili nell'argomento [Personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
