---
title: Ottimizzazione del Web Application Firewall (WAF) per Azure front door
description: Questo articolo illustra come ottimizzare WAF per la porta anteriore.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: 4c710792dd7966fad76b33954fdf7c2253cf18f0
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488239"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Ottimizzazione del Web Application Firewall (WAF) per Azure front door
 
Il set di regole predefinite gestito da Azure è basato sul [set di regole di base di OWASP (CRS)](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) ed è progettato per essere limitato. Si prevede spesso che le regole WAF debbano essere ottimizzate in base alle esigenze specifiche dell'applicazione o dell'organizzazione mediante WAF. Questa operazione viene in genere eseguita definendo le esclusioni delle regole, creando regole personalizzate e persino disabilitando le regole che potrebbero causare problemi o falsi positivi. Se le richieste che devono passare attraverso il Web Application Firewall (WAF) sono bloccate, è possibile eseguire alcune operazioni.

Prima di tutto, assicurarsi di aver letto la [Panoramica di WAF](afds-overview.md) e il [criterio WAF per](waf-front-door-create-portal.md) i documenti della porta anteriore. Assicurarsi anche di aver abilitato il [monitoraggio e la registrazione di WAF](waf-front-door-monitor.md). Questi articoli illustrano il funzionamento delle funzioni WAF, il modo in cui il set di regole WAF funziona e come accedere ai log di WAF.
 
## <a name="understanding-waf-logs"></a>Informazioni sui log di WAF
 
Lo scopo dei log WAF è mostrare ogni richiesta corrispondente o bloccata dal WAF. Si tratta di una raccolta di tutte le richieste valutate corrispondenti o bloccate. Se si nota che WAF blocca una richiesta che non dovrebbe (un falso positivo), è possibile eseguire alcune operazioni. Per prima cosa, limitare e trovare la richiesta specifica. Se lo si desidera, è possibile [configurare un messaggio di risposta personalizzato](./waf-front-door-configure-custom-response-code.md) in modo da includere il `trackingReference` campo per identificare facilmente l'evento ed eseguire una query di log su tale valore specifico. Esaminare i log per trovare l'URI specifico, il timestamp o l'indirizzo IP client della richiesta. Quando si trovano le voci di log correlate, è possibile iniziare a agire su falsi positivi. 
 
Si immagini, ad esempio, di avere un traffico legittimo contenente la stringa `1=1` che si vuole passare attraverso la WAF. Ecco come appare la richiesta:

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

Se si prova la richiesta, il WAF blocca il traffico che contiene la stringa *1 = 1* in qualsiasi parametro o campo. Si tratta di una stringa spesso associata a un attacco SQL injection. È possibile esaminare i log e visualizzare il timestamp della richiesta e le regole bloccate o corrispondenti.
 
Nell'esempio seguente viene esaminato un `FrontdoorWebApplicationFirewallLog` log generato a causa di una corrispondenza della regola.
 
Nel campo "requestUri" è possibile vedere che la richiesta è stata eseguita in `/api/Feedbacks/` modo specifico. L'ID della regola verrà trovato `942110` nel campo "RuleName". Conoscendo l'ID regola, è possibile accedere al [repository ufficiale del set di regole OWASP ModSecurity Core](https://github.com/coreruleset/coreruleset) e cercare in base a tale [ID regola](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) di esaminare il codice e conoscere esattamente le corrispondenze di questa regola. 
 
Quindi, selezionando il `action` campo si noterà che questa regola è impostata per bloccare le richieste al momento della corrispondenza e si conferma che la richiesta è stata effettivamente bloccata da WAF perché `policyMode` è impostato su `prevention` . 
 
Verranno ora controllate le informazioni nel `details` campo. Qui è possibile visualizzare le `matchVariableName` `matchVariableValue` informazioni e. Si apprende che questa regola è stata attivata perché un utente ha inserito *1 = 1* nel `comment` campo dell'app Web.
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
È anche possibile controllare i log di accesso per espandere le informazioni su un determinato evento WAF. Di seguito viene esaminato il `FrontdoorAccessLog` log generato come risposta all'evento precedente.
 
È possibile notare che si tratta di log correlati, in base al `trackingReference` valore. Tra i vari campi che forniscono informazioni generali, ad esempio `userAgent` e `clientIP` , viene chiamato attenzione ai `httpStatusCode` `httpStatusDetails` campi e. Qui è possibile verificare che il client abbia ricevuto una risposta HTTP 403, che conferma assolutamente che la richiesta è stata negata e bloccata. 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>Risoluzione di falsi positivi
 
Per prendere una decisione consapevole sulla gestione di un falso positivo, è importante acquisire familiarità con le tecnologie utilizzate dall'applicazione. Ad esempio, supponiamo che non esista un server SQL nello stack della tecnologia e che si stiano ottenendo falsi positivi correlati a tali regole. La disabilitazione di tali regole non indebolisce necessariamente la sicurezza. 

Con queste informazioni e la conoscenza della regola 942110 è quella che corrisponde alla `1=1` stringa nell'esempio, possiamo eseguire alcune operazioni per arrestare la richiesta legittima da bloccare:
 
* USA elenchi di esclusione
  * Per ulteriori informazioni sugli elenchi di esclusione, vedere [Web Application Firewall (WAF) con gli elenchi di esclusione del servizio front door](waf-front-door-exclusion.md) . 
* Modificare le azioni di WAF
  * Vedere [azioni WAF](afds-overview.md#waf-actions) per altre informazioni sulle azioni che è possibile intraprendere quando una richiesta soddisfa le condizioni di una regola.
* Usa regole personalizzate
  * Per altre informazioni sulle regole personalizzate, vedere [regole personalizzate per il Web Application Firewall con sportello anteriore di Azure](waf-front-door-custom-rules.md) .
* Disabilitare le regole 

> [!TIP]
> Quando si seleziona un approccio per consentire richieste legittime tramite il WAF, provare a eseguire questa operazione in modo più restrittivo possibile. Ad esempio, è preferibile usare un elenco di esclusioni anziché disabilitare completamente una regola.

### <a name="using-exclusion-lists"></a>Utilizzo degli elenchi di esclusione

Un vantaggio dell'uso di un elenco di esclusione è che solo la variabile di corrispondenza che si sceglie di escludere non verrà più controllata per la richiesta specificata. Ovvero è possibile scegliere tra intestazioni di richiesta specifiche, cookie di richiesta, argomenti della stringa di query o argomenti post del corpo della richiesta da escludere se viene soddisfatta una determinata condizione, anziché escludere l'intera richiesta da ispezionare. Le altre variabili non specificate della richiesta verranno comunque controllate normalmente.
 
È importante tenere presente che le esclusioni sono un'impostazione globale. Ciò significa che l'esclusione configurata verrà applicata a tutto il traffico che passa attraverso il WAF, non solo a un'app Web o a un URI specifico. Ad esempio, potrebbe trattarsi di un problema se *1 = 1* è una richiesta valida nel corpo di una determinata app Web, ma non per altre con gli stessi criteri di WAF. Se è opportuno usare elenchi di esclusioni diversi per applicazioni diverse, è consigliabile usare criteri WAF diversi per ogni applicazione e applicarli al front-end di ogni applicazione.
 
Quando si configurano gli elenchi di esclusione per le regole gestite, è possibile scegliere di escludere tutte le regole in un set di regole, tutte le regole all'interno di un gruppo di regole o una singola regola. È possibile configurare un elenco di esclusioni usando [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-4.7.0&viewFallbackFrom=azps-3.5.0), l'interfaccia della riga di comando di [Azure](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext_front_door_az_network_front_door_waf_policy_managed_rules_exclusion_add), l' [API REST](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)o la portale di Azure.

* Esclusioni a livello di regola
  * L'applicazione di esclusioni a livello di regola significa che le esclusioni specificate non verranno analizzate solo in base a tale regola, mentre verranno comunque analizzate da tutte le altre regole del set di regole. Questo è il livello più granulare per le esclusioni e può essere usato per ottimizzare il set di regole gestite in base alle informazioni trovate nei log di WAF durante la risoluzione dei problemi relativi a un evento.
* Esclusioni a livello di gruppo di regole
  * L'applicazione di esclusioni a livello di gruppo di regole significa che le esclusioni specificate non verranno analizzate rispetto a tale set specifico di tipi di regole. Se ad esempio si seleziona *SQLI* come gruppo di regole escluse, le esclusioni delle richieste definite non verranno controllate da nessuna delle regole specifiche di SQLI, ma verranno comunque controllate da regole in altri gruppi, ad esempio *php*, *RFI* o *XSS*. Questo tipo di esclusione può essere utile quando si è certi che l'applicazione non è soggetta a specifici tipi di attacchi. Ad esempio, un'applicazione che non dispone di alcun database SQL potrebbe avere tutte le regole *SQLI* escluse senza che sia dannosa al livello di sicurezza.
* Esclusioni a livello di set di regole 
  * L'applicazione di esclusioni a livello di set di regole indica che le esclusioni specificate non verranno analizzate in base alle regole di sicurezza disponibili in tale set di regole. Si tratta di un'esclusione completa, quindi deve essere usata con cautela.

In questo esempio si eseguirà un'esclusione a livello più granulare (applicando l'esclusione a una singola regola) e si sta cercando di escludere il **nome del corpo della richiesta** della variabile di corrispondenza che contiene `comment` . Questa operazione è evidente perché è possibile visualizzare i dettagli della variabile di corrispondenza nel registro del firewall: `"matchVariableName": "PostParamValue:comment"` . L'attributo è `comment`. È anche possibile trovare il nome di questo attributo in altri modi, vedere [ricerca di nomi di attributi di richiesta](#finding-request-attribute-names).

![Regole di esclusione](../media/waf-front-door-tuning/exclusion-rules.png)

![Esclusione della regola per una regola specifica](../media/waf-front-door-tuning/exclusion-rule.png)

Occasionalmente, esistono casi in cui i parametri specifici vengono passati in WAF in modo che potrebbero non essere intuitivi. Ad esempio, è presente un token che viene passato quando si esegue l'autenticazione con Azure Active Directory. Questo token, `__RequestVerificationToken` , viene in genere passato come cookie di richiesta. Tuttavia, in alcuni casi in cui i cookie sono disabilitati, questo token viene anche passato come argomento request post. Per questo motivo, per risolvere Azure AD token falsi positivi, è necessario assicurarsi che `__RequestVerificationToken` venga aggiunto all'elenco di esclusione sia per che per `RequestCookieNames` `RequestBodyPostArgsNames` .

Le esclusioni in un nome di campo (*selector*) significa che il valore non verrà più valutato da WAF. Tuttavia, il nome del campo continua a essere valutato e in rari casi può corrispondere a una regola WAF e attivare un'azione.

![Esclusione di regole per set di regole](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>Modifica delle azioni WAF

Un altro modo per gestire il comportamento delle regole WAF è scegliere l'azione che verrà eseguita quando una richiesta corrisponde alle condizioni di una regola. Le azioni disponibili sono: [Allow, Block, log e redirect](afds-overview.md#waf-actions).

In questo esempio il *blocco* di azioni predefinito è stato modificato in azione *Log* sulla regola 942110. In questo modo il WAF registrerà la richiesta e continuerà a valutare la stessa richiesta rispetto alle regole di priorità più bassa rimanenti.

![Azioni di WAF](../media/waf-front-door-tuning/actions.png)

Dopo aver eseguito la stessa richiesta, è possibile fare riferimento ai log. si noterà che questa richiesta corrisponde alla regola con ID 942110 e che `action_s` ora il campo indica il *Registro* anziché il *blocco*. La query di log è stata quindi espansa per includere le `trackingReference_s` informazioni e vedere che cosa è successo con questa richiesta.

![Log che Mostra più corrispondenze di regole](../media/waf-front-door-tuning/actions-log.png)

È interessante notare che si verifica una corrispondenza della regola SQLI diversa, in millisecondi dopo l'elaborazione dell'ID regola 942110. La stessa richiesta corrisponde all'ID regola 942310 e questa volta è stato attivato il *blocco* azione predefinito.

Un altro vantaggio dell'uso dell'azione *log* durante l'ottimizzazione o la risoluzione dei problemi di WAF è la possibilità di identificare se più regole all'interno di un gruppo di regole specifico corrispondono e bloccano una determinata richiesta. È quindi possibile creare le esclusioni al livello appropriato, ad esempio a livello di regola o di gruppo di regole. 

### <a name="using-custom-rules"></a>Uso di regole personalizzate

Dopo aver identificato la causa della corrispondenza di una regola WAF, è possibile usare regole personalizzate per modificare il modo in cui WAF risponde all'evento. Le regole personalizzate vengono elaborate prima delle regole gestite, possono contenere più di una condizione e le relative azioni possono essere [Allow, Deny, log o redirect](afds-overview.md#waf-actions). Quando esiste una regola corrispondente, il motore WAF interrompe l'elaborazione. Questo significa che altre regole personalizzate con priorità più bassa e regole gestite non vengono più eseguite.

Nell'esempio seguente è stata creata una regola personalizzata con due condizioni. La prima condizione è la ricerca del `comment` valore nel corpo della richiesta. La seconda condizione è la ricerca del `/api/Feedbacks/` valore nell'URI della richiesta.

L'uso di una regola personalizzata consente di ottenere la massima granularità quando si ottimizzano le regole di WAF e per la gestione di falsi positivi. In questo caso, l'azione non viene eseguita solo in base al `comment` valore del corpo della richiesta, che può esistere in più siti o app con lo stesso criterio WAF. Se si include anche un'altra condizione per trovare una corrispondenza con un URI di richiesta specifico `/api/Feedbacks/` , la regola personalizzata si applica a questo caso di utilizzo esplicito che è stato esaminato. In questo modo si garantisce che lo stesso attacco, se eseguito in condizioni diverse, verrebbe comunque controllato e impedito dal motore WAF.

![File di log](../media/waf-front-door-tuning/custom-rule.png)

Quando si Esplora il registro, è possibile osservare che il `ruleName_s` campo contiene il nome assegnato alla regola personalizzata creata: `redirectcomment` . Nel `action_s` campo è possibile vedere che è stata eseguita l'azione di *Reindirizzamento* per questo evento. Nel `details_matches_s` campo è possibile visualizzare i dettagli di entrambe le condizioni.

### <a name="disabling-rules"></a>Disabilitazione di regole

Un altro modo per aggirare un falso positivo è disabilitare la regola corrispondente all'input considerato dannoso dal WAF. Poiché i log WAF sono stati analizzati e la regola è stata limitata a 942110, è possibile disabilitarla nel portale di Azure. Vedere [personalizzare le regole del Web Application Firewall usando il portale di Azure](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules).
 
La disabilitazione di una regola costituisce un vantaggio quando si è certi che tutte le richieste che soddisfano la condizione specifica sono in realtà richieste legittime o quando si è certi che la regola non è applicabile all'ambiente (ad esempio, la disabilitazione di una regola SQL injection perché sono presenti backend non SQL). 
 
Tuttavia, la disabilitazione di una regola è un'impostazione globale che si applica a tutti gli host front-end associati al criterio WAF. Quando si sceglie di disabilitare una regola, è possibile che si stiano lasciando le vulnerabilità esposte senza protezione o rilevamento per tutti gli altri host front-end associati al criterio WAF.
 
Se si desidera utilizzare Azure PowerShell per disabilitare una regola gestita, vedere la [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject?preserve-view=true&view=azps-4.7.0) documentazione dell'oggetto. Se si vuole usare l'interfaccia della riga di comando di Azure, vedere la [`az network front-door waf-policy managed-rules override`](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/override?preserve-view=true&view=azure-cli-latest) documentazione.

![Regole di WAF](../media/waf-front-door-tuning/waf-rules.png)

## <a name="finding-request-fields"></a>Ricerca di campi di richiesta

Usando un proxy del browser come [Fiddler](https://www.telerik.com/fiddler), è possibile esaminare le singole richieste e determinare quali campi specifici di una pagina Web vengono chiamati. Questa operazione è utile quando è necessario escludere determinati campi dall'ispezione usando elenchi di esclusioni in WAF.

### <a name="finding-request-attribute-names"></a>Ricerca dei nomi degli attributi di richiesta
 
In questo esempio, è possibile visualizzare il campo in cui `1=1` è stata immessa la stringa denominata `comment` . Questi dati sono stati passati nel corpo di una richiesta POST.

![Richiesta Fiddler che mostra il corpo](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

Si tratta di un campo che è possibile escludere. Per ulteriori informazioni sugli elenchi di esclusione, vedere l'articolo relativo agli [elenchi di esclusione di Web Application Firewall](./waf-front-door-exclusion.md). È possibile escludere la valutazione in questo caso configurando l'esclusione seguente:

![Regola di esclusione](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

È anche possibile esaminare i log del firewall per ottenere le informazioni necessarie per visualizzare gli elementi da aggiungere all'elenco di esclusione. Per abilitare la registrazione, vedere [monitoraggio delle metriche e dei log nel front-end di Azure](./waf-front-door-monitor.md).

Esaminare il log del firewall nel `PT1H.json` file per l'ora in cui si è verificata la richiesta che si desidera esaminare. `PT1H.json` i file sono disponibili nei contenitori dell'account di archiviazione in cui `FrontDoorWebApplicationFirewallLog` `FrontDoorAccessLog` sono archiviati i log di diagnostica e.

In questo esempio, è possibile visualizzare la regola che ha bloccato la richiesta (con lo stesso riferimento alla transazione) e si è verificata esattamente allo stesso tempo:

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

Con la conoscenza del funzionamento dei set di regole gestiti da Azure (vedere [Web Application Firewall in Azure front door](afds-overview.md)), si sa che la regola con la proprietà *Action: Block* è bloccata in base ai dati corrispondenti nel corpo della richiesta. È possibile vedere nei dettagli che corrisponde a un modello ( `1=1` ) e il campo è denominato `comment` . Seguire gli stessi passaggi precedenti per escludere il nome dell'argomento del corpo della richiesta che contiene `comment` .

### <a name="finding-request-header-names"></a>Ricerca dei nomi delle intestazioni di richiesta

Fiddler è uno strumento utile ancora una volta per trovare i nomi delle intestazioni di richiesta. Nello screenshot seguente è possibile visualizzare le intestazioni per questa richiesta GET, che includono Content-Type, User-Agent e così via. È anche possibile usare le intestazioni delle richieste per creare esclusioni e regole personalizzate in WAF.

![Intestazione che mostra la richiesta Fiddler](../media/waf-front-door-tuning/fiddler-request-header-name.png)

Un altro modo per visualizzare le intestazioni di richiesta e risposta consiste nel cercare all'interno degli strumenti di sviluppo del browser, ad esempio Edge o Chrome. È possibile premere F12 o fare clic con il pulsante destro del mouse su > **controllare**  ->  **strumenti di sviluppo**, quindi selezionare la scheda **rete** . caricare una pagina Web, quindi fare clic sulla richiesta che si desidera controllare.

![Richiesta di controllo di rete](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>Ricerca dei nomi dei cookie di richiesta

Se la richiesta contiene cookie, è possibile selezionare la scheda cookie per visualizzarli in Fiddler. Le informazioni sui cookie possono essere utilizzate anche per creare esclusioni o regole personalizzate in WAF.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [web application firewall di Azure](../overview.md).
- Informazioni su come [creare una Frontdoor](../../frontdoor/quickstart-create-front-door.md).