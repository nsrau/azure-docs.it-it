---
title: Report di Collaborative Translation Framework (CTF) - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Come usare i report di Collaborative Translation Framework (CTF).
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-pawal
ms.openlocfilehash: 166c152828a91889d7d1d7eb6f8c03dac48172f5
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389383"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Come usare i report di Collaborative Translation Framework (CTF)

> [!NOTE]
> Questo metodo è deprecato. Non è disponibile nella versione 3.0 dell'API Traduzione testuale.
> 
> Collaborative Translations Framework (CTF), precedentemente disponibile per la versione 2.0 dell'API Traduzione testuale, è stato deprecato a partire dal 1 febbraio 2018. Le funzioni AddTranslation e AddTranslationArray consentono agli utenti di attivare le correzioni tramite Collaborative Translation Framework. Dal 31 gennaio 2018, queste due funzioni non accettano più l'invio di nuove frasi e gli utenti ricevono un messaggio di errore. Queste funzioni sono state ritirate e non verranno sostituite.

L'API di creazione report di Collaborative Translation Framework (CTF) restituisce statistiche e il contenuto effettivo dell'archivio CTF. Questa API è diversa dal metodo GetTranslations() in quanto:
* Restituisce il contenuto tradotto e il conteggio totale solo per lo specifico account (appId o account di Azure Marketplace).
* Restituisce il contenuto tradotto e il conteggio totale senza bisogno di una corrispondenza della frase di origine.
* Non restituisce la traduzione automatica.

## <a name="endpoint"></a>Endpoint
L'endpoint dell'API di creazione report di CTF è https://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Metodi
| NOME |    Descrizione|
|:---|:---|
| Metodo GetUserTranslationCounts | Ottiene i conteggi delle traduzioni create dall'utente. |
| Metodo GetUserTranslations | Recupera le traduzioni create dall'utente. |

Questi metodi consentono di:
* Recuperare il set completo di traduzioni utente e correzioni associate all'ID del proprio account e scaricarle.
* Ottenere l'elenco dei collaboratori frequenti. Assicurarsi che in AddTranslation() sia fornito il nome utente corretto.
* Compilare un'interfaccia utente che consente agli utenti attendibili di vedere tutti i candidati disponibili, se necessario limitatamente a una sola parte del sito, in base al prefisso URI.

> [!NOTE]
> Entrambi i metodi sono relativamente lenti e dispendiosi. È consigliabile usarli solo in casi limitati.

## <a name="getusertranslationcounts-method"></a>Metodo GetUserTranslationCounts

Questo metodo recupera il conteggio delle traduzioni create dall'utente. Fornisce l'elenco dei conteggi delle traduzioni raggruppato in base ai parametri della richiesta uriPrefix, from, to, user, minRating e maxRating.

**Sintassi**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslationCount[]GetUserTranslationCounts(
>            string appId,
>            string uriPrefix,
>            string from,
>            string to,
>            int? minRating,
>            int? maxRating,
>            string user,
>            string category
>            DateTime? minDateUtc,
>            DateTime? maxDateUtc,
>            int? skip,
>            int? take);
> ```

**Parameters**

| Parametro | Descrizione |
|:---|:---|
| appId | **Obbligatorio** Se si usa l'intestazione dell'autorizzazione, lasciare vuoto il campo appid. In caso contrario specificare una stringa contenente il token di accesso "Bearer" + " " +.|
| uriPrefix | **Facoltativo** Stringa contenente il prefisso dell'URI della traduzione.|
| from | **Facoltativo** Stringa che rappresenta il codice della lingua del testo della traduzione. |
| to | **Facoltativo** Stringa che rappresenta il codice della lingua in cui tradurre il testo.|
| minRating| **Facoltativo** Valore intero che rappresenta la classificazione di qualità minima per il testo tradotto. Il valore valido è compreso tra -10 e 10. Il valore predefinito è 1.|
| maxRating| **Facoltativo** Valore intero che rappresenta la classificazione di qualità massima per il testo tradotto. Il valore valido è compreso tra -10 e 10. Il valore predefinito è 1.|
| user | **Facoltativo** Stringa usata per filtrare i risultati in base all'iniziatore dell'invio. |
| category| **Facoltativo** Stringa che contiene la categoria o il dominio della traduzione. Questo parametro supporta solo l'opzione predefinita general.|
| minDateUtc| **Facoltativo** Data da cui si vogliono recuperare le traduzioni. La data deve essere nel formato UTC. |
| maxDateUtc| **Facoltativo** Data fino a cui si vogliono recuperare le traduzioni. La data deve essere nel formato UTC. |
| skip| **Facoltativo** Numero di risultati da ignorare in una pagina. Ad esempio, se si vogliono ignorare le prime 20 righe di risultati e visualizzare a partire dal record dei risultati 21, specificare 20 per questo parametro. Il valore predefinito per questo parametro è 0.|
| take | **Facoltativo** Numero di risultati da recuperare. Il numero massimo di ogni richiesta è 100. Il valore predefinito è 100.|

> [!NOTE]
> I parametri della richiesta skip e take consentono la paginazione per un numero elevato di record dei risultati.

**Valore restituito**

Il set di risultati contiene una matrice di **UserTranslationCount**. Ogni UserTranslationCount presenta gli elementi seguenti:

| Campo | Descrizione |
|:---|:---|
| Conteggio| Numero di risultati recuperati|
| Da | Lingua di origine|
| Rating| Classificazione applicata dall'autore dell'invio nella chiamata al metodo AddTranslation()|
| A| Lingua di destinazione|
| Uri| URI applicato nella chiamata al metodo AddTranslation()|
| Utente| Nome dell'utente|

**Eccezioni**

| Eccezione | `Message` | Condizioni |
|:---|:---|:---|
| ArgumentOutOfRangeException | The parameter '**maxDateUtc**' must be greater than or equal to '**minDateUtc**' (Il parametro 'maxDateUtc' deve essere maggiore di o uguale a 'minDateUtc').| Il valore del parametro **maxDateUtc** è minore del valore del parametro **minDateUtc**.|
| TranslateApiException | IP is over the quota (IP oltre la quota).| <ul><li>È stato raggiunto il limite per il numero di richieste al minuto.</li><li>Le dimensioni delle richieste restano limitate a 10.000 caratteri.</li><li>Il numero di caratteri accettati dall'API Microsoft Translator è limitato da una quota giornaliera e da una quota oraria.</li></ul>|
| TranslateApiException | AppId is over the quota (AppId oltre la quota).| L'ID applicazione ha superato la quota oraria o giornaliera.|

> [!NOTE]
> La quota verrà regolata per garantire l'equità tra tutti gli utenti del servizio.

**Visualizzare esempi di codice in GitHub**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>Metodo GetUserTranslations

Questo metodo recupera le traduzioni create dall'utente. Fornisce le traduzioni raggruppate in base ai parametri della richiesta uriPrefix, from, to, user, minRating e maxRating.

**Sintassi**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslation[] GetUserTranslations (
>             string appId,
>             string uriPrefix,
>             string from,
>             string to,
>             int? minRating,
>             int? maxRating,
>             string user,
>             string category
>             DateTime? minDateUtc,
>             DateTime? maxDateUtc,
>             int? skip,
>             int? take);
> ```

**Parameters**

| Parametro | Descrizione |
|:---|:---|
| appId | **Obbligatorio** Se si usa l'intestazione dell'autorizzazione, lasciare vuoto il campo appid. In caso contrario specificare una stringa contenente il token di accesso "Bearer" + " " +.|
| uriPrefix| **Facoltativo** Stringa contenente il prefisso dell'URI della traduzione.|
| from| **Facoltativo** Stringa che rappresenta il codice della lingua del testo della traduzione.|
| to| **Facoltativo** Stringa che rappresenta il codice della lingua in cui tradurre il testo.|
| minRating| **Facoltativo** Valore intero che rappresenta la classificazione di qualità minima per il testo tradotto. Il valore valido è compreso tra -10 e 10. Il valore predefinito è 1.|
| maxRating| **Facoltativo** Valore intero che rappresenta la classificazione di qualità massima per il testo tradotto. Il valore valido è compreso tra -10 e 10. Il valore predefinito è 1.|
| user| **Facoltativo. Stringa usata per filtrare i risultati in base all'iniziatore dell'invio**|
| category| **Facoltativo** Stringa che contiene la categoria o il dominio della traduzione. Questo parametro supporta solo l'opzione predefinita general.|
| minDateUtc| **Facoltativo** Data da cui si vogliono recuperare le traduzioni. La data deve essere nel formato UTC.|
| maxDateUtc| **Facoltativo** Data fino a cui si vogliono recuperare le traduzioni. La data deve essere nel formato UTC.|
| skip| **Facoltativo** Numero di risultati da ignorare in una pagina. Ad esempio, se si vogliono ignorare le prime 20 righe di risultati e visualizzare a partire dal record dei risultati 21, specificare 20 per questo parametro. Il valore predefinito per questo parametro è 0.|
| take| **Facoltativo** Numero di risultati da recuperare. Il numero massimo di ogni richiesta è 100. Il valore predefinito è 50.|

> [!NOTE]
> I parametri della richiesta skip e take consentono la paginazione per un numero elevato di record dei risultati.

**Valore restituito**

Il set di risultati contiene una matrice di **UserTranslation**. Ogni UserTranslation presenta gli elementi seguenti:

| Campo | Descrizione |
|:---|:---|
| CreatedDateUtc| Data di creazione della voce mediante AddTranslation()|
| Da| Lingua di origine|
| OriginalText| Testo nella lingua di origine usato durante l'invio della richiesta|
|Rating |Classificazione applicata dall'autore dell'invio nella chiamata al metodo AddTranslation()|
|A|    Lingua di destinazione|
|TranslatedText|    Traduzione così come inviata nella chiamata al metodo AddTranslation()|
|Uri|   URI applicato nella chiamata al metodo AddTranslation()|
|Utente   |Nome dell'utente|

**Eccezioni**

| Eccezione | `Message` | Condizioni |
|:---|:---|:---|
| ArgumentOutOfRangeException | The parameter '**maxDateUtc**' must be greater than or equal to '**minDateUtc**' (Il parametro 'maxDateUtc' deve essere maggiore di o uguale a 'minDateUtc').| Il valore del parametro **maxDateUtc** è minore del valore del parametro **minDateUtc**.|
| TranslateApiException | IP is over the quota (IP oltre la quota).| <ul><li>È stato raggiunto il limite per il numero di richieste al minuto.</li><li>Le dimensioni delle richieste restano limitate a 10.000 caratteri.</li><li>Il numero di caratteri accettati dall'API Microsoft Translator è limitato da una quota giornaliera e da una quota oraria.</li></ul>|
| TranslateApiException | AppId is over the quota (AppId oltre la quota).| L'ID applicazione ha superato la quota oraria o giornaliera.|

> [!NOTE]
> La quota verrà regolata per garantire l'equità tra tutti gli utenti del servizio.

**Visualizzare esempi di codice in GitHub**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
