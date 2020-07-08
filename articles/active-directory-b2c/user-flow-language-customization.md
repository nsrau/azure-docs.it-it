---
title: Personalizzazione della lingua in Azure AD B2C
description: Informazioni sulla personalizzazione dell'esperienza per la lingua nei flussi utente.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dd6e22052d2fab6d39382b21eec7a7bd379adb82
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85384074"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personalizzazione della lingua in Azure AD B2C

La personalizzazione della lingua in Azure Active Directory B2C (Azure AD B2C) consente al flusso utente di modificare la lingua in base alle esigenze dei clienti. Microsoft fornisce le traduzioni per [36 lingue](#supported-languages), ma è anche possibile inserire le proprie traduzioni per qualsiasi lingua. Anche se l'esperienza è disponibile per una sola lingua, è possibile personalizzare qualsiasi testo nelle pagine.

## <a name="how-language-customization-works"></a>Funzionamento della personalizzazione della lingua

La personalizzazione della lingua consente di selezionare le lingue in cui è disponibile il flusso utente. Dopo aver abilitato la funzionalità, è possibile specificare il parametro della stringa di query, `ui_locales`, dall'applicazione. Quando si esegue la chiamata ad Azure AD B2C, la pagina viene tradotta nelle impostazioni locali indicate. Questo tipo di configurazione garantisce il controllo completo sulle lingue del flusso utente e ignora le impostazioni della lingua del browser del cliente.

Potrebbe non essere necessario un tale livello di controllo sulle lingue visualizzate dal cliente. Se non si specifica un parametro `ui_locales`, l'esperienza del cliente è determinata dalle impostazioni del browser. È comunque possibile controllare le lingue in cui il flusso utente è tradotto aggiungendole come lingue supportate. Se il browser del cliente è impostato su una lingua che non si vuole supportare, viene visualizzata la lingua selezionata come predefinita nelle impostazioni cultura supportate.

* **UI-impostazioni locali lingua specificata**: dopo aver abilitato la personalizzazione della lingua, il flusso utente viene convertito nella lingua specificata qui.
* **Lingua richiesta dal browser**: se non `ui_locales` è stato specificato alcun parametro, il flusso utente viene convertito nella lingua richiesta dal browser, *se la lingua è supportata*.
* **Lingua predefinita dei criteri**: se il browser non specifica una lingua o ne specifica una non supportata, il flusso utente viene convertito nella lingua predefinita del flusso utente.

> [!NOTE]
> Se si fa uso di attributi utente personalizzati, è necessario fornire le traduzioni. Per altre informazioni, vedere [Personalizzazione delle stringhe](#customize-your-strings).

## <a name="support-requested-languages-for-ui_locales"></a>Supporto delle lingue richieste per ui_locales

Per i criteri creati prima della disponibilità generale della personalizzazione della lingua occorre prima abilitare questa funzionalità. Per i criteri e i flussi utente creati successivamente la personalizzazione della lingua è abilitata per impostazione predefinita.

Quando si abilita la personalizzazione della lingua in un flusso utente, è possibile controllare la lingua del flusso utente aggiungendo il parametro `ui_locales`.

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Fare clic sul flusso utente da abilitare per le traduzioni.
1. Selezionare **Lingue**.
1. Selezionare **Abilita personalizzazione della lingua**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Selezionare le lingue abilitate per il flusso utente

Abilitare un set di lingue in cui tradurre il flusso utente quando richiesto dal browser senza il parametro `ui_locales`.

1. Assicurarsi che la personalizzazione della lingua sia abilitata nel flusso utente in base alle istruzioni precedenti.
1. Nella pagina **Lingua** del flusso utente selezionare una lingua che si vuole supportare.
1. Nel riquadro delle proprietà impostare **Attivato** su **Sì**.
1. Scegliere **Salva** nella parte superiore del riquadro delle proprietà.

>[!NOTE]
>Se non viene specificato un parametro `ui_locales`, la pagina viene tradotta nella lingua del browser del cliente solo se è abilitata.
>

## <a name="customize-your-strings"></a>Personalizzazione delle stringhe

La personalizzazione della lingua consente di personalizzare qualsiasi stringa nel flusso utente.

1. Assicurarsi che la personalizzazione della lingua sia abilitata nel flusso utente in base alle istruzioni precedenti.
1. Nella pagina **Lingua** del flusso utente selezionare la lingua che si vuole personalizzare.
1. In **File di risorse a livello di pagina** selezionare la pagina che si vuole modificare.
1. Selezionare **Scarica impostazioni predefinite** (o **Scarica override** se in precedenza si è già modificata questa lingua).

Questa procedura permette di ottenere un file JSON con cui iniziare a modificare le stringhe.

### <a name="change-any-string-on-the-page"></a>Modificare stringhe nella pagina

1. In un editor JSON aprire il file JSON scaricato seguendo le istruzioni precedenti.
1. Trovare l'elemento da modificare. È possibile trovare il valore `StringId` della stringa che si sta cercando o cercare l'attributo `Value` che si vuole modificare.
1. Aggiornare l'attributo `Value` con i dati da visualizzare.
1. Per ogni stringa che si vuole modificare impostare `Override` su `true`.
1. Salvare il file e caricare le modifiche. Il controllo di caricamento si trova nella stessa posizione in cui è stato scaricato il file JSON.

> [!IMPORTANT]
> Se è necessario eseguire l'override di una stringa, assicurarsi di impostare il valore `Override` su `true`. Se il valore non viene modificato, la voce viene ignorata.

### <a name="change-extension-attributes"></a>Cambiare gli attributi di estensione

Per modificare la stringa di un attributo utente personalizzato o aggiungerne uno al file JSON, usare il formato seguente:

```json
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

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Fornire un elenco di valori usando LocalizedCollections

Se si vuole specificare un elenco preimpostato di valori per le risposte, è necessario creare un attributo `LocalizedCollections`. `LocalizedCollections` è una matrice di coppie `Name`-`Value`. L'ordine degli elementi sarà l'ordine in che cui vengono visualizzati. Per aggiungere `LocalizedCollections`, usare il formato seguente:

```json
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

* `ElementId` è l'attributo utente per il quale l'attributo `LocalizedCollections` rappresenta una risposta.
* `Name` è il valore che viene visualizzato all'utente.
* `Value` è il valore restituito nell'attestazione quando questa opzione è selezionata.

### <a name="upload-your-changes"></a>Caricamento delle modifiche

1. Dopo aver completato le modifiche al file JSON, tornare al tenant B2C.
1. Selezionare **Flussi utente** e fare clic sul flusso utente che si vuole abilitare per le traduzioni.
1. Selezionare **Lingue**.
1. Selezionare la lingua in cui si vuole tradurre.
1. Selezionare la pagina per cui si vogliono fornire traduzioni.
1. Selezionare l'icona della cartella e selezionare il file JSON da caricare.

Le modifiche vengono salvate automaticamente nel flusso utente.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personalizzare l'interfaccia utente della pagina usando la personalizzazione della lingua

Esistono due modi per localizzare il contenuto HTML. Un modo consiste nell'attivare la [personalizzazione della lingua](user-flow-language-customization.md). L'abilitazione di questa funzionalità consente Azure AD B2C di inviare il parametro OpenID Connect, `ui-locales` , all'endpoint. Il server di contenuti può usare questo parametro per fornire pagine HTML personalizzate specifiche della lingua.

In alternativa, è possibile eseguire il pull del contenuto da posizioni diverse in base alle impostazioni locali in uso. Nell'endpoint abilitato per CORS è possibile configurare una struttura di cartelle in cui ospitare il contenuto per specifiche lingue. Se si inserisce il valore del carattere jolly `{Culture:RFC5646}` verrà chiamata la lingua corretta. Ad esempio, supponiamo che questo sia l'URI della pagina personalizzata:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

È possibile caricare la pagina in `fr`. Quando la pagina esegue il pull del contenuto HTML e CSS, lo fa da:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Aggiungere lingue personalizzate

È anche possibile aggiungere per cui attualmente Microsoft non fornisce traduzioni. Sarà necessario fornire le traduzioni per tutte le stringhe contenute nel flusso utente. I codici della lingua e delle impostazioni internazionali sono limitati a quelli nello standard ISO 639-1.

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
2. Fare clic sul flusso utente in cui si vuole aggiungere le lingue personalizzate e quindi fare clic su **Lingue**.
3. Selezionare **Aggiungi lingua personalizzata** nella parte superiore della pagina.
4. Nel riquadro del contesto che viene visualizzato identificare la lingua per cui si stanno inserendo le traduzioni immettendo un codice delle impostazioni locali valido.
5. Per ogni pagina è possibile scaricare un set di sostituzioni per la lingua inglese e lavorare sulle traduzioni.
6. Una volta terminato con i file JSON, è possibile caricarli per ogni pagina.
7. Selezionare **Abilita**. Il flusso utente può ora visualizzare la lingua per gli utenti.
8. Salvare la lingua.

>[!IMPORTANT]
>È necessario abilitare le lingue personalizzate o caricare gli override per la lingua prima di poterla salvare.

## <a name="additional-information"></a>Informazioni aggiuntive

### <a name="page-ui-customization-labels-as-overrides"></a>Etichette di personalizzazione dell'interfaccia utente della pagina come sostituzioni

Quando si abilita la personalizzazione della lingua, le modifiche precedenti apportate alle etichette usando la personalizzazione dell'interfaccia utente della pagina vengono rese persistenti in un file JSON per la lingua Inglese (en). È possibile continuare a modificare le etichette e le altre stringhe caricando le risorse di lingua nella funzionalità di personalizzazione della lingua.

### <a name="up-to-date-translations"></a>Traduzioni aggiornate

Microsoft si impegna a fornire le traduzioni più aggiornate. Continuerà a migliorare le traduzioni e a garantirne la conformità. Verranno identificati i bug e le modifiche alla terminologia globale e verranno eseguiti aggiornamenti compatibili con il flusso utente.

### <a name="support-for-right-to-left-languages"></a>Supporto per lingue da destra a sinistra

Microsoft non fornisce attualmente supporto per le lingue da destra a sinistra. A questo scopo, è possibile usare impostazioni locali personalizzate e modificare la modalità di visualizzazione delle stringhe tramite CSS. Se si ha bisogno di questa funzionalità, votarla in [Commenti e suggerimenti su Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Traduzioni per provider di identità basati su social network

Microsoft fornisce il parametro OIDC `ui_locales` per gli account di accesso ai social network, che però non viene riconosciuto da alcuni provider di identità basati su social network, tra cui Facebook e Google.

### <a name="browser-behavior"></a>Comportamento dei browser

Chrome e Firefox richiedono entrambi la relativa lingua preimpostata. Se è supportata, tale lingua viene visualizzata prima di quella predefinita. Microsoft Edge attualmente non richiede alcuna lingua e passa direttamente alla lingua predefinita.

## <a name="supported-languages"></a>Lingue supportate

Azure AD B2C include il supporto per le lingue seguenti. Le lingue dei flussi utente sono fornite da Azure AD B2C. Le lingue di notifica dell'autenticazione a più fattori (MFA, Multi-Factor Authentication) sono fornite da [Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Linguaggio              | Codice lingua | Flussi degli utenti         | Notifiche MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabo                | ar            | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Bulgaro             | bg            | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Bengalese                | bn            | ![sì](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| Catalano               | ca            | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Ceco                 | cs            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Danese                | da            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Tedesco                | de            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Greco                 | el            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Inglese               | en            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Spagnolo               | es            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Estone              | et            | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Basco                | eu            | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Finlandese               | fi            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Francese                | fr            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Galiziano              | gl            | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Gujarati              | gu            | ![sì](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| Ebraico                | he            | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Hindi                 | hi            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Croato              | hr            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Ungherese             | hu            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Indonesiano            | id            | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Italiano               | it            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Giapponese              | ja            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Kazako                | kk            | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Kannada               | kn            | ![sì](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| Coreano                | ko            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Lituano            | lt            | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Lettone               | lv            | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Malayalam             | ml            | ![sì](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| Marathi               | mr            | ![sì](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| Malese                 | ms            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Norvegese Bokmål      | nb            | ![sì](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| Olandese                 | nl            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Norvegese             | no            | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Punjabi               | pa            | ![sì](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| Polacco                | pl            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Portoghese (Brasile)   | pt-br         | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Portoghese (Portogallo) | pt-pt         | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Rumeno              | ro            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Russo               | ru            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Slovacco                | sk            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Sloveno             | sl            | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Serbo - Alfabeto cirillico    | sr-cryl-cs    | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Serbo - Alfabeto latino       | sr-latn-cs    | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Svedese               | sv            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Tamil                 | ta            | ![sì](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| Telugu                | te            | ![sì](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| Thai                  | th            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Turco               | tr            | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Ucraino             | uk            | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Vietnamita            | vi            | ![no](./media/user-flow-language-customization/no.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Cinese (semplificato)  | zh-hans       | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
| Cinese (tradizionale) | zh-hant       | ![sì](./media/user-flow-language-customization/yes.png) | ![sì](./media/user-flow-language-customization/yes.png) |
