---
title: Limiti di dimensioni di richiesta di Web application firewall ed elenchi di esclusione nel gateway applicazione Azure - Portale di Azure
description: Questo articolo fornisce informazioni su limiti di dimensioni di richiesta di Web application firewall e sulla configurazione di elenchi di esclusione nel gateway applicazione con il portale di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/15/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 5ddcdeca41e2f21fa27db25f7e0721c7ef87e491
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65620286"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Limiti delle dimensioni di richiesta di Web application firewall ed elenchi di esclusione

Il Web application firewall del gateway applicazione di Azure (WAF) fornisce la protezione per le Applicazioni Web. Questo articolo descrive i limiti di dimensioni di richiesta di WAF e la configurazione di elenchi di esclusione.

## <a name="waf-request-size-limits"></a>Limiti di dimensioni di richiesta WAF

![Limiti di dimensioni di richiesta](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Web application firewall consente agli utenti di configurare i limiti di dimensioni di richiesta entro i limiti inferiori e superiori. Sono disponibili le seguenti due configurazioni di limiti di dimensioni:

- Il campo di dimensioni del corpo massimo per la richiesta è specificato nel kilobyte e controlli carica limite di dimensioni richiesta complessivo esclusi tutti i file. Questo campo può variare da un minimo di 1 kB a un valore massimo di 128 kB. Il valore predefinito per le dimensioni del corpo della richiesta è 128 kB.
- Il campo limite di caricamento file è specificato in MB e determina le dimensioni massime consentite per il caricamento file. Questo campo può avere un valore minimo di 1 MB e uno massimo di 500 MB per le istanze SKU di grandi dimensioni, mentre lo SKU medio non può superare i 100 MB. Il valore predefinito per il limite di caricamento file è 100 MB.

Web application firewall offre anche una funzione configurabile per attivare o disattivare l'ispezione del corpo della richiesta. Per impostazione predefinita, viene abilitata l'ispezione del corpo della richiesta. Se l'ispezione del corpo della richiesta è stata disattivata, Web Application firewall non valuta il contenuto del corpo del messaggio HTTP. In questi casi, WAF continua applicare le regole WAF su intestazioni, cookie e URI. Se l'ispezione del corpo della richiesta è stata disattivata, il campo dimensioni massime del corpo di richiesta non è applicabile e non può essere impostato. La disattivazione dell'ispezione del corpo della richiesta consente l'invio a WAF di messaggi superiori a 128 KB, ma non vengono analizzate eventuali vulnerabilità nel corpo del messaggio.

## <a name="waf-exclusion-lists"></a>Elenchi di esclusione di WAF

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

Gli elenchi di esclusione di Web Application firewall consentono agli utenti di omettere determinati attributi di richiesta da una valutazione di WAF. Un esempio comune è rappresentato dai token inseriti in Active Directory che vengono usati per l'autenticazione o per i campi password. Tali attributi sono soggetti a contenere caratteri speciali che possono attivare un falso positivo dalle regole di WAF. Una volta che un attributo è stato aggiunto all'elenco di esclusione WAF, non viene preso in considerazione da nessuna regola WAF configurata e attiva. Gli elenchi di esclusione hanno ambito globale.

Gli attributi seguenti possono essere aggiunti agli elenchi di esclusione:

* Intestazioni richiesta
* Cookie della richiesta
* Richiedere il nome dell'attributo (argumenty)

   * Dati multiparte del modulo
   * XML
   * JSON
   * Argomenti di query dell'URL

È possibile specificare un'esatta intestazione di richiesta, un corpo, un cookie o una corrispondenza dell'attributo stringa della query.  In alternativa, è possibile specificare facoltativamente corrispondenze parziali. L'esclusione è sempre in un campo di intestazione, mai sul relativo valore. Le regole di esclusione hanno ambito globale e si applicano a tutte le pagine e regole.

Di seguito sono riportati gli operatori di criteri di corrispondenza supportati:

- **Uguale a**:  operatore viene usato per una corrispondenza esatta. Ad esempio, per selezionare un'intestazione denominata **bearerToken**, usare l'operatore uguale a con il selettore impostato come **bearerToken**.
- **Inizia con**: questo operatore corrisponde a tutti i campi che iniziano con il valore del selettore specificato.
- **Termina con**:  questo operatore corrisponde a tutti i campi di richiesta che terminano con il valore del selettore specificato.
- **Contiene**: questo operatore corrisponde a tutti i campi di richiesta che contengono il valore del selettore specificato.
- **Uguale a qualsiasi**: Questo operatore consente di ricercare tutti i campi di richiesta. * sarà il valore del selettore.

In tutti i casi, la corrispondenza non distingue le maiuscole e le minuscole e le espressioni regolari non sono consentite come selettori.

### <a name="examples"></a>Esempi

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Negli esempi seguenti illustrano l'utilizzo di esclusioni.

### <a name="example-1"></a>Esempio 1

In questo esempio si desidera escludere l'intestazione agente utente. L'intestazione della richiesta dell'agente utente contiene una stringa di caratteristiche che consente ai peer di protocollo di rete identificare il tipo di applicazione, sistema operativo, fornitore di software o versione del software dell'agente utente richiedente software. Per altre informazioni, vedere [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Possono esserci diversi motivi per disattivare la valutazione di questa intestazione. Potrebbero esserci una stringa che il firewall WAF vede e presupporre che lo sia dannoso. Ad esempio, l'attacco SQL classico "x = x" in una stringa. In alcuni casi, può trattarsi di traffico legittimo. Pertanto, potrebbe essere necessario escludere questa intestazione dalla versione di valutazione di Web Application firewall.

Il cmdlet di Azure PowerShell seguente esclude l'intestazione agente utente dalla versione di valutazione:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```

### <a name="example-2"></a>Esempio 2

In questo esempio esclude il valore di *utente* parametro passato nella richiesta tramite l'URL. Pronunciare, ad esempio, che è comune nell'ambiente in uso per il campo utente deve contenere una stringa che il firewall WAF consente di visualizzare come contenuti dannosi, lo blocca.  È possibile escludere in questo caso il parametro user in modo che il firewall WAF non restituisce un valore nel campo.

Il seguente cmdlet Azure PowerShell consente di escludere il parametro user dalla versione di valutazione:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "user"
```
Pertanto, se l'URL **http://www.contoso.com/?user=fdafdasfda** viene passato per il Web Application firewall, non valuta, la stringa **fdafdasfda**.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le impostazioni di WAF, viene descritto come visualizzare i log WAF. Per altre informazioni, vedere [Diagnostica del gateway applicazione](application-gateway-diagnostics.md#diagnostic-logging).
