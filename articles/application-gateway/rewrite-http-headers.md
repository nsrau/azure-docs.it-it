---
title: Riscrivere le intestazioni HTTP nel gateway applicazione di Azure | Microsoft Docs
description: Questo articolo offre una panoramica della funzionalità per riscrivere le intestazioni HTTP nel gateway applicazione di Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/11/2019
ms.author: absha
ms.openlocfilehash: efb7b46919066beb1382d70b676a2115ea0fb8ac
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544147"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Riscrivere le intestazioni HTTP con il gateway applicazione (anteprima pubblica)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Le intestazioni HTTP consentono al client e al server di passare informazioni aggiuntive insieme alla richiesta o alla risposta. Riscrivere le intestazioni HTTP consente di realizzare diversi scenari importanti, ad esempio l'aggiunta di campi di intestazione di sicurezza, ad esempio HSTS / X-XSS-Protection, la rimozione di un'intestazione di risposta di campi che potrebbe rivelare informazioni riservate, le informazioni sulla porta stripping da Le intestazioni X-Forwarded-For, e così via. Il gateway applicazione supporta la possibilità di aggiungere, rimuovere o aggiornare le intestazioni di richiesta e risposta HTTP durante la richiesta e spostare pacchetti di risposta tra il pool back-end e client. Fornisce inoltre la possibilità di aggiungere le condizioni per garantire che le intestazioni specificate vengono riscritti solo quando vengono soddisfatte determinate condizioni.
> [!NOTE]
>
> Il supporto alla riscrittura dell'intestazione HTTP è disponibile solo per il [nuovo SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

## <a name="headers-supported-for-rewrite"></a>Intestazioni supportate per riscrivere

La funzionalità consente di riscrivere tutte le intestazioni nella richiesta e risposta ad eccezione delle intestazioni Host, la connessione e aggiornamento. È anche possibile usare il gateway applicazione per creare le intestazioni personalizzate e aggiungerle alla richiesta e le risposte vengono inoltrate tramite lo. 

## <a name="rewrite-conditions"></a>Riscrivere le condizioni

Tramite la riscrittura condizioni è possibile valutare il contenuto delle richieste HTTP (S) e le risposte ed eseguire un'intestazione di riscrivere solo quando vengono soddisfatte una o più condizioni. I seguenti 3 tipi di variabili vengono usati dal gateway applicazione per valutare il contenuto delle richieste HTTP (S) e le risposte:

- Intestazioni HTTP nella richiesta
- Intestazioni HTTP nella risposta
- Variabili del server gateway applicazione

Una condizione è utilizzabile per valutare se la variabile specificata è presente, indica se la variabile specificata corrisponde esattamente a un valore specifico oppure se la variabile specificata corrisponde esattamente a un modello specifico. [Libreria Perl compatibile regolare le espressioni (PCRE)](https://www.pcre.org/) viene usato per implementare il modello di espressione regolare nelle condizioni di corrispondenza. Per altre informazioni sulla sintassi delle espressioni regolari, vedere la [uomo con espressioni regolari Perl pagina](http://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Riscrivere le azioni

Riscrivere le azioni vengono utilizzate per specificare le intestazioni di richiesta e risposta che si prevede di riscrittura e il nuovo valore che dovranno essere riscritte per le intestazioni originale. È possibile creare una nuova intestazione, modificare il valore di un'intestazione esistente o eliminare un'intestazione esistente. Per i tipi seguenti di valori, è possibile impostare il valore di una nuova intestazione o un'intestazione esistente:

- Text 
- Intestazione della richiesta: Per specificare un'intestazione di richiesta, è necessario usare la sintassi {http_req_*headerName*}
- Intestazione della risposta: Per specificare un'intestazione di risposta, è necessario usare la sintassi {http_resp_*headerName*}
- Variabile server: Per specificare una variabile del server, è necessario usare la sintassi {var_*serverVariable*}
- Combinazione di testo, l'intestazione della richiesta, un'intestazione di risposta e una variabile del server.

## <a name="server-variables"></a>Variabili del server

Il gateway applicazione usa le variabili del server per archiviare informazioni utili sul server, la connessione con il client e la richiesta corrente per la connessione, ad esempio l'indirizzo IP del client o il tipo di browser web. Queste variabili di modificare in modo dinamico, ad esempio quando viene caricata una nuova pagina o un modulo viene inviato. È possibile usare queste variabili del server per valutare le condizioni di riscrittura e riscrivere le intestazioni. 

Il gateway applicazione supporta le seguenti variabili server:

| Variabili server supportate | DESCRIZIONE                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Contiene il "X-Forwarded-For" client richiesta campo di intestazione con il `client_ip` (illustrata in questa tabella riportata di seguito) variabile aggiunta nel formato (IP1, IP2, lt;IP3,...). Se il campo "X-Forwarded-For" non è presente nell'intestazione della richiesta client, il `add_x_forwarded_for_proxy` variabile è uguale al `$client_ip` variabile. Questa variabile è particolarmente utile negli scenari in cui i clienti desidera riscrivere l'intestazione X-Forwarded-For impostata dal Gateway applicazione, in modo che l'intestazione contiene solo l'indirizzo IP senza le informazioni sulla porta. |
| ciphers_supported          | Restituisce l'elenco di modalità di crittografia supportate dal client          |
| ciphers_used               | Restituisce la stringa di crittografia usata per una connessione SSL stabilita |
| client_ip                  | Indirizzo IP del client da cui il gateway applicazione ha ricevuto la richiesta. Se è presente un proxy inverso prima il gateway applicazione e il client di origine, quindi *client_ip* restituirà l'indirizzo IP del proxy inverso. |
| client_port                | Porta del client                                                  |
| client_tcp_rtt             | Informazioni sulla connessione TCP client. Disponibile nei sistemi che supportano l'opzione di socket TCP_INFO |
| client_user                | Quando si usa l'autenticazione HTTP, il nome utente fornito per l'autenticazione |
| host                       | In questo ordine di precedenza: nome host dalla riga della richiesta o nome host dal campo di intestazione della richiesta "Host" oppure nome del server corrispondente a una richiesta |
| cookie_*nome*              | il *nome* cookie                                            |
| http_method                | Metodo usato per eseguire la richiesta di URL. Ad esempio GET, POST e così via. |
| http_status                | Stato della sessione, ad esempio: 200, 400, 403 e così via.                       |
| http_version               | Protocollo di richiesta, in genere "HTTP/1.0", "HTTP/1.1" o "HTTP/2.0" |
| query_string               | Elenco di coppie variabile-valore che seguono il simbolo "?" nell'URL richiesto. |
| received_bytes             | Lunghezza della richiesta, incluse riga della richiesta, intestazione e corpo della richiesta |
| request_query              | Argomenti nella riga della richiesta                                |
| request_scheme             | Schema della richiesta, "http" o "https"                            |
| request_uri                | URI completo originale della richiesta (con gli argomenti)                   |
| sent_bytes                 | Numero di byte inviati a un client                             |
| server_port                | Porta del server che ha accettato una richiesta                 |
| ssl_connection_protocol    | Restituisce il protocollo di una connessione SSL stabilita        |
| ssl_enabled                | Restituisce "on" se la connessione opera in modalità SSL o una stringa vuota in caso contrario |

## <a name="rewrite-configuration"></a>Configurazione di riscrittura

Per configurare riscrittura dell'intestazione HTTP, è necessario:

1. Creare i nuovi oggetti necessari per riscrivere le intestazioni http:

   - **Riscrivere azione**: consente di specificare la richiesta e campi di intestazione di richiesta che si prevede di riscrittura e il nuovo valore che dovranno essere riscritte per le intestazioni originale. È possibile scegliere di associare uno o più condizioni di riscrittura con un'azione di riscrittura.

   - **Riscrivere condizione**: È una configurazione facoltativa. Se viene aggiunta una condizione di riscrittura, valuterà il contenuto delle richieste HTTP (S) e le risposte. La decisione di eseguire l'azione di riscrittura associato alla condizione di riscrittura si baseranno se la richiesta HTTP (S) o la risposta corrispondente con la condizione di riscrittura. 

     Se più condizioni sono associati a un'azione, quindi l'azione verrà eseguita solo quando vengono soddisfatte tutte le condizioni, ad esempio, verrà eseguita un'operazione con AND logica.

   - **Regola di riscrittura**: regola di riscrittura contiene più azioni di riscrittura - riscrive le combinazioni di condizione.

   - **Sequenza di regole**: consente di determinare l'ordine in cui le diverse regole di riscrittura venga eseguito. Ciò risulta utile quando sono presenti più regole di riscrittura in un set di riscrittura. La regola di riscrittura con valore di sequenza minore regola Ottiene eseguita per prime. Se si fornisce la stessa sequenza di regole da due regole di riscrittura, l'ordine di esecuzione sarà non deterministica.

   - **Riscrivere Set**: contiene più regole di riscrittura degli indirizzi che verrà associate a una regola di routing di richiesta.

2. Sarà necessario collegare il set di riscrittura (*rewriteRuleSet*) con una regola di routing. Questo avviene perché la configurazione di riscrittura è allegata al listener di origine tramite la regola di routing. Quando si usa una regola di routing di base, la configurazione di riscrittura delle intestazioni viene associata a un listener di origine e la riscrittura è globale. Quando viene usata una regola di routing basata sul percorso, la configurazione di riscrittura delle intestazioni è definita sulla mappa del percorso URL. Pertanto, si applica solo all'area del percorso specifico di un sito.

È possibile creare più set di riscrittura dell'intestazione http e ogni set di riscrittura degli indirizzi può essere applicato a più listener. Tuttavia, è possibile applicare solo una riscrittura impostato su un listener specifico.

## <a name="common-scenarios"></a>Scenari comuni

Di seguito sono riportate alcuni scenari comuni che richiedono la riscrittura di intestazione.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Rimuovere le informazioni della porta dall'intestazione X-Forwarded-For

Il gateway applicazione inserisce intestazione X-Forwarded-For a tutte le richieste prima che inoltra le richieste al back-end. Il formato per questa intestazione è un elenco delimitato da virgole di IP: Port. Tuttavia, potrebbero esserci scenari in cui i server back-end richiedono l'intestazione contenga solo gli indirizzi IP. Per portare a termine questi scenari, riscrittura di intestazione è utilizzabile per rimuovere le informazioni della porta dall'intestazione X-Forwarded-For. Un modo per eseguire questa operazione consiste nell'impostare l'intestazione a add_x_forwarded_for_proxy variabile del server. 

![Rimuovere la porta](media/rewrite-http-headers/remove-port.png)

### <a name="modify-the-redirection-url"></a>Modificare l'URL di reindirizzamento

Quando un'applicazione back-end invia una risposta di reindirizzamento, è possibile reindirizzare il client a un URL diverso da quello specificato dall'applicazione back-end. Un tale scenario è quando un servizio app è ospitato dietro un gateway applicazione e richiede il client può eseguire un reindirizzamento per il percorso relativo (reindirizzamento da contoso.azurewebsites.net/path1 a contoso.azurewebsites.net/path2). 

Poiché il servizio app è un servizio multi-tenant, Usa l'intestazione host nella richiesta per il routing all'endpoint corretto. Servizi App hanno un nome di dominio predefinito di *. azurewebsites.net (ad esempio contoso.azurewebsites.net) che è diverso dal nome di dominio del gateway applicazione (ad esempio contoso.com). Poiché la richiesta originale inviata dal client dispone di nome di dominio contoso.com del gateway applicazione come nome host, il gateway applicazione modifica il nome host per contoso.azurewebsites.net, in modo che il servizio app può indirizzarlo all'endpoint corretto. Quando il servizio app invia una risposta di reindirizzamento, Usa lo stesso nome host nell'intestazione location della risposta di quello nella richiesta che riceve dal gateway applicazione. Pertanto, il client di effettuare la richiesta direttamente a contoso.azurewebsites.net/path2, invece di passare attraverso il gateway applicazione (contoso.com/path2). Ignorando il gateway applicazione non è auspicabile. 

Questo problema può essere risolto tramite l'impostazione del nome host nell'intestazione location al nome di dominio del gateway applicazione. A tale scopo, è possibile creare una regola di riscrittura con una condizione che restituisce se l'intestazione location nella risposta contiene azurewebsites.net immettendo `(https?):\/\/.*azurewebsites\.net(.*)$` come il modello ed esegue un'azione per riscrivere l'intestazione location per disporre del gateway applicazione nome host immettendo `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` come il valore dell'intestazione.

![Modificare intestazione location](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementare le intestazioni di sicurezza HTTP per impedire vulnerabilità

Le vulnerabilità di sicurezza diversi possono essere risolti mediante l'implementazione di intestazioni necessarie nella risposta dell'applicazione. Alcune di queste intestazioni di sicurezza sono X-XSS-Protection, Strict-Transport-Security, Content-Security-Policy, e così via. È possibile usare il gateway applicazione per impostare queste intestazioni per tutte le risposte.

![Intestazione di sicurezza](media/rewrite-http-headers/security-header.png)

## <a name="limitations"></a>Limitazioni

- Riscrivere le intestazioni di connessione, aggiornamento e l'Host non è ancora supportata.

- I nomi di intestazioni possono contenere qualsiasi carattere alfanumerico e simboli specifici come definito in [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Tuttavia, nel nome dell'intestazione attualmente non è supportato il carattere speciale di sottolineatura"" (\_). 

## <a name="need-help"></a>Richiesta di assistenza

Contattare Microsoft all'indirizzo [AGHeaderRewriteHelp@microsoft.com](mailto:AGHeaderRewriteHelp@microsoft.com) per assistenza sull'uso di questa funzionalità.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come riscrivere le intestazioni HTTP, vedere:

-  [Riscrivere le intestazioni HTTP tramite il portale di Azure](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
-  [Riscrivere le intestazioni HTTP usando Azure PowerShell](add-http-header-rewrite-rule-powershell.md)