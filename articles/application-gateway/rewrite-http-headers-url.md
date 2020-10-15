---
title: Riscrivere le intestazioni HTTP e l'URL con applicazione Azure gateway | Microsoft Docs
description: Questo articolo fornisce una panoramica della riscrittura di intestazioni e URL HTTP in applicazione Azure gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: surmb
ms.openlocfilehash: 2ee34e1a7959aafa5db949b443fd58cca58719c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87281192"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>Riscrivere le intestazioni HTTP e l'URL con il gateway applicazione

 Il gateway applicazione consente di riscrivere il contenuto selezionato di richieste e risposte. Con questa funzionalità è possibile tradurre URL, parametri di stringa di query, nonché modificare le intestazioni di richiesta e risposta. Consente inoltre di aggiungere condizioni per garantire che l'URL o le intestazioni specificate vengano riscritte solo quando vengono soddisfatte determinate condizioni. Queste condizioni sono basate sulle informazioni della richiesta e della risposta.

>[!NOTE]
>Le funzionalità di intestazione HTTP e riscrittura URL sono disponibili solo per lo [SKU del gateway applicazione V2](application-gateway-autoscaling-zone-redundant.md)

## <a name="rewrite-types-supported"></a>Tipi riscritti supportati

### <a name="request-and-response-headers"></a>Intestazioni di richiesta e risposta

Le intestazioni HTTP consentono a un client e a un server di passare informazioni aggiuntive con una richiesta o una risposta. Riscrivendo queste intestazioni, è possibile eseguire attività importanti, ad esempio l'aggiunta di campi di intestazione correlati alla sicurezza come HSTS/X-XSS-Protection, la rimozione dei campi di intestazione della risposta che potrebbero rivelare informazioni riservate e la rimozione delle informazioni sulle porte dalle intestazioni X-Inoltred-for.

Il gateway applicazione permette di aggiungere, rimuovere o aggiornare le intestazioni di richieste e risposte HTTP durante lo spostamento dei pacchetti di richiesta e risposta tra il client e i pool back-end.

Per informazioni su come riscrivere le intestazioni di richiesta e risposta con il gateway applicazione usando portale di Azure, vedere [qui](rewrite-url-portal.md).

![immagine](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**Intestazioni supportate**

È possibile riscrivere tutte le intestazioni nelle richieste e nelle risposte, ad eccezione delle intestazioni di connessione e di aggiornamento. È anche possibile usare il gateway applicazione per creare intestazioni personalizzate e aggiungerle alle richieste e alle risposte instradate attraverso il gateway.

### <a name="url-path-and-query-string-preview"></a>Percorso URL e stringa di query (anteprima)

Con la funzionalità di riscrittura URL nel gateway applicazione, è possibile:

* Riscrivere il nome host, il percorso e la stringa di query dell'URL della richiesta 

* Scegliere di riscrivere l'URL di tutte le richieste in un listener o solo le richieste che corrispondono a una o più delle condizioni impostate. Queste condizioni sono basate sulle proprietà di richiesta e risposta (richiesta, intestazione, intestazione della risposta e variabili del server).

* Scegliere di instradare la richiesta (selezionare il pool back-end) in base all'URL originale o all'URL riscritto

Per informazioni su come riscrivere l'URL con il gateway applicazione usando portale di Azure, vedere [qui](rewrite-url-portal.md).

![immagine](./media/rewrite-http-headers-url/url-rewrite-overview.png)

>[!NOTE]
> La funzionalità di riscrittura URL è in anteprima ed è disponibile solo per Standard_v2 e WAF_v2 SKU del gateway applicazione. Non è consigliato per l'uso nell'ambiente di produzione. Per ulteriori informazioni sulle anteprime, vedere [le condizioni per l'utilizzo qui](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="rewrite-actions"></a>Riscrivere le azioni

Per specificare l'URL, le intestazioni della richiesta o le intestazioni di risposta da riscrivere e il nuovo valore in cui si intende riscriverli, è possibile usare le azioni di riscrittura. Il valore di un URL o di un'intestazione nuova o esistente può essere impostato su questi tipi di valori:

* Testo
* Intestazione della richiesta. Per specificare un'intestazione di richiesta, è necessario usare la sintassi {http_req_*headerName*}
* Intestazione della risposta. Per specificare un'intestazione della risposta, è necessario usare la sintassi {http_resp_*headerName*}
* Variabile server. Per specificare una variabile del server, è necessario usare la sintassi {var_*serverVariable*}. Vedere l'elenco delle variabili server supportate
* Una combinazione di testo, un'intestazione di richiesta, un'intestazione di risposta e una variabile server. 

## <a name="rewrite-conditions"></a>Condizioni di riscrittura

È possibile utilizzare le condizioni di riscrittura, una configurazione facoltativa, per valutare il contenuto di richieste e risposte HTTP (S) ed eseguire una riscrittura solo quando vengono soddisfatte una o più condizioni. Il gateway applicazione usa questi tipi di variabili per valutare il contenuto di richieste e risposte:

* Intestazioni HTTP nella richiesta
* Intestazioni HTTP nella risposta
* Variabili server del gateway applicazione

È possibile usare una condizione per valutare se una variabile specificata è presente, se una variabile specificata corrisponde a un valore specifico o se una variabile specificata corrisponde a un modello specifico. 


### <a name="pattern-matching"></a>Criteri di ricerca 

Il gateway applicazione usa espressioni regolari per i criteri di ricerca nella condizione. È possibile usare la [libreria PCRE (Compatible Regular Expressions) Perl](https://www.pcre.org/) per configurare i criteri di ricerca di espressioni regolari nelle condizioni. Per informazioni sulla sintassi delle espressioni regolari, vedere la [pagina principale relativa alle espressioni regolari Perl](https://perldoc.perl.org/perlre.html).

### <a name="capturing"></a>Acquisizione

Per acquisire una sottostringa da usare in un secondo momento, racchiudere tra parentesi il criterio secondario corrispondente nella definizione Regex della condizione. La prima coppia di parentesi archivia la relativa sottostringa in 1, la seconda coppia in 2 e così via. È possibile utilizzare tutte le parentesi desiderate; Perl continua a definire le variabili più numerate per rappresentare queste stringhe acquisite. Di seguito sono riportati alcuni esempi di [ref](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm): 

*  /(\d) (\d)/# corrisponde a due cifre, che le acquisisce nei gruppi 1 e 2 

* /(\d +)/# corrisponde a una o più cifre, per poi acquisirle tutte nel gruppo 1 

* /(\d) +/# corrisponde a una cifra una o più volte, acquisendo l'ultimo nel gruppo 1

Una volta acquisita, è possibile farvi riferimento nel set di azioni usando il formato seguente:

* Per l'acquisizione di un'intestazione di richiesta, è necessario usare {http_req_headerName_groupNumber}. Ad esempio, {http_req_User-Agent_1} o {http_req_User-Agent_2}
* Per un'acquisizione dell'intestazione della risposta, è necessario usare {http_resp_headerName_groupNumber}. Ad esempio, {http_resp_Location_1} o {http_resp_Location_2}
* Per una variabile server è necessario usare {var_serverVariableName_groupNumber}. Ad esempio, {var_uri_path_1} o {var_uri_path_2}

Se si vuole usare l'intero valore, non è necessario menzionare il numero. Usare semplicemente il formato {http_req_headerName} e così via senza groupNumber.

## <a name="server-variables"></a>Variabili del server

Il gateway applicazione usa variabili server per archiviare informazioni utili sul server, sulla connessione con il client e sulla richiesta corrente sulla connessione. Esempi di informazioni archiviate includono l'indirizzo IP del client e il tipo di Web browser. Le variabili del server cambiano in modo dinamico, ad esempio quando viene caricata una nuova pagina o quando viene pubblicato un modulo. È possibile utilizzare queste variabili per valutare le condizioni di riscrittura e riscrivere le intestazioni. Per usare il valore delle variabili server per riscrivere le intestazioni, è necessario specificare queste variabili nella sintassi {var_*NomeVariabileServer*}

Il gateway applicazione supporta le variabili server seguenti:

|   Nome variabile    |                   Descrizione                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | Il campo di intestazione della richiesta client X-Inoltred-for con la `client_ip` variabile (vedere la spiegazione più avanti in questa tabella) aggiunto ad esso nel formato IP1, IP2, IP3 e così via. Se il campo X-Inoltred-for non è incluso nell'intestazione della richiesta client, la `add_x_forwarded_for_proxy` variabile è uguale alla `$client_ip` variabile.   Questa variabile è particolarmente utile quando si vuole riscrivere l'intestazione X-Inoltred-for impostata dal gateway applicazione in modo che l'intestazione contenga solo l'indirizzo IP senza le informazioni sulla porta. |
| ciphers_supported         | Elenco di crittografie supportate dal client.               |
| ciphers_used              | Stringa di crittografie utilizzata per una connessione TLS stabilita. |
| client_ip                 | Indirizzo IP del client da cui il gateway applicazione ha ricevuto la richiesta. Se è presente un proxy inverso prima del gateway applicazione e del client di origine, `client_ip` restituirà l'indirizzo IP del proxy inverso. |
| client_port               | Porta client.                                             |
| client_tcp_rtt            | Informazioni sulla connessione TCP del client. Disponibile nei sistemi che supportano l'opzione socket TCP_INFO. |
| client_user               | Quando si usa l'autenticazione HTTP, il nome utente specificato per l'autenticazione. |
| host                      | In questo ordine di precedenza: il nome host dalla riga della richiesta, il nome host dal campo dell'intestazione della richiesta host o il nome del server corrispondente a una richiesta. Esempio: nella richiesta `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` , il valore dell'host sarà `contoso.com` |
| *nome* cookie_             | Cookie del *nome* .                                           |
| http_method               | Metodo utilizzato per effettuare la richiesta dell'URL. Ad esempio, GET o POST. |
| http_status               | Stato della sessione. Ad esempio, 200, 400 o 403.           |
| http_version              | Protocollo della richiesta. In genere HTTP/1.0, HTTP/1.1 o HTTP/2.0. |
| query_string              | Elenco di coppie variabile/valore che seguono "?" nell'URL richiesto. Esempio: nella richiesta `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` QUERY_STRING valore sarà `id=123&title=fabrikam` |
| received_bytes            | Lunghezza della richiesta, incluse la riga della richiesta, l'intestazione e il corpo della richiesta. |
| request_query             | Argomenti nella riga della richiesta.                           |
| request_scheme            | Schema di richiesta: http o HTTPS.                           |
| request_uri               | URI completo della richiesta originale (con argomenti). Esempio: nella richiesta `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` REQUEST_URI valore sarà `/article.aspx?id=123&title=fabrikam` |
| sent_bytes                | Numero di byte inviati a un client.                        |
| server_port               | Porta del server che ha accettato una richiesta.              |
| ssl_connection_protocol   | Protocollo di una connessione TLS stabilita.               |
| ssl_enabled               | "On" se la connessione funziona in modalità TLS. In caso contrario, una stringa vuota. |
| uri_path                  | Identifica la risorsa specifica nell'host a cui il client Web vuole accedere. Questa è la parte dell'URI della richiesta senza gli argomenti. Esempio: nella richiesta `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` uri_path valore sarà `/article.aspx` |

 

## <a name="rewrite-configuration"></a>Riscrivere la configurazione

Per configurare una regola di riscrittura, è necessario creare un set di regole di riscrittura e aggiungere la configurazione della regola di riscrittura.

Un set di regole di riscrittura contiene:

* **Associazione della regola di routing delle richieste:** La configurazione di riscrittura è associata al listener di origine tramite la regola di routing. Quando si usa una regola di routing di base, la configurazione di riscrittura è associata a un listener di origine ed è una riscrittura dell'intestazione globale. Quando si usa una regola di routing basata sul percorso, la configurazione di riscrittura è definita nella mappa del percorso URL. In tal caso, si applica solo all'area del percorso specifica di un sito. È possibile creare più set di riscrittura e applicare ciascun set di riscrittura a più listener. È tuttavia possibile applicare un solo set di riscrittura a un listener specifico.

* **Condizione di riscrittura**: è una configurazione facoltativa. Le condizioni di riscrittura valutano il contenuto delle richieste e delle risposte HTTP (S). L'azione di riscrittura si verificherà se la richiesta o la risposta HTTP (S) corrisponde alla condizione di riscrittura. Se si associa più di una condizione a un'azione, l'azione si verifica solo quando vengono soddisfatte tutte le condizioni. In altre parole, l'operazione è un'operazione AND logica.

* **Tipo di riscrittura**: sono disponibili 3 tipi di riscritture:
   * Riscrittura delle intestazioni delle richieste 
   * Riscrittura delle intestazioni di risposta.
   * Riscrittura URL: tipo di riscrittura URL con 3 componenti
      * **URL path**: valore in cui deve essere riscritto il percorso. 
      * **Stringa di query URL**: valore in cui deve essere riscritta la stringa di query. 
      * **Rivalutare il mapping dei percorsi**: usato per determinare se il mapping del percorso dell'URL deve essere valutato di nuovo. Se viene mantenuta deselezionata, il percorso dell'URL originale verrà usato per trovare la corrispondenza con il modello di percorso nella mappa del percorso URL. Se è impostato su true, il mapping del percorso URL verrà rivalutato per verificare la corrispondenza con il percorso riscritto. L'abilitazione di questa opzione consente di instradare la richiesta a un pool back-end diverso dopo la riscrittura.

### <a name="common-scenarios-for-header-rewrite"></a>Scenari comuni per la riscrittura delle intestazioni

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Rimuovere le informazioni sulla porta dall'intestazione X-Inoltred-for

Il gateway applicazione inserisce un'intestazione X-Inoltred-for in tutte le richieste prima che inoltri le richieste al back-end. Questa intestazione è un elenco delimitato da virgole di porte IP. Potrebbero esserci scenari in cui i server back-end necessitano solo delle intestazioni che contengono indirizzi IP. È possibile utilizzare la riscrittura dell'intestazione per rimuovere le informazioni sulla porta dall'intestazione X-Inoltred-for. Un modo per eseguire questa operazione consiste nell'impostare l'intestazione sulla variabile del server add_x_forwarded_for_proxy. In alternativa, è possibile usare anche la variabile client_ip:

![Rimuovi porta](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>Modificare un URL di Reindirizzamento

Quando un'applicazione back-end invia una risposta di reindirizzamento, potrebbe essere necessario reindirizzare il client a un URL diverso da quello specificato dall'applicazione back-end. Ad esempio, è possibile eseguire questa operazione quando un servizio app è ospitato dietro un gateway applicazione e richiede al client di eseguire un reindirizzamento al percorso relativo. Ad esempio, un Reindirizzamento da contoso.azurewebsites.net/path1 a contoso.azurewebsites.net/path2.

Poiché il servizio app è un servizio multi-tenant, usa l'intestazione host nella richiesta per instradare la richiesta all'endpoint corretto. I servizi app hanno un nome di dominio predefinito *. azurewebsites.net (Say contoso.azurewebsites.net) diverso dal nome di dominio del gateway applicazione (ad indicare contoso.com). Poiché la richiesta originale del client ha il nome di dominio del gateway applicazione (contoso.com) come nome host, il gateway applicazione modifica il nome host in contoso.azurewebsites.net. Questa modifica viene eseguita in modo che il servizio app possa instradare la richiesta all'endpoint corretto.

Quando il servizio app invia una risposta di reindirizzamento, USA lo stesso nome host nell'intestazione Location della risposta come quello nella richiesta ricevuta dal gateway applicazione. Quindi, il client effettuerà la richiesta direttamente a contoso.azurewebsites.net/path2 anziché passare attraverso il gateway applicazione (contoso.com/path2). Non è consigliabile ignorare il gateway applicazione.

È possibile risolvere questo problema impostando il nome host nell'intestazione Location sul nome di dominio del gateway applicazione.

Ecco i passaggi per la sostituzione del nome host:

1. Creare una regola di riscrittura con una condizione che valuta se l'intestazione Location nella risposta contiene azurewebsites.net. Immettere il modello `(https?):\/\/.*azurewebsites\.net(.*)$` .
2. Eseguire un'azione per riscrivere l'intestazione del percorso in modo che includa il nome host del gateway applicazione. A tale scopo, immettere `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` come valore di intestazione. In alternativa, è possibile usare anche la variabile server `host` per impostare il nome host in modo che corrisponda alla richiesta originale.

![Modifica intestazione percorso](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementare intestazioni HTTP di sicurezza per evitare vulnerabilità

È possibile correggere diverse vulnerabilità di sicurezza implementando le intestazioni necessarie nella risposta dell'applicazione. Queste intestazioni di sicurezza includono X-XSS-Protection, Strict-Transport-Security e Content-Security-Policy. È possibile usare il gateway applicazione per impostare queste intestazioni per tutte le risposte.

![Intestazione di sicurezza](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>Elimina intestazioni indesiderate

Potrebbe essere necessario rimuovere le intestazioni che consentono di rivelare informazioni riservate da una risposta HTTP. Ad esempio, potrebbe essere necessario rimuovere informazioni come il nome del server back-end, il sistema operativo o i dettagli della libreria. Per rimuovere le intestazioni, è possibile usare il gateway applicazione:

![Eliminazione dell'intestazione](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>Verificare la presenza di un'intestazione

È possibile valutare un'intestazione di richiesta o di risposta HTTP per la presenza di un'intestazione o di una variabile del server. Questa valutazione è utile quando si desidera eseguire un'intestazione di riscrittura solo quando è presente una determinata intestazione.

![Verifica della presenza di un'intestazione](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>Scenari comuni per la riscrittura URL

#### <a name="parameter-based-path-selection"></a>Selezione percorso basato su parametri

Per eseguire scenari in cui si vuole scegliere il pool back-end in base al valore di un'intestazione, a una parte dell'URL o alla stringa di query nella richiesta, è possibile usare la combinazione di funzionalità di riscrittura URL e routing basato sul percorso.  Se, ad esempio, si dispone di un sito Web di acquisti e la categoria di prodotto viene passata come stringa di query nell'URL e si desidera instradare la richiesta al back-end in base alla stringa di query, eseguire le operazioni seguenti:

**Step1:**  Creare una mappa percorso, come illustrato nell'immagine seguente.

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="Scenario di riscrittura URL 1-1.":::

**Passaggio 2 (a):** Creare un set di riscrittura con 3 regole di riscrittura: 

* La prima regola presenta una condizione che controlla la variabile di *QUERY_STRING* per *Category = Shoes* e ha un'azione che riscrive il percorso URL in/*listing1* ed è abilitata la **mappa di percorso di nuova valutazione.**

* La seconda regola presenta una condizione che controlla la variabile di *QUERY_STRING* per *Category = bags* e ha un'azione che riscrive il percorso URL in/*listing2* ed è abilitata la **mappa di percorso di nuova valutazione.**

* La terza regola presenta una condizione che controlla la variabile di *QUERY_STRING* per *Category = Accessories* e ha un'azione che riscrive il percorso URL in/*listing3* ed è abilitata la mappa di **percorso di nuova valutazione.**

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="Scenario di riscrittura URL 1-1.":::

 

**Passaggio 2 (b):** Associa questo set di riscrittura al percorso predefinito della regola basata sul percorso precedente

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="Scenario di riscrittura URL 1-1.":::

A questo punto, se l'utente richiede *contoso.com/Listing?Category=any*, viene confrontato con il percorso predefinito, perché nessuno dei modelli di percorso nella mappa di percorso (/listing1,/listing2,/listing3) corrisponderà. Poiché il set di riscrittura precedente è stato associato a questo percorso, questo set di riscrittura verrà valutato. Poiché la stringa di query non corrisponderà alla condizione in nessuna delle 3 regole di riscrittura in questo set di riscrittura, non verrà eseguita alcuna azione di riscrittura e, di conseguenza, la richiesta verrà instradata senza modifiche al back-end associato al percorso predefinito (ovvero *generics*).

 Se l'utente richiede *contoso.com/Listing?Category=Shoes,* viene nuovamente stabilita la corrispondenza con il percorso predefinito. Tuttavia, in questo caso la condizione nella prima regola corrisponderà e quindi verrà eseguita l'azione associata alla condizione che riscriverà il percorso URL in/*listing1*  e valuterà nuovamente la mappa di percorso. Quando la mappa percorso viene rivalutata, la richiesta corrisponderà al percorso associato al modello */listing1* e la richiesta verrà indirizzata al back-end associato a questo modello, ovvero ShoesListBackendPool

>[!NOTE]
>Questo scenario può essere esteso a qualsiasi valore di intestazione o di cookie, a un percorso URL, a una stringa di query o a variabili del server in base alla condizione definita e in sostanza consente di instradare le richieste in base a tali condizioni.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>Riscrivere i parametri della stringa di query in base all'URL

Si consideri uno scenario di un sito Web di acquisti in cui il collegamento visibile all'utente deve essere semplice e leggibile, ma il server back-end necessita dei parametri della stringa di query per visualizzare il contenuto corretto.

In tal caso, il gateway applicazione può acquisire parametri dall'URL e aggiungere coppie chiave-valore della stringa di query da quelle dell'URL. Si immagini, ad esempio, che l'utente desideri riscrivere, `https://www.contoso.com/fashion/shirts` in `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` , può essere eseguito tramite la configurazione di riscrittura URL seguente.

**Condition** -se la variabile server `uri_path` è uguale al modello `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="Scenario di riscrittura URL 1-1.":::

**Azione** : impostare il percorso dell'URL su `buy.aspx` e la stringa di query su `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="Scenario di riscrittura URL 1-1.":::

Per una guida dettagliata per ottenere lo scenario descritto in precedenza, vedere [riscrivere l'URL con il gateway applicazione usando portale di Azure](rewrite-url-portal.md)

### <a name="url-rewrite-vs-url-redirect"></a>Reindirizzamento URL di Visual Studio Rewrite

In caso di riscrittura URL, il gateway applicazione riscrive l'URL prima che la richiesta venga inviata al back-end. Questa operazione non modificherà ciò che gli utenti visualizzeranno nel browser perché le modifiche sono nascoste all'utente.

In caso di reindirizzamento URL, il gateway applicazione invia una risposta di reindirizzamento al client con il nuovo URL. Che, a sua volta, richiede al client di inviare nuovamente la richiesta al nuovo URL fornito nel reindirizzamento. L'URL visualizzato dall'utente nel browser viene aggiornato al nuovo URL

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Scenario di riscrittura URL 1-1.":::

## <a name="limitations"></a>Limitazioni

- Se una risposta ha più di un'intestazione con lo stesso nome, la riscrittura del valore di una di queste intestazioni comporterà la rimozione delle altre intestazioni nella risposta. Questo problema si verifica in genere con Set-Cookie intestazione poiché è possibile avere più di un'intestazione di Set-Cookie in una risposta. Uno di questi scenari è quando si usa un servizio app con un gateway applicazione e si è configurata l'affinità di sessione basata su cookie nel gateway applicazione. In questo caso la risposta conterrà due intestazioni Set-Cookie: una usata dal servizio app, ad esempio: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` e un'altra per l'affinità del gateway applicazione, ad esempio `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . La riscrittura di una delle intestazioni Set-Cookie in questo scenario può causare la rimozione dell'altra intestazione Set-Cookie dalla risposta.
- Le riscritture non sono supportate quando il gateway applicazione è configurato per reindirizzare le richieste o per visualizzare una pagina di errore personalizzata.
- I nomi delle intestazioni possono contenere qualsiasi carattere alfanumerico e simboli specifici, come definito nella [specifica RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Al momento non è supportato il carattere speciale di sottolineatura (_) nei nomi di intestazione.
- Non è possibile riscrivere le intestazioni di connessione e di aggiornamento

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come riscrivere le intestazioni HTTP con il gateway applicazione usando portale di Azure](rewrite-http-headers-portal.md)
- [Informazioni su come riscrivere l'URL con il gateway applicazione usando portale di Azure](rewrite-url-portal.md)
