---
title: Limiti di dimensioni di richiesta di Web application firewall ed elenchi di esclusione nel gateway applicazione Azure - Portale di Azure
description: Questo articolo fornisce informazioni su limiti di dimensioni di richiesta di Web application firewall e sulla configurazione di elenchi di esclusione nel gateway applicazione con il portale di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 7/17/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e13884fb0c39beabf543fd04c9808373a68ec26a
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304310"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Limiti delle dimensioni di richiesta di Web application firewall ed elenchi di esclusione

Il Web application firewall del gateway applicazione di Azure (WAF) fornisce la protezione per le Applicazioni Web. Questo articolo descrive i limiti di dimensioni di richiesta di WAF e la configurazione di elenchi di esclusione.

## <a name="waf-request-size-limits"></a>Limiti di dimensioni di richiesta WAF

![Limiti di dimensioni di richiesta](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Web application firewall consente agli utenti di configurare i limiti di dimensioni di richiesta entro i limiti inferiori e superiori. Sono disponibili le seguenti due configurazioni di limiti di dimensioni:

- Il campo dimensioni massime del corpo della richiesta è specificato in kilobyte e controlla il limite complessivo delle dimensioni della richiesta, esclusi i caricamenti di file. Questo campo può variare da un minimo di 1 kB a un valore massimo di 128 kB. Il valore predefinito per le dimensioni del corpo della richiesta è 128 kB.
- Il campo limite di caricamento file è specificato in MB e determina le dimensioni massime consentite per il caricamento file. Questo campo può avere un valore minimo di 1 MB e uno massimo di 500 MB per le istanze SKU di grandi dimensioni, mentre lo SKU medio non può superare i 100 MB. Il valore predefinito per il limite di caricamento file è 100 MB.

Web application firewall offre anche una funzione configurabile per attivare o disattivare l'ispezione del corpo della richiesta. Per impostazione predefinita, viene abilitata l'ispezione del corpo della richiesta. Se l'ispezione del corpo della richiesta è disattivata, WAF non valuta il contenuto del corpo del messaggio HTTP. In questi casi, WAF continua applicare le regole WAF su intestazioni, cookie e URI. Se l'ispezione del corpo della richiesta è stata disattivata, il campo dimensioni massime del corpo di richiesta non è applicabile e non può essere impostato. La disattivazione dell'ispezione del corpo della richiesta consente l'invio a WAF di messaggi superiori a 128 KB, ma non vengono analizzate eventuali vulnerabilità nel corpo del messaggio.

## <a name="waf-exclusion-lists"></a>Elenchi di esclusione di WAF

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

Gli elenchi di esclusione di Web Application firewall consentono agli utenti di omettere determinati attributi di richiesta da una valutazione di WAF. Un esempio comune è rappresentato dai token inseriti in Active Directory che vengono usati per l'autenticazione o per i campi password. Tali attributi sono soggetti a contenere caratteri speciali che possono attivare un falso positivo dalle regole di WAF. Una volta che un attributo è stato aggiunto all'elenco di esclusione WAF, non viene preso in considerazione da nessuna regola WAF configurata e attiva. Gli elenchi di esclusione hanno ambito globale.

Gli attributi seguenti possono essere aggiunti agli elenchi di esclusione in base al nome. I valori del campo scelto non vengono valutati rispetto alle regole WAF, ma i relativi nomi sono ancora (vedere l'esempio 1 riportato di seguito, il valore dell'intestazione User-Agent è escluso dalla valutazione WAF). Gli elenchi di esclusione rimuovono l'ispezione del valore del campo.

* Intestazioni richiesta
* Cookie della richiesta
* Il nome dell'attributo della richiesta (args) può essere aggiunto come elemento di esclusione, ad esempio:

   * Nome campo modulo
   * Entità XML
   * Entità JSON
   * Argomenti della stringa di query dell'URL

È possibile specificare un'esatta intestazione di richiesta, un corpo, un cookie o una corrispondenza dell'attributo stringa della query.  In alternativa, è possibile specificare facoltativamente corrispondenze parziali. Le regole di esclusione hanno ambito globale e si applicano a tutte le pagine e regole.

Di seguito sono riportati gli operatori di criteri di corrispondenza supportati:

- **Uguale a**:  operatore viene usato per una corrispondenza esatta. Ad esempio, per selezionare un'intestazione denominata **bearerToken**, usare l'operatore uguale a con il selettore impostato come **bearerToken**.
- **Inizia con**: questo operatore corrisponde a tutti i campi che iniziano con il valore del selettore specificato.
- **Termina con**:  questo operatore corrisponde a tutti i campi di richiesta che terminano con il valore del selettore specificato.
- **Contiene**: questo operatore corrisponde a tutti i campi di richiesta che contengono il valore del selettore specificato.
- **Uguale a any**: Questo operatore corrisponde a tutti i campi della richiesta. * sarà il valore del selettore.

In tutti i casi, la corrispondenza non distingue le maiuscole e le minuscole e le espressioni regolari non sono consentite come selettori.

> [!NOTE]
> Per ulteriori informazioni e per la risoluzione dei problemi, vedere [risoluzione dei problemi di WAF](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Esempi

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Negli esempi seguenti viene illustrato l'utilizzo delle esclusioni.

### <a name="example-1"></a>Esempio 1

In questo esempio si vuole escludere l'intestazione User-Agent. L'intestazione della richiesta dell'agente utente contiene una stringa caratteristica che consente ai peer del protocollo di rete di identificare il tipo di applicazione, il sistema operativo, il fornitore del software o la versione software dell'agente utente del software richiedente. Per ulteriori informazioni, vedere [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

La valutazione di questa intestazione può essere dovuta a diversi motivi. Potrebbe essere presente una stringa che il WAF vede e presuppone che sia dannoso. Ad esempio, l'attacco SQL classico "x = x" in una stringa. In alcuni casi, può trattarsi di un traffico legittimo. Potrebbe quindi essere necessario escludere questa intestazione dalla versione di valutazione di WAF.

Il cmdlet di Azure PowerShell seguente esclude l'intestazione User-Agent dalla valutazione:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```

### <a name="example-2"></a>Esempio 2

In questo esempio viene escluso il valore del parametro *User* passato nella richiesta tramite l'URL. Si immagini, ad esempio, che nel proprio ambiente il campo utente includa una stringa che WAF Visualizza come contenuto dannoso, quindi la blocca.  In questo caso, è possibile escludere il parametro User, in modo che il WAF non valuti alcun valore nel campo.

Il cmdlet di Azure PowerShell seguente esclude il parametro User dalla valutazione:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "user"
```
Quindi, se l' **http://www.contoso.com/?user=fdafdasfda** URL viene passato a WAF, non valuterà la stringa **fdafdasfda**.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le impostazioni di WAF, viene descritto come visualizzare i log WAF. Per altre informazioni, vedere [Diagnostica del gateway applicazione](application-gateway-diagnostics.md#diagnostic-logging).
