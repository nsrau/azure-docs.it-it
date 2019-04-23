---
title: Che cos'è il firewall applicazione web di Azure per l'ingresso principale di Azure? (Anteprima)
description: Informazioni su web application firewall come Azure per il servizio di ingresso principale di Azure protegge le applicazioni web da attacchi dannosi.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: kumud;tyao
ms.openlocfilehash: 17cf6629aca6c73bc96e4cf0c172a2e87a7aafb8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799168"
---
# <a name="what-is-azure-web-application-firewall-for-azure-front-door-preview"></a>Che cos'è il firewall applicazione web di Azure per l'ingresso principale di Azure? (Anteprima)

Azure web application firewall (WAF) (anteprima) fornisce una protezione centralizzata per le applicazioni web che vengono recapitati a livello globale usando l'ingresso principale di Azure. È progettato e gestito per mantenere il servizio a disponibilità elevata per gli utenti oltre alla possibilità di soddisfare i requisiti di conformità e difendere i servizi web da exploit e vulnerabilità comuni.

> [!IMPORTANT]
> Azure web application firewall (WAF) per l'ingresso principale di Azure è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le applicazioni Web sono sempre più destinazioni di attacchi dannosi, ad esempio un attacco denial of attacchi flood di servizio, attacchi SQL injection e attacchi di scripting intersito. Questi attacchi dannosi potrebbe causare la perdita di un'interruzione e i dati del servizio, rappresentano una minaccia notevole per i proprietari delle applicazioni web.

Impedire questo tipo di attacchi nel codice dell'applicazione può risultare un'operazione complessa e potrebbe richiedere una manutenzione rigorosa, l'applicazione di patch e il monitoraggio a più livelli della topologia dell'applicazione. Un Web application firewall centralizzato semplifica notevolmente la gestione della sicurezza e offre agli amministratori delle applicazioni migliori garanzie contro le minacce o le intrusioni. Inoltre, una soluzione WAF può reagire a una minaccia alla sicurezza più veloce tramite l'applicazione delle patch di una vulnerabilità nota in una posizione centrale, anziché proteggere ogni singola applicazione web.

Web Application firewall per porta d'ingresso è una soluzione globale e centralizzata. Viene distribuita in posizioni periferiche di rete di Azure in tutto il mondo e ogni richiesta in ingresso per un'applicazione web WAF abilitato recapitata da porta principale viene controllato al perimetro della rete. Ciò consente di Web Application firewall impedire gli attacchi dannosi vicino le origini di attacco, prima che possa accedere alla rete virtuale e offre protezione globale su larga scala senza sacrificare le prestazioni. Un criterio di Web Application firewall può essere collegato facilmente a qualsiasi profilo di porta d'ingresso nella sottoscrizione e nuove regole possono essere distribuite in pochi minuti, consentendo di rispondere rapidamente alle mutevoli modelli delle minacce.

![Firewall applicazione web di Azure](./media/waf-overview/web-application-firewall-overview.png)

Web Application firewall di Azure può anche essere attivato con il Gateway applicazione. Per altre informazioni, vedere [firewall applicazione Web](../application-gateway/waf-overview.md).

## <a name="waf-policy-and-rules"></a>Regole e criteri di Web Application firewall

È possibile configurare un criterio di Web Application firewall e associare i criteri a uno o più porta d'ingresso front-end per la protezione. Un criterio di Web Application firewall è costituito da due tipi di regole di sicurezza:
- regole personalizzate che vengono create dal cliente.
- set di regole gestite che sono una raccolta di Azure-managed i set di regole preconfigurato. Quando sono presenti entrambi, regole personalizzate vengono eseguite prima dell'esecuzione delle regole in un set di regole gestite. Una regola è costituita da una condizione di corrispondenza, una priorità e un'azione. Tipi di azione supportati sono: Consenti BLOCCHI, LOG e reindirizzamento. È possibile creare criteri completamente personalizzato che soddisfino i requisiti di protezione dell'applicazione specifico combinando le regole personalizzate e non gestito.

Le regole all'interno di un criterio vengono eseguite in un ordine di priorità dove la priorità è un integer univoco che definisce l'ordine di esecuzione della regola. Valore integer più piccolo indica una priorità più alta e quelli vengono valutati prima delle regole con un valore integer più elevato. Dopo aver assegnato una regola, l'azione corrispondente che è stata definita nella regola viene applicata alla richiesta. Dopo l'elaborazione di una corrispondenza, è possibile che le regole con priorità inferiore non vengono elaborate ulteriormente.

Un'applicazione web fornita da porta principale può avere un solo criterio WAF associato alla volta. Tuttavia, è possibile avere una configurazione di ingresso principale senza alcun criterio di Web Application firewall è associato. Se è presente un criterio di WAF, replicato in tutti i percorsi di edge per garantire la coerenza nei criteri di sicurezza in tutto il mondo.

## <a name="waf-modes"></a>Modalità del WAF

Criteri di Web Application firewall possono essere configurati per l'esecuzione in due modalità seguenti:

- **Modalità di rilevamento:** Quando eseguito in modalità di rilevamento, Web Application firewall non accetta tutte le altre azioni diverse da monitoraggi e registra la richiesta e la regola WAF corrispondente per i log WAF. È possibile attivare la registrazione diagnostica per la porta di ingresso (quando si usa portale, questo scopo, passare al **diagnostica** sezione nel portale di Azure).

- **Modalità di prevenzione:** Quando è configurato per l'esecuzione in modalità di prevenzione, WAF accetta l'azione specificata se una richiesta corrisponde a una regola e se viene trovata una corrispondenza, non altre regole con priorità inferiore vengono valutate. Tutte le richieste corrispondenti vengono inoltre registrate nei registri del WAF.

## <a name="waf-actions"></a>Azioni di Web Application firewall

I clienti di Web Application firewall è possono scegliere di eseguire una delle azioni quando una richiesta corrisponde alle condizioni della regola:
- **Consenti:**  Richiesta passa attraverso il firewall WAF e viene inoltrata al back-end. Senza ulteriori regole con priorità inferiore possono bloccare questa richiesta.
- **Blocco:** La richiesta è bloccata e WAF invia una risposta al client senza inoltrare le richieste al back-end.
- **Log:**  Richiesta viene registrata nei log di Web Application firewall e WAF continua la valutazione delle regole con priorità inferiore.
- **Il reindirizzamento:** Web Application firewall reindirizza la richiesta all'URI specificato. L'URI specificato è un'impostazione a livello di criteri. Una volta configurato, tutte le richieste che soddisfano le **reindirizzare** azione verrà inviata all'URI.


## <a name="waf-rules"></a>Regole del WAF

Un criterio di Web Application firewall può essere costituito da due tipi di regole di sicurezza - regole personalizzate, creati dai clienti e i set di regole gestiti, set di regole gestite di Azure preconfigurata.

### <a name="custom-authored-rules"></a>Regole personalizzate create
È possibile configurare regole personalizzate WAF nel modo seguente:

- **Indirizzi IP consentiti elenchi e blocco:** È possibile configurare regole personalizzate per controllare l'accesso alle applicazioni web in base a un elenco di indirizzi IP client o intervalli di indirizzi IP. Sono supportati i tipi di indirizzi IPv4 e IPv6. Questo elenco può essere configurato per bloccare o consentire le richieste in cui l'indirizzo IP di origine corrisponde a un indirizzo IP nell'elenco.

- **Controllo degli accessi in base geografiche:** È possibile configurare regole personalizzate per controllare l'accesso alle applicazioni web in base al codice di paese associato a un indirizzo IP del client.

- **Controllo degli accessi basato su parametri HTTP:** È possibile configurare regole personalizzate basate su stringa corrispondente ai parametri di richiesta HTTP/HTTPS, ad esempio le stringhe di query, args POST, richiesta URI, intestazione della richiesta e il corpo della richiesta.

- **Richiedi il controllo di accesso basato su metodo:** È possibile configurare regole personalizzate basate su metodo di richiesta HTTP della richiesta, ad esempio GET, PUT o HEAD.

- **Vincolo delle dimensioni:** È possibile configurare regole personalizzate basate sulle lunghezze delle parti specifiche di una richiesta, ad esempio la stringa di query, Uri, o corpo della richiesta.

- **Le regole di limitazione della frequenza:** Una regola di controllo della frequenza prevede la limitazione di traffico elevato anomalo da qualsiasi indirizzo IP client. È possibile configurare una soglia per il numero di richieste web consentite da un indirizzo IP del client durante una durata di un minuto. Ciò è diverso da una regola di personalizzato basato su elenchi consentire o bloccare IP che uno consente tutti i blocchi o tutte le richieste da un indirizzo IP del client. La limitazione della frequenza può essere combinata con le condizioni di corrispondenza aggiuntive, ad esempio parametri HTTP (S) corrispondente per il controllo della frequenza granulare.


### <a name="azure-managed-rule-sets"></a>Set di regole gestite di Azure

Set di regole gestite di Azure forniscono un modo semplice per distribuire la protezione rispetto a un set comune di minacce alla sicurezza. Poiché tale set di regole sono gestite da Azure, le regole vengono aggiornate in base alle esigenze per proteggersi da nuove firme di attacchi. In fase di anteprima pubblica, gestita da Azure regola Set predefiniti include le regole a fronte di categorie di minacce seguenti:

- Scripting intersito
- Attacchi di Java
- Inclusione di file locale
- Attacchi PHP injection
- Esecuzione comandi in remoto
- Inclusione di file remota
- Ovvero Session fixation
- Protezione dagli attacchi SQL injection

Il numero di versione del Set regola predefinita verrà incrementato quando vengono aggiunte nuove firme di attacchi al set di regole.
Set di regole predefinito è abilitato per impostazione predefinita in modalità di rilevamento nei criteri del WAF. È possibile disabilitare o abilitare regole singole all'interno di Default Set di regole per soddisfare i requisiti dell'applicazione. È anche possibile impostare azioni specifiche (Consenti/blocco/reindirizzamento/LOG) per ogni regola. Azione predefinita consiste nel blocco. Inoltre, regole personalizzate possono essere configurate nello stesso criterio WAF se si vuole ignorare qualsiasi delle regole preconfigurate in cui la regola predefinita impostata.
Regole personalizzate vengono sempre applicate prima la regola predefinita impostata le regole vengono valutate. Se una richiesta corrisponde a una regola personalizzata, viene applicata l'azione di regola corrispondente e la richiesta viene bloccata o passata al back-end, senza la chiamata di qualsiasi regola personalizzata ulteriormente oppure le regole in un Set regola predefinita. Inoltre, è possibile rimuovere il Set di regole predefiniti dai criteri di Web Application firewall.


## <a name="configuration"></a>Configurazione
Durante l'anteprima pubblica:
- Configurazione e distribuzione di tutti i tipi di regole WAF è completamente supportato tramite le API REST, modelli di Azure Resource Manager e Azure PowerShell.
- Tramite il portale di Azure, è possibile configurare o visualizzare solo gestiti di Azure predefinita Set di regole.

## <a name="monitoring"></a>Monitoraggio

Monitoraggio per WAF ingresso principale è integrato con monitoraggio di Azure per tenere traccia degli avvisi e monitorare facilmente le tendenze del traffico.

## <a name="pricing"></a>Prezzi

Durante l'anteprima pubblica, qualsiasi utilizzo associato con Web Application firewall per porta d'ingresso è gratuito e non verrà addebitato.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).

