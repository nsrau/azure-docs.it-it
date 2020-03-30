---
title: Esempi JavaScript
titleSuffix: Azure AD B2C
description: Informazioni sull'uso di JavaScript in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a26f6c5e69ca083335580a0368459e062de3941e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187662"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Esempi JavaScript da usare in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

È possibile aggiungere il proprio codice lato client JavaScript alle applicazioni Azure Active Directory B2C (Azure AD B2C).

Per abilitare JavaScript per le applicazioni:

* Aggiungere un elemento ai [criteri personalizzatiAdd](custom-policy-overview.md) an element to your custom policy
* Selezionare un [layout di pagina](page-layout.md)
* Utilizzare [b2clogin.com](b2clogin.md) nelle richieste

Questo articolo descrive come modificare i criteri personalizzati per abilitare l'esecuzione di script.

> [!NOTE]
> Se si desidera abilitare JavaScript per i flussi utente, vedere Versioni di JavaScript e layout di [pagina in Azure Active Directory B2C.](user-flow-javascript-overview.md)

## <a name="prerequisites"></a>Prerequisiti

### <a name="select-a-page-layout"></a>Selezionare un layout di pagina

* Selezionare un layout di [pagina](contentdefinitions.md#select-a-page-layout) per gli elementi dell'interfaccia utente dell'applicazione.

    Se si intende utilizzare JavaScript, è necessario definire `contract` una versione del layout di [pagina](contentdefinitions.md#migrating-to-page-layout) con la versione della pagina per tutte *le* definizioni di contenuto nei criteri personalizzati.

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

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

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

Altre informazioni su come personalizzare l'interfaccia utente delle applicazioni sono disponibili nell'argomento [Personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato in Azure Active Directory B2C](custom-policy-ui-customization.md).
