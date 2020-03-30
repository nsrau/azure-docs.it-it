---
title: Riscrivere le intestazioni HTTP con il gateway applicazione di Azure Documenti Microsoft
description: Questo articolo offre una panoramica della riscrittura delle intestazioni HTTP nel gateway applicazione di AzureThis article provides an overview of rewriting HTTP headers in Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: d0b28770940f0e1adeec16aa89cd087299bd4abc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132986"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Riscrivere le intestazioni HTTP con il gateway applicazioneRewrite HTTP headers with Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Le intestazioni HTTP consentono a un client e a un server di passare informazioni aggiuntive con una richiesta o una risposta. Riscrivendo queste intestazioni, è possibile eseguire attività importanti, ad esempio l'aggiunta di campi di intestazione correlati alla sicurezza come HSTS/ X-XSS-Protection, la rimozione di campi di intestazione di risposta che potrebbero rivelare informazioni riservate e la rimozione delle informazioni sulle porte dalle intestazioni X-Forwarded-For.

Il gateway applicazione permette di aggiungere, rimuovere o aggiornare le intestazioni di richieste e risposte HTTP durante lo spostamento dei pacchetti di richiesta e risposta tra il client e i pool back-end. Consente anche di aggiungere le condizioni necessarie per garantire che le intestazioni specificate vengano riscritte solo in presenza di determinate condizioni.

Il gateway applicazione supporta inoltre diverse [variabili server](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) che consentono di archiviare informazioni aggiuntive su richieste e risposte. In questo modo è più semplice creare potenti regole di riscrittura.

> [!NOTE]
>
> Il supporto per la riscrittura dell'intestazione HTTP è disponibile solo per il [Standard_V2 e WAF_v2 SKU.](application-gateway-autoscaling-zone-redundant.md)

![Riscrittura delle intestazioni](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Intestazioni supportate

È possibile riscrivere tutte le intestazioni nelle richieste e nelle risposte, ad eccezione delle intestazioni Host, Connection e Upgrade. È inoltre possibile utilizzare il gateway applicazione per creare intestazioni personalizzate e aggiungerle alle richieste e alle risposte instradate attraverso di esso.

## <a name="rewrite-conditions"></a>Riscrivere le condizioni

È possibile utilizzare le condizioni di riscrittura per valutare il contenuto delle richieste e delle risposte HTTP(S) ed eseguire una riscrittura dell'intestazione solo quando vengono soddisfatte una o più condizioni. Il gateway applicazione utilizza questi tipi di variabili per valutare il contenuto delle richieste e delle risposte HTTP(S):

- Intestazioni HTTP nella richiesta.
- Intestazioni HTTP nella risposta.
- Variabili del server del gateway applicazione.

È possibile utilizzare una condizione per valutare se una variabile specificata è presente, se una variabile specificata corrisponde a un valore specifico o se una variabile specificata corrisponde a un modello specifico. Utilizzare la [libreria PCRE (Compatibile Condizioni operative Perl)](https://www.pcre.org/) per impostare la corrispondenza dei criteri di espressione regolare nelle condizioni. Per informazioni sulla sintassi delle espressioni regolari, vedere la [pagina principale delle espressioni regolari Perl](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Riscrivere le azioni

Le azioni di riscrittura consentono di specificare le intestazioni di richiesta e risposta che si desidera riscrivere e il nuovo valore per le intestazioni. È possibile creare una nuova intestazione, modificare il valore di un'intestazione esistente o eliminare un'intestazione esistente. Il valore di una nuova intestazione o di un'intestazione esistente può essere impostato su questi tipi di valori:The value of a new header or an existing header can be set to these types of values:

- Text.
- Intestazione della richiesta. Per specificare un'intestazione di richiesta, è necessario utilizzare la sintassi http_req_*headerName.*
- Intestazione della risposta. Per specificare un'intestazione di risposta, è necessario utilizzare la sintassi ,http_resp_*headerName.*
- Variabile server. Per specificare una variabile server, è necessario utilizzare la sintassi var_*serverVariable.*
- Combinazione di testo, un'intestazione di richiesta, un'intestazione di risposta e una variabile server.

## <a name="server-variables"></a>Variabili del server

Il gateway applicazione utilizza le variabili del server per archiviare informazioni utili sul server, la connessione con il client e la richiesta corrente sulla connessione. Esempi di informazioni memorizzate includono l'indirizzo IP del client e il tipo di browser web. Le variabili server cambiano dinamicamente, ad esempio quando viene caricata una nuova pagina o quando viene pubblicato un modulo. È possibile utilizzare queste variabili per valutare le condizioni di riscrittura e riscrivere le intestazioni.

Il gateway applicazione supporta queste variabili server:Application gateway supports these server variables:

| Nome variabile | Descrizione                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Il campo di intestazione della richiesta `client_ip` client X-Forwarded-For con la variabile (vedere la spiegazione più avanti in questa tabella) vi è stata aggiunta nel formato IP1, IP2, IP3 e così via. Se il campo X-Forwarded-For non si trova `add_x_forwarded_for_proxy` nell'intestazione `$client_ip` della richiesta client, la variabile è uguale alla variabile. Questa variabile è particolarmente utile quando si desidera riscrivere l'intestazione X-Forwarded-For impostata dal gateway applicazione in modo che l'intestazione contenga solo l'indirizzo IP senza le informazioni sulla porta. |
| ciphers_supported          | Elenco delle crittografie supportate dal client.          |
| ciphers_used               | Stringa di crittografia utilizzata per una connessione TLS stabilita. |
| client_ip                  | Indirizzo IP del client da cui il gateway applicazione ha ricevuto la richiesta. Se è presente un proxy inverso prima del gateway applicazione e del client di origine, *client_ip* restituirà l'indirizzo IP del proxy inverso. |
| client_port                | Porta client.                                                  |
| client_tcp_rtt             | Informazioni sulla connessione TCP client. Disponibile su sistemi che supportano l'opzione TCP_INFO socket. |
| client_user                | Quando viene utilizzata l'autenticazione HTTP, il nome utente fornito per l'autenticazione. |
| host                       | In questo ordine di precedenza: il nome host dalla riga della richiesta, il nome host dal campo di intestazione della richiesta host o il nome del server corrispondente a una richiesta. |
| *nome* cookie_              | Cookie del *nome.*                                            |
| http_method                | Metodo utilizzato per effettuare la richiesta URL. Ad esempio, GET o POST. |
| http_status                | Stato della sessione. Ad esempio, 200, 400 o 403.                       |
| http_version               | Protocollo di richiesta. In genere HTTP/1.0, HTTP/1.1 o HTTP/2.0. |
| query_string               | Elenco di coppie variabile/valore che segue il "?" nell'URL richiesto. |
| received_bytes             | Lunghezza della richiesta (inclusa la riga della richiesta, l'intestazione e il corpo della richiesta). |
| request_query              | Argomenti nella riga della richiesta.                                |
| request_scheme             | Lo schema di richiesta: http o https.                            |
| request_uri                | URI della richiesta originale completo (con argomenti).                   |
| sent_bytes                 | Numero di byte inviati a un client.                             |
| server_port                | Porta del server che ha accettato una richiesta.                 |
| ssl_connection_protocol    | Protocollo di una connessione TLS stabilita.        |
| ssl_enabled                | "Attivato" se la connessione funziona in modalità TLS. In caso contrario, una stringa vuota. |

## <a name="rewrite-configuration"></a>Riscrivere la configurazione

Per configurare la riscrittura dell'intestazione HTTP, è necessario completare questi passaggi.

1. Creare gli oggetti necessari per la riscrittura dell'intestazione HTTP:Create the objects that are required for HTTP header rewrite:

   - **Azione di riscrittura**: utilizzata per specificare i campi di intestazione della richiesta e della richiesta che si desidera riscrivere e il nuovo valore per le intestazioni. È possibile associare una o più condizioni di riscrittura a un'azione di riscrittura.

   - **Riscrittura: una**configurazione facoltativa. Le condizioni di riscrittura valutano il contenuto delle richieste e delle risposte HTTP(S). L'azione di riscrittura si verificherà se la richiesta o la risposta HTTP(S) corrisponde alla condizione di riscrittura.

     Se si associano più condizioni a un'azione, l'azione viene eseguita solo quando vengono soddisfatte tutte le condizioni. In altre parole, l'operazione è un'operazione AND logica.

   - **Regola di riscrittura**: Contiene più combinazioni di azioni/condizioni di riscrittura.

   - **Sequenza delle**regole : consente di determinare l'ordine di esecuzione delle regole di riscrittura. Questa configurazione è utile quando si dispone di più regole di riscrittura in un set di riscrittura. Una regola di riscrittura con un valore di sequenza di regole inferiore viene eseguita per prima. Se si assegna la stessa sequenza di regole a due regole di riscrittura, l'ordine di esecuzione non è deterministico.

   - **Rewrite set**: Contiene più regole di riscrittura che verranno associate a una regola di routing delle richieste.

2. Collegare il set di riscrittura (*rewriteRuleSet*) a una regola di routing. La configurazione di riscrittura viene collegata al listener di origine tramite la regola di routing. Quando si utilizza una regola di routing di base, la configurazione di riscrittura dell'intestazione è associata a un listener di origine ed è una riscrittura dell'intestazione globale. Quando si utilizza una regola di routing basata sul percorso, la configurazione di riscrittura dell'intestazione viene definita nella mappa del percorso URL. In tal caso, si applica solo all'area del percorso specifico di un sito.
   > [!NOTE]
   > La riscrittura URL altera le intestazioni; non modifica l'URL del percorso.

È possibile creare più set di riscrittura dell'intestazione HTTP e applicare ogni set di riscrittura a più listener. È tuttavia possibile applicare un solo set di riscrittura a un listener specifico.

## <a name="common-scenarios"></a>Scenari comuni

Ecco alcuni scenari comuni per l'uso della riscrittura dell'intestazione.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Rimuovere le informazioni sulla porta dall'intestazione X-Forwarded-For

Il gateway applicazione inserisce un'intestazione X-Forwarded-For in tutte le richieste prima di inoltrare le richieste al back-end. Questa intestazione è un elenco separato da virgole di porte IP. Potrebbero esserci scenari in cui i server back-end richiedono solo le intestazioni per contenere gli indirizzi IP. È possibile utilizzare la riscrittura dell'intestazione per rimuovere le informazioni sulla porta dall'intestazione X-Forwarded-For. Un modo per eseguire questa operazione consiste nell'impostare l'intestazione sulla variabile server add_x_forwarded_for_proxy:

![Rimuovi porta](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Modificare un URL di reindirizzamento

Quando un'applicazione back-end invia una risposta di reindirizzamento, è possibile reindirizzare il client a un URL diverso da quello specificato dall'applicazione back-end. Ad esempio, è possibile eseguire questa operazione quando un servizio app è ospitato dietro un gateway applicazione e richiede al client di eseguire un reindirizzamento al percorso relativo. Ad esempio, un reindirizzamento da contoso.azurewebsites.net/path1 a contoso.azurewebsites.net/path2.

Poiché il servizio app è un servizio multi-tenant, usa l'intestazione host nella richiesta per instradare la richiesta all'endpoint corretto. I servizi app hanno un nome di dominio predefinito con estensione azurewebsites.net (ad esempio contoso.azurewebsites.net) diverso dal nome di dominio del gateway applicazione (ad esempio contoso.com). Poiché la richiesta originale dal client ha il nome di dominio del gateway applicazione (contoso.com) come nome host, il gateway applicazione modifica il nome host in contoso.azurewebsites.net. Rende questa modifica in modo che il servizio app possa instradare la richiesta all'endpoint corretto.

Quando il servizio app invia una risposta di reindirizzamento, usa lo stesso nome host nell'intestazione del percorso della risposta di quella nella richiesta che riceve dal gateway applicazione. In questo modo il client effettuerà la richiesta direttamente per contoso.azurewebsites.net/path2 anziché passare attraverso il gateway applicazione (contoso.com/path2). Ignorare il gateway applicazione non è auspicabile.

È possibile risolvere questo problema impostando il nome host nell'intestazione del percorso sul nome di dominio del gateway applicazione.

Di seguito sono riportati i passaggi per la sostituzione del nome host:

1. Creare una regola di riscrittura con una condizione che valuta se l'intestazione di posizione nella risposta contiene azurewebsites.net. Immettere `(https?):\/\/.*azurewebsites\.net(.*)$`il modello .
1. Eseguire un'azione per riscrivere l'intestazione della posizione in modo che abbia il nome host del gateway applicazione. A tale `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` scopo immettendo come valore dell'intestazione.

![Modificare l'intestazione della posizione](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementare intestazioni HTTP di sicurezza per evitare vulnerabilità

È possibile correggere diverse vulnerabilità della sicurezza implementando le intestazioni necessarie nella risposta dell'applicazione. Queste intestazioni di sicurezza includono X-XSS-Protection, Strict-Transport-Security e Content-Security-Policy. È possibile utilizzare il gateway applicazione per impostare queste intestazioni per tutte le risposte.

![Intestazione di sicurezza](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Eliminare intestazioni indesiderate

È possibile rimuovere le intestazioni che rivelano informazioni riservate da una risposta HTTP. Ad esempio, è possibile rimuovere informazioni come il nome del server back-end, il sistema operativo o i dettagli della libreria. È possibile utilizzare il gateway applicazione per rimuovere queste intestazioni:You can use the application gateway to remove these headers:

![Eliminazione dell'intestazione](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Verificare la presenza di un'intestazione

È possibile valutare un'intestazione di richiesta o risposta HTTP per la presenza di un'intestazione o di una variabile server. Questa valutazione è utile quando si desidera eseguire una riscrittura dell'intestazione solo quando è presente una determinata intestazione.

![Verifica della presenza di un'intestazione](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Limitazioni

- Se una risposta ha più di un'intestazione con lo stesso nome, la riscrittura del valore di una di queste intestazioni comporterà l'eliminazione delle altre intestazioni nella risposta. Questo in genere può accadere con l'intestazione Set-Cookie poiché è possibile avere più di un'intestazione Set-Cookie in una risposta. Uno di questi scenari è quando si usa un servizio app con un gateway applicazione e si è configurata l'affinità di sessione basata su cookie nel gateway applicazione. In questo caso la risposta conterrà due intestazioni Set-Cookie: una `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` usata dal servizio app, `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`ad esempio: e un'altra per l'affinità del gateway applicazione, ad esempio . La riscrittura di una delle intestazioni Set-Cookie in questo scenario può comportare la rimozione dell'altra intestazione Set-Cookie dalla risposta.

- La riscrittura delle intestazioni Connection, Upgrade e Host non è attualmente supportata.

- I nomi delle intestazioni possono contenere qualsiasi carattere alfanumerico e simboli specifici definiti in [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Al momento non è supportato\_il carattere speciale di sottolineatura ( ) nei nomi di intestazione.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come riscrivere le intestazioni HTTP, vedere:To learn how to rewrite HTTP headers, see:

- [Riscrivere le intestazioni HTTP usando il portale di AzureRewrite HTTP headers using Azure portal](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Riscrivere le intestazioni HTTP usando Azure PowerShellRewrite HTTP headers using Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
