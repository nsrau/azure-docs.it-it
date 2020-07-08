---
title: Personalizzazione della lingua nei flussi utente di Azure AD
description: Informazioni sulla personalizzazione dell'esperienza per la lingua nei flussi utente.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/06/2020
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd6423ebbdba11cd1b0e0c2d00cfd36aa745e72
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85551149"
---
# <a name="language-customization-in-azure-active-directory-preview"></a>Personalizzazione della lingua in Azure Active Directory (anteprima)

> [!NOTE]
> L'iscrizione self-service è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La personalizzazione della lingua in Azure Active Directory (Azure AD) consente al flusso utente di supportare lingue diverse in base alle esigenze dell'utente. Microsoft offre le traduzioni in [36 lingue](#supported-languages). Anche se l'esperienza è disponibile per una sola lingua, è possibile personalizzare i nomi degli attributi nella pagina di raccolta degli attributi.

## <a name="how-language-customization-works"></a>Funzionamento della personalizzazione della lingua

Per impostazione predefinita, la personalizzazione della lingua è abilitata per gli utenti che si iscrivono, per assicurare un'esperienza di iscrizione coerente. È possibile usare le lingue per modificare le stringhe visualizzate per gli utenti nell'ambito del processo di raccolta degli attributi durante l'iscrizione.

> [!NOTE]
> Se si fa uso di attributi utente personalizzati, è necessario fornire le traduzioni. Per altre informazioni, vedere [Personalizzazione delle stringhe](#customize-your-strings).

## <a name="customize-your-strings"></a>Personalizzazione delle stringhe

La personalizzazione della lingua consente di personalizzare qualsiasi stringa nel flusso utente.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD.
2. In **Servizi di Azure** selezionare **Azure Active Directory**.
3. Nel menu a sinistra selezionare **Identità esterne**.
4. Selezionare **Flussi utente (anteprima)** .
3. Selezionare il flusso utente da abilitare per le traduzioni.
4. Selezionare **Lingue**.
5. Nella pagina **Lingua** del flusso utente selezionare la lingua che si vuole personalizzare.
6. Espandere la **pagina di raccolta degli attributi**.
7. Selezionare **Scarica impostazioni predefinite** (o **Scarica override** se in precedenza si è già modificata questa lingua).

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

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Fornire un elenco di valori usando LocalizedCollections

Se si vuole specificare un elenco preimpostato di valori per le risposte, è necessario creare un attributo `LocalizedCollections`. `LocalizedCollections` è una matrice di coppie `Name`-`Value`. L'ordine degli elementi sarà l'ordine in che cui vengono visualizzati. Per aggiungere `LocalizedCollections`, usare il formato seguente:

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

* `ElementId` è l'attributo utente per il quale l'attributo `LocalizedCollections` rappresenta una risposta.
* `Name` è il valore che viene visualizzato all'utente.
* `Value` è il valore restituito nell'attestazione quando questa opzione è selezionata.

### <a name="upload-your-changes"></a>Caricamento delle modifiche

1. Dopo aver completato le modifiche al file JSON, tornare al tenant.
1. Selezionare **Flussi utente** e fare clic sul flusso utente che si vuole abilitare per le traduzioni.
1. Selezionare **Lingue**.
1. Selezionare la lingua in cui si vuole tradurre.
1. Selezionare la **pagina di raccolta degli attributi**.
1. Selezionare l'icona della cartella e selezionare il file JSON da caricare.

Le modifiche vengono salvate automaticamente nel flusso utente.

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

Azure AD include il supporto per le lingue seguenti. Le lingue dei flussi utente sono fornite da Azure AD. Le lingue di notifica dell'autenticazione a più fattori (MFA, Multi-Factor Authentication) sono fornite da [Azure MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

| Linguaggio              | Codice lingua | Flussi degli utenti         | Notifiche MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabo                | ar            | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Bulgaro             | bg            | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Bengalese                | bn            | ![sì](./media/user-flow-customize-language/yes.png) | ![no](./media/user-flow-customize-language/no.png) |
| Catalano               | ca            | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Ceco                 | cs            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Danese                | da            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Tedesco                | de            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Greco                 | el            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Inglese               | en            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Spagnolo               | es            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Estone              | et            | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Basco                | eu            | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Finlandese               | fi            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Francese                | fr            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Galiziano              | gl            | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Gujarati              | gu            | ![sì](./media/user-flow-customize-language/yes.png) | ![no](./media/user-flow-customize-language/no.png) |
| Ebraico                | he            | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Hindi                 | hi            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Croato              | hr            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Ungherese             | hu            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Indonesiano            | id            | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Italiano               | it            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Giapponese              | ja            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Kazako                | kk            | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Kannada               | kn            | ![sì](./media/user-flow-customize-language/yes.png) | ![no](./media/user-flow-customize-language/no.png) |
| Coreano                | ko            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Lituano            | lt            | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Lettone               | lv            | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Malayalam             | ml            | ![sì](./media/user-flow-customize-language/yes.png) | ![no](./media/user-flow-customize-language/no.png) |
| Marathi               | mr            | ![sì](./media/user-flow-customize-language/yes.png) | ![no](./media/user-flow-customize-language/no.png) |
| Malese                 | ms            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Norvegese Bokmål      | nb            | ![sì](./media/user-flow-customize-language/yes.png) | ![no](./media/user-flow-customize-language/no.png) |
| Olandese                 | nl            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Norvegese             | no            | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Punjabi               | pa            | ![sì](./media/user-flow-customize-language/yes.png) | ![no](./media/user-flow-customize-language/no.png) |
| Polacco                | pl            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Portoghese (Brasile)   | pt-br         | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Portoghese (Portogallo) | pt-pt         | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Rumeno              | ro            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Russo               | ru            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Slovacco                | sk            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Sloveno             | sl            | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Serbo - Alfabeto cirillico    | sr-cryl-cs    | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Serbo - Alfabeto latino       | sr-latn-cs    | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Svedese               | sv            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Tamil                 | ta            | ![sì](./media/user-flow-customize-language/yes.png) | ![no](./media/user-flow-customize-language/no.png) |
| Telugu                | te            | ![sì](./media/user-flow-customize-language/yes.png) | ![no](./media/user-flow-customize-language/no.png) |
| Thai                  | th            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Turco               | tr            | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Ucraino             | uk            | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Vietnamita            | vi            | ![no](./media/user-flow-customize-language/no.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Cinese (semplificato)  | zh-hans       | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |
| Cinese (tradizionale) | zh-hant       | ![sì](./media/user-flow-customize-language/yes.png) | ![sì](./media/user-flow-customize-language/yes.png) |