---
title: 'Azure Active Directory B2C: personalizzazione dell''interfaccia utente | Microsoft Docs'
description: Argomento che descrive le funzionalità di personalizzazione dell'interfaccia utente in Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: swkrish
manager: mbaldwin
editor: bryanla

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: swkrish

---
# Azure Active Directory B2C: personalizzare l'interfaccia utente di Azure AD B2C
L'esperienza utente rappresenta l'aspetto più importante in un'applicazione rivolta agli utenti finali. Fa la differenza tra una buona applicazione e un'applicazione straordinaria e tra utenti semplicemente attivi e utenti coinvolti. Azure Active Directory (Azure AD) B2C permette di personalizzare le pagine di iscrizione, accesso (*vedere la nota seguente*), modifica del profilo e reimpostazione della password degli utenti con controllo Pixel Perfect.

> [!NOTE]
> Attualmente le pagine di accesso dell'account locale, le pagine di reimpostazione della password correlate e i messaggi di posta elettronica di verifica possono essere personalizzati solo tramite la [funzionalità di personalizzazione aziendale](../active-directory/active-directory-add-company-branding.md) e non con i meccanismi descritti in questo articolo.
> 
> 

Contenuto dell'articolo:

* Funzionalità di personalizzazione dell'interfaccia utente della pagina.
* Strumento che consente di testare la funzionalità di personalizzazione dell'interfaccia utente della pagina usando il contenuto di esempio.
* Elementi principali dell'interfaccia utente in ogni tipo di pagina.
* Procedure consigliate per l'utilizzo di questa funzionalità.

## Funzionalità di personalizzazione dell'interfaccia utente della pagina
La funzionalità di personalizzazione dell'interfaccia utente della pagina consente di personalizzare l'aspetto delle pagine di iscrizione, accesso, reimpostazione password e modifica del profilo visualizzate dagli utenti, tramite la configurazione di [criteri](active-directory-b2c-reference-policies.md). Gli utenti potranno usufruire di un'esperienza semplice durante lo spostamento tra le pagine dell'applicazione e le pagine gestite da Azure AD B2C.

Diversamente da altri servizi in cui le opzioni dell'interfaccia utente sono limitate o sono disponibili solo tramite le API, Azure AD B2C usa un approccio moderno e più semplice rispetto alla personalizzazione dell'interfaccia utente della pagina.

Ecco come funziona: Azure AD B2C esegue il codice nel browser dell'utente e usa un approccio moderno denominato [Condivisione di risorse tra le origini (CORS)](http://www.w3.org/TR/cors/) per caricare il contenuto da un URL specificato nei criteri. È possibile specificare URL diversi per pagine diverse. Il codice unisce elementi dell'interfaccia utente di Azure AD B2C e il contenuto caricato dall'URL e mostra la pagina agli utenti. Operazioni da eseguire:

1. Creare contenuto HTML5 ben formato con un elemento `<div id="api"></div>`, che deve essere un elemento vuoto, inserito all'interno dell'elemento `<body>`. Questo elemento corrisponde al punto in cui viene inserito il contenuto di Azure AD B2C.
2. Ospitare il contenuto in un endpoint HTTPS in cui è consentita la condivisione CORS.
3. Applicare lo stile agli elementi dell'interfaccia utente inseriti da Azure AD B2C.

## Provare la funzionalità di personalizzazione dell'interfaccia utente
Se si vuole provare la funzionalità di personalizzazione dell'interfaccia utente usando il contenuto HTML e CSS di esempio, è disponibile [un semplice strumento di supporto](active-directory-b2c-reference-ui-customization-helper-tool.md) per caricare e configurare il contenuto di esempio nell'archivio BLOB di Azure.

> [!NOTE]
> È possibile ospitare il contenuto dell'interfaccia utente in qualsiasi punto: in server Web, reti CDN, AWS S3, sistemi di condivisione file e così via. A condizione di avere contenuto ospitato in un endpoint HTTPS disponibile pubblicamente (con abilitata la condivisione CORS), è possibile iniziare. L'archivio BLOB di Azure viene usato solo a scopo illustrativo.
> 
> 

### Contenuto HTML di base per il test
Di seguito è illustrato il contenuto HTML di base che è possibile usare per testare questa funzionalità. Utilizzare lo stesso strumento di supporto specificato in precedenza per caricare e configurare il contenuto nell'archivio di BLOB di Azure. È quindi possibile verificare che i pulsanti di base, senza stili e i campi del modulo in ogni pagina siano visualizzati e funzionali.

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>    <!-- IMP: This element is intentionally empty; don't enter anything here -->
    </body>
</html>

```

## Elementi principali dell'interfaccia utente in ogni tipo di pagina
Le sezioni seguenti includono alcuni esempi di frammenti HTML5 che Azure AD B2C unisce nell'elemento `<div id="api"></div>` che si trova nel contenuto. **Non inserire questi frammenti nel contenuto HTML5.** Il servizio Azure AD B2C li inserisce in fase di esecuzione. Usare questi esempi per progettare i propri fogli di stile.

### Contenuto di Azure AD B2C inserito nella "Pagina di selezione del provider di identità"
Questa pagina contiene un elenco dei provider di identità che l'utente può scegliere durante la procedura di iscrizione o di accesso. Sono presenti sia i provider di identità basati su social network, ad esempio Facebook e Google+, sia gli account locali (basati su indirizzo di posta elettronica o nome utente).

```HTML

<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>

```

### Contenuto di Azure AD B2C inserito nella "Pagina di iscrizione dell'account locale"
Questa pagina contiene un modulo di iscrizione che l'utente deve compilare per eseguire l'iscrizione a un account locale basato su indirizzo di posta elettronica o nome utente. Il modulo può contenere diversi controlli di input, ad esempio caselle per l'immissione di testo, caselle per l'immissione della password, pulsanti di opzione, caselle a discesa a selezione singola e caselle di controllo con selezione multipla.

```HTML

<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ "; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>

```

### Contenuto di Azure AD B2C inserito nella "Pagina di iscrizione all'account di social networking"
Questa pagina contiene un modulo di iscrizione che l'utente deve compilare per effettuare l'iscrizione usando un account esistente di un provider di identità basato su social network, ad esempio Facebook o Google+. Questa pagina è simile alla pagina di iscrizione dell'account locale (mostrata nella sezione precedente), ad eccezione dei campi di immissione della password.

### Contenuto di Azure AD B2C inserito nella "Pagina unificata per l'iscrizione o l'accesso"
Questa pagina consente di gestire sia l'iscrizione che l'accesso degli utenti finali, che possono usare provider di identità social come Facebook o Google+ o account locali.

```HTML

<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>

```

### Contenuto di Azure AD B2C inserito nella pagina "Multi-Factor Authentication"
In questa pagina gli utenti possono verificare il proprio numero di telefono (tramite SMS o chiamata vocale) durante la procedura di iscrizione o di accesso.

```HTML

<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>

```

### Contenuto di Azure AD B2C inserito nella "Pagina di errore"
```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>

```

## Aspetti da ricordare durante la fase di creazione del contenuto
Se si prevede di usare la funzionalità di personalizzazione dell'interfaccia utente della pagina, fare riferimento alle procedure consigliate seguenti:

* Non copiare il contenuto predefinito di Azure AD B2C né provare a modificarlo. È preferibile creare il contenuto HTML5 da zero e usare il contenuto predefinito come riferimento.
* In tutte le pagine (tranne le pagine di errore) gestite tramite criteri di accesso, iscrizione e modifica del profilo, i fogli di stile forniti dall'utente dovranno sostituire i fogli di stile predefiniti aggiunti a queste pagine nei frammenti <head>. In tutte le pagine gestite tramite criteri di iscrizione o accesso e reimpostazione password, oltre alle pagine di errore in tutti i criteri, sarà necessario fornire tutti gli stili manualmente.
* Per motivi di sicurezza, non è consentito includere codice JavaScript nel contenuto. La maggior parte degli elementi necessari dovrebbe già essere disponibile. In caso contrario, usare [UserVoice](http://feedback.azure.com/forums/169401-azure-active-directory) per richiedere nuove funzionalità.
* Versioni di browser supportate:
  * Internet Explorer 11
  * Internet Explorer 10
  * Internet Explorer 9 (supporto limitato)
  * Internet Explorer 8 (supporto limitato)
  * Google Chrome 43.0
  * Google Chrome 42.0
  * Mozilla Firefox 38.0
  * Mozilla Firefox 37.0

<!---HONumber=AcomDC_0727_2016-->