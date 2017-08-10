---
title: 'Azure Active Directory B2C: uso della personalizzazione della lingua | Microsoft Docs'
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
ms.date: 04/25/2017
ms.author: sama
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 3c7c49ee5fbd98762da0eef6f02e7c2f036453cb
ms.contentlocale: it-it
ms.lasthandoff: 08/08/2017

---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C: uso della personalizzazione della lingua

>[!NOTE] 
>Questa funzionalità è disponibile in anteprima pubblica.  È consigliabile usare un tenant di test quando si usa questa funzionalità.  Tra la versione di anteprima e quella di disponibilità generale non sono previste modifiche di rilievo, ma Microsoft si riserva il diritto di apportare modifiche per migliorare la funzionalità.  Microsoft sarà lieta di ricevere commenti e suggerimenti sull'esperienza di utilizzo della funzionalità provata al fine di migliorarla.  Per fornire commenti e suggerimenti è possibile usare lo strumento smile in alto a destra nel portale di Azure.   Specificando a Microsoft eventuali requisiti aziendali e scenari per l'attivazione di questa funzionalità durante la fase di anteprima, sarà possibile ricevere le informazioni e l'assistenza necessarie.  È possibile scrivere a [aadb2cpreview@microsoft.com](mailto:aadb2cpreview@microsoft.com).

La personalizzazione della lingua consente di modificare la lingua del percorso utente in base alle esigenze dei clienti.  Sono disponibili traduzioni in 36 lingue. Vedere a tal proposito la sezione [Informazioni aggiuntive](#additional-information).  Anche se l'esperienza è disponibile per una sola lingua, è possibile personalizzare il testo nelle pagine in base alle esigenze.  

## <a name="how-does-language-customization-work"></a>Funzionamento della personalizzazione della lingua
La personalizzazione della lingua consente di selezionare le lingue in cui è disponibile il percorso utente.  Dopo aver abilitato la funzionalità, è possibile specificare il parametro della stringa di query, ui_locales, dall'applicazione.  Quando si esegue la chiamata ad Azure AD B2C, la pagina viene convertita nelle impostazioni locali indicate.  L'uso del tipo di configurazione garantisce il controllo completo sulle lingue del percorso utente e ignora le impostazioni della lingua del browser del cliente. Potrebbe non essere necessario un tale livello di controllo sulle lingue visualizzate dal cliente.  Se non si specifica un parametro ui_locales, l'esperienza del cliente è determinata dalle impostazioni del browser.  È comunque possibile controllare le lingue in cui il percorso utente è tradotto aggiungendole come lingue supportate.  Se il browser del cliente è impostato su una lingua che non si vuole supportare, viene visualizzata la lingua selezionata come impostazione predefinita nelle impostazioni cultura supportate.

1. **Lingua specificata tramite ui-locales**: dopo aver abilitato la personalizzazione della lingua, il percorso utente viene tradotto nella lingua qui specificata.
2. **Lingua richiesta dal browser**: se il parametro ui-locales non è stato specificato, la pagina viene tradotta nella lingua richiesta dal browser, **se inclusa nelle lingue supportate**.
3. **Lingua predefinita nei criteri**: se il browser non specifica alcuna lingua oppure è stata specificata una lingua non supportata, la pagina viene tradotta nella lingua predefinita nei criteri

>[!NOTE]
>Se si fa uso di attributi utente personalizzati, è necessario fornire le traduzioni. Per informazioni dettagliate, vedere la sezione [Personalizzazione delle stringhe](#customize-your-strings).
>

## <a name="support-uilocales-requested-languages"></a>Supporto di lingue richieste dal parametro ui_locales 
Se si abilita la personalizzazione della lingua nei criteri, è ora possibile controllare la lingua del percorso utente aggiungendo il parametro ui_locales.
1. [Seguire questa procedura per passare al pannello delle funzionalità B2C nel portale di Azure](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings).
2. Passare al criterio da abilitare per le traduzioni.
3. Fare clic su **Personalizzazione della lingua**.
4. Leggere attentamente l'avviso.  Non è possibile disabilitare la personalizzazione della lingua dopo averla abilitata.
5. Abilitare la funzionalità e fare clic su **OK**.
6. Salvare il criterio nell'angolo in alto a sinistra del pannello **Modifica criterio**.

## <a name="select-which-languages-your-user-journey-supports"></a>Selezione delle lingue supportate dal percorso utente 
Creare un elenco delle lingue consentite in cui tradurre il percorso utente quando non viene specificato il parametro ui_locales.
1. Assicurarsi che la personalizzazione della lingua sia abilitata nei criteri in base alle istruzioni precedenti.
2. Nel pannello **Modifica criterio** selezionare **Personalizzazione della lingua**.
3. Verrà visualizzato il pannello **Lingue supportate**.  Da qui è possibile selezionare **Aggiungi lingua**.
4. Selezionare tutte le lingue da supportare.  

>[!NOTE]
>Se non viene specificato un parametro ui_locales, la pagina viene tradotta nella lingua del browser del cliente solo se è inclusa in questo elenco.
>

5. Fare clic su **OK** in basso.
6. Chiudere il pannello **Personalizzazione della lingua** e **salvare** i criteri.

## <a name="customize-your-strings"></a>Personalizzazione delle stringhe
La personalizzazione della lingua consente di personalizzare qualsiasi stringa nel percorso utente.
1. Assicurarsi che la personalizzazione della lingua sia abilitata nei criteri in base alle istruzioni precedenti.
2. Nel pannello **Modifica criterio** selezionare **Personalizzazione della lingua**.
3. Dal menu di spostamento a sinistra selezionare **Scarica contenuto**.
4. Selezionare la pagina da modificare.
5. Nell'elenco a discesa selezionare la lingua per cui apportare le modifiche.
6. Fare clic su **Download**. 

Questa procedura permette di ottenere un file JSON con cui iniziare a modificare le stringhe.

### <a name="changing-any-string-on-the-page"></a>Modifica di stringhe nella pagina
1. In un editor JSON aprire il file JSON scaricato seguendo le istruzioni precedenti.
2. Trovare l'elemento da modificare.  È possibile cercare l'oggetto `StringId` della stringa da trovare o l'attributo `Value` da modificare.
3. Aggiornare l'attributo `Value` con i dati da visualizzare.
4. Salvare il file e caricare le modifiche.

### <a name="changing-extension-attributes"></a>Modifica degli attributi di estensione
Per modificare la stringa di un attributo utente personalizzato o aggiungerne uno al file JSON, usare il formato seguente:
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": false,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```
>[!IMPORTANT]
>Se è necessario eseguire l'override di una stringa, assicurarsi di impostare il valore `Override` su `true`.  Se il valore non viene modificato, la voce viene ignorata. 
>

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
      "Override": false,
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
>[!IMPORTANT]
>Se è necessario eseguire l'override di una stringa, assicurarsi di impostare il valore `Override` su `true`.  Se il valore non viene modificato, la voce viene ignorata. 
>

* `ElementId` è l'attributo utente per il quale l'oggetto `LocalizedCollections` rappresenta una risposta.
* `Name` il ciò che viene mostrato all'utente.
* `Value` è il valore restituito nell'attestazione quando questa opzione è selezionata.

### <a name="upload-your-changes"></a>Caricamento delle modifiche
1. Dopo aver apportato le modifiche al file JSON, tornare al tenant B2C.
2. Nel pannello **Modifica criterio** selezionare **Personalizzazione della lingua**.
3. Dal menu di spostamento a sinistra selezionare **Carica contenuto**.
4. Selezionare la pagina per cui caricare le modifiche apportate.
5. Se si vuole caricare una lingua per la quale in precedenza è stato specificato un file JSON, è necessario eliminare il file precedente.  Per eliminarlo è possibile fare clic su `...` a destra della lingua e selezionare **Elimina**.
6. Fare clic su **Aggiungi** in alto a sinistra.
7. Selezionare la lingua del file JSON.
8. Fare clic sul pulsante della cartella sulla destra e selezionare il file JSON.
9. Fare clic sul pulsante **Carica** nella parte inferiore del pannello.
10. Tornare al pannello **Modifica criterio** e fare clic su **Salva**.

## <a name="additional-information"></a>Informazioni aggiuntive
### <a name="recommendations-for-language-customization"></a>Raccomandazioni per la personalizzazione della lingua
È consigliabile aggiungere voci alle risorse di lingua solo per le stringhe da sostituire in modo esplicito.  È previsto un limite di dimensioni per il file compilato con tutte le traduzioni JSON.  Un file di dimensioni eccessive influisce negativamente sulle prestazioni del percorso utente.
### <a name="page-ui-customization-labels-are-removed-once-language-customization-is-enabled"></a>Dopo aver abilitato la personalizzazione della lingua le etichette di personalizzazione dell'interfaccia utente della pagina vengono rimosse
Quando si abilita la personalizzazione della lingua, le modifiche precedenti apportate alle etichette usando la personalizzazione dell'interfaccia utente della pagina vengono rimosse, ad eccezione degli attributi utente personalizzati.  Questo avviene allo scopo di evitare conflitti dovuti alla possibilità modificare le stringhe.  È possibile continuare a modificare le etichette e le altre stringhe caricando le risorse di lingua nella funzionalità di personalizzazione della lingua.
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft si impegna a fornire le traduzioni più aggiornate
Microsoft continuerà a migliorare le traduzioni e a garantirne la conformità.  Verranno identificati i bug e le modifiche alla terminologia globale e verranno eseguiti aggiornamenti compatibili con il percorso utente.
### <a name="support-for-right-to-left-languages"></a>Supporto per lingue da destra a sinistra
Non è disponibile il supporto per le lingue da destra a sinistra; se è necessaria questa funzionalità, richiederla in [Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Traduzioni per provider di identità basati su social network
Vengono fornito il parametro OIDC ui_locales per gli account di accesso ai social network, che però non viene riconosciuto da alcuni provider di identità basati su social network, tra cui Facebook e Google. 
### <a name="browser-behavior"></a>Comportamento dei browser
Chrome e Firefox richiedono entrambi la relativa lingua preimpostata. Se è supportata, tale lingua viene visualizzata prima di quella predefinita.  Microsoft Edge attualmente non richiede alcuna lingua e passa direttamente alla lingua predefinita.
### <a name="known-issues"></a>Problemi noti
* Il caricamento delle risorse di lingua per la pagina MFA nei criteri di modifica del profilo non è attualmente disponibile.
* L'oggetto `LocalizedCollections` non viene generato per i valori quando è richiesto dal tipo di risposta.
### <a name="what-if-i-want-a-language-that-isnt-supported"></a>Per le lingue non supportate,
è prevista un'estensione di questa funzionalità che consente di caricare una risorsa JSON nelle lingue personalizzate.  La funzionalità consente di specificare il nome e il codice della lingua per qualsiasi lingua e di fornire *tutte* le traduzioni per tale lingua.  Per usufruire di questa funzionalità, inviare lo scenario a [aadb2cpreview@microsoft.com](mailto:aadb2cpreview@microsoft.com).  

### <a name="what-languages-are-supported"></a>Quali lingue sono supportate?

| Lingua              | Codice lingua |
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

