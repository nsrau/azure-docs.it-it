---
title: Uso della rete CDN di Azure con CORS | Documentazione Microsoft
description: Informazioni su come usare la rete per la distribuzione di contenuti (rete CDN) di Azure con CORS (Cross-Origin Resource Sharing).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 3c8fab85d71f5f81bbf81bc3dd7a22d6c0b7f11b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551841"
---
# <a name="using-azure-cdn-with-cors"></a>Uso della rete CDN di Azure con CORS
## <a name="what-is-cors"></a>Informazioni su CORS
CORS (Cross Origin Resource Sharing) è una funzionalità HTTP che consente a un'applicazione Web in esecuzione in un dominio di accedere alle risorse in un altro dominio. Per ridurre il rischio di attacchi tramite script da altri siti, tutti i Web browser moderni implementano una restrizione di sicurezza nota come [regola della stessa origine](https://www.w3.org/Security/wiki/Same_Origin_Policy).  Questo impedisce a una pagina Web di chiamare le API in un dominio diverso.  CORS offre un modo sicuro per consentire a una origine, ovvero il dominio di origine, di chiamare le API in un'altra origine.

## <a name="how-it-works"></a>Funzionamento
Esistono due tipi di richieste CORS, le *richieste semplici* e le *richieste complesse*.

### <a name="for-simple-requests"></a>Per le richieste semplici:

1. Il browser invia la richiesta CORS con un'ulteriore intestazione della richiesta HTTP **Origin**. Il valore di questa intestazione è l'origine che ha gestito la pagina padre, definita come la combinazione di *protocollo*, *dominio* e *porta*.  Quando una pagina prova ad accedere da https://www.contoso.com ai dati di un utente nell'origine fabrikam.com, a tale sito viene inviata l'intestazione di richiesta seguente:

   `Origin: https://www.contoso.com`

2. Il server può rispondere con uno degli elementi seguenti:

   * Un'intestazione **Access-Control-Allow-Origin** presente nella risposta, per indicare il sito di origine consentito. Ad esempio: 

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Un codice di errore HTTP, ad esempio 403, se il server non consente la richiesta multiorigine dopo il controllo dell'intestazione Origin.

   * Un'intestazione **Access-Control-Allow-Origin** con un carattere jolly che consente tutte le origini:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Per le richieste complesse:

Una richiesta complessa è una richiesta CORS in cui il browser deve inviare una *richiesta preliminare*, ovvero un probe preliminare, prima di inviare la richiesta CORS effettiva. La richiesta preliminare chiede l'autorizzazione del server perché la richiesta CORS originale possa procedere e si tratta di una richiesta `OPTIONS` allo stesso URL.

> [!TIP]
> Per altre informazioni sui flussi CORS e i problemi comuni, vedere [Guide to CORS for REST APIs](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/) (Guida di CORS per le API REST).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Scenari con caratteri jolly o singola origine
La condivisione CORS sulla rete CDN di Azure funzionerà automaticamente senza operazioni di configurazione aggiuntive quando l'intestazione **Access-Control-Allow-Origin** è impostata sul carattere jolly asterisco (*) o su una singola origine.  La rete CDN memorizzerà nella cache la prima risposta e le richieste successive useranno la stessa intestazione.

Se sono state inviate richieste alla rete CDN prima che la condivisione CORS venisse impostata nell'origine, sarà necessario eliminare il contenuto sull'endpoint e ricaricarlo con l'intestazione **Access-Control-Allow-Origin**.

## <a name="multiple-origin-scenarios"></a>Scenari con più origini
Se si desidera autorizzare per CORS uno specifico elenco di origini, le operazioni da eseguire sono più complesse. Il problema si verifica quando la rete CDN memorizza nella cache l'intestazione **Access-Control-Allow-Origin** per la prima origine CORS.  Quando un'origine CORS differente effettua una richiesta successiva, la rete CDN gestisce l'intestazione **Access-Control-Allow-Origin** memorizzata nella cache, che però non corrisponde.  Esistono diversi modi per risolvere il problema.

### <a name="azure-cdn-premium-from-verizon"></a>Rete CDN Premium di Azure fornita da Verizon
Il modo migliore per abilitare questa rete consiste nell'usare la **rete CDN Premium di Azure fornita da Verizon**, in cui sono disponibili alcune funzionalità avanzate. 

È necessario [creare una regola](cdn-rules-engine.md) per verificare l'intestazione **Origin** nella richiesta.  Se l'origine è valida, la regola imposterà l'intestazione **Access-Control-Allow-Origin** sull'origine indicata nella richiesta.  Se l'origine specificata nell'intestazione **Origin** non è consentita, la regola dovrà omettere l'intestazione **Access-Control-Allow-Origin**, che causerà il rifiuto della richiesta da parte del browser. 

Per eseguire questa operazione è possibile procedere in due modi usando il motore regole: In entrambi i casi, l'intestazione **Access-Control-Allow-Origin** proveniente dal server di origine del file viene ignorata e il motore regole della rete CDN gestisce interamente le origini CORS consentite.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Un'espressione regolare con tutte le origini valide
In questo caso verrà creata un'espressione regolare che include tutte le origini che si desidera consentire: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> La **rete CDN Premium di Azure con tecnologia Verizon** usa la libreria [PCRE (Perl Compatible Regular Expressions)](https://pcre.org/) come motore per le espressioni regolari.  Per convalidare le espressioni regolari, è possibile usare uno strumento come [Regular Expressions 101](https://regex101.com/).  Si noti che il carattere "/" è valido nelle espressioni regolari e non deve essere preceduto da un carattere di escape. Tuttavia, l'inserimento di un carattere di escape prima di "/" è considerato una procedura consigliata ed è previsto da alcuni strumenti di convalida delle espressioni regolari.
> 
> 

Se l'espressione regolare corrisponde, la regola specificata sostituirà l'intestazione **Access-Control-Allow-Origin** (se presente) proveniente dall'origine con l'origine che ha inviato la richiesta.  È inoltre possibile aggiungere altre intestazioni CORS, ad esempio **Access-Control-Allow-Methods**.

![Esempio di regole con espressione regolare](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Regola intestazione richiesta per ciascuna origine.
Anziché usare espressioni regolari, è possibile creare una regola separata per ogni origine che si vuole consentire usando la [condizione di corrispondenza](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1) **Request Header Wildcard** (Carattere jolly intestazione richiesta). Come per il metodo delle espressioni regolari, il motore regole imposta le intestazioni CORS. 

![Esempio di regole senza espressione regolare](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> Nell'esempio precedente l'uso del carattere jolly asterisco (*) indica al motore regole di mettere in corrispondenza sia HTTP che HTTPS.
> 
> 

### <a name="azure-cdn-standard-profiles"></a>Profili di rete CDN Standard di Azure
Nei profili di rete CDN Standard di Azure (**rete CDN Standard di Azure CDN con tecnologia Microsoft**, **rete CDN Standard di Azure CDN con tecnologia Akamai** e **rete CDN Standard di Azure CDN con tecnologia Verizon**), l'unico meccanismo per consentire più origini senza l'uso dell'origine dei caratteri jolly consiste nell'usare la [memorizzazione delle stringhe di query](cdn-query-string.md). Abilitare l'impostazione della stringa di query per l'endpoint della rete CDN e usare quindi una stringa di query univoca per le richieste provenienti da ciascun dominio consentito. Con questa operazione la rete CDN memorizza nella cache un oggetto separato per ciascuna stringa di query univoca. Questo approccio tuttavia non rappresenta la soluzione ideale, poiché avrà come risultato la memorizzazione nella cache di più copie dello stesso file nella rete CDN.  

