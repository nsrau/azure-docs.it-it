---
title: Sportello anteriore di Azure-metodi di routing del traffico | Microsoft Docs
description: Questo articolo fornisce informazioni sui diversi metodi di routing del traffico usati da Frontdoor
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 2bc056620ff964747dfd83e7525cb5bfd2eb8e52
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449145"
---
# <a name="front-door-routing-methods"></a>Metodi di routing di Frontdoor

Il front-end di Azure supporta diversi tipi di metodi di routing del traffico per determinare come indirizzare il traffico HTTP/HTTPS a endpoint di servizio diversi. Quando il client richiede il raggiungimento della porta anteriore, viene applicato il metodo di routing configurato per assicurarsi che le richieste vengano inviate alla migliore istanza back-end. 

Sono disponibili quattro metodi di routing del traffico nella porta anteriore:

* **[Latenza](#latency):** il routing basato sulla latenza assicura che le richieste vengano inviate al back-end con la latenza più bassa accettabile all'interno di un intervallo di sensibilità. In pratica, le richieste degli utenti vengono inviate al set "più vicino" di backend rispetto alla latenza di rete.
* ** [Priorità](#priority):** È possibile assegnare le priorità ai back-end quando si vuole configurare un back-end primario per il servizio di tutto il traffico. Il back-end secondario può essere un backup nel caso in cui il back-end primario diventi non disponibile.
* ** [Ponderato](#weighted):** È possibile assegnare pesi ai backend quando si vuole distribuire il traffico in un set di backend. Se si desidera distribuire uniformemente o in base ai coefficienti di peso.
* ** [Affinità di sessione](#affinity):** È possibile configurare l'affinità di sessione per gli host o i domini front-end per assicurarsi che le richieste provenienti dallo stesso utente siano inviate allo stesso back-end.

Tutte le configurazioni di Frontdoor includono il monitoraggio dell'integrità back-end e il failover globale immediato automatizzato. Per altre informazioni, vedere [Front Door Backend Monitoring](front-door-health-probes.md) (Monitoraggio del back-end Frontdoor). La porta anteriore può funzionare in base a un singolo metodo di routing. Tuttavia, a seconda delle esigenze dell'applicazione, è anche possibile combinare più metodi di routing per creare una topologia di routing ottimale.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Routing del traffico in base alla latenza più bassa

La distribuzione di backend in due o più posizioni in tutto il mondo può migliorare la velocità di risposta delle applicazioni indirizzando il traffico verso la destinazione più vicina agli utenti finali. Il metodo di routing del traffico predefinito per la configurazione della porta anteriore Invia le richieste dagli utenti finali al back-end più vicino dell'ambiente di sportello anteriore che ha ricevuto la richiesta. In combinazione con l'architettura Anycast di Azure front door, questo approccio garantisce che ogni utente finale ottenga le massime prestazioni personalizzate in base alla propria posizione.

Il back-end "più vicino" non è necessariamente più vicino a quanto misurato in base alla distanza geografica. Frontdoor determina invece il back-end più vicino misurando la latenza di rete. Per altre informazioni, vedere [Front Door's routing architecture](front-door-routing-architecture.md) (Architettura di routing di Frontdoor). 

Di seguito è illustrato il flusso complessivo delle decisioni:

| Back-end disponibili | Priorità | Segnale di latenza (basato sui probe di integrità) | Pesi |
|-------------| ----------- | ----------- | ----------- |
| Per prima cosa, selezionare tutti i backend abilitati e restituiti integri (200 OK) per il probe di integrità. Se sono presenti sei backend A, B, C, D, e e F e tra di essi C non è integro e e è disabilitato. L'elenco dei backend disponibili è A, B, D e F.  | Successivamente, vengono selezionati i backend con priorità superiore tra quelli disponibili. Se il back-end A, B e D hanno priorità 1 e il back-end F ha una priorità pari a 2. Quindi, i backend selezionati saranno A, B e D.| Selezionare i back-end con l'intervallo di latenza (latenza inferiore e sensibilità di latenza in ms specificata). Se il back-end A è 15 ms, B è 30 ms e D è 60 MS dall'ambiente della porta anteriore in cui la richiesta è stata sbarcata e la sensibilità di latenza è 30 ms, il pool di latenza più bassa è costituito da back-end A e B, perché D supera 30 ms dal back-end più vicino che è un. | Infine, Frontdoor eseguirà il round robin del traffico tra il pool selezionato finale di back-end nel rapporto di pesi specificati. Se il back-end A ha un peso di 5 e il back-end B ha un peso di 8, il traffico verrà distribuito in un rapporto di 5:8 tra i back-end A e B. |

>[!NOTE]
> Per impostazione predefinita, la proprietà di sensibilità di latenza è impostata su 0 ms, ovvero inoltrare sempre la richiesta al back-end più veloce disponibile.

## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Routing del traffico basato sulla priorità

Spesso un'organizzazione vuole offrire la disponibilità elevata per i servizi distribuendo più di un servizio di backup nel caso in cui il database primario si arresti. A livello di settore, questa topologia è anche denominata topologia di distribuzione attiva/standby o attiva/passiva. Il metodo di routing del traffico "Priorità" consente ai clienti di Azure di implementare facilmente questo modello di failover.

Il sistema Frontdoor predefinito contiene un elenco con uguale priorità dei back-end. Per impostazione predefinita, Frontdoor invia il traffico solo ai back-end con la massima priorità (il valore più basso per la priorità), ovvero il set primario di back-end. Se i backend primari non sono disponibili, la porta anteriore instrada il traffico al set secondario di backend (secondo valore più basso per priorità). Se i backend primario e secondario non sono disponibili, il traffico viene indirizzato al terzo e così via. La disponibilità del back-end si basa sullo stato configurato (abilitato o disabilitato) e lo stato di integrità del back-end, determinato continuamente tramite i probe di integrità.

### <a name="configuring-priority-for-backends"></a>Configurazione della priorità per i back-end

Ogni back-end nel pool back-end della configurazione della porta anteriore ha una proprietà denominata "Priority", che può essere un numero compreso tra 1 e 5. Con Azure front door è possibile configurare la priorità di back-end in modo esplicito usando questa proprietà per ogni back-end. Questa proprietà accetta un valore compreso tra 1 e 5, dove i valori più bassi rappresentano una priorità più elevata. I back-end possono condividere i valori di priorità.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Metodo di routing del traffico Ponderato
Il metodo di routing del traffico "Ponderato" consente di distribuire il traffico tra tutti gli endpoint in modo uniforme o con un peso predefinito.

Nel metodo di routing del traffico "Ponderato" viene assegnato un peso a ogni back-end nella configurazione di Frontdoor del pool back-end. Ogni peso è un numero intero compreso tra 1 e 1000. Questo parametro usa un peso predefinito pari a "50".

Con l'elenco dei backend disponibili con una sensibilità di latenza accettabile, il traffico viene distribuito con un meccanismo Round Robin usando il rapporto tra i pesi specificati. Se la sensibilità di latenza viene impostata su 0 millisecondi, questa proprietà non viene applicata a meno che non siano presenti due backend con la stessa latenza di rete. 

Il metodo "Ponderato" abilita alcuni scenari utili:

* **Aggiornamento graduale dell'applicazione**: fornisce una percentuale di traffico da indirizzare a un nuovo back-end e aumenta gradualmente il traffico nel tempo per portarlo alla parità con altri back-end.
* **Migrazione dell'applicazione in Azure**: creare un pool back-end con back-end di Azure ed esterni. Regolare il peso dei back-end per preferire i nuovi back-end. È possibile applicare questa configurazione gradualmente, iniziando con i nuovi back-end disabilitati, quindi assegnando loro i pesi più bassi e aumentandoli lentamente fino ai livelli di massimo traffico. È infine possibile disabilitare i back-end con preferenza inferiore e rimuoverli dal pool.  
* **Espansione del cloud per capacità aggiuntiva**: espandere rapidamente una distribuzione locale nel cloud posizionandola dietro Frontdoor. In caso di necessità di capacità aggiuntiva nel cloud, si possono aggiungere o abilitare più back-end e si può specificare la quantità di traffico da indirizzare a ogni back-end.

## <a name="session-affinity"></a><a name = "affinity"></a>Affinità di sessione
Per impostazione predefinita, senza affinità di sessione, il portello anteriore Invia le richieste originate dallo stesso client a backend diversi. Alcune applicazioni con stato o in determinati scenari che derivano dalle richieste dello stesso utente preferiscono lo stesso back-end che ha elaborato la richiesta iniziale. L'affinità di sessione basata su cookie è utile quando si vuole mantenere una sessione utente nello stesso back-end. Usando i cookie gestiti, lo sportello anteriore di Azure può indirizzare il traffico da una sessione utente allo stesso back-end per l'elaborazione.

L'affinità di sessione può essere abilitata a livello di host front-end per tutti i domini (o i sottodomini) configurati. Una volta abilitata, Frontdoor aggiunge un cookie alla sessione dell'utente. L'affinità di sessione basata su cookie consente a Frontdoor di identificare i diversi utenti, anche se usano lo stesso indirizzo IP, permettendo una distribuzione più uniforme del traffico tra back-end differenti.

La durata del cookie corrisponde a quella della sessione utente, perché Frontdoor attualmente supporta solo i cookie di sessione. 

> [!NOTE]
> I proxy pubblici possono interferire con l'affinità di sessione, perché per stabilire una sessione è necessario che Frontdoor aggiunga un cookie di affinità di sessione nella risposta, operazione che non può essere eseguita se la risposta è memorizzabile nella cache, in quanto potrebbe compromettere i cookie di altri client che richiedono la stessa risorsa. Per proteggersi da questa situazione, l'affinità di sessione **non** verrà stabilita se il back-end invia una risposta memorizzabile nella cache quando si tenta di eseguire questa operazione. Se la sessione è già stata stabilita, non importa se la risposta dal back-end è memorizzabile nella cache.
> L'affinità di sessione verrà stabilita nelle circostanze seguenti, **a meno che** la risposta non abbia un codice di stato HTTP 304:
> - Per la risposta sono stati impostati valori specifici per l' ```Cache-Control``` intestazione che impedisce la memorizzazione nella cache, ad esempio "privato" o nessun archivio ".
> - La risposta contiene un'intestazione ```Authorization``` non scaduta.
> - La risposta contiene un codice di stato HTTP 302.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
