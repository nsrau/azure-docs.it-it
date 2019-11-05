---
title: Che cos'è Azure web application firewall per la porta anteriore di Azure? (Anteprima)
description: Informazioni sul modo in cui Azure web application firewall per il servizio front-end di Azure protegge le applicazioni Web da attacchi dannosi.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 624a5af59a39cb0ebf647f549f3a40ed56f78b9e
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73549240"
---
# <a name="what-is-azure-web-application-firewall-for-azure-front-door"></a>Che cos'è Azure web application firewall per la porta anteriore di Azure?

Azure web application firewall (WAF) fornisce una protezione centralizzata per le applicazioni Web che vengono recapitate a livello globale usando Frontdoor di Azure. È progettato e gestito per difendere i servizi Web da vulnerabilità comuni e per mantenere una disponibilità elevata del servizio per gli utenti, oltre che per aiutare a soddisfare i requisiti di conformità.


Le applicazioni Web sono sempre più le destinazioni di attacchi dannosi, ad esempio attacchi di tipo Denial of Service, attacchi intrusivi nel codice SQL e attacchi di scripting tra siti. Questi attacchi dannosi possono causare interruzioni del servizio e perdita di dati, rappresentano una minaccia significativa per i proprietari di applicazioni Web.

Impedire questo tipo di attacchi nel codice dell'applicazione può risultare un'operazione complessa e potrebbe richiedere una manutenzione rigorosa, l'applicazione di patch e il monitoraggio a più livelli della topologia dell'applicazione. Un Web application firewall centralizzato semplifica notevolmente la gestione della sicurezza e offre agli amministratori delle applicazioni migliori garanzie contro le minacce o le intrusioni. Inoltre, una soluzione WAF può rispondere a una minaccia per la sicurezza più rapidamente applicando un'applicazione di patch a una vulnerabilità nota in una posizione centrale, anziché proteggere ogni singola applicazione Web.

WAF per la porta anteriore è una soluzione globale e centralizzata. Viene distribuito in percorsi di rete perimetrale di Azure in tutto il mondo e ogni richiesta in ingresso per un'applicazione Web abilitata per WAF fornita da sportello anteriore viene ispezionata al perimetro della rete. Questo consente a WAF di impedire gli attacchi dannosi vicini alle origini di attacco, prima di immettere la rete virtuale e offre protezione globale su larga scala senza sacrificare le prestazioni. Un criterio WAF può essere facilmente collegato a qualsiasi profilo di porta anteriore nella sottoscrizione e le nuove regole possono essere distribuite in pochi minuti, consentendo di rispondere rapidamente ai mutevoli modelli di minaccia.

![web application firewall di Azure](./media/waf-overview/web-application-firewall-overview.png)

Azure WAF può anche essere abilitato con il gateway applicazione. Per ulteriori informazioni, vedere [Web Application Firewall](../application-gateway/waf-overview.md).

## <a name="waf-policy-and-rules"></a>Regole e criteri di WAF

È possibile configurare un criterio WAF e associare tale criterio a uno o più front-end front-end per la protezione. Un criterio WAF è costituito da due tipi di regole di sicurezza:

- regole personalizzate create dal cliente.

- set di regole gestite che sono una raccolta di regole preconfigurate gestite da Azure.

Quando sono presenti entrambe, le regole personalizzate vengono elaborate prima di elaborare le regole in un set di regole gestito. Una regola è costituita da una condizione di corrispondenza, una priorità e un'azione. I tipi di azione supportati sono: ALLOW, BLOCK, LOG e redirect. È possibile creare criteri completamente personalizzati che soddisfino i requisiti di protezione dell'applicazione specifici combinando regole gestite e personalizzate.

Le regole all'interno di un criterio vengono elaborate in ordine di priorità in cui Priority è un intero univoco che definisce l'ordine di elaborazione delle regole. Un valore integer più piccolo indica una priorità più alta e questi vengono valutati prima delle regole con un valore integer più elevato. Quando viene trovata una corrispondenza per una regola, alla richiesta viene applicata l'azione corrispondente definita nella regola. Una volta elaborata tale corrispondenza, le regole con priorità più bassa non vengono elaborate ulteriormente.

Un'applicazione Web distribuita dalla porta anteriore può avere un solo criterio WAF associato alla volta. Tuttavia, è possibile avere una configurazione di sportello anteriore senza alcun criterio WAF associato. Se è presente un criterio WAF, viene replicato in tutti i percorsi perimetrali per garantire la coerenza nei criteri di sicurezza in tutto il mondo.

## <a name="waf-modes"></a>Modalità WAF

Il criterio WAF può essere configurato per l'esecuzione nelle due modalità seguenti:

- **Modalità di rilevamento:** Quando viene eseguita in modalità di rilevamento, WAF non esegue altre azioni diverse dai monitoraggi e registra la richiesta e la relativa regola WAF corrispondente nei log di WAF. È possibile attivare la diagnostica della registrazione per lo sportello anteriore (quando si usa il portale, questa operazione può essere eseguita passando alla sezione relativa alla **diagnostica** nella portale di Azure).

- **Modalità di prevenzione:** Quando è configurato per l'esecuzione in modalità di prevenzione, WAF esegue l'azione specificata se una richiesta corrisponde a una regola e se viene trovata una corrispondenza, non vengono valutate altre regole con priorità più bassa. Tutte le richieste corrispondenti vengono registrate anche nei registri WAF.

## <a name="waf-actions"></a>Azioni WAF

I clienti di WAF possono scegliere di eseguire una delle azioni quando una richiesta soddisfa le condizioni di una regola:

- **Consenti:**  La richiesta passa attraverso il WAF e viene inoltrata al back-end. Nessuna regola di priorità più bassa può bloccare questa richiesta.
- **Blocca:** La richiesta è bloccata e WAF invia una risposta al client senza inoltrare la richiesta al back-end.
- **Log:**  La richiesta viene registrata nei log WAF e WAF continua la valutazione delle regole con priorità più bassa.
- **Reindirizzamento:** WAF reindirizza la richiesta all'URI specificato. L'URI specificato è un'impostazione a livello di criteri. Una volta configurate, tutte le richieste che corrispondono all'azione di **Reindirizzamento** verranno inviate a tale URI.

## <a name="waf-rules"></a>Regole WAF

Un criterio WAF può essere costituito da due tipi di regole di sicurezza: regole personalizzate, create dal cliente e RuleSet gestiti, un set di regole preconfigurato gestito da Azure.

### <a name="custom-authored-rules"></a>Regole personalizzate create

È possibile configurare le regole personalizzate WAF come segue:

- Elenco **di indirizzi IP consentiti e bloccati:** È possibile configurare regole personalizzate per controllare l'accesso alle applicazioni Web in base a un elenco di indirizzi IP o intervalli di indirizzi IP del client. Sono supportati sia i tipi di indirizzi IPv4 che IPv6. Questo elenco può essere configurato in modo da bloccare o consentire le richieste in cui l'indirizzo IP di origine corrisponde a un indirizzo IP nell'elenco. Un indirizzo IP client può essere diverso dall'indirizzo IP WAF osserva, ad esempio, quando un client accede a WAF tramite un proxy. È possibile creare [regole di restrizione IP](https://docs.microsoft.com/azure/frontdoor/waf-front-door-configure-ip-restriction) basate su indirizzi IP client (IndirizzoRemoto) o indirizzi IP visualizzati da WAF (SocketAddr). La configurazione di una regola di restrizione IP SocketAddr è attualmente supportata con PowerShell e l'interfaccia della riga di comando di Azure

- **Controllo degli accessi in base al geografico:** È possibile configurare regole personalizzate per controllare l'accesso alle applicazioni Web in base al codice paese associato all'indirizzo IP di un client.

- **Controllo degli accessi in base ai parametri http:** È possibile configurare regole personalizzate in base alle stringhe corrispondenti a parametri di richiesta HTTP/HTTPS, ad esempio stringhe di query, argomenti POST, URI della richiesta, intestazione della richiesta e corpo della richiesta.

- **Controllo degli accessi in base al metodo di richiesta:** È possibile configurare regole personalizzate in base al metodo di richiesta HTTP della richiesta, ad esempio GET, PUT o HEAD.

- **Vincolo Size:** È possibile configurare regole personalizzate in base alle lunghezze di parti specifiche di una richiesta, ad esempio la stringa di query, l'URI o il corpo della richiesta.

- **Regole di limitazione della frequenza:** Una regola di controllo della velocità consiste nel limitare il traffico elevato anomalo da qualsiasi IP del client. È possibile configurare una soglia per il numero di richieste Web consentite da un indirizzo IP client durante una durata di un minuto. Si noti che le richieste aggiuntive oltre la soglia potrebbero essere in grado di superare mentre il numero di richieste viene aggiornato. La limitazione della frequenza può essere combinata con condizioni di corrispondenza aggiuntive, ad esempio parametri HTTP (S) corrispondenti per il controllo della frequenza granulare.

### <a name="azure-managed-rule-sets"></a>Set di regole gestiti da Azure

I set di regole gestiti da Azure consentono di distribuire in modo semplice la protezione da un insieme comune di minacce per la sicurezza. Poiché tali RuleSet sono gestiti da Azure, le regole vengono aggiornate in base alle esigenze per proteggersi dalle nuove firme di attacco. In versione di anteprima pubblica, il set di regole predefinite gestito da Azure include regole per le categorie di minacce seguenti:

- Scripting tra siti
- Attacchi Java
- Inclusione file locale
- Attacchi PHP Injection
- Esecuzione comando remoto
- Inclusione di file remoti
- Fissazione della sessione
- Protezione dagli attacchi SQL injection
- Utenti malintenzionati di protocollo

Il numero di versione del set di regole predefinito viene incrementato quando vengono aggiunte nuove firme di attacco al set di regole.
Il set di regole predefinito è abilitato per impostazione predefinita in modalità di rilevamento nei criteri di WAF. È possibile disabilitare o abilitare singole regole nel set di regole predefinite per soddisfare i requisiti dell'applicazione. È anche possibile impostare azioni specifiche (Consenti/blocca/REINDIRIZZA/LOG) per regola. L'azione predefinita consiste nel blocco. Inoltre, è possibile configurare le regole personalizzate nello stesso criterio WAF se si desidera ignorare le regole preconfigurate nel set di regole predefinito.
Le regole personalizzate vengono sempre applicate prima della valutazione delle regole del set di regole predefinite. Se una richiesta corrisponde a una regola personalizzata, viene applicata l'azione della regola corrispondente e la richiesta viene bloccata o passata al back-end, senza richiamare altre regole personalizzate o le regole nel set di regole predefinito. Inoltre, è possibile scegliere di rimuovere il set di regole predefinito dai criteri di WAF.


### <a name="bot-protection-rule-preview"></a>Regola di protezione bot (anteprima)

È possibile abilitare un set di regole di protezione bot gestito per WAF per eseguire azioni personalizzate sulle richieste da categorie di bot note.
Sono supportate tre categorie di bot: bot cattivi, bot validi e bot sconosciuti. Nella categoria Bad Bot, una regola rileva Bot dannosi in base alla reputazione IP degli indirizzi clieny. La reputazione IP viene originata dal feed di intelligence per le minacce di Microsoft. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) Power Intelligence per le minacce di Microsoft e viene usato da più servizi, incluso il Centro sicurezza di Azure. Inoltre, le richieste che fingono di essere motori di ricerca noti vengono rilevate come dannose.
I bot validi sono i motori seacrh convalidati. Le categorie sconosciute includono richieste sono identificate come bot, ma con intenzioni sconosciute. È possibile impostare azioni personalizzate per bloccare, consentire, registrare o reindirizzare per diverse categorie di bot.

![Set di regole di protezione bot](./media/waf-front-door-configure-bot-protection/BotProtect2.png)

> [!IMPORTANT]
> Il set di regole di protezione da bot è attualmente disponibile in anteprima pubblica e viene fornito con un contratto di servizio in anteprima. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.  Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se è abilitata la protezione bot, le richieste in ingresso che corrispondono alle regole bot vengono registrate nel registro FrontdoorWebApplicationFirewallLog. È possibile accedere ai log di WAF dall'account di archiviazione, dall'hub eventi o da log Analytics. 

## <a name="configuration"></a>Configurazione

La configurazione e la distribuzione di tutti i tipi di regole WAF sono completamente supportate usando portale di Azure, le API REST, i modelli di Azure Resource Manager e Azure PowerShell.

## <a name="monitoring"></a>Monitoraggio

Il monitoraggio di WAF alla porta anteriore è integrato con monitoraggio di Azure per tenere traccia degli avvisi e monitorare facilmente le tendenze del traffico.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [configurare un criterio WAF per lo sportello anteriore usando il portale di Azure](waf-front-door-create-portal.md)
