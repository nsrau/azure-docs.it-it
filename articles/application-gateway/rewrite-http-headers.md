---
title: Riscrivere le intestazioni HTTP nel gateway applicazione di Azure | Microsoft Docs
description: Questo articolo offre una panoramica della funzionalità per riscrivere le intestazioni HTTP nel gateway applicazione di Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: e89fe10768331f5b4099ce9a9e2204dd72aa0bff
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793465"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Riscrivere le intestazioni HTTP con il gateway applicazione (anteprima pubblica)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Le intestazioni HTTP consentono al client e al server di passare informazioni aggiuntive insieme alla richiesta o alla risposta. Riscrivere le intestazioni HTTP consente di affrontare diversi scenari importanti, ad esempio l'aggiunta di campi di intestazione correlati come HSTS/X-XSS-Protection o la rimozione di campi di intestazione di risposta, che potrebbero rivelare informazioni riservate come il nome del server back-end.

Il gateway applicazione supporta ora la possibilità di riscrivere le intestazioni delle richieste HTTP in ingresso e in uscita. Sarà possibile aggiungere, rimuovere o aggiornare le intestazioni di richiesta e risposta HTTP, mentre i pacchetti di richiesta/risposta si spostano tra client e pool di back-end. È possibile riscrivere sia i campi di intestazione standard che quelli non standard.

> [!NOTE]
> 
> Il supporto alla riscrittura dell'intestazione HTTP è disponibile solo per il [nuovo SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

Il supporto per la riscrittura dell'intestazione del gateway applicazione offre:

- **Riscrittura dell'intestazione globale**: è possibile riscrivere intestazioni specifiche per tutte le richieste e risposte relative al sito.
- **Intestazione in base al percorso di riscrittura**: questo tipo di riscrittura consente riscrittura intestazione solo per le richieste e risposte che riguardano solo in un'area specifica del sito, ad esempio un'area del carrello acquisti indicata da /cart/\*.

Con questa modifica, è necessario:

1. Creare i nuovi oggetti necessari per riscrivere le intestazioni http: 
   - **RequestHeaderConfiguration**: questo oggetto viene usato per specificare i campi di intestazione della richiesta che si intende riscrivere e il nuovo valore da sostituire alle intestazioni originali.
   - **ResponseHeaderConfiguration**: questo oggetto viene usato per specificare i campi di intestazione della risposta che si intende riscrivere e il nuovo valore da sostituire alle intestazioni originali.
   - **ActionSet**: questo oggetto contiene le configurazioni delle intestazioni di richiesta e risposta specificate in precedenza. 
   - **RewriteRule**: questo oggetto contiene tutti gli *ActionSet* specificati in precedenza. 
   - **RewriteRuleSet**: questo oggetto contiene tutte le *RewriteRule* e deve essere collegato a una regola di routing delle richieste, di base o basata sul percorso.
2. È quindi necessario collegare il set di regole di riscrittura a una regola di routing. Dopo la creazione, questa configurazione di riscrittura viene collegata al listener di origine tramite la regola di routing. Quando si usa una regola di routing di base, la configurazione di riscrittura delle intestazioni viene associata a un listener di origine e la riscrittura è globale. Quando viene usata una regola di routing basata sul percorso, la configurazione di riscrittura delle intestazioni è definita sulla mappa del percorso URL. Pertanto, si applica solo all'area del percorso specifico di un sito.

È possibile creare più set di regole riscrittura dell'intestazione http e ognuno può essere applicato a più listener. Tuttavia, è possibile applicare a un listener specifico un solo set di regole di riscrittura http.

È possibile riscrivere il valore nelle intestazioni in:

- Valore di testo. 

  *Esempio:* 

  ```azurepowershell-interactive
  $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -  HeaderValue "max-age=31536000")
  ```

- Valore da un'altra intestazione. 

  *Esempio 1:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration= New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-RequestHeader" -HeaderValue {http_req_oldHeader}
  ```

  > [!Note] 
  > Per specificare un'intestazione di richiesta, è necessario usare la sintassi: {http_req_headerName}

  *Esempio 2:*

  ```azurepowershell-interactive
  $responseHeaderConfiguration= New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-ResponseHeader" -HeaderValue {http_resp_oldHeader}
  ```

  > [!Note] 
  > Per specificare un'intestazione di risposta, è necessario usare la sintassi: {http_resp_headerName}

- Valore delle variabili server supportate.

  *Esempio:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Ciphers-Used" -HeaderValue "{var_ciphers_used}"
  ```

  > [!Note] 
  > Per specificare una variabile server, è necessario usare la sintassi: {var_serverVariable}

- Una combinazione delle precedenti.

## <a name="server-variables"></a>Variabili del server

Le variabili del server archiviano informazioni utili su un server Web. Queste variabili forniscono informazioni relative al server, la connessione con il client e la richiesta corrente per la connessione, come l'indirizzo IP del client o il tipo di Web browser. Cambiano in modo dinamico, ad esempio quando viene caricata una nuova pagina o viene inviato un modulo.  Usando queste variabili gli utenti possono impostare le intestazioni della richiesta, oltre alle intestazioni della risposta. 

Questa funzionalità supporta la riscrittura delle intestazioni per le variabili server seguenti:

| Variabili server supportate | DESCRIZIONE                                                  |
| -------------------------- | :----------------------------------------------------------- |
| ciphers_supported          | Restituisce l'elenco di modalità di crittografia supportate dal client          |
| ciphers_used               | Restituisce la stringa di crittografia usata per una connessione SSL stabilita |
| client_ip                  | Indirizzo IP del client da cui il gateway applicazione ha ricevuto la richiesta. Se è presente un proxy inverso prima il gateway applicazione e il client di origine, quindi *client_ip* restituirà l'indirizzo IP del proxy inverso. Questa variabile è particolarmente utile negli scenari in cui i clienti desidera riscrivere l'intestazione X-Forwarded-For impostata dal Gateway applicazione, in modo che l'intestazione contiene solo l'indirizzo IP senza le informazioni sulla porta. |
| client_port                | Porta del client                                                  |
| client_tcp_rtt             | Informazioni sulla connessione TCP client. Disponibile nei sistemi che supportano l'opzione di socket TCP_INFO |
| client_user                | Quando si usa l'autenticazione HTTP, il nome utente fornito per l'autenticazione |
| host                       | In questo ordine di precedenza: nome host dalla riga della richiesta o nome host dal campo di intestazione della richiesta "Host" oppure nome del server corrispondente a una richiesta |
| cookie_*nome*              | il *nome* cookie |
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

## <a name="limitations"></a>Limitazioni

- Questa funzionalità per la riscrittura delle intestazioni HTTP è attualmente disponibile solo tramite Azure PowerShell, API di Azure e Azure SDK. Il supporto tramite il portale e l'interfaccia della riga di comando di Azure sarà disponibile a breve.

- Il supporto della riscrittura dell'intestazione HTTP è disponibile solo per il nuovo SKU [Standard_V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). La funzionalità non sarà supportata nello SKU precedente.

- Riscrivere le intestazioni di connessione, aggiornamento e l'Host non è ancora supportata.

- La possibilità di riscrivere in modo condizionale le intestazioni http disponibile a breve.

- I nomi di intestazioni possono contenere qualsiasi carattere alfanumerico e simboli specifici come definito in [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Tuttavia, nel nome dell'intestazione attualmente non è supportato il carattere speciale di sottolineatura"" (\_). 

## <a name="need-help"></a>Richiesta di assistenza

Contattare Microsoft all'indirizzo [AGHeaderRewriteHelp@microsoft.com](mailto:AGHeaderRewriteHelp@microsoft.com) per assistenza sull'uso di questa funzionalità.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito familiarità con la funzionalità di riscrittura delle intestazioni HTTP, vedere [Create an application gateway and rewrite HTTP headers](tutorial-http-header-rewrite-powershell.md) (Creare un gateway applicazione e riscrivere le intestazioni HTTP) o [Riscrivere le intestazioni HTTP in un gateway applicazione esistente](add-http-header-rewrite-rule-powershell.md)
