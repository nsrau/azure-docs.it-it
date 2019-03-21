---
title: ID della stringa di localizzazione - Azure Active Directory B2C | Microsoft Docs
description: Specificare gli ID per una definizione del contenuto con un ID api.signuporsignin nei criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5f937a86e4e063ad1e8346dc629a917ae7ce8b65
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077208"
---
# <a name="localization-string-ids"></a>ID della stringa di localizzazione

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

L'elemento **Localizzazione** consente di supportare più impostazioni locali o lingue nei criteri per i percorsi utente. Questo articolo fornisce l'elenco degli ID di localizzazione che è possibile usare nei criteri. Per acquisire familiarità con la localizzazione dell'interfaccia utente, vedere [Localizzazione](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Elementi della pagina di iscrizione o accesso

Gli ID seguenti vengono usati per una definizione del contenuto con un ID `api.signuporsignin`.

| ID | Valore predefinito |
| -- | ------------- |
| **local_intro_email** | Accesso con l'account esistente |
| **logonIdentifier_email** | Indirizzo di posta elettronica |
| **requiredField_email** | Immettere l'indirizzo di posta elettronica |
| **invalid_email** | Immettere un indirizzo di posta elettronica valido |
| **email_pattern** | ^ [a-zA-Z0-9.. & $% & "*c++ /CLI =? ^ _\`{\|} ~-]+@[a-zA-Z0-9-]+ (?:\\. [ un-zA-Z0 - 9-] +)*$ |
| **local_intro_username** | Accesso con il nome utente |
| **logonIdentifier_username** | Username |
| **requiredField_username** | Immettere il nome utente |
| **password** | Password |
| **requiredField_password** | Immettere la password |
| **invalid_password** | Il formato della password immessa non è quello previsto. |
| **forgotpassword_link** | Password dimenticata? |
| **createaccount_intro** | Non si dispone di un account? |
| **createaccount_link** | Effettua l'iscrizione ora |
| **divider_title** | Oppure |
| **cancel_message** | L'utente ha dimenticato la password |
| **button_signin** | Accesso |
| **social_intro** | Accedere con l'account di social networking |
  **remember_me** |Mantenere l'accesso|
| **unknown_error** | Si è verificato un errore durante l'accesso. Riprovare più tardi. |

L'esempio seguente illustra l'uso di alcuni elementi dell'interfaccia utente nella pagina per l'iscrizione o l'accesso:

![Elementi dell'esperienza utente della pagina per l'iscrizione o l'accesso](./media/localization-string-ids/localization-susi.png)

L'ID dei provider di identità viene configurato nell'elemento del percorso utente **ClaimsExchange**. Per localizzare il titolo del provider di identità **ElementType** è impostato su `ClaimsProvider` mentre **StringId** è impostato sull'ID di `ClaimsExchange`.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

L'esempio seguente localizza il provider di identità Facebook sull'arabo:

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>Messaggi di errore in fase di iscrizione o di accesso

| ID | Valore predefinito |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | La password non è corretta. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | L'account non è stato trovato. |
| **UserMessageIfOldPasswordUsed** | È stata usata una password non aggiornata. |  
| **DefaultMessage** | Nome utente o password non validi. |  
| **UserMessageIfUserAccountDisabled** | L'account è stato bloccato. Contattare il supporto tecnico per sbloccarlo, quindi riprovare. |  
| **UserMessageIfUserAccountLocked** | L'account è stato temporaneamente bloccato per impedirne l'uso non autorizzato. Riprovare. |  
| **AADRequestsThrottled** | In questo momento sono presenti troppe richieste. Attendere qualche minuto e riprovare. |  

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Elementi dell'interfaccia utente delle pagine di iscrizione e di autocertificazione

Di seguito sono riportati gli ID per una definizione del contenuto con ID `api.localaccountsignup` o qualsiasi definizione del contenuto che inizia con `api.selfasserted`, ad esempio `api.selfasserted.profileupdate` e `api.localaccountpasswordreset`.

| ID | Valore predefinito |
| -- | ------------- |
| **ver_sent** | Il codice di verifica è stato inviato a: |
| **ver_but_default** | Predefinito |
| **cancel_message** | L'utente ha annullato l'immissione delle informazioni autocertificate |
| **preloader_alt** | Attendere |
| **ver_but_send** | Inviare il codice di verifica |
| **alert_yes** | Sì |
| **error_fieldIncorrect** | Uno o più campi sono stati compilati in modo non corretto. Verificare le immissioni e riprovare. |
| **year** | Year |
| **verifying_blurb** | Attendere mentre vengono elaborate le informazioni. |
| **button_cancel** | Annulla |
| **ver_fail_no_retry** | Sono stati effettuati troppi tentativi non validi. Riprovare più tardi. |
| **mese** | Mese |
| **ver_success_msg** | Indirizzo di posta elettronica verificato. È ora possibile continuare. |
| **months** | Gennaio, febbraio, marzo, aprile, maggio, giugno, luglio, agosto, settembre, ottobre, novembre, dicembre |
| **ver_fail_server** | Si sono verificati problemi nella verifica dell'indirizzo di posta elettronica. Immettere un indirizzo di posta elettronica valido e riprovare. |
| **error_requiredFieldMissing** | Manca un campo obbligatorio. Compilare tutti i campi obbligatori e riprovare. |
| **initial_intro** | Specificare i dettagli seguenti. |
| **ver_but_resend** | Inviare un nuovo codice |
| **button_continue** | Create |
| **error_passwordEntryMismatch** | I campi di immissione della password non corrispondono. Immettere la stessa password in entrambi i campi e riprovare. |
| **ver_incorrect_format** | Il formato non è corretto. |
| **ver_but_edit** | Cambiare l'indirizzo di posta elettronica |
| **ver_but_verify** | Verificare il codice |
| **alert_no** | No  |
| **ver_info_msg** | Il codice di verifica è stato inviato alla Posta in arrivo. Copiarlo nella casella di input qui di seguito. |
| **giorno** | Giorno |
| **ver_fail_throttled** | Sono state effettuate troppe richieste di verifica di questo indirizzo di posta elettronica. Attendere qualche minuto e riprovare. |
| **helplink_text** | Che cos'è? |
| **ver_fail_retry** | Il codice non è corretto. Riprova più tardi. |
| **alert_title** | Annullare l'immissione dei dettagli personali |
| **required_field** | Informazione obbligatoria. |
| **alert_message** | Annullare l'immissione dei dettagli personali? |
| **ver_intro_msg** | È necessaria la verifica. Fare clic sul pulsante Invia. |
| **ver_input** | Codice di verifica |

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>Messaggi di errore delle pagine di iscrizione e di autocertificazione

| ID | Valore predefinito |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Un utente con il nome specificato esiste già. Sceglierne un altro. |
| **UserMessageIfClaimNotVerified** | Attestazione non verificata: {0} |
| **UserMessageIfIncorrectPattern** | Modello non corretto per: {0} |
| **UserMessageIfMissingRequiredElement** | Manca un elemento obbligatorio: {0} |
| **UserMessageIfValidationError** | Errore durante la convalida da: {0} |
| **UserMessageIfInvalidInput** | {0} contiene un input non valido. |
| **ServiceThrottled** | In questo momento sono presenti troppe richieste. Attendere qualche minuto e riprovare. |

L'esempio seguente illustra l'uso di alcuni elementi dell'interfaccia utente nella pagina di iscrizione:

![Elementi dell'esperienza utente della pagina di iscrizione](./media/localization-string-ids/localization-sign-up.png)

L'esempio seguente illustra l'uso di alcuni elementi dell'interfaccia utente nella pagina di iscrizione, dopo che l'utente ha fatto clic sul pulsante di invio del codice di verifica:

![Elementi dell'esperienza utente per la verifica della posta elettronica della pagina di iscrizione](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Elementi dell'interfaccia utente della pagina di autenticazione del fattore telefono

I seguenti sono gli ID di definizione del contenuto con ID `api.phonefactor`. 

| ID | Valore predefinito |
| -- | ------------- |
| **button_verify** | Chiama |
| **country_code_label** | Prefisso internazionale |
| **cancel_message** | L'utente ha annullato l'autenticazione a più fattori |
| **text_button_send_second_code** | Inviare un nuovo codice |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Nel record dell'utente è presente il seguente numero. È possibile inviare un codice di autenticazione via SMS o con una telefonata. |
| **intro_mixed_p** | Nel record dell'utente sono presenti i seguenti numeri. Scegliere un numero al quale sia possibile inviare un codice di autenticazione con una telefonata o via SMS. |
| **button_verify_code** | Verificare il codice |
| **requiredField_code** | Immettere il codice di verifica ricevuto |
| **invalid_code** | Immettere il codice a sei cifre ricevuto |
| **button_cancel** | Annulla |
| **local_number_input_placeholder_text** | Numero di telefono |
| **button_retry** | Retry |
| **alternative_text** | Non ho il telefono con me |
| **intro_phone_p** | Nel record dell'utente sono presenti i seguenti numeri. Scegliere un numero al quale si vuole ricevere una telefonata per l'autenticazione. |
| **intro_phone** | Nel record dell'utente è presente il seguente numero. Faremo una telefonata per autenticare l'utente. |
| **enter_code_text_intro** | Immettere il codice di verifica nel campo seguente oppure  |
| **intro_entry_phone** | Nel campo seguente immettere il numero al quale si vuole ricevere una telefonata per l'autenticazione. |
| **intro_entry_sms** | Nel campo seguente immettere il numero al quale si vuole ricevere un SMS con il codice di autenticazione. |
| **button_send_code** | Invia codice |
| **invalid_number** | Immettere un numero di telefono valido |
| **intro_sms** | Nel record dell'utente è presente il seguente numero. Verrà inviato un codice di autenticazione via SMS. |
| **intro_entry_mixed** | Nel campo seguente immettere il numero al quale si vuole ricevere un SMS con il codice o una telefonata per l'autenticazione. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |Nel record dell'utente sono presenti i seguenti numeri. Scegliere un numero al quale sia possibile inviare un codice di autenticazione via SMS. |
| **requiredField_countryCode** | Selezionare il prefisso internazionale |
| **requiredField_number** | Immettere il proprio numero di telefono |
| **country_code_input_placeholder_text** |Paese o area geografica |
| **number_label** | Numero di telefono |
| **error_tryagain** | Il numero di telefono specificato è occupato o non disponibile. Verificare il numero e riprovare. |
| **error_incorrect_code** | Il codice di verifica immesso non corrisponde a quello in archivio. Riprovare o richiedere un nuovo codice. |
| **countryList** | {\"DEFAULT\":\"Paese/Regione\",\"AF\":\"Afghanistan\",\"AX\":\"Isole Åland\",\"AL\":\"Albania\",\"DZ\":\"Algeria\",\"AS\":\"Samoa americane\",\"AD\":\"Andorra\",\"AO\":\"Angola\",\"AI\":\"Anguilla\",\"AQ\":\"Antartide\",\"AG\":\"Antigua e Barbuda\",\"AR\":\"Argentina\",\"AM\":\"Armenia\",\"AW\":\"Aruba\",\"AU\":\"Australia\",\"AT\":\"Austria\",\"AZ\":\"Azerbaijan\",\"BS\":\"Bahamas\",\"BH\":\"Bahrain\",\"BD\":\"Bangladesh\",\"BB\":\"Barbados\",\"BY\":\"Bielorussia\",\"BE\":\"Belgio\",\"BZ\":\"Belize\",\"BJ\":\"Benin\",\"BM\":\"Bermuda\",\"BT\":\"Bhutan\",\"BO\":\"Bolivia\",\"BQ\":\"Bonaire\",\"BA\":\"Bosnia ed Erzegovina\",\"BW\":\"Botswana\",\"BV\":\"Isola Bouvet\",\"BR\":\"Brasile\",\"IO\":\"Territori britannici dell'Oceano Indiano\",\"VG\":\"Isole Vergini britanniche\",\"BN\":\"Brunei\",\"BG\":\"Bulgaria\",\"BF\":\"Burkina Faso\",\"BI\":\"Burundi\",\"CV\":\"Cabo Verde\",\"KH\":\"Cambogia\",\"CM\":\"Camerun\",\"CA\":\"Canada\",\"KY\":\"Isole Cayman\",\"CF\":\"Repubblica Centrafricana\",\"TD\":\"Ciad\",\"CL\":\"Cile\",\"CN\":\"Cina\",\"CX\":\"Isola di Natale\",\"CC\":\"Cocos (Keeling) Islands\",\"CO\":\"Colombia\",\"KM\":\"Comore\",\"CG\":\"Congo\",\"CD\":\"Congo (RDC)\",\"CK\":\"Isole Cook\",\"CR\":\"Costa Rica\",\"CI\":\"Costa d'avorio\",\"HR\":\"Croazia\",\"CU\":\"Cuba\",\"CW\":\"Curaçao\",\"CY\":\"Cipro\",\"CZ\":\"Repubblica Ceca\",\"DK\":\"Danimarca\",\"DJ\":\"Gibuti\",\"DM\":\"Dominica\",\"DO\":\"Repubblica Dominicana\",\"EC\":\"Ecuador\",\"EG\":\"Egitto\",\"SV\":\"El Salvador\",\"GQ\":\"Guinea Equatoriale\",\"ER\":\"Eritrea\",\"EE\":\"Estonia\",\"ET\":\"Etiopia\",\"FK\":\"Isole Falkland\",\"FO\":\"Isole Faroe\",\"FJ\":\"Fiji\",\"FI\":\"Finlandia\",\"FR\":\"Francia\",\"GF\":\"Guiana francese\",\"PF\":\"Polinesia francese\",\"TF\":\"Terre australi e antartiche francesi\",\"GA\":\"Gabon\",\"GM\":\"Gambia\",\"GE\":\"Georgia\",\"DE\":\"Germania\",\"GH\":\"Ghana\",\"GI\":\"Gibraltar\",\"GR\":\"Grecia\",\"GL\":\"Groenlandia\",\"GD\":\"Grenada\",\"GP\":\"Guadalupe\",\"GU\":\"Guam\",\"GT\":\"Guatemala\",\"GG\":\"Guernsey\",\"GN\":\"Guinea\",\"GW\":\"Guinea-Bissau\",\"GY\":\"Guyana\",\"HT\":\"Haiti\",\"HM\":\"Isole Heard e McDonald\",\"HN\":\"Honduras\",\"HK\":\"RAS di Hong Kong\",\"HU\":\"Ungheria\",\"IS\":\"Islanda\",\"IN\":\"India\",\"ID\":\"Indonesia\",\"IR\":\"Iran\",\"IQ\":\"Iraq\",\"IE\":\"Irlanda\",\"IM\":\"Isola di an\",\"IL\":\"Israele\",\"IT\":\"Italia\",\"JM\":\"Giamaica\",\"JP\":\"Giappone\",\"JE\":\"Jersey\",\"JO\":\"Giordania\",\"KZ\":\"Kazakistan\",\"KE\":\"Kenya\",\"KI\":\"Kiribati\",\"KR\":\"Corea\",\"KW\":\"Kuwait\",\"KG\":\"Kirghizistan\",\"LA\":\"Laos\",\"LV\":\"Lettonia\",\"LB\":\"Libano\",\"LS\":\"Lesotho\",\"LR\":\"Liberia\",\"LY\":\"Libia\",\"LI\":\"Liechtenstein\",\"LT\":\"Lituania\",\"LU\":\"Lussemburgo\",\"MO\":\"RAS di Macao\",\"MK\":\"Macedonia, ex Repubblica Jugoslava di\",\"MG\":\"Madagascar\",\"MW\":\"Malawi\",\"MY\":\"Malesia\",\"MV\":\"Maldive\",\"ML\":\"Mali\",\"MT\":\"Malta\",\"MH\":\"Isole di Marshall\",\"MQ\":\"Martinica\",\"MR\":\"Mauritania\",\"MU\":\"Mauritius\",\"YT\":\"Mayotte\",\"MX\":\"Messico\",\"FM\":\"Micronesia\",\"MD\":\"Moldova\",\"MC\":\"Monaco\",\"MN\":\"Mongolia\",\"ME\":\"Montenegro\",\"MS\":\"Montserrat\",\"MA\":\"Marocco\",\"MZ\":\"Mozambico\",\"MM\":\"\",\"NA\":\"Namibia\",\"NR\":\"Nauru\",\"NP\":\"Nepal\",\"NL\":\"Paesi Bassi\",\"NC\":\"Nuova Caledonia\",\"NZ\":\"Nuova Zelanda\",\"NI\":\"Nicaragua\",\"NE\":\"Niger\",\"NG\":\"Nigeria\",\"NU\":\"Niue\",\"NF\":\"Isola di Norfolk\",\"KP\":\"Corea del nord\",\"MP\":\"Isole Marianne Settentrionali\",\"NO\":\"Norvegia\",\"OM\":\"Oman\",\"PK\":\"Pakistan\",\"PW\":\"Palau\",\"PS\":\"Autorità Nazionale Palestinese\",\"PA\":\"Panama\",\"PG\":\"Papua Nuova Guinea\",\"PY\":\"Paraguay\",\"PE\":\"Peru\",\"PH\":\"Filippine\",\"PN\":\"Isole Pitcairn\",\"PL\":\"Polonia\",\"PT\":\"Portogallo\",\"PR\":\"Porto Rico\",\"QA\":\"Qatar\",\"RE\":\"Réunion\",\"RO\":\"Romania\",\"RU\":\"Russia\",\"RW\":\"Ruanda\",\"BL\":\"Saint Barthélemy\",\"KN\":\"Saint Kitts e Nevis\",\"LC\":\"Saint Lucia\",\"MF\":\"Saint Martin\",\"PM\":\"Saint Pierre e Miquelon\",\"VC\":\"Saint Vincent e Grenadine\",\"WS\":\"Samoa\",\"SM\":\"San Marino\",\"ST\":\"São Tomé e Príncipe\",\"SA\":\"Arabia Saudita\",\"SN\":\"Senegal\",\"RS\":\"Serbia\",\"SC\":\"Seychelles\",\"SL\":\"Sierra Leone\",\"SG\":\"Singapore\",\"SX\":\"Sint Maarten\",\"SK\":\"Slovacchia\",\"SI\":\"Slovenia\",\"SB\":\"Isole Salomone\",\"SO\":\"Somalia\",\"ZA\":\"Sud Africa\",\"GS\":\"Georgia del Sud e Isole Sandwich Australi\",\"SS\":\"Sudan del Sud\",\"ES\":\"Spagna\",\"LK\":\"Sri Lanka\",\"SH\":\"Sant'Elena, Ascensione e Tristan da Cunha\",\"SD\":\"Sudan\",\"SR\":\"Suriname\",\"SJ\":\"Svalbard\",\"SZ\":\"Swaziland\",\"SE\":\"Svezia\",\"CH\":\"Svizzera\",\"SY\":\"Siria\",\"TW\":\"Taiwan\",\"TJ\":\"Tagikistan\",\"TZ\":\"Tanzania\",\"TH\":\"Thailandia\",\"TL\":\"Timor Leste\",\"TG\":\"Togo\",\"TK\":\"Tokelau\",\"TO\":\"Tonga\",\"TT\":\"Trinidad e Tobago\",\"TN\":\"Tunisia\",\"TR\":\"Turchia\",\"TM\":\"Turkmenistan\",\"TC\":\"Turks e Caicos\",\"TV\":\"Tuvalu\",\"UM\":\"U.S. Isole Minori Esterne degli Stati Uniti\",\"VI\":\"U.S. Isole Vergini\",\"UG\":\"Uganda\",\"UA\":\"Ucraina\",\"AE\":\"Emirati Arabi Uniti\",\"GB\":\"Regno Unito\",\"US\":\"Stati Uniti\",\"UY\":\"Uruguay\",\"UZ\":\"Uzbekistan\",\"VU\":\"Vanuatu\",\"VA\":\"Città del Vaticano\",\"VE\":\"Venezuela\",\"VN\":\"Vietnam\",\"WF\":\"Wallis e Futuna\",\"YE\":\"Yemen\",\"ZM\":\"Zambia\",\"ZW\":\"Zimbabwe\"} |
| **error_448** | Il numero di telefono specificato non è raggiungibile. |
| **error_449** | L'utente ha superato il numero di tentativi consentiti. |
| **verification_code_input_placeholder_text** | Codice di verifica |

L'esempio seguente illustra l'uso di alcuni elementi dell'interfaccia utente nella pagina di registrazione MFA:

![Elementi dell'esperienza utente per la verifica della posta elettronica della pagina di iscrizione](./media/localization-string-ids/localization-mfa1.png)

L'esempio seguente illustra l'uso di alcuni elementi dell'interfaccia utente nella pagina di convalida MFA:

![Elementi dell'esperienza utente per la verifica della posta elettronica della pagina di iscrizione](./media/localization-string-ids/localization-mfa2.png)







