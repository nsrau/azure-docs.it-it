---
title: Informazioni di riferimento sull'API Traduzione testuale Microsoft V3.0 | Microsoft Docs
description: Documentazione di riferimento per l'API Traduzione testuale Microsoft V3.0.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: cfaa9584e833b137b417d9074fbfcf606eb21388
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374497"
---
#<a name="translator-text-api-v30"></a>API Traduzione testuale v3.0

## <a name="whats-new"></a>Novità

La versione 3 dell'API Traduzione testuale Microsoft fornisce un'API Web moderna basata su JSON. Migliora l'usabilità e le prestazioni consolidando le funzionalità esistenti in un minor numero di operazioni e offre nuove funzionalità.

 * Traslitterazione per convertire il testo in una lingua da un carattere all'altro.
 * Traduzione in più lingue in una sola richiesta.
 * Rilevamento della lingua, traduzione e traslitterazione in una sola richiesta.
 * Dizionario per la ricerca di traduzioni alternative di un termine, traduzioni inverse ed esempi dell'uso dei termini nel contesto.
 * Altri risultati informativi sul rilevamento della lingua.

## <a name="base-urls"></a>URL di base

L'API Traduzione testuale v3.0 è disponibile nel cloud seguente:

| DESCRIZIONE | Region | URL di base                                        |
|-------------|--------|-------------------------------------------------|
| Azure       | Globale | api.cognitive.microsofttranslator.com           |


## <a name="authentication"></a>Authentication

Sottoscrivere l'API Traduzione testuale in Servizi cognitivi Microsoft e usare la chiave di sottoscrizione (disponibile nel portale di Azure) per l'autenticazione. 

Il modo più semplice consiste nel passare la chiave privata di Azure al servizio Translator usando l'intestazione della richiesta `Ocp-Apim-Subscription-Key`.

Un'alternativa è quella di usare la chiave privata per ottenere un token di autorizzazione dal servizio token, che verrà a sua volta passato al servizio Translator usando l'intestazione della richiesta `Authorization`. Per ottenere un token di autorizzazione, effettuare una richiesta `POST` all'URL seguente:

| Environment     | URL servizio di autenticazione                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Di seguito sono riportati esempi di richieste per ottenere un token con una chiave privata:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'
// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Una richiesta con esito positivo restituisce il token di accesso codificato come testo normale nel corpo della risposta. Il token valido viene passato al servizio Translator come token di connessione nell'autorizzazione.

```
Authorization: Bearer <Base64-access_token>
```

Un token di autenticazione è valido per 10 minuti. Il token deve essere riutilizzato quando si effettuano più chiamate alle API del servizio Translator. Tuttavia, se il programma effettua richieste all'API del servizio Translator per un periodo di tempo prolungato, il programma deve richiedere un nuovo token di accesso a intervalli regolari (ad esempio, ogni 8 minuti).

Riassumendo, una richiesta client all'API del servizio Translator includerà un'intestazione dell'autorizzazione ricavata dalla tabella seguente:

<table width="100%">
  <th width="30%">Headers</th>
  <th>DESCRIZIONE</th>
  <tr>
    <td>Ocp-Apim-Subscription-Key</td>
    <td>*Usare con la sottoscrizione di Servizi cognitivi se si passa la chiave privata*.<br/>Il valore è la chiave privata di Azure per la sottoscrizione dell'API Traduzione testuale.</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>*Usare con la sottoscrizione di Servizi cognitivi se si passa un token di autenticazione*.<br/>Il valore è il token di connessione: `Bearer <token>`.</td>
  </tr>
</table> 

## <a name="errors"></a>Errors

Una risposta di errore standard è un oggetto JSON con coppia nome/valore denominato `error`. Il valore è anche un oggetto JSON con proprietà:

  * `code`: codice di errore definito dal server.

  * `message`: stringa che fornisce una rappresentazione dell'errore leggibile dall'utente.

Ad esempio, un cliente con una sottoscrizione della versione di valutazione gratuita riceverebbe l'errore seguente al superamento della quota gratuita:

```
{
  "error": {
    "code":403000,
    "message":"The subscription has exceeded its free quota."
    }
}
```
