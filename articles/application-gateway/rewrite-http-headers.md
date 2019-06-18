---
title: Riscrivere le intestazioni HTTP con il Gateway applicazione di Azure | Microsoft Docs
description: Questo articolo offre una panoramica di riscrittura di intestazioni HTTP nel Gateway applicazione di Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/29/2019
ms.author: absha
ms.openlocfilehash: 9160d300270bf1ab5043bee632d27bcc4b7bf332
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476027"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Riscrivere le intestazioni HTTP con il Gateway applicazione

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Intestazioni HTTP consentono un client e server passare informazioni aggiuntive con una richiesta o risposta. Riscrivendo le intestazioni, è possibile eseguire attività importanti, ad esempio l'aggiunta di campi di intestazione di sicurezza, ad esempio HSTS / X-XSS-Protection, la rimozione di campi di intestazione di risposta che potrebbero rivelare informazioni riservate e rimozione di informazioni sulle porte da Intestazioni X-Forwarded-For.

Il gateway applicazione permette di aggiungere, rimuovere o aggiornare le intestazioni di richieste e risposte HTTP durante lo spostamento dei pacchetti di richiesta e risposta tra il client e i pool back-end. Consente anche di aggiungere le condizioni necessarie per garantire che le intestazioni specificate vengano riscritte solo in presenza di determinate condizioni.

Il Gateway applicazione supporta anche diversi [variabili server](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) che consentono archiviare informazioni aggiuntive sulle richieste e risposte. Questo rende più semplice per creare regole di riscrittura potenti.

> [!NOTE]
>
> Il supporto alla riscrittura dell'intestazione HTTP è disponibile solo per i [Standard_V2 e SKU WAF_v2](application-gateway-autoscaling-zone-redundant.md).

![Intestazioni di riscrittura](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Intestazioni supportate

È possibile riscrivere tutte le intestazioni di richieste e risposte, fatta eccezione per le intestazioni Host, la connessione e aggiornamento. È anche possibile usare il gateway applicazione per creare le intestazioni personalizzate e aggiungerle a richieste e risposte viene indirizzate attraverso il.

## <a name="rewrite-conditions"></a>Riscrivere le condizioni

È possibile usare le condizioni di riscrittura per valutare il contenuto di risposte e richieste HTTP (S) ed eseguire una riscrittura intestazione solo quando per uno o più condizioni vengono soddisfatte. Il gateway applicazione usa questi tipi di variabili da valutare il contenuto di richieste HTTP (S) e risposte:

- Intestazioni HTTP nella richiesta.
- Intestazioni HTTP nella risposta.
- Variabili del server Gateway applicazione.

È possibile usare una condizione da valutare se una variabile specificata è presente, indica se una variabile specificata corrisponde a un valore specifico oppure se una variabile specificata corrisponde a un modello specifico. Si utilizza il [libreria Perl compatibile regolare le espressioni (PCRE)](https://www.pcre.org/) configurare ricerca di espressioni regolari nelle condizioni di corrispondenza. Per altre informazioni sulla sintassi delle espressioni regolari, vedere la [pagina principale di espressioni regolari Perl](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Riscrivere le azioni

Usare le azioni di riscrittura per specificare le intestazioni di richiesta e risposta che si desidera riscrivere e il nuovo valore per le intestazioni. È possibile creare una nuova intestazione, modificare il valore di un'intestazione esistente oppure eliminare un'intestazione esistente. Il valore di una nuova intestazione o un'intestazione esistente può essere impostato su questi tipi di valori:

- Text.
- Intestazione della richiesta. Per specificare un'intestazione di richiesta, è necessario usare la sintassi {http_req_*headerName*}.
- Intestazione della risposta. Per specificare un'intestazione di risposta, è necessario usare la sintassi {http_resp_*headerName*}.
- Variabile server. Per specificare una variabile del server, è necessario usare la sintassi {var_*serverVariable*}.
- Una combinazione di testo, un'intestazione di richiesta, un'intestazione di risposta e una variabile del server.

## <a name="server-variables"></a>Variabili del server

Il Gateway applicazione usa le variabili del server per archiviare informazioni utili sul server, la connessione con il client e la richiesta corrente per la connessione. Indirizzo IP del client e il tipo di browser web sono esempi di informazioni archiviate. Variabili server modificare in modo dinamico, ad esempio, quando una nuova pagina viene caricata o quando si invia un form. È possibile usare queste variabili per valutare le condizioni di riscrittura e riscrivere le intestazioni.

Il gateway applicazione supporta le variabili del server:

| Nome variabile | Descrizione                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Il campo di intestazione di richiesta X-Forwarded-For client con il `client_ip` variabile (vedere la spiegazione più avanti in questa tabella) aggiunto a tale file nel formato IP1, IP2, lt;IP3 e così via. Se il campo X-Forwarded-For non è nell'intestazione della richiesta client, il `add_x_forwarded_for_proxy` variabile è uguale al `$client_ip` variabile. Questa variabile è particolarmente utile quando si desidera riscrivere l'intestazione X-Forwarded-For impostata dal Gateway applicazione in modo che l'intestazione contiene solo l'indirizzo IP senza le informazioni sulla porta. |
| ciphers_supported          | Elenco di crittografie di supportate dal client.          |
| ciphers_used               | La stringa di crittografia utilizzato per la connessione SSL. |
| client_ip                  | L'indirizzo IP del client da cui il gateway applicazione ha ricevuto la richiesta. Se è presente un proxy inverso prima il gateway applicazione e il client di origine, *client_ip* restituirà l'indirizzo IP del proxy inverso. |
| client_port                | La porta del client.                                                  |
| client_tcp_rtt             | Informazioni sul client di connessione TCP. Disponibile nei sistemi che supportano l'opzione di socket TCP_INFO. |
| client_user                | Quando viene utilizzata l'autenticazione HTTP, il nome utente fornito per l'autenticazione. |
| host                       | In questo ordine di precedenza: il nome host dalla riga di richiesta, il nome host del campo di intestazione di richiesta Host o il nome del server corrispondente a una richiesta. |
| cookie_*nome*              | Il *nome* cookie.                                            |
| http_method                | il metodo utilizzato per eseguire la richiesta di URL. Ad esempio, GET o POST. |
| http_status                | Lo stato della sessione. Ad esempio, 200, 400 o 403.                       |
| http_version               | Il protocollo di richiesta. In genere HTTP/1.0, 1.1/HTTP o HTTP/2.0. |
| query_string               | L'elenco di coppie valore/variabile che segue il "?" nell'URL richiesto. |
| received_bytes             | La lunghezza della richiesta (tra cui la riga della richiesta, intestazione e corpo della richiesta). |
| request_query              | Gli argomenti nella riga della richiesta.                                |
| request_scheme             | Lo schema di richiesta: http o https.                            |
| request_uri                | URI di richiesta originale completo (con gli argomenti).                   |
| sent_bytes                 | Il numero di byte inviati a un client.                             |
| server_port                | La porta del server che ha accettato una richiesta.                 |
| ssl_connection_protocol    | Il protocollo di connessione SSL.        |
| ssl_enabled                | "Sì" se la connessione viene eseguita in modalità SSL. In caso contrario, una stringa vuota. |

## <a name="rewrite-configuration"></a>Configurazione di riscrittura

Per configurare riscrittura dell'intestazione HTTP, è necessario completare questi passaggi.

1. Creare gli oggetti che sono necessari per la riscrittura di intestazione HTTP:

   - **Riscrivere azione**: Utilizzato per specificare la richiesta e campi di intestazione di richiesta che si desidera riscrivere e il nuovo valore per le intestazioni. È possibile associare uno o più condizioni con un'azione di riscrittura di riscrittura.

   - **Riscrivere condizione**: Configurazione facoltativa. Le condizioni di riscrittura di valutare il contenuto delle richieste HTTP (S) e le risposte. L'azione di riscrittura verificherà se la richiesta HTTP (S) o la risposta soddisfa la condizione di riscrittura.

     Se si associa più di una condizione con un'azione, l'azione si verifica solo quando vengono soddisfatte tutte le condizioni. In altre parole, l'operazione è un'operazione con AND logica.

   - **Regola di riscrittura**: Contiene più azioni di riscrittura / riscrivere le combinazioni di condizione.

   - **Sequenza di regole**: Consente di determinare l'ordine in cui vengono eseguite le regole di riscrittura. Questa configurazione è utile quando sono presenti più regole di riscrittura in un set di riscrittura. Una regola di riscrittura con un valore di sequenza più basso di regole viene eseguito per prima. Se si assegna la stessa sequenza di regole da due regole di riscrittura, l'ordine di esecuzione è non deterministico.

   - **Riscrivere set**: Contiene più regole di riscrittura degli indirizzi che verranno associate a una regola di routing di richiesta.

2. Collegare il set di riscrittura (*rewriteRuleSet*) a una regola di routing. La configurazione di riscrittura è allegata al listener di origine tramite la regola di routing. Quando si usa una regola di routing di base, la configurazione di riscrittura di intestazione è associata a un listener di origine ed è una riscrittura intestazione globale. Quando si usa una regola di routing basato sul percorso, la configurazione di riscrittura di intestazione è definita nel mapping del percorso URL. In tal caso, si applica solo all'area di percorso specifico di un sito.

È possibile creare più set di riscrittura dell'intestazione HTTP e applicare ogni riscrittura impostato su più listener. Ma è possibile applicare solo una riscrittura impostato su un listener specifico.

## <a name="common-scenarios"></a>Scenari comuni

Ecco alcuni scenari comuni per l'uso di riscrittura di intestazione.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Rimuovere le informazioni della porta dall'intestazione X-Forwarded-For

Il Gateway applicazione inserisce un'intestazione X-Forwarded-For in tutte le richieste prima che inoltra le richieste al back-end. Questa intestazione è un elenco delimitato da virgole di porte IP. Potrebbero essere presenti scenari in cui i server back-end devono solo le intestazioni per contenere gli indirizzi IP. È possibile usare la riscrittura dell'intestazione per rimuovere le informazioni della porta dall'intestazione X-Forwarded-For. Un modo per eseguire questa operazione consiste nell'impostare l'intestazione per la variabile server add_x_forwarded_for_proxy:

![Rimuovere la porta](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Modificare un URL di reindirizzamento

Quando un'applicazione back-end invia una risposta di reindirizzamento, è possibile reindirizzare il client a un URL diverso da quello specificato dall'applicazione back-end. Ad esempio, è possibile eseguire questa operazione quando un servizio app è ospitato dietro un gateway applicazione e richiede il client può eseguire un reindirizzamento per il percorso relativo. (Ad esempio, un reindirizzamento da contoso.azurewebsites.net/path1 contoso.azurewebsites.net/path2.)

Poiché il servizio App è un servizio multi-tenant, utilizza l'intestazione host nella richiesta per indirizzare la richiesta all'endpoint corretto. Servizi App hanno un nome di dominio predefinito di *. azurewebsites.net (ad esempio contoso.azurewebsites.net) che è diverso dal nome di dominio del gateway applicazione (ad esempio contoso.com). Poiché la richiesta originale inviata dal client con il nome di dominio del gateway applicazione (contoso.com) come il nome host, il gateway applicazione modifica il nome host per contoso.azurewebsites.net. Rende questa modifica in modo che il servizio app può instradare la richiesta all'endpoint corretto.

Quando il servizio app invia una risposta di reindirizzamento, Usa lo stesso nome host nell'intestazione location della risposta di quello nella richiesta che riceve dal gateway applicazione. Quindi, il client di effettuare la richiesta direttamente a contoso.azurewebsites.net/path2 invece di passare attraverso il gateway applicazione (contoso.com/path2). Ignorando il gateway applicazione non è auspicabile.

È possibile risolvere questo problema impostando il nome host nell'intestazione location al nome di dominio del gateway applicazione.

Ecco i passaggi per la sostituzione del nome host:

1. Creare una regola di riscrittura con una condizione che restituisce se l'intestazione location nella risposta contiene azurewebsites.net. Immettere il motivo `(https?):\/\/.*azurewebsites\.net(.*)$`.
1. Eseguire un'azione per riscrivere l'intestazione location in modo che includa il nome host del gateway applicazione. Effettuare questa operazione immettendo `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` come il valore dell'intestazione.

![Modificare intestazione location](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementare le intestazioni di sicurezza HTTP per impedire vulnerabilità

È possibile risolvere le vulnerabilità di sicurezza diversi implementando intestazioni necessarie nella risposta dell'applicazione. Queste intestazioni di sicurezza includono X-XSS-Protection Strict-Transport-Security e Content-Security-Policy. È possibile usare il Gateway applicazione per impostare queste intestazioni per tutte le risposte.

![Intestazione di sicurezza](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Eliminazione delle intestazioni indesiderate

È possibile rimuovere le intestazioni che rivelano informazioni riservate da una risposta HTTP. Ad esempio, è possibile rimuovere le informazioni come il nome del server back-end, sistema operativo o i dettagli della libreria. È possibile usare il gateway applicazione per rimuovere queste intestazioni:

![L'eliminazione di intestazione](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Verificare la presenza di un'intestazione

È possibile valutare un'intestazione di richiesta o risposta HTTP per la presenza di una variabile di intestazione o un server. Questa versione di valutazione è utile quando si desidera eseguire una riscrittura intestazione solo quando è presente un'intestazione determinata.

![Controllo della presenza di un'intestazione](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Limitazioni

- Se una risposta dispone di più intestazioni con lo stesso nome, quindi la riscrittura il valore di una di queste intestazioni comporterà eliminando le altre intestazioni della risposta. In genere, ciò può verificarsi con intestazione Set-Cookie poiché è possibile avere più di un'intestazione Set-Cookie in una risposta. Un tale scenario è quando si usa un servizio app con un gateway applicazione e aver configurato l'affinità di sessione basata su cookie sul gateway applicazione. In questo caso la risposta conterrà 2 intestazioni Set-Cookie: uno viene usato dal servizio app, ad esempio, `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` e l'altro per l'affinità del gateway applicazione, ad esempio, `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`. Riscrittura di una delle intestazioni del Cookie in questo scenario può comportare la rimozione di altri intestazione Set-Cookie dalla risposta.

- Riscrivere le intestazioni Host, aggiornamento e connessione non è attualmente supportata.

- I nomi di intestazione possono contenere caratteri alfanumerici e simboli specifici come definito in [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Il carattere di sottolineatura non sono attualmente supportate (\_) caratteri speciali nei nomi di intestazione.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come riscrivere le intestazioni HTTP, vedere:

- [Riscrivere le intestazioni HTTP tramite il portale di Azure](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Riscrivere le intestazioni HTTP usando Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
