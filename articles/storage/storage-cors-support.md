---
title: Supporto di condivisione delle risorse multiorigine (CORS) | Microsoft Docs
description: Informazioni su come attivare il supporto CORS per i servizi di archiviazione di Microsoft Azure.
services: storage
documentationcenter: .net
author: cbrooks
manager: carmonm
editor: tysonn
ms.assetid: a0229595-5b64-4898-b8d6-fa2625ea6887
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/17/2016
ms.author: cbrooks
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c61be739ce592d75b04bee15d14850cdf94c09da


---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Supporto di condivisione delle risorse multiorigine (CORS) per i servizi di archiviazione di Azure
A partire dalla versione del 15 agosto 2013, i servizi di archiviazione di Azure supportano la condivisione risorse tra le origini (CORS) per i servizi BLOB, tabelle, code e file. CORS è una funzionalità HTTP che consente a un'applicazione Web in esecuzione in un dominio di accedere alle risorse in un altro dominio. Nei browser Web è implementata una restrizione di sicurezza nota come [criterio della stessa origine](http://www.w3.org/Security/wiki/Same_Origin_Policy) che impedisce a una pagina Web di chiamare API in un dominio differente. CORS offre una modalità sicura per consentire a un dominio (quello di origine) di chiamare API in un altro dominio. Per altri dettagli su CORS, vedere la [specifica CORS](http://www.w3.org/TR/cors/).

È possibile impostare regole CORS singolarmente per ogni servizio di archiviazione chiamando [Set Blob Service Properties](https://msdn.microsoft.com/library/hh452235.aspx), [Set Queue Service Properties](https://msdn.microsoft.com/library/hh452232.aspx) e [Set Table Service Properties](https://msdn.microsoft.com/library/hh452240.aspx). Una volta impostate le regole CORS per il servizio, una richiesta correttamente autenticata, eseguita al servizio e autenticata correttamente, verrà valutata per determinare se è consentita in base alle regole specificate.

> [!NOTE]
> CORS non è un meccanismo di autenticazione. Qualsiasi richiesta eseguita a una risorsa di archiviazione quando è abilitata la condivisione CORS deve disporre di una firma di autenticazione appropriata o deve essere eseguita su una risorsa pubblica.
> 
> 

## <a name="understanding-cors-requests"></a>Informazioni sulle richieste CORS
Una richiesta CORS proveniente da un dominio di origine può essere costituita da due richieste distinte:

* Una richiesta preliminare, che esegue query sulle restrizioni di CORS imposte dal servizio. La richiesta preliminare è obbligatoria, a meno che il metodo di richiesta sia un [metodo semplice](http://www.w3.org/TR/cors/), ovvero GET, HEAD o POST.
* La richiesta effettiva, effettuata alla risorsa desiderata.

### <a name="preflight-request"></a>Richiesta preliminare
La richiesta preliminare esegue una query sulle restrizioni per la condivisione CORS definite per il servizio di archiviazione dal proprietario dell'account. Dal Web browser (o da altro agente utente) viene inviata una richiesta OPTIONS che include le intestazioni della richiesta, il metodo e il dominio di origine. Il servizio di archiviazione valuta l'operazione desiderata in base a un set di regole preconfigurate per la condivisione CORS, che specificano quali domini di origine, metodi e intestazioni di richiesta possono essere specificati in una richiesta effettiva a una risorsa di archiviazione.

Se la condivisione CORS è abilitata per il servizio ed è presente una regola CORS che corrisponde alla richiesta preliminare, il servizio risponde con il codice di stato 200 (OK) e include nella risposta le intestazioni Access-Control obbligatorie.

Se la condivisione CORS non è abilitata per il servizio o non è presente alcuna regola CORS corrispondente alla richiesta preliminare, il servizio risponderà con il codice di stato 403 (Accesso negato).

Se nella richiesta OPTIONS non sono contenute le intestazioni CORS obbligatorie (intestazioni Origin e Access-Control-Request-Method), il servizio risponderà con un codice di stato 400 (Richiesta non valida).

La richiesta preliminare viene valutata rispetto al servizio (BLOB, coda e tabella) e non rispetto alla risorsa richiesta. Affinché la richiesta abbia esito positivo, il proprietario dell'account deve aver abilitato la condivisione CORS come parte delle proprietà del servizio account.

### <a name="actual-request"></a>Richiesta effettiva
Una volta che la richiesta preliminare viene accettata e viene restituita la risposta, il browser invierà la richiesta effettiva alla risorsa di archiviazione. Se la richiesta preliminare viene rifiutata, il browser negherà immediatamente la richiesta effettiva.

La richiesta effettiva viene trattata come una normale richiesta al servizio di archiviazione. La presenza dell'intestazione di origine indica che si tratta di una richiesta CORS e che il servizio controllerà le corrispondenti regole CORS. Se viene rilevata una corrispondenza, le intestazioni Access-Control vengono aggiunte alla risposta e inviate di nuovo al client. In caso contrario, le intestazioni Access-Control CORS non vengono restituite.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Abilitazione della condivisione CORS per i servizi di archiviazione di Azure
Le regole CORS vengono impostate a livello di servizio, pertanto è necessario abilitare o disabilitare la condivisione CORS separatamente per ciascun servizio (BLOB, coda e tabella). Per impostazione predefinita, la condivisione CORS è disabilitata per ciascun servizio. Per abilitare la condivisione CORS, è necessario definire le proprietà del servizio appropriate, tramite la versione del 15 agosto 2013 o successive e aggiungere le regole CORS alle proprietà del servizio. Per informazioni su come abilitare o disabilitare la condivisione CORS per un servizio e su come impostare le relative regole, fare riferimento a [Set Blob Service Properties](https://msdn.microsoft.com/library/hh452235.aspx), [Set Queue Service Properties](https://msdn.microsoft.com/library/hh452232.aspx) e [Set Table Service Properties](https://msdn.microsoft.com/library/hh452240.aspx).

Di seguito è riportato un esempio di una regola CORS singola, specificata tramite un'operazione Set Service Properties:

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Ciascun elemento incluso nella regola CORS è descritto di seguito:

* **AllowedOrigins**: domini di origine ai quali è consentito effettuare una richiesta al servizio di archiviazione tramite condivisione CORS. Il dominio di origine è quello da cui proviene la richiesta. L'origine deve corrispondere esattamente, anche con distinzione tra maiuscole e minuscole, all'origine inviata al servizio dall'agente utente. È inoltre possibile utilizzare il carattere jolly '*' per consentire a tutti i domini di origine di effettuare richieste tramite condivisione CORS. Nell'esempio precedente, ai domini [http://www.contoso.com](http://www.contoso.com) e [http://www.fabrikam.com](http://www.fabrikam.com) è consentito eseguire richieste al servizio tramite la condivisione CORS.
* **AllowedMethods**: metodi (verbi di richiesta HTTP) che possono essere utilizzati dal dominio di origine per una richiesta CORS. Nell'esempio precedente, sono consentite solo le richieste PUT e GET.
* **AllowedHeaders**: intestazioni di richiesta che possono essere specificate dal dominio di origine nella richiesta CORS. Nell'esempio precedente, sono consentite tutte le intestazioni di metadati che iniziano con x-ms-meta-data, x-ms-meta-target e x-ms-meta-abc. Il carattere jolly "*" indica che è consentita qualsiasi intestazione che inizi col prefisso specificato.
* **ExposedHeaders**: intestazioni di risposta che possono essere inviate in risposta alla richiesta CORS ed esposte al richiedente dal browser. Nell'esempio precedente, al browser viene indicato di esporre qualsiasi intestazione che inizi con x-ms-meta.
* **MaxAgeInSeconds**: quantità di tempo massima in cui la richiesta preliminare OPTIONS deve essere memorizzata nella cache di un browser.

I servizi di archiviazione di Azure supportano la specifica di intestazioni con prefisso sia per gli elementi **AllowedHeaders**, sia per gli elementi **ExposedHeaders**. Per consentire una categoria di intestazioni, è possibile specificare un prefisso comune a tale categoria. Ad esempio, specificando *x-ms-meta** come intestazione con prefisso, viene impostata una regola corrispondente a tutte le intestazioni che iniziano con x-ms-meta.

Alle regole CORS vengono applicate le limitazioni seguenti:

* È possibile specificare un massimo di cinque regole CORS per servizio di archiviazione (BLOB, tabella e coda).
* Le dimensioni massime di tutte le impostazioni delle regole CORS nella richiesta, esclusi i tag XML, non devono superare i 2 KB.
* La lunghezza di un'intestazione consentita, di un'intestazione esposta o di un'origine consentita non deve superare 256 caratteri.
* Le intestazioni consentite e quelle esposte possono essere:
  * Intestazioni letterali, per cui viene fornito il nome esatto dell'intestazione, ad esempio **x-ms-meta-processed**. Nella richiesta è possibile specificare un massimo di 64 intestazioni letterali.
  * Intestazioni con prefisso, per cui viene fornito un prefisso dell'intestazione, ad esempio **x-ms-meta-data***. Specificando un prefisso in questo modo, si consente o si espone qualsiasi intestazione che inizi con il prefisso specificato. Nella richiesta è possibile specificare un massimo di due intestazioni con prefisso.
* I metodi (o verbi HTTP) specificati nell'elemento **AllowedMethods** devono essere conformi ai metodi supportati dalle API del servizio di archiviazione di Azure. I metodi supportati sono DELETE, GET, HEAD, MERGE, POST, OPTIONS e PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Informazioni sulla logica di valutazione delle regole CORS
Quando il servizio di archiviazione riceve una richiesta preliminare o effettiva, la valuta in base alle regole CORS definite per il servizio tramite l'operazione Set Service Properties appropriata. Le regole CORS vengono valutate nell'ordine in cui sono state impostate nel corpo della richiesta relativo all'operazione Set Service Properties.

Le regole CORS vengono valutate come segue:

1. In primo luogo, il dominio di origine della richiesta viene confrontato con i domini elencati per l'elemento **AllowedOrigins** . Se il dominio di origine è incluso nell'elenco oppure tramite il carattere jolly "*" sono consentiti tutti i domini, la valutazione delle regole prosegue. Se il dominio di origine non è incluso, la richiesta ha esito negativo.
2. Successivamente, il metodo (o verbo HTTP) della richiesta viene confrontato con i metodi elencati nell'elemento **AllowedMethods** . Se il metodo è incluso nell'elenco, la valutazione delle regole prosegue; in caso contrario, la richiesta ha esito negativo.
3. Se la richiesta corrisponde a una regola nel relativo dominio di origine e nel relativo metodo, tale regola viene selezionata per elaborare la richiesta e non ne vengono valutate altre. Tuttavia, prima che la richiesta possa avere esito positivo, vengono controllate tutte le intestazioni specificate nella richiesta rispetto alle intestazioni elencate nell'elemento **AllowedHeaders** . Se le intestazioni inviate non corrispondono alle intestazioni consentite, la richiesta ha esito negativo.

Poiché le regole vengono elaborate nell'ordine in cui si trovano nel corpo della richiesta, nell'elenco è consigliabile specificare in primo luogo le regole più restrittive, in modo che vengano valutate per prime. Specificare le regole meno restrittive (ad esempio, una regola che consente tutte le origini) alla fine dell'elenco.

### <a name="example--cors-rules-evaluation"></a>Esempio: valutazione di regole CORS
Nell'esempio seguente viene illustrato il corpo di una richiesta parziale per un'operazione di impostazione delle regole CORS per i servizi di archiviazione. Per informazioni dettagliate sulla costruzione della richiesta, vedere [Set Blob Service Properties](https://msdn.microsoft.com/library/hh452235.aspx), [Set Queue Service Properties](https://msdn.microsoft.com/library/hh452232.aspx) e [Set Table Service Properties](https://msdn.microsoft.com/library/hh452240.aspx).

```xml
<Cors>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>PUT,HEAD</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>*</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
    </CorsRule>
</Cors>
```

Successivamente, considerare le seguenti richieste CORS:

| Richiesta |  |  | Response |  |
| --- | --- | --- | --- | --- |
| **Metodo** |**Origine** |**Intestazioni della richiesta** |**Corrispondenza regola** |**Risultato** |
| **PUT** |http://www.contoso.com |x-ms-blob-content-type |Prima regola |Success |
| **GET** |http://www.contoso.com |x-ms-blob-content-type |Seconda regola |Success |
| **GET** |http://www.contoso.com |x-ms-blob-content-type |Seconda regola |Esito negativo |

La prima richiesta corrisponde alla prima regola (il dominio di origine corrisponde alle origini consentite, il metodo corrisponde ai metodi consentiti e l'intestazione corrisponde alle intestazioni consentite), pertanto ha esito positivo.

La seconda richiesta non corrisponde alla prima regola, perché il metodo non corrisponde ai metodi consentiti. Tuttavia corrisponde alla seconda regola, pertanto ha esito positivo.

La terza richiesta corrisponde alla seconda regola nel relativo metodo e dominio di origine, pertanto non vengono valutate altre regole. Tuttavia, l' *intestazione x-ms-client-request-id* non è consentita dalla seconda regola, quindi la richiesta ha esito negativo, anche se la semantica della terza regola ne avrebbe consentito l'esito positivo.

> [!NOTE]
> Sebbene nell'esempio sia riportata una regola meno restrittiva prima di una più restrittiva, in generale è consigliabile elencare prima le regole più restrittive.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Informazioni sulla procedura per impostare l'intestazione Vary
*Vary* è un'intestazione standard HTTP/1.1 costituita da un set di campi di intestazione della richiesta che indicano al browser o all'agente utente i criteri selezionati dal server per l'elaborazione della richiesta. L'intestazione *Vary* viene utilizzata principalmente per la memorizzazione nella cache da parte di proxy, browser e reti CDN, che la impiegano per determinare in che modo la risposta deve essere memorizzata nella cache. Per informazioni dettagliate, vedere la specifica dell' [intestazione Vary](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Quando la risposta a una richiesta CORS viene memorizzata nella cache dal browser o da un altro agente utente, il dominio di origine viene memorizzato nella cache come origine consentita. Se un secondo dominio invia la stessa richiesta per una risorsa di archiviazione mentre la cache è attiva, l'agente utente recupera il dominio di origine presente nella cache. Il secondo dominio non corrisponde al dominio presente nella cache, pertanto l'esito della richiesta è negativo quando avrebbe potuto essere positivo. In alcuni casi, nel servizio di archiviazione Azure l'intestazione Vary viene impostata su **Origin** per indicare all'agente utente di inviare la richiesta CORS successiva al servizio, quando il dominio della richiesta è diverso dall'origine memorizzata nella cache.

Nel servizio di archiviazione di Azure l'intestazione *Vary* viene impostata su **Origin** per le richieste GET/HEAD effettive nei seguenti casi:

* Quando l'origine della richiesta corrisponde esattamente all'origine consentita definita da una regola CORS. Affinché una corrispondenza sia esatta, la regola CORS non può includere il carattere jolly ' * '.
* Non esiste alcuna regola corrispondente all'origine della richiesta, ma la condivisione CORS è abilitata per il servizio di archiviazione.

Se una richiesta GET/HEAD corrisponde a una regola CORS che consente tutte le origini, la risposta indica che tutte le origini sono consentite e, finché la cache resta attiva, la cache dell'agente utente consentirà le richieste successive provenienti da qualsiasi dominio di origine.

Per le richieste che utilizzano metodi diversi da GET/HEAD, nei servizi di archiviazione non verrà impostata l'intestazione Vary, poiché le risposte a questi metodi non vengono memorizzate nella cache dagli agenti utente.

Nella tabella seguente viene indicata la risposta del servizio di archiviazione di Azure alle richieste GET/HEAD in base ai casi riportati in precedenza:

| Richiesta | Impostazione account e risultato della valutazione della regola |  |  | Response |  |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Intestazione di origine presente sulla richiesta** |**Regole CORS specificate per questo servizio** |**Presenza di una regola di corrispondenza che consente tutte le origini (*)** |**Presenza di una regola per l'esatta corrispondenza dell'origine** |**Risposta che include l'intestazione Vary impostata su Origin** |**Risposta che include Access-Control-Allowed-Origin: "*"** |**Risposta che include Access-Control-Exposed-Headers** |
| No |No |No |No |No |No |No |
| No |Sì |No |No |Sì |No |No |
| No |Sì |Sì |No |No |Sì |Sì |
| Sì |No |No |No |No |No |No |
| Sì |Sì |No |Sì |Sì |No |Sì |
| Sì |Sì |No |No |Sì |No |No |
| Sì |Sì |Sì |No |No |Sì |Sì |

## <a name="billing-for-cors-requests"></a>Fatturazione per le richieste CORS
Le richieste preliminari con esito positivo vengono fatturate qualora la condivisione CORS sia stata abilitata per i servizi di archiviazione dell'account, chiamando [Set Blob Service Properties](https://msdn.microsoft.com/library/hh452235.aspx), [Set Queue Service Properties](https://msdn.microsoft.com/library/hh452232.aspx) o [Set Table Service Properties](https://msdn.microsoft.com/library/hh452240.aspx). Per ridurre al minimo le spese, impostare l'elemento **MaxAgeInSeconds** nelle regole CORS su un valore elevato, in modo che la richiesta venga memorizzata nella cache dall'agente utente.

Le richieste preliminari con esito negativo non verranno fatturate.

## <a name="next-steps"></a>Passaggi successivi
[Set Blob Service Properties](https://msdn.microsoft.com/library/hh452235.aspx)

[Set Queue Service Properties](https://msdn.microsoft.com/library/hh452232.aspx)

[Set Table Service Properties](https://msdn.microsoft.com/library/hh452240.aspx)

[Specifica del W3C relativa alla condivisione delle risorse multiorigine (CORS)](http://www.w3.org/TR/cors/)




<!--HONumber=Nov16_HO3-->


