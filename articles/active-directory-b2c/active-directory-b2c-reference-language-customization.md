---
title: Uso della personalizzazione della lingua - Azure AD B2C | Microsoft Docs
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 02/26/2018
ms.author: sama
ms.openlocfilehash: 332c6b4ffd841a2c7aef9db5c8ba9e843790f4d6
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C: uso della personalizzazione della lingua

>[!NOTE]
>Questa funzionalità è disponibile in anteprima pubblica.
>

La personalizzazione della lingua consente ai criteri di modificare la lingua del percorso utente in base alle esigenze dei clienti.  Microsoft fornisce le traduzioni per le 36 lingue (vedere [Informazioni aggiuntive](#additional-information)), ma è anche possibile inserire le proprie traduzioni per qualsiasi lingua.  Anche se l'esperienza è disponibile per una sola lingua, è possibile personalizzare il testo nelle pagine.  

## <a name="how-does-language-customization-work"></a>Funzionamento della personalizzazione della lingua
La personalizzazione della lingua consente di selezionare le lingue in cui è disponibile il percorso utente.  Dopo aver abilitato la funzionalità, è possibile specificare il parametro della stringa di query, ui_locales, dall'applicazione.  Quando si esegue la chiamata ad Azure AD B2C, la pagina viene convertita nelle impostazioni locali indicate.  Questo tipo di configurazione garantisce il controllo completo sulle lingue del percorso utente e ignora le impostazioni della lingua del browser del cliente. Potrebbe non essere necessario un tale livello di controllo sulle lingue visualizzate dal cliente.  Se non si specifica un parametro ui_locales, l'esperienza del cliente è determinata dalle impostazioni del browser.  È comunque possibile controllare le lingue in cui il percorso utente è tradotto aggiungendole come lingue supportate.  Se il browser del cliente è impostato su una lingua che non si vuole supportare, viene visualizzata la lingua selezionata come impostazione predefinita nelle impostazioni cultura supportate.

1. **Lingua specificata tramite ui-locales**: dopo aver abilitato la personalizzazione della lingua, il percorso utente viene tradotto nella lingua qui specificata.
2. **Lingua richiesta dal browser**: se il parametro ui-locales non è stato specificato, la pagina viene tradotta nella lingua richiesta dal browser, **se inclusa nelle lingue supportate**.
3. **Lingua predefinita nei criteri**: se il browser non specifica alcuna lingua oppure è stata specificata una lingua non supportata, la pagina viene tradotta nella lingua predefinita nei criteri

>[!NOTE]
>Se si fa uso di attributi utente personalizzati, è necessario fornire le traduzioni. Per informazioni dettagliate, vedere la sezione [Personalizzazione delle stringhe](#customize-your-strings).
>

## <a name="support-uilocales-requested-languages"></a>Supporto di lingue richieste dal parametro ui_locales 
Per i criteri creati prima della versione di disponibilità generale della personalizzazione della lingua sarà necessario abilitare questa funzionalità.  I criteri creati successivamente avranno la personalizzazione della lingua abilitata per impostazione predefinita.  Se si abilita la personalizzazione della lingua nei criteri, è ora possibile controllare la lingua del percorso utente aggiungendo il parametro ui_locales.
1. [Seguire questa procedura per passare alla pagina delle funzionalità B2C nel portale di Azure.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. Passare al criterio da abilitare per le traduzioni.
3. Fare clic su **Personalizzazione della lingua**.  
4. Fare clic su **Abilita personalizzazione della lingua** in alto.
5. Leggere la finestra di dialogo e fare clic su "Sì".

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>Selezionare le lingue abilitate per il percorso utente 
Abilitare un elenco delle lingue consentite in cui tradurre il percorso utente quando non viene specificato il parametro ui_locales.
1. Assicurarsi che la personalizzazione della lingua sia abilitata nei criteri in base alle istruzioni precedenti.
2. Nella pagina **Modifica criterio** selezionare **Personalizzazione della lingua**.
3. Selezionare una lingua da supportare.
4. Nel riquadro delle proprietà impostare **Attivato** su Sì.  
5. Fare clic su **Salva** nella parte superiore del riquadro delle proprietà.

>[!NOTE]
>Se non viene specificato un parametro ui_locales, la pagina viene tradotta nella lingua del browser del cliente solo se è abilitata
>

## <a name="customize-your-strings"></a>Personalizzazione delle stringhe
La personalizzazione della lingua consente di personalizzare qualsiasi stringa nel percorso utente.
1. Assicurarsi che la personalizzazione della lingua sia abilitata nei criteri in base alle istruzioni precedenti.
2. Nella pagina **Modifica criterio** selezionare **Personalizzazione della lingua**.
3. Selezionare la lingua da personalizzare.
4. Selezionare la pagina da modificare.
5. Fare clic su **Scarica impostazioni predefinite** (o **Scarica override** se in precedenza si è già modificata questa lingua). 

Questa procedura permette di ottenere un file JSON con cui iniziare a modificare le stringhe.

### <a name="changing-any-string-on-the-page"></a>Modifica di stringhe nella pagina
1. In un editor JSON aprire il file JSON scaricato seguendo le istruzioni precedenti.
2. Trovare l'elemento da modificare.  È possibile cercare l'oggetto `StringId` della stringa da trovare o l'attributo `Value` da modificare.
3. Aggiornare l'attributo `Value` con i dati da visualizzare.
4. Per ogni stringa che si vuole modificare, ricordare di attivare o impostare `Override` su **Vero**.
5. Salvare il file e caricare le modifiche. Il controllo di caricamento si trova nella stessa posizione in cui è stato scaricato il file JSON. 

>[!IMPORTANT]
>Se è necessario eseguire l'override di una stringa, assicurarsi di impostare il valore `Override` su `true`.  Se il valore non viene modificato, la voce viene ignorata. 
>

### <a name="changing-extension-attributes"></a>Modifica degli attributi di estensione
Per modificare la stringa di un attributo utente personalizzato o aggiungerne uno al file JSON, usare il formato seguente:
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Sostituire `<ExtensionAttribute>` con il nome dell'attributo utente personalizzato.  

Sostituire `<ExtensionAttributeValue>` con la nuova stringa da visualizzare.

### <a name="using-localizedcollections"></a>Uso di LocalizedCollections
Se si vuole specificare un elenco preimpostato di valori per le risposte, è necessario creare un oggetto `LocalizedCollections`.  `LocalizedCollections` è una matrice di coppie `Name`-`Value`.  `Name` è il nome visualizzato, mentre `Value` è il valore restituito nell'attestazione.  Per aggiungere un oggetto `LocalizedCollections`, usare il formato seguente:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` è l'attributo utente per il quale l'oggetto `LocalizedCollections` rappresenta una risposta.
* `Name` il ciò che viene mostrato all'utente.
* `Value` è il valore restituito nell'attestazione quando questa opzione è selezionata.

### <a name="upload-your-changes"></a>Caricamento delle modifiche
1. Dopo aver apportato le modifiche al file JSON, tornare al tenant B2C.
2. Nella pagina **Modifica criterio** selezionare **Personalizzazione della lingua**.
3. Selezionare la lingua in cui si vogliono fornire traduzioni.
4. Selezionare la pagina per cui si vogliono fornire traduzioni.
5. Fare clic sull'icona della cartella e selezionare il file JSON da caricare.
6. La modifica viene salvata automaticamente nel criterio.

## <a name="using-page-ui-customization-with-language-customization"></a>Uso della personalizzazione dell'interfaccia utente della pagina con la personalizzazione della lingua

Esistono due modi per localizzare il contenuto HTML.  Attivando la [Personalizzazione della lingua](active-directory-b2c-reference-language-customization.md).  L'abilitazione di questa funzionalità consente ad Azure AD B2C di inoltrare il parametro Open ID Connect, `ui-locales`, all'endpoint.  Il server di contenuti può usare questo parametro per fornire pagine HTML personalizzate specifiche della lingua.

In alternativa, è possibile eseguire il pull del contenuto da posizioni diverse in base alle impostazioni locali in uso.  Nell'endpoint abilitato per CORS è possibile configurare una struttura di cartelle in cui ospitare il contenuto per specifiche lingue e se si inserisce il valore del carattere jolly `{Culture:RFC5646}` verrà chiamata la lingua corretta.  Ad esempio, se l'URI della pagina personalizzata è il seguente:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
È possibile caricare la pagina in `fr` e il pull di contenuto html e css verrà eseguito da:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="custom-locales"></a>Impostazioni locali personalizzate

È anche possibile aggiungere per cui attualmente Microsoft non fornisce traduzioni.  Sarà necessario fornire le traduzioni per tutte le stringhe contenute nei criteri.

1. Nella pagina **Modifica criterio** selezionare **Personalizzazione della lingua**.
2. Selezionare **Aggiungi lingua personalizzata** nella parte superiore della pagina.
3. Nel riquadro del contesto che viene visualizzato identificare la lingua per cui si stanno inserendo le traduzioni immettendo un codice delle impostazioni locali valido.
4. Per ogni pagina è possibile scaricare un set di sostituzioni per la lingua inglese e lavorare sulle traduzioni.
5. Una volta terminato con i file JSON, è possibile caricarli per ogni pagina.
6. Selezionare **Abilita** e il criterio potrà visualizzare la lingua per l'utente.
7. Ricordare di salvare la lingua dopo averla abilitata.

## <a name="additional-information"></a>Informazioni aggiuntive

### <a name="page-ui-customization-labels-are-persisted-as-your-first-set-of-overrides-once-language-customization-is-enabled"></a>Le etichette di personalizzazione dell'interfaccia utente della pagina vengono rese persistenti come primo set di sostituzioni non appena viene abilitata la personalizzazione della lingua
Quando si abilita la personalizzazione della lingua, le modifiche precedenti apportate alle etichette usando la personalizzazione dell'interfaccia utente della pagina vengono rese persistenti in un file JSON per la lingua Inglese (en).  È possibile continuare a modificare le etichette e le altre stringhe caricando le risorse di lingua nella funzionalità di personalizzazione della lingua.
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft si impegna a fornire le traduzioni più aggiornate
Microsoft continuerà a migliorare le traduzioni e a garantirne la conformità.  Verranno identificati i bug e le modifiche alla terminologia globale e verranno eseguiti aggiornamenti compatibili con il percorso utente.
### <a name="support-for-right-to-left-languages"></a>Supporto per lingue da destra a sinistra
Attualmente non è disponibile il supporto per le lingue da destra a sinistra; se è necessaria questa funzionalità, votarla nel forum di [feedback su Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Traduzioni per provider di identità basati su social network
Vengono fornito il parametro OIDC ui_locales per gli account di accesso ai social network, che però non viene riconosciuto da alcuni provider di identità basati su social network, tra cui Facebook e Google. 
### <a name="browser-behavior"></a>Comportamento dei browser
Chrome e Firefox richiedono entrambi la relativa lingua preimpostata. Se è supportata, tale lingua viene visualizzata prima di quella predefinita.  Microsoft Edge attualmente non richiede alcuna lingua e passa direttamente alla lingua predefinita.

### <a name="what-languages-are-supported"></a>Quali lingue sono supportate?

| Linguaggio              | Codice lingua |
|-----------------------|---------------|
| Bengalese                | bn            |
| Ceco                 | cs            |
| Danese                | da            |
| Tedesco                | de            |
| Greco                 | el            |
| Inglese               | en            |
| Spagnolo               | es            |
| Finlandese               | fi            |
| Francese                | fr            |
| Gujarati              | gu            |
| Hindi                 | hi            |
| Croato              | hr            |
| Ungherese             | hu            |
| Italiano               | it            |
| Giapponese              | ja            |
| Kannada               | kn            |
| Coreano                | ko            |
| Malayalam             | ml            |
| Marathi               | mr            |
| Malese                 | ms            |
| Norvegese bokmål      | nb            |
| Olandese                 | nl            |
| Punjabi               | pa            |
| Polacco                | pl            |
| Portoghese (Brasile)   | pt-br         |
| Portoghese (Portogallo) | pt-pt         |
| Romeno              | ro            |
| Russo               | ru            |
| Slovacco                | sk            |
| Svedese               | sv            |
| Tamil                 | ta            |
| Telugu                | te            |
| Thai                  | th            |
| Turco               | tr            |
| Cinese (semplificato)  | zh-hans       |
| Cinese (tradizionale) | zh-hant       |
