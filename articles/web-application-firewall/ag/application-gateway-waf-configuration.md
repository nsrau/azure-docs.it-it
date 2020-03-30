---
title: Limiti di dimensioni di richiesta di Web application firewall ed elenchi di esclusione nel gateway applicazione Azure - Portale di Azure
description: In questo articolo vengono fornite informazioni sui limiti delle richieste di Web Application Firewall e sulla configurazione degli elenchi di esclusione nel gateway applicazione con il portale di Azure.This article provides information on Web Application Firewall request size limits and exclusion lists configuration in Application Gateway with the Azure portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7244788bbc7431c7f26363b2852babb72d5697e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526791"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Limiti delle dimensioni delle richieste di Web Application Firewall e degli elenchi di esclusione

Il firewall dell'applicazione Web del gateway applicazione di Azure (WAF) fornisce protezione per le applicazioni Web. Questo articolo descrive i limiti di dimensioni di richiesta di WAF e la configurazione di elenchi di esclusione. Queste impostazioni si trovano nel criterio WAF associato al gateway applicazione. Per altre informazioni sui criteri WAF, vedere Firewall applicazione Web di Azure nel gateway applicazione di Azure e [Creare criteri Firewall applicazione Web per il gateway applicazioneTo](create-waf-policy-ag.md) learn more about WAF Policies, see Azure Web Application Firewall on Azure [Application Gateway](ag-overview.md) and Create Web Application Firewall policies for Application Gateway

## <a name="waf-exclusion-lists"></a>Elenchi di esclusione di WAF

![Limiti di dimensioni di richiesta](../media/application-gateway-waf-configuration/waf-policy.png)

Gli elenchi di esclusione di Web Application firewall consentono agli utenti di omettere determinati attributi di richiesta da una valutazione di WAF. Un esempio comune è rappresentato dai token inseriti in Active Directory che vengono usati per l'autenticazione o per i campi password. Tali attributi sono soggetti a contenere caratteri speciali che possono attivare un falso positivo dalle regole di WAF. Una volta che un attributo è stato aggiunto all'elenco di esclusione WAF, non viene preso in considerazione da nessuna regola WAF configurata e attiva. Gli elenchi di esclusione hanno ambito globale.

Gli attributi seguenti possono essere aggiunti agli elenchi di esclusione in base al nome. I valori del campo scelto non vengono valutati in base alle regole WAF, ma i relativi nomi sono ancora (vedere l'esempio 1 di seguito, il valore dell'intestazione User-Agent viene escluso dalla valutazione WAF). Gli elenchi di esclusione rimuovono l'ispezione del valore del campo.

* Intestazioni richiesta
* Cookie della richiesta
* Il nome dell'attributo request (args) può essere aggiunto come elemento di esclusione, ad esempio:

   * Nome campo modulo
   * Entità XML
   * Entità JSON
   * Argomenti della stringa di query URL

È possibile specificare un'esatta intestazione di richiesta, un corpo, un cookie o una corrispondenza dell'attributo stringa della query.  In alternativa, è possibile specificare facoltativamente corrispondenze parziali. Le regole di esclusione hanno ambito globale e si applicano a tutte le pagine e regole.

Di seguito sono riportati gli operatori di criteri di corrispondenza supportati:

- **Uguale a**: questo operatore viene usato per una corrispondenza esatta. Ad esempio, per selezionare un'intestazione denominata **bearerToken**, usare l'operatore uguale a con il selettore impostato come **bearerToken**.
- **Inizia con**: questo operatore corrisponde a tutti i campi che iniziano con il valore del selettore specificato.
- **Termina con**: questo operatore corrisponde a tutti i campi di richiesta che terminano con il valore del selettore specificato.
- **Contiene**: questo operatore corrisponde a tutti i campi di richiesta che contengono il valore del selettore specificato.
- **Uguale a qualsiasi**: questo operatore corrisponde a tutti i campi di richiesta. : sarà il valore del selettore.

In tutti i casi, la corrispondenza non distingue le maiuscole e le minuscole e le espressioni regolari non sono consentite come selettori.

> [!NOTE]
> Per ulteriori informazioni e assistenza per la risoluzione dei problemi, vedere [Risoluzione dei problemi relativi a WAF](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Esempi

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Negli esempi seguenti viene illustrato l'uso delle esclusioni.

#### <a name="example-1"></a>Esempio 1

In questo esempio, si desidera escludere l'intestazione user-agent. L'intestazione della richiesta agente utente contiene una stringa caratteristica che consente ai peer del protocollo di rete di identificare il tipo di applicazione, il sistema operativo, il fornitore del software o la versione software dell'agente utente software richiedente. Per ulteriori informazioni, vedere [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Ci possono essere un numero qualsiasi di motivi per disabilitare la valutazione di questa intestazione. Ci potrebbe essere una stringa che il WAF vede e presuppone che sia dannoso. Ad esempio, l'attacco SQL classico "x x" in una stringa. In alcuni casi, questo può essere un traffico legittimo. Pertanto potrebbe essere necessario escludere questa intestazione dalla valutazione WAF.

Il cmdlet di Azure PowerShell seguente esclude l'intestazione user-agent dalla valutazione:The following Azure PowerShell cmdlet excludes the user-agent header from evaluation:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>Esempio 2

In questo esempio viene escluso il valore nel parametro *user* passato nella richiesta tramite l'URL. Si supponga, ad esempio, che sia comune nell'ambiente in cui il campo utente contenga una stringa che il WAF visualizza come contenuto dannoso, pertanto viene bloccare.  In questo caso è possibile escludere il parametro utente in modo che WAF non valuti nulla nel campo.

Il cmdlet di Azure PowerShell seguente esclude il parametro utente dalla valutazione:The following Azure PowerShell cmdlet excludes the user parameter from evaluation:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Pertanto, `http://www.contoso.com/?user%281%29=fdafdasfda` se l'URL viene passato al WAF, non verrà valutata la stringa **fdafdasfda**, ma verrà comunque valutato il nome di parametro **user%281%29**. 

## <a name="waf-request-size-limits"></a>Limiti di dimensioni di richiesta WAF



Web application firewall consente agli utenti di configurare i limiti di dimensioni di richiesta entro i limiti inferiori e superiori. Sono disponibili le seguenti due configurazioni di limiti di dimensioni:

- Il campo dimensione massima del corpo della richiesta è specificato in kilobyte e controlla la dimensione complessiva della richiesta limitando qualsiasi caricamento di file. Questo campo può variare da un minimo di 1 kB a un valore massimo di 128 kB. Il valore predefinito per le dimensioni del corpo della richiesta è 128 kB.
- Il campo limite di caricamento file è specificato in MB e determina le dimensioni massime consentite per il caricamento file. Questo campo può avere un valore minimo di 1 MB e i seguenti valori massimi:

   - 100 MB per gateway WAF medi v1
   - 500 MB per gateway WAF di grandi dimensioni v1
   - 750 MB per gateway WAF v2 

 Il valore predefinito per il limite di caricamento file è 100 MB.

Web application firewall offre anche una funzione configurabile per attivare o disattivare l'ispezione del corpo della richiesta. Per impostazione predefinita, viene abilitata l'ispezione del corpo della richiesta. Se l'ispezione del corpo della richiesta è disattivata, WAF non valuta il contenuto del corpo del messaggio HTTP. In questi casi, WAF continua applicare le regole WAF su intestazioni, cookie e URI. Se l'ispezione del corpo della richiesta è stata disattivata, il campo dimensioni massime del corpo di richiesta non è applicabile e non può essere impostato. La disattivazione dell'ispezione del corpo della richiesta consente l'invio a WAF di messaggi superiori a 128 KB, ma non vengono analizzate eventuali vulnerabilità nel corpo del messaggio.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le impostazioni di WAF, viene descritto come visualizzare i log WAF. Per ulteriori informazioni, vedere [Diagnostica gateway applicazione](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).
