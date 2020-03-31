---
title: Che cos'è Web Application Firewall di Azure nella rete CDN di Azure?
description: Informazioni sul modo in cui Web Application Firewall di Azure nel servizio di rete CDN di Azure protegge le applicazioni Web da attacchi.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 559ce4d1500dc5f1b67a3b3dbdbeb5d8dda7e487
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79475802"
---
# <a name="azure-web-application-firewall-on-azure-content-delivery-network"></a>Azure Web Application Firewall nella rete di distribuzione dei contenuti di Azure

Azure Web Application Firewall (WAF) nella rete di distribuzione dei contenuti di Azure (CDN) fornisce una protezione centralizzata per il contenuto Web. WAF protegge i servizi Web da exploit e vulnerabilità comuni. Mantiene una disponibilità elevata del servizio per gli utenti e consente di soddisfare i requisiti di conformità.

> [!IMPORTANT]
> WAF nella rete CDN di Azure è attualmente disponibile in anteprima pubblica e viene fornito con un contratto di servizio di anteprima. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.  Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

WAF nella rete CDN di Azure è una soluzione globale centralizzata, distribuita nei percorsi di rete perimetrale di Azure in tutto il mondo. WAF ferma gli eventuali attacchi in prossimità delle fonti di attacco, prima che raggiungano la propria origine, offrendo protezione globale su larga scala senza compromettere le prestazioni. 

I criteri WAF vengono facilmente collegati a qualsiasi endpoint di rete CDN nella sottoscrizione. È possibile implementare rapidamente nuove regole, in modo da rispondere con tempestività ai mutevoli modelli di minacce.

![Web Application Firewall di Azure](../media/cdn-overview/waf-cdn-overview.png)

## <a name="waf-policy-and-rules"></a>Regole e criteri di WAF

È possibile configurare un criterio WAF e associarlo a uno o più endpoint di rete CDN per la protezione. I criteri di WAF sono costituiti da due tipi di regole di sicurezza:

- Regole personalizzate che è possibile creare.

- Set di regole gestiti, ovvero raccolte di regole preconfigurate e gestite da Azure.

Quando sono presenti entrambi i tipi, le regole personalizzate vengono elaborate prima di quelle incluse in un set di regole gestito. Una regola è costituita da una condizione di corrispondenza, una priorità e un'azione. I tipi di azione supportati sono: *ALLOW*, *BLOCK*, *LOG* e *REDIRECT*. È possibile creare criteri completamente personalizzati che soddisfino specifici requisiti di protezione delle applicazioni combinando regole gestite e personalizzate.

Le regole all'interno dei criteri vengono elaborate in ordine di priorità. La priorità è un numero intero univoco che definisce l'ordine di elaborazione delle regole. I numeri più bassi indicano una priorità più alta e quindi le regole corrispondenti vengono valutate prima di quelle con valori più alti. Una volta trovata una corrispondenza per una regola, alla richiesta viene applicata l'azione definita in tale regola. Dopo l'elaborazione di una corrispondenza di questo tipo, le regole con priorità più bassa non vengono elaborate ulteriormente.

A un'applicazione Web ospitata nella rete CDN di Azure è possibile associare un solo criterio WAF alla volta. È tuttavia possibile avere un endpoint di rete CDN senza criteri di WAF associati. Se presenti, i criteri di WAF vengono replicati in tutti i percorsi perimetrali per assicurare la coerenza nei criteri di sicurezza in tutto il mondo.

## <a name="waf-modes"></a>Modalità di WAF

I criteri di WAF possono essere configurati per l'esecuzione nelle due modalità seguenti:

- *Modalità di rilevamento*: quando è attiva la modalità di rilevamento, WAF esegue solo le azioni di monitoraggio e registrazione nei log della richiesta e della regola di WAF corrispondente. È possibile attivare la diagnostica della registrazione per Frontdoor. Se si usa il portale, andare alla sezione **Diagnostica**.

- *Modalità di prevenzione*: In modalità di prevenzione, WAF esegue l'azione specificata se una richiesta corrisponde a una regola. Se viene trovata una corrispondenza, non vengono valutate ulteriori regole con priorità più bassa. Tutte le richieste corrispondenti vengono anche registrate nei log di WAF.

## <a name="waf-actions"></a>Azioni di WAF

Quando una richiesta soddisfa la condizioni di una regola, è possibile scegliere una delle azioni seguenti:

- *Allow*: la richiesta passa attraverso WAF e viene inoltrata al back-end. Nessuna regola con priorità più bassa può bloccare questa richiesta.
- *Block*: la richiesta viene bloccata e WAF invia una risposta al client senza inoltrare la richiesta al back-end.
- *Log*:  la richiesta viene registrata nei log di WAF e WAF continua la valutazione delle regole con priorità più bassa.
- *Redirect*: WAF reindirizza la richiesta all'URI specificato. Quest'ultimo è impostato a livello di criterio. Una volta configurate, tutte le richieste corrispondenti all'azione *Redirect* verranno inviate a tale URI.

## <a name="waf-rules"></a>Regole di WAF

I criteri di WAF sono costituiti da due tipi di regole di sicurezza:

- *Regole personalizzate*: regole create manualmente 
- *Set di regole gestiti*: set di regole preconfigurato e gestito da Azure

### <a name="custom-rules"></a>Regole personalizzate

Le regole personalizzate possono includere regole di corrispondenza e regole di controllo della frequenza.

È possibile configurare le regole di corrispondenza personalizzate seguenti:

- *Elenco di indirizzi IP consentiti e bloccati*: è possibile controllare l'accesso alle applicazioni Web in base a un elenco di indirizzi o intervallo di indirizzi IP client. Entrambi i tipi di indirizzi IPv4 e IPv6 sono supportati. Questo elenco può essere configurato in modo da bloccare o consentire le richieste in cui l'indirizzo IP di origine corrisponde a un indirizzo IP nell'elenco.

- *Controllo di accesso geografico*: è possibile controllare l'accesso alle applicazioni Web in base al codice paese associato all'indirizzo IP di un client.

- *Controllo di accesso in base ai parametri HTTP*: è possibile configurare le regole in base alla corrispondenza di stringhe nei parametri della richiesta HTTP/HTTPS,  ad esempio stringhe di query, argomenti POST, URI della richiesta, Intestazione della richiesta e Corpo della richiesta.

- *Controllo di accesso in base al metodo della richiesta*: è possibile configurare le regole in base al metodo HTTP della richiesta, ad esempio GET, PUT o HEAD.

- *Vincolo di dimensione*: è possibile configurare le regole in base alla lunghezza di parti specifiche di una richiesta, ad esempio la stringa di query, l'URI o il corpo della richiesta.

Una regola di controllo della frequenza limita il traffico eccessivamente elevato proveniente da qualsiasi indirizzo IP client.

- *Regole di limitazione della frequenza*: è possibile configurare una soglia per il numero di richieste Web consentite provenienti da un indirizzo IP client durante un intervallo di un minuto. Questa regola è diversa da una regola personalizzata di tipo Consenti/Blocca basata su un elenco di indirizzi IP, che consente o blocca tutte le richieste provenienti da un indirizzo IP client. I limiti della frequenza possono essere combinati con condizioni di corrispondenza aggiuntive, ad esempio le corrispondenze dei parametri HTTP(S) per il controllo della frequenza a livello granulare.

### <a name="azure-managed-rule-sets"></a>Set di regole gestiti da Azure

I set di regole gestiti da Azure consentono di distribuire in modo semplice la protezione da un set comune di minacce alla sicurezza. Poiché questi set sono gestiti da Azure, le regole vengono opportunamente aggiornate per assicurare la protezione da nuove firme di attacco. Il set di regole predefinito gestito da Azure include regole per le categorie di minacce seguenti:

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
Il set di regole predefinito è abilitato per impostazione predefinita in modalità *Rilevamento* nei criteri di WAF. È possibile disabilitare o abilitare singole regole nel set predefinito in base ai requisiti dell'applicazione. È anche possibile impostare azioni specifiche (ALLOW/BLOCK/REDIRECT/LOG) per ogni regola. L'azione predefinita per il set di regole predefinito gestito è *Block*.

Le regole personalizzate vengono sempre applicate prima della valutazione delle regole del set predefinito. Se una richiesta corrisponde a una regola personalizzata, viene applicata l'azione della regola corrispondente. La richiesta viene bloccata o passata al back-end. Non vengono elaborate altre regole personalizzate o le regole include nel set predefinito. È anche possibile rimuovere il set di regole predefinito dai criteri di WAF.

## <a name="configuration"></a>Configurazione

È possibile configurare e distribuire tutti i tipi di regole di WAF tramite il portale di Azure, le API REST, i modelli di Azure Resource Manager e Azure PowerShell.

## <a name="monitoring"></a>Monitoraggio

Il monitoraggio per WAF con la rete CDN è integrato con Monitoraggio di Azure per tenere traccia degli avvisi e monitorare con facilità le tendenze del traffico.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Creare un criterio WAF con la rete CDN di Azure usando il portale di Azure](waf-cdn-create-portal.md)