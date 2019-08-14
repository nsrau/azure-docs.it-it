---
title: Riscrivere le intestazioni HTTP con applicazione Azure gateway | Microsoft Docs
description: Questo articolo fornisce una panoramica della riscrittura delle intestazioni HTTP in applicazione Azure gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: b6f26eca0592017306eaefd3f5fecb544dc6fb36
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932202"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Riscrivere le intestazioni HTTP con il gateway applicazione

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Le intestazioni HTTP consentono a un client e a un server di passare informazioni aggiuntive con una richiesta o una risposta. Riscrivendo queste intestazioni, è possibile eseguire attività importanti, ad esempio l'aggiunta di campi di intestazione correlati alla sicurezza come HSTS/X-XSS-Protection, la rimozione dei campi di intestazione della risposta che potrebbero rivelare informazioni riservate e la rimozione delle informazioni sulle porte da X-Inoltred-per le intestazioni.

Il gateway applicazione permette di aggiungere, rimuovere o aggiornare le intestazioni di richieste e risposte HTTP durante lo spostamento dei pacchetti di richiesta e risposta tra il client e i pool back-end. Consente anche di aggiungere le condizioni necessarie per garantire che le intestazioni specificate vengano riscritte solo in presenza di determinate condizioni.

Il gateway applicazione supporta inoltre diverse [variabili server](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) che consentono di archiviare informazioni aggiuntive su richieste e risposte. In questo modo è più semplice creare regole di riscrittura avanzate.

> [!NOTE]
>
> Il supporto per la riscrittura dell'intestazione HTTP è disponibile solo per lo [SKU Standard_V2 e WAF_v2](application-gateway-autoscaling-zone-redundant.md).

![Riscrittura di intestazioni](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Intestazioni supportate

È possibile riscrivere tutte le intestazioni nelle richieste e nelle risposte, ad eccezione delle intestazioni host, Connection e upgrade. È anche possibile usare il gateway applicazione per creare intestazioni personalizzate e aggiungerle alle richieste e alle risposte instradate attraverso il gateway.

## <a name="rewrite-conditions"></a>Condizioni di riscrittura

È possibile utilizzare le condizioni di riscrittura per valutare il contenuto di richieste e risposte HTTP (S) ed eseguire una riscrittura dell'intestazione solo quando vengono soddisfatte una o più condizioni. Il gateway applicazione usa questi tipi di variabili per valutare il contenuto delle richieste e delle risposte HTTP (S):

- Intestazioni HTTP nella richiesta.
- Intestazioni HTTP nella risposta.
- Variabili del server del gateway applicazione.

È possibile usare una condizione per valutare se una variabile specificata è presente, se una variabile specificata corrisponde a un valore specifico o se una variabile specificata corrisponde a un modello specifico. Usare la [libreria PCRE (Compatible Regular Expressions) Perl](https://www.pcre.org/) per configurare i criteri di ricerca di espressioni regolari nelle condizioni. Per informazioni sulla sintassi delle espressioni regolari, vedere la [pagina principale relativa alle espressioni regolari Perl](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Riscrivere le azioni

È possibile utilizzare le azioni di riscrittura per specificare le intestazioni di richiesta e risposta che si desidera riscrivere e il nuovo valore per le intestazioni. È possibile creare una nuova intestazione, modificare il valore di un'intestazione esistente o eliminare un'intestazione esistente. Il valore di una nuova intestazione o di un'intestazione esistente può essere impostato su questi tipi di valori:

- Testo.
- Intestazione della richiesta. Per specificare un'intestazione di richiesta, è necessario usare la sintassi {http_req_*headerName*}.
- Intestazione della risposta. Per specificare un'intestazione della risposta, è necessario usare la sintassi {http_resp_*headerName*}.
- Variabile server. Per specificare una variabile del server, è necessario usare la sintassi {var_*serverVariable*}.
- Una combinazione di testo, un'intestazione di richiesta, un'intestazione di risposta e una variabile server.

## <a name="server-variables"></a>Variabili del server

Il gateway applicazione usa variabili server per archiviare informazioni utili sul server, sulla connessione con il client e sulla richiesta corrente sulla connessione. Esempi di informazioni archiviate includono l'indirizzo IP del client e il tipo di Web browser. Le variabili del server cambiano in modo dinamico, ad esempio quando viene caricata una nuova pagina o quando viene pubblicato un modulo. È possibile utilizzare queste variabili per valutare le condizioni di riscrittura e riscrivere le intestazioni.

Il gateway applicazione supporta queste variabili server:

| Nome variabile | Descrizione                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Il campo di intestazione della richiesta client X-inoltred-for `client_ip` con la variabile (vedere la spiegazione più avanti in questa tabella) aggiunto ad esso nel formato IP1, IP2, IP3 e così via. Se il campo X-inoltred-for non è incluso nell'intestazione della richiesta client `add_x_forwarded_for_proxy` , la variabile è uguale `$client_ip` alla variabile. Questa variabile è particolarmente utile quando si vuole riscrivere l'intestazione X-Inoltred-for impostata dal gateway applicazione in modo che l'intestazione contenga solo l'indirizzo IP senza le informazioni sulla porta. |
| ciphers_supported          | Elenco di crittografie supportate dal client.          |
| ciphers_used               | Stringa di crittografie utilizzata per una connessione SSL stabilita. |
| client_ip                  | Indirizzo IP del client da cui il gateway applicazione ha ricevuto la richiesta. Se è presente un proxy inverso prima del gateway applicazione e del client di origine, *Client_IP* restituirà l'indirizzo IP del proxy inverso. |
| client_port                | Porta client.                                                  |
| client_tcp_rtt             | Informazioni sulla connessione TCP del client. Disponibile nei sistemi che supportano l'opzione socket TCP_INFO. |
| client_user                | Quando si usa l'autenticazione HTTP, il nome utente specificato per l'autenticazione. |
| host                       | In questo ordine di precedenza: il nome host dalla riga della richiesta, il nome host dal campo dell'intestazione della richiesta host o il nome del server corrispondente a una richiesta. |
| *nome* cookie_              | Cookie del *nome* .                                            |
| http_method                | Metodo utilizzato per effettuare la richiesta dell'URL. Ad esempio, GET o POST. |
| http_status                | Stato della sessione. Ad esempio, 200, 400 o 403.                       |
| http_version               | Protocollo della richiesta. In genere HTTP/1.0, HTTP/1.1 o HTTP/2.0. |
| query_string               | Elenco di coppie variabile/valore che seguono "?" nell'URL richiesto. |
| received_bytes             | Lunghezza della richiesta, incluse la riga della richiesta, l'intestazione e il corpo della richiesta. |
| request_query              | Argomenti nella riga della richiesta.                                |
| request_scheme             | Schema di richiesta: http o HTTPS.                            |
| request_uri                | URI completo della richiesta originale (con argomenti).                   |
| sent_bytes                 | Numero di byte inviati a un client.                             |
| server_port                | Porta del server che ha accettato una richiesta.                 |
| ssl_connection_protocol    | Protocollo di una connessione SSL stabilita.        |
| ssl_enabled                | "On" se la connessione funziona in modalità SSL. In caso contrario, una stringa vuota. |

## <a name="rewrite-configuration"></a>Riscrivere la configurazione

Per configurare la riscrittura dell'intestazione HTTP, è necessario completare questi passaggi.

1. Creare gli oggetti necessari per la riscrittura dell'intestazione HTTP:

   - **Azione**di riscrittura: Utilizzato per specificare i campi di richiesta e di intestazione della richiesta che si desidera riscrivere e il nuovo valore per le intestazioni. È possibile associare una o più condizioni di riscrittura con un'azione di riscrittura.

   - **Condizione**di riscrittura: Configurazione facoltativa. Le condizioni di riscrittura valutano il contenuto delle richieste e delle risposte HTTP (S). L'azione di riscrittura si verificherà se la richiesta o la risposta HTTP (S) corrisponde alla condizione di riscrittura.

     Se si associa più di una condizione a un'azione, l'azione si verifica solo quando vengono soddisfatte tutte le condizioni. In altre parole, l'operazione è un'operazione AND logica.

   - **Regola**di riscrittura: Contiene più combinazioni di operazioni di riscrittura/riscrittura delle condizioni.

   - **Sequenza di regole**: Consente di determinare l'ordine in cui vengono eseguite le regole di riscrittura. Questa configurazione è utile quando si dispone di più regole di riscrittura in un set di riscrittura. Viene eseguita prima una regola di riscrittura con un valore di sequenza di regole inferiore. Se si assegna la stessa sequenza di regole a due regole di riscrittura, l'ordine di esecuzione è non deterministico.

   - **Set**di riscrittura: Contiene più regole di riscrittura che saranno associate a una regola di routing delle richieste.

2. Alleghi il set di riscrittura (*rewriteRuleSet*) a una regola di routing. La configurazione di riscrittura è collegata al listener di origine tramite la regola di routing. Quando si usa una regola di routing di base, la configurazione dell'intestazione di riscrittura è associata a un listener di origine ed è una riscrittura dell'intestazione globale. Quando si usa una regola di routing basata sul percorso, la configurazione dell'intestazione di riscrittura è definita nella mappa del percorso URL. In tal caso, si applica solo all'area del percorso specifica di un sito.
   > [!NOTE]
   > La riscrittura URL modifica le intestazioni; non modifica l'URL per il percorso.

È possibile creare più set di riscrittura dell'intestazione HTTP e applicare ciascun set di riscrittura a più listener. È tuttavia possibile applicare un solo set di riscrittura a un listener specifico.

## <a name="common-scenarios"></a>Scenari comuni

Di seguito sono riportati alcuni scenari comuni per l'uso della riscrittura dell'intestazione.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Rimuovere le informazioni sulla porta dall'intestazione X-Inoltred-for

Il gateway applicazione inserisce un'intestazione X-Inoltred-for in tutte le richieste prima che inoltri le richieste al back-end. Questa intestazione è un elenco delimitato da virgole di porte IP. Potrebbero esserci scenari in cui i server back-end necessitano solo delle intestazioni che contengono indirizzi IP. È possibile utilizzare la riscrittura dell'intestazione per rimuovere le informazioni sulla porta dall'intestazione X-Inoltred-for. Un modo per eseguire questa operazione consiste nell'impostare l'intestazione sulla variabile del server add_x_forwarded_for_proxy:

![Rimuovi porta](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Modificare un URL di Reindirizzamento

Quando un'applicazione back-end invia una risposta di reindirizzamento, potrebbe essere necessario reindirizzare il client a un URL diverso da quello specificato dall'applicazione back-end. Ad esempio, è possibile eseguire questa operazione quando un servizio app è ospitato dietro un gateway applicazione e richiede al client di eseguire un reindirizzamento al percorso relativo. Ad esempio, un Reindirizzamento da contoso.azurewebsites.net/path1 a contoso.azurewebsites.net/path2.

Poiché il servizio app è un servizio multi-tenant, usa l'intestazione host nella richiesta per instradare la richiesta all'endpoint corretto. I servizi app hanno un nome di dominio predefinito *. azurewebsites.net (Say contoso.azurewebsites.net) diverso dal nome di dominio del gateway applicazione (ad indicare contoso.com). Poiché la richiesta originale del client ha il nome di dominio del gateway applicazione (contoso.com) come nome host, il gateway applicazione modifica il nome host in contoso.azurewebsites.net. Questa modifica viene eseguita in modo che il servizio app possa instradare la richiesta all'endpoint corretto.

Quando il servizio app invia una risposta di reindirizzamento, USA lo stesso nome host nell'intestazione Location della risposta come quello nella richiesta ricevuta dal gateway applicazione. Quindi, il client effettuerà la richiesta direttamente a contoso.azurewebsites.net/path2 anziché passare attraverso il gateway applicazione (contoso.com/path2). Non è consigliabile ignorare il gateway applicazione.

È possibile risolvere questo problema impostando il nome host nell'intestazione Location sul nome di dominio del gateway applicazione.

Ecco i passaggi per la sostituzione del nome host:

1. Creare una regola di riscrittura con una condizione che valuta se l'intestazione Location nella risposta contiene azurewebsites.net. Immettere il modello `(https?):\/\/.*azurewebsites\.net(.*)$`.
1. Eseguire un'azione per riscrivere l'intestazione del percorso in modo che includa il nome host del gateway applicazione. A tale scopo, `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` immettere come valore di intestazione.

![Modifica intestazione percorso](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementare intestazioni HTTP di sicurezza per evitare vulnerabilità

È possibile correggere diverse vulnerabilità di sicurezza implementando le intestazioni necessarie nella risposta dell'applicazione. Queste intestazioni di sicurezza includono X-XSS-Protection, Strict-Transport-Security e Content-Security-Policy. È possibile usare il gateway applicazione per impostare queste intestazioni per tutte le risposte.

![Intestazione di sicurezza](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Elimina intestazioni indesiderate

Potrebbe essere necessario rimuovere le intestazioni che consentono di rivelare informazioni riservate da una risposta HTTP. Ad esempio, potrebbe essere necessario rimuovere informazioni come il nome del server back-end, il sistema operativo o i dettagli della libreria. Per rimuovere le intestazioni, è possibile usare il gateway applicazione:

![Eliminazione dell'intestazione](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Verificare la presenza di un'intestazione

È possibile valutare un'intestazione di richiesta o di risposta HTTP per la presenza di un'intestazione o di una variabile del server. Questa valutazione è utile quando si desidera eseguire un'intestazione di riscrittura solo quando è presente una determinata intestazione.

![Verifica della presenza di un'intestazione](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Limitazioni

- Se una risposta ha più di un'intestazione con lo stesso nome, la riscrittura del valore di una di queste intestazioni comporterà la rimozione delle altre intestazioni nella risposta. Questo problema si verifica in genere con l'intestazione set-cookie poiché è possibile avere più di un'intestazione set-cookie in una risposta. Uno di questi scenari è quando si usa un servizio app con un gateway applicazione e si è configurata l'affinità di sessione basata su cookie nel gateway applicazione. In questo caso, la risposta conterrà due intestazioni set-cookie: una usata dal servizio app, ad esempio: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` e un'altra per l'affinità del gateway applicazione, `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`ad esempio. La riscrittura di una delle intestazioni set-cookie in questo scenario può causare la rimozione dell'altra intestazione set-cookie dalla risposta.

- La riscrittura delle intestazioni di connessione, aggiornamento e host non è attualmente supportata.

- I nomi delle intestazioni possono contenere qualsiasi carattere alfanumerico e simboli specifici, come definito nella [specifica RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Attualmente non è supportato il carattere speciale di\_sottolineatura () nei nomi di intestazione.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come riscrivere le intestazioni HTTP, vedere:

- [Riscrivere le intestazioni HTTP usando portale di Azure](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Riscrivere le intestazioni HTTP usando Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
