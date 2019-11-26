---
title: Metodi di routing del traffico di Gestione traffico di Azure
description: Questo articolo fornisce informazioni sui diversi metodi di routing del traffico usati da Gestione traffico
services: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: allensu
ms.openlocfilehash: 824fabf6e694b7148486d6593cf17f741d9e5c9e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483785"
---
# <a name="traffic-manager-routing-methods"></a>Metodi di routing di Gestione traffico

Gestione traffico di Azure supporta sei metodi di routing del traffico per determinare la modalità di instradamento del traffico di rete ai vari endpoint di servizio. Gestione traffico applica il metodo di routing del traffico associato a qualsiasi profilo a ogni query DNS ricevuta. Il metodo di routing del traffico determina l'endpoint restituito nella risposta DNS.

In Gestione traffico sono disponibili i metodi di routing del traffico seguenti:

* **[Priorità](#priority-traffic-routing-method):** selezionare **Priorità** quando si vuole usare un endpoint di servizio primario per tutto il traffico e prevedere endpoint di backup nel caso in cui l'endpoint primario o gli endpoint di backup non siano disponibili.
* **[Ponderato](#weighted):** selezionare **Ponderato** quando si vuole distribuire il traffico in un set di endpoint in modo uniforme o in base alle ponderazioni definite.
* **[Prestazioni](#performance):** selezionare **Prestazioni** quando gli endpoint si trovano in aree geografiche diverse e si vuole che gli utenti finali usino l'endpoint "più vicino" in termini di latenza di rete più bassa.
* **[Geografico](#geographic):** selezionare **Geografico** in modo che gli utenti vengono indirizzati a endpoint specifici (Azure, Esterno o Annidato) in base alla posizione geografica da cui provengono le query DNS. Questo consente ai clienti di Gestione traffico di abilitare scenari in cui è importante conoscere l'area geografica dell'utente ed eseguirne il routing in base a questa. Ne sono esempi la presenza di determinati requisiti sui dati, la localizzazione del contenuto e dell'esperienza dell'utente e la misurazione del traffico da aree diverse.
* **[Multivalore](#multivalue):** selezionare **Multivalore** per i profili di Gestione traffico che possono avere come endpoint solo indirizzi IPv4/IPv6. Quando viene ricevuta una query per profili di questo tipo, vengono restituiti tutti gli endpoint integri.
* **[Subnet](#subnet):** selezionare il metodo di routing del traffico **Subnet** per eseguire il mapping di set di intervalli di indirizzi IP dell'utente finale a uno specifico endpoint all'interno di un profilo di Gestione traffico. Al ricevimento di una richiesta, verrà restituito l'endpoint mappato per l'indirizzo IP di origine della richiesta. 


Tutti i profili di Gestione traffico includono il monitoraggio continuo dell'integrità degli endpoint e il failover automatico degli endpoint. Per altre informazioni, vedere [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md). Un singolo profilo di Gestione traffico può usare un solo metodo di routing del traffico. È possibile selezionare in qualsiasi momento un metodo di routing del traffico diverso per il profilo. Le modifiche vengono applicate entro un minuto e non si verificano periodi di inattività. I metodi di routing del traffico possono essere combinati usando profili nidificati di Gestione traffico. I profili annidati consentono di creare configurazioni di routing del traffico sofisticate e flessibili per soddisfare le esigenze di applicazioni più grandi e più complesse. Per altre informazioni, vedere [nested Traffic Manager profiles](traffic-manager-nested-profiles.md)(Profili nidificati di Gestione traffico).

## <a name="priority-traffic-routing-method"></a>Metodo di routing del traffico Priorità

Un'organizzazione vuole spesso offrire la massima affidabilità per i propri servizi dotandosi di uno o più servizi di backup in caso di inattività del servizio primario. Il metodo di routing del traffico "Priorità" consente ai clienti di Azure di implementare facilmente questo modello di failover.

![Metodo di routing del traffico "Priorità" di Gestione traffico di Azure](media/traffic-manager-routing-methods/priority.png)

Il profilo di Gestione traffico contiene un elenco con priorità di endpoint di servizio. Per impostazione predefinita, tutto il traffico viene inviato all'endpoint primario (o con priorità più elevata). Se l'endpoint primario non è disponibile, Gestione traffico indirizza il traffico verso il secondo endpoint. Se l'endpoint primario e secondario non sono disponibili, il traffico viene indirizzato al terzo e così via. La disponibilità dell'endpoint si basa sullo stato configurato (abilitato o disabilitato) e sul monitoraggio degli endpoint in corso.

### <a name="configuring-endpoints"></a>Configurazione degli endpoint

In Azure Resource Manager la priorità degli endpoint viene configurata in modo esplicito, definendo la proprietà "priority" di ogni endpoint. Questa proprietà accetta un valore compreso tra 1 e 1000, dove i valori più bassi rappresentano una priorità più elevata. Gli endpoint non possono avere lo stesso valore di priorità. La proprietà è facoltativa e, se viene omessa, viene usata una priorità predefinita in base all'ordine degli endpoint.

## <a name = "weighted"></a>Metodo di routing del traffico Ponderato
Il metodo di routing del traffico "Ponderato" consente di distribuire il traffico tra tutti gli endpoint in modo uniforme o con un peso predefinito.

![Metodo di routing del traffico "Ponderato" di Gestione traffico di Azure](media/traffic-manager-routing-methods/weighted.png)

Nel metodo di routing del traffico "Ponderato" viene assegnato un peso a ogni endpoint come parte della configurazione del profilo di Gestione traffico. Ogni peso è un numero intero compreso tra 1 e 1000. Questo parametro è facoltativo e, se omesso, viene usato il valore di peso predefinito "1". Maggiore è il peso, maggiore è la priorità.

Per ogni query DNS ricevuta, viene scelto un endpoint disponibile in modo casuale, con una probabilità di scelta basata sul peso assegnato a tutti gli endpoint disponibili. L'uso dello stesso peso in tutti gli endpoint comporta una distribuzione uniforme del traffico. Se vengono usati pesi superiori o inferiori in specifici endpoint, questi verranno restituiti più o meno frequentemente nelle risposte DNS.

Il metodo "Ponderato" abilita alcuni scenari utili:

* Aggiornamento graduale dell'applicazione: allocare una percentuale di traffico da indirizzare a un nuovo endpoint, quindi incrementare gradualmente il traffico nel tempo fino al 100%.
* Migrazione dell'applicazione in Azure: creare un profilo con endpoint di Azure ed esterni. Regolare il peso degli endpoint per preferire i nuovi endpoint.
* Espansione del cloud per capacità aggiuntiva: espandere rapidamente una distribuzione locale nel cloud posizionandola dietro a un profilo di Gestione traffico. In caso di necessità di capacità aggiuntiva nel cloud, si possono aggiungere o abilitare più endpoint e si può specificare la quantità di traffico da indirizzare a ogni endpoint.

Oltre che tramite il portale di Azure, i pesi possono essere configurati usando Azure PowerShell, l'interfaccia della riga di comando e le API REST.

È importante comprendere che le risposte DNS vengono memorizzate nella cache dai client e dai server ricorsivi usati per risolvere i nomi DNS. Questa memorizzazione ha un impatto sulle distribuzioni del traffico ponderate. Se il numero di client e di server DNS ricorsivi è elevato, la distribuzione del traffico funziona come previsto. Se invece il numero di client o di server DNS ricorsivi è limitato, la memorizzazione nella cache può modificare significativamente la distribuzione del traffico.

I casi d'uso comuni in cui può verificarsi questa situazione includono:

* Ambienti di sviluppo e test
* Comunicazioni tra applicazioni
* Applicazioni destinate a una base utenti ristretta che condivide un'infrastruttura DNS ricorsiva comune, ad esempio i dipendenti di un'organizzazione che si connettono tramite proxy.

Questi effetti della memorizzazione nella cache DNS non riguardano solo Gestione traffico di Azure, ma sono comuni a tutti i sistemi di routing del traffico basati su DNS. In alcuni casi, la cancellazione della cache DNS in modo esplicito può risolvere il problema. In altri casi, può essere più appropriato un metodo di routing del traffico alternativo.

## <a name = "performance"></a>Metodo di routing del traffico Prestazioni

La velocità di risposta di molte applicazioni può essere migliorata distribuendo gli endpoint in due o più posizioni a livello globale e indirizzando il traffico alla posizione più vicina. Questo è lo scopo del metodo di routing del traffico "Prestazioni".

![Metodo di routing del traffico "Prestazioni" di Gestione traffico di Azure](media/traffic-manager-routing-methods/performance.png)

L'endpoint più vicino non è necessariamente il più vicino in termini di distanza geografica. Il metodo di routing del traffico "Prestazioni" determina invece l'endpoint più vicino in termini di latenza di rete. La tabella della latenza di Internet indica il tempo di round trip tra intervalli di indirizzi IP e ciascun data center di Azure.

Gestione traffico individua nella tabella la riga relativa all'indirizzo IP di origine della richiesta DNS in ingresso sceglie quindi nel data center di Azure l'endpoint disponibile con la latenza più bassa per l'intervallo di indirizzi IP specifico e restituisce tale endpoint nella risposta DNS.

Come spiegato in [Modalità di funzionamento di Gestione traffico](traffic-manager-how-it-works.md), le query DNS non vengono ricevute direttamente dai client, ma dal servizio DNS ricorsivo per il quale sono configurati. Pertanto, l'indirizzo IP usato per determinare l'endpoint più vicino non è l'indirizzo IP del client, ma l'indirizzo IP del servizio DNS ricorsivo. In pratica, questo indirizzo IP è un proxy valido a questo scopo per il client.


Per adeguarsi alle modifiche nella rete Internet globale e all'aggiunta di nuove aree di Azure, Gestione traffico aggiorna regolarmente la tabella della latenza di Internet usata. Tuttavia, le prestazioni dell'applicazione variano in base alle variazioni del carico in tempo reale registrate in Internet. Il routing del traffico "Prestazioni" non prende in considerazione il monitoraggio del carico su un determinato endpoint di servizio. Se un endpoint non è più disponibile, non viene incluso nelle risposte alle query DNS.


Punti da notare:

* Se il profilo contiene più endpoint nella stessa area di Azure, il traffico indirizzato all'area in questione viene distribuito in modo uniforme tra gli endpoint disponibili nell'area. Se si preferisce una distribuzione del traffico diversa all'interno di un'area, usare i [profili annidati di Gestione traffico](traffic-manager-nested-profiles.md).
* Se tutti gli endpoint abilitati nell'area di Azure più vicina sono danneggiati, Gestione traffico sposta il traffico sugli endpoint nella successiva area di Azure più vicina. Se si preferisce definire una sequenza di failover, usare i [profili annidati di Gestione traffico](traffic-manager-nested-profiles.md).
* Quando si usa il metodo di routing del traffico "Prestazioni" con endpoint esterni o endpoint annidati, è necessario specificare la posizione di tali endpoint. Scegliere l'area di Azure più vicina alla distribuzione specifica. Si tratta delle posizioni supportate dalla tabella della latenza di Internet.
* L'algoritmo che sceglie l'endpoint è deterministico. Le query DNS ripetute dallo stesso client vengono indirizzate allo stesso endpoint. Quando viaggiano, i client solitamente usano server DNS ricorsivi diversi, e possono pertanto essere indirizzati a un endpoint differente. Il routing potrebbe inoltre risentire degli aggiornamenti della tabella della latenza di Internet. Ne consegue che il metodo di routing del traffico "Prestazioni" non garantisce che un client sia sempre indirizzato allo stesso endpoint.
* Quando viene aggiornata la tabella della latenza di Internet, è possibile notare che alcuni client vengono indirizzati a un endpoint diverso. Ciò riflette un routing più accurato in base ai dati di latenza correnti. Questi aggiornamenti sono essenziali per garantire la precisione del routing del traffico Prestazioni man mano che Internet si evolve.

## <a name = "geographic"></a>Metodo di routing del traffico Geografico

I profili di Gestione traffico possono essere configurati per l'uso del metodo di routing Geografico, in modo che gli utenti vengono indirizzati a endpoint specifici (Azure, Esterno o Annidato) in base alla posizione geografica da cui provengono le query DNS. Questo consente ai clienti di Gestione traffico di abilitare scenari in cui è importante conoscere l'area geografica dell'utente ed eseguirne il routing in base a questa. Ne sono esempi la presenza di determinati requisiti sui dati, la localizzazione del contenuto e dell'esperienza dell'utente e la misurazione del traffico da aree diverse.
Quando un profilo è configurato per il routing geografico, ogni endpoint associato al profilo deve avere un set di aree geografiche assegnate. Un'area geografica può avere i seguenti livelli di granularità 
- Mondo: qualsiasi area
- Raggruppamento di aree: ad esempio Africa, Medio Oriente, Australia/Pacifico e così via. 
- Paese/area geografica: ad esempio Irlanda, Perù, Regione amministrativa speciale di Hong Kong e così via. 
- Stato/Provincia: ad esempio Stati Uniti-California, Australia-Queensland, Canada-Alberta e così via. Si noti che questo livello di granularità è supportato solo per gli stati e/o le province di Australia, Canada e Stati Uniti.

Quando un'area o un gruppo di aree è assegnato a un endpoint, tutte le richieste da tali aree vengono instradate solo a tale endpoint. Gestione traffico usa l'indirizzo IP di origine della query DNS per determinare l'area da cui l'utente sta eseguendo la query: in genere si tratta dell'indirizzo IP del resolver DNS locale che esegue la query per conto dell'utente.  

![Metodo geografico di routing del traffico di Gestione traffico di Azure](./media/traffic-manager-routing-methods/geographic.png)

Gestione traffico legge l'indirizzo IP di origine della query DNS e decide da quale area geografica è stato originato. Verifica quindi se esiste un endpoint a cui è mappata questa area geografica. Questa ricerca viene avviata al livello di granularità più basso (stato/provincia dove è supportato, altrimenti a livello di paese/area geografica) e arriva fino al livello più alto che è il **mondo**. La prima corrispondenza trovata usando questo attraversamento viene indicata come endpoint da restituire nella risposta alla query. In caso di corrispondenza con un endpoint di tipo nidificato, viene restituito un endpoint all'interno di tale profilo figlio, in base al suo metodo di routing. I punti seguenti sono applicabili a questo comportamento:

- Un'area geografica può essere mappata a un solo endpoint in un profilo di Gestione traffico quando il tipo di routing è quello geografico. Ciò garantisce che il routing degli utenti sia deterministico e i clienti possano abilitare scenari che richiedono limiti geografici non ambigui.
- Se l'area geografica di un utente ricade entro il mapping geografico di due endpoint diversi, Gestione traffico seleziona l'endpoint con la granularità più bassa e non considera le richieste di routing di tale area all'altro endpoint. Ad esempio, si consideri un profilo con il tipo di routing geografico e con due endpoint: Endpoint1 ed Endpoint2. L'Endpoint1 è configurato per ricevere il traffico dall'Irlanda e l'Endpoint2 è configurato per ricevere il traffico dall'Europa. Se una richiesta proviene dall'Irlanda, viene sempre instradata all'Endpoint1.
- Poiché un'area può essere mappata a un solo endpoint, Gestione traffico la restituisce indipendentemente dal fatto che l'endpoint sia integro o meno.

    >[!IMPORTANT]
    >È consigliabile che i clienti usino il metodo di routing geografico associato con endpoint di tipo nidificato e profili figlio contenenti almeno due endpoint ciascuno.
- Se viene trovata una corrispondenza di endpoint e l'endpoint è nello stato **Arrestato**, Gestione traffico restituisce una risposta NODATA. In questo caso, non vengono eseguite ulteriori ricerche più in alto nella gerarchia dell'area geografica. Questo comportamento è applicabile anche per i tipi di endpoint annidati quando il profilo figlio è nello stato **Arrestato** o **Disabilitato**.
- Se un endpoint si trova nello stato **Disabilitato**, non sarà incluso nel processo di ricerca di corrispondenza dell'area. Questo comportamento si applica anche per i tipi di endpoint annidati quando l'endpoint si trova nello stato **Disabilitato**.
- Se una query proviene da un'area geografica che non ha alcun mapping in quel profilo, Gestione traffico restituisce una risposta NODATA. Pertanto è consigliabile che i clienti usino il routing geografico con un solo endpoint, idealmente di tipo nidificato con almeno due endpoint all'interno del profilo figlio, con l'area **Mondo** assegnata. Questo garantisce inoltre che vengano gestiti tutti gli indirizzi IP che non sono mappati a un'area.

Come spiegato in [Modalità di funzionamento di Gestione traffico](traffic-manager-how-it-works.md), le query DNS non vengono ricevute direttamente dai client, ma dal servizio DNS ricorsivo per il quale sono configurati. Pertanto l'indirizzo IP utilizzato per determinare l'area non è l'indirizzo IP del client ma l'indirizzo IP del servizio DNS ricorsivo. In pratica, questo indirizzo IP è un proxy valido a questo scopo per il client.

### <a name="faqs"></a>Domande frequenti

* [What are some use cases where geographic routing is useful?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-geographic-routing-is-useful)

* [How do I decide if I should use Performance routing method or Geographic routing method?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [What are the regions that are supported by Traffic Manager for geographic routing?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [How does traffic manager determine where a user is querying from?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Is it guaranteed that Traffic Manager can correctly determine the exact geographic location of the user in every case?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Does an endpoint need to be physically located in the same region as the one it is configured with for geographic routing?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Can I assign geographic regions to endpoints in a profile that is not configured to do geographic routing?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Why am I getting an error when I try to change the routing method of an existing profile to Geographic?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Why is it strongly recommended that customers create nested profiles instead of endpoints under a profile with geographic routing enabled?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Are there any restrictions on the API version that supports this routing type?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name = "multivalue"></a>Metodo di routing del traffico Multivalore
Il metodo di routing del traffico **Multivalore** consente di ottenere più endpoint integri in un'unica risposta a una query DNS. This enables the caller to do client-side retries with other endpoints in the event of a returned endpoint being unresponsive. Questo modello può aumentare la disponibilità di un servizio e ridurre la latenza associata a una nuova query DNS per ottenere un endpoint integro. Il metodo di routing Multivalore funziona solo se tutti gli endpoint di tipo Esterno sono specificati come indirizzi IPv4 o IPv6. Quando si riceve una query per profili di questo tipo, tutti gli endpoint integri vengono restituiti e sono soggetti a un numero massimo di restituzioni configurabili.

### <a name="faqs"></a>Domande frequenti

* [What are some use cases where MultiValue routing is useful?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [How many endpoints are returned when MultiValue routing is used?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Will I get the same set of endpoints when MultiValue routing is used?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name = "subnet"></a>Metodo di routing del traffico Subnet
Il metodo di routing del traffico **Subnet** consente di eseguire il mapping di un set di intervalli di indirizzi IP dell'utente finale a endpoint specifici di un profilo. Successivamente, se Gestione traffico riceve una query DNS per tale profilo, controllerà l'indirizzo IP di origine della richiesta (nella maggior parte dei casi si tratta dell'indirizzo IP in uscita del resolver DNS usato dal chiamante), determinerà l'endpoint a cui eseguire il mapping e restituirà l'endpoint nella risposta alla query. 

L'indirizzo IP di cui eseguire il mapping a un endpoint può essere specificato come intervallo CIDR, ad esempio 1.2.3.0/24, o come intervallo di indirizzi, ad esempio 1.2.3.4-5.6.7.8. Gli intervalli IP associati a un endpoint devono essere univoci nell'ambito di tale profilo e non possono sovrapporsi con il set di indirizzi IP di un endpoint diverso nello stesso profilo.
Se si definisce un endpoint senza intervallo di indirizzi, questo funziona come fallback e accetta il traffico da qualsiasi subnet rimanente. Se non è incluso alcun endpoint di fallback, Gestione traffico invia una risposta NODATA per tutti gli intervalli non definiti. Di conseguenza, è consigliabile definire un endpoint di fallback o assicurarsi che negli endpoint siano specificati tutti gli intervalli di indirizzi IP possibili.

È possibile usare il metodo di routing Subnet per offrire un'esperienza diversa per gli utenti che si connettono da uno spazio di IP specifico. Ad esempio, con questo metodo un cliente può fare in modo che tutte le richieste provenienti dall'ufficio aziendale vengano instradate a un endpoint diverso dove è possibile testare una versione solo interna dell'app. Un altro scenario può essere quello in cui si voglia offrire un'esperienza diversa agli utenti che si connettono da un ISP specifico, ad esempio per bloccare gli utenti di un determinato ISP.

### <a name="faqs"></a>Domande frequenti

* [What are some use cases where subnet routing is useful?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-subnet-routing-is-useful)

* [How does Traffic Manager know the IP address of the end user?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [How can I specify IP addresses when using Subnet routing?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [How can I specify a fallback endpoint when using Subnet routing?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [What happens if an endpoint is disabled in a Subnet routing type profile?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come sviluppare applicazioni a disponibilità elevata tramite [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md)




