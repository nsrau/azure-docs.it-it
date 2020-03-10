---
title: Che cos'è Web Application Firewall di Azure in Frontdoor di Azure?
description: Informazioni sul modo in cui Web Application Firewall di Azure nel servizio Frontdoor di Azure protegge le applicazioni Web da attacchi dannosi.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: c8ff1849668d5effe15b6c25d00f3965a17b8e3e
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915640"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Web Application Firewall di Azure in Frontdoor di Azure

Azure Web Application Firewall (WAF) in Frontdoor di Azure fornisce una protezione centralizzata per le applicazioni Web. WAF protegge i servizi Web da exploit e vulnerabilità comuni. Mantiene una disponibilità elevata del servizio per gli utenti e consente di soddisfare i requisiti di conformità.

WAF in Frontdoor è una soluzione globale centralizzata, distribuita nei percorsi di rete perimetrale di Azure in tutto il mondo. Le applicazioni Web abilitate per WAF ispezionano ogni richiesta in ingresso distribuita da Frontdoor in corrispondenza del perimetro della rete. 

WAF respinge eventuali attacchi dannosi in prossimità delle origini di attacco, impedendone l'accesso alla rete virtuale, offrendo protezione globale su larga scala senza compromettere le prestazioni. I criteri WAF vengono facilmente collegati a qualsiasi profilo di Frontdoor nella sottoscrizione. È possibile implementare rapidamente nuove regole, in modo da rispondere con tempestività ai mutevoli modelli di minacce.

![Web Application Firewall di Azure](../media/overview/wafoverview.png)

## <a name="waf-policy-and-rules"></a>Regole e criteri di WAF

È possibile configurare criteri di WAF e associarli a uno o più front-end di Frontdoor per la protezione. I criteri di WAF sono costituiti da due tipi di regole di sicurezza:

- Regole personalizzate create dal cliente.

- Set di regole gestiti, ovvero raccolte di regole preconfigurate e gestite da Azure.

Quando sono presenti entrambi i tipi, le regole personalizzate vengono elaborate prima di quelle incluse in un set di regole gestito. Una regola è costituita da una condizione di corrispondenza, una priorità e un'azione. I tipi di azione supportati sono: ALLOW, BLOCK, LOG e REDIRECT. È possibile creare criteri completamente personalizzati che soddisfino specifici requisiti di protezione delle applicazioni combinando regole gestite e personalizzate.

Le regole all'interno dei criteri vengono elaborate in ordine di priorità. La priorità è un numero intero univoco che definisce l'ordine di elaborazione delle regole. Un valore intero più basso indica una priorità più alta e quindi le regole con valori di priorità più bassi vengono elaborate prima di quelle con valori più alti. Una volta trovata una corrispondenza per una regola, alla richiesta viene applicata l'azione definita in tale regola. Dopo l'elaborazione di una corrispondenza di questo tipo, le regole con priorità più bassa non vengono elaborate ulteriormente.

Un'applicazione Web distribuita da Frontdoor può avere un solo criterio di WAF associato alla volta. È tuttavia possibile anche una configurazione di Frontdoor senza criteri di WAF associati. Se presenti, i criteri di WAF vengono replicati in tutti i percorsi perimetrali per assicurare la coerenza nei criteri di sicurezza in tutto il mondo.

## <a name="waf-modes"></a>Modalità di WAF

I criteri di WAF possono essere configurati per l'esecuzione nelle due modalità seguenti:

- **Modalità di rilevamento**: quando è attiva la modalità di rilevamento, WAF esegue solo le azioni di monitoraggio e registrazione nei log della richiesta e della regola di WAF corrispondente. È possibile attivare la diagnostica della registrazione per Frontdoor. Se si usa il portale, andare alla sezione **Diagnostica**.

- **Modalità di prevenzione**: In modalità di prevenzione, WAF esegue l'azione specificata se una richiesta corrisponde a una regola. Se viene trovata una corrispondenza, le eventuali altre regole con priorità più bassa non vengono valutate. Tutte le richieste corrispondenti vengono anche registrate nei log di WAF.

## <a name="waf-actions"></a>Azioni di WAF

I clienti di WAF possono scegliere di eseguire una delle azioni seguenti quando una richiesta soddisfa le condizioni di una regola:

- **ALLOW**:  la richiesta passa attraverso WAF e viene inoltrata al back-end. Nessuna regola con priorità più bassa può bloccare questa richiesta.
- **BLOCK**: la richiesta viene bloccata e WAF invia una risposta al client senza inoltrare la richiesta al back-end.
- **LOG**:  la richiesta viene registrata nei log di WAF e WAF continua la valutazione delle regole con priorità più bassa.
- **REDIRECT**: WAF reindirizza la richiesta all'URI specificato. Quest'ultimo è impostato a livello di criterio. Una volta configurate, tutte le richieste corrispondenti all'azione **REDIRECT** verranno inviate a tale URI.

## <a name="waf-rules"></a>Regole di WAF

Un criterio di WAF può essere costituito da due tipi di regole di sicurezza: regole personalizzate, create dal cliente, e set di regole gestiti, ovvero preconfigurati e gestiti da Azure.

### <a name="custom-authored-rules"></a>Regole personalizzate

È possibile configurare regole personalizzate di WAF nel modo seguente:

- **Elenco di indirizzi IP consentiti e bloccati**: è possibile controllare l'accesso alle applicazioni Web in base a un elenco di indirizzi o intervallo di indirizzi IP client. Entrambi i tipi di indirizzi IPv4 e IPv6 sono supportati. Questo elenco può essere configurato in modo da bloccare o consentire le richieste in cui l'indirizzo IP di origine corrisponde a un indirizzo IP nell'elenco.

- **Controllo di accesso geografico**: è possibile controllare l'accesso alle applicazioni Web in base al codice paese associato all'indirizzo IP di un client.

- **Controllo di accesso in base ai parametri HTTP**: è possibile configurare le regole in base alla corrispondenza di stringhe nei parametri della richiesta HTTP/HTTPS,  ad esempio stringhe di query, argomenti POST, URI della richiesta, Intestazione della richiesta e Corpo della richiesta.

- **Controllo di accesso in base al metodo della richiesta**: è possibile configurare le regole in base al metodo HTTP della richiesta, ad esempio GET, PUT o HEAD.

- **Vincolo di dimensione**: è possibile configurare le regole in base alla lunghezza di parti specifiche di una richiesta, ad esempio la stringa di query, l'URI o il corpo della richiesta.

- **Regole di limitazione della frequenza**: Una regola di controllo della frequenza prevede la limitazione di traffico elevato anomalo da qualsiasi indirizzo IP client. È possibile configurare una soglia per il numero di richieste Web consentite da un indirizzo IP client per la durata di un minuto. Questa regola è diversa da una regola personalizzata di tipo Consenti/Blocca basata su un elenco di indirizzi IP, che consente o blocca tutte le richieste provenienti da un indirizzo IP client. I limiti della frequenza possono essere combinati con condizioni di corrispondenza aggiuntive, ad esempio le corrispondenze dei parametri HTTP(S) per il controllo della frequenza a livello granulare.

### <a name="azure-managed-rule-sets"></a>Set di regole gestiti da Azure

I set di regole gestiti da Azure consentono di distribuire in modo semplice la protezione da un set comune di minacce alla sicurezza. Poiché questi set sono gestiti da Azure, le regole vengono opportunamente aggiornate per assicurare la protezione dalle nuove firme di attacco. Il set di regole predefinito gestito da Azure include regole per le categorie di minacce seguenti:

- Scripting intersito
- Attacchi Java
- Attacchi di tipo Local File Inclusion
- Attacchi PHP injection
- Attacchi di tipo Remote Command Execution
- Attacchi di tipo Remote File Inclusion
- Attacchi di tipo Session Fixation
- Protezione dagli attacchi SQL injection
- Attacchi a protocollo

Il numero di versione del set di regole predefinito viene incrementato man mano vengono aggiunte nuove firme di attacco.
Il set di regole predefinito è abilitato per impostazione predefinita nei criteri di WAF quando è attiva la modalità di rilevamento. È possibile disabilitare o abilitare singole regole nel set predefinito in base ai requisiti dell'applicazione. È anche possibile impostare azioni specifiche (ALLOW/BLOCK/REDIRECT/LOG) per ogni regola.

Talvolta può essere necessario omettere determinati attributi delle richieste da una valutazione di WAF. Un esempio comune è rappresentato dai token inseriti in Active Directory che vengono usati per l'autenticazione. È possibile configurare un elenco di esclusione per una regola gestita, un gruppo di regole o per l'intero set di regole.  

L'azione predefinita è BLOCK. È inoltre possibile configurare regole personalizzate nello stesso criterio di WAF se si vuole ignorare una qualsiasi regola preconfigurata nel set predefinito.

Le regole personalizzate vengono sempre applicate prima della valutazione delle regole del set predefinito. Se una richiesta corrisponde a una regola personalizzata, viene applicata l'azione della regola corrispondente. La richiesta viene bloccata o passata al back-end. Non vengono elaborate altre regole personalizzate o le regole include nel set predefinito. È anche possibile rimuovere il set di regole predefinito dai criteri di WAF.

### <a name="bot-protection-rule-set-preview"></a>Set di regole di protezione dai bot (anteprima)

È possibile abilitare un set di regole gestito di protezione dai bot per eseguire azioni personalizzate sulle richieste provenienti da categorie note di bot. 

Sono supportate tre categorie di bot: bot dannosi, legittimi e sconosciuti. Le firme dei bot vengono gestite e aggiornate dinamicamente dalla piattaforma WAF.

I bot dannosi includono bot da indirizzi IP dannosi e bot che hanno falsificato le proprie identità. Gli indirizzi IP dannosi provengono dal feed di Microsoft Threat Intelligence e vengono aggiornati ogni ora. La soluzione Microsoft Threat Intelligence, basata su [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence), viene usata da più servizi, tra cui Centro sicurezza di Azure.

I bot legittimi includono i motori di ricerca convalidati. Le categorie sconosciute includono altri gruppi di bot che si sono identificati come bot. Ad esempio, market analyzer, feed fetcher e agenti di raccolta dati. 

I bot sconosciuti sono classificati tramite agenti utente pubblicati senza convalida aggiuntiva. È possibile impostare azioni personalizzate per bloccare, consentire, registrare o reindirizzare diversi tipi di bot.

![Set di regole di protezione dai bot](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> Il set di regole di protezione dai bot è attualmente disponibile in anteprima pubblica e con un contratto di servizio di anteprima. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.  Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se la protezione dai bot è abilitata, le richieste in ingresso che corrispondono a regole di bot vengono registrate nel log FrontdoorWebApplicationFirewallLog. È possibile accedere ai log di WAF da un account di archiviazione, da un hub eventi o da Log Analytics.

## <a name="configuration"></a>Configurazione

È possibile configurare e distribuire tutti i tipi di regole di WAF tramite il portale di Azure, le API REST, i modelli di Azure Resource Manager e Azure PowerShell.

## <a name="monitoring"></a>Monitoraggio

Il monitoraggio per WAF in Frontdoor è integrato con Monitoraggio di Azure per tenere traccia degli avvisi e monitorare con facilità le tendenze del traffico.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Web Application Firewall di Azure nel gateway applicazione di Azure](../ag/ag-overview.md)