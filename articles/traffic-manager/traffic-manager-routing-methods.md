---
title: Gestione traffico di Azure - Metodi di routing del traffico | Microsoft Docs
description: Questo articolo fornisce informazioni sui diversi metodi di routing del traffico usati da Gestione traffico
services: traffic-manager
documentationcenter: 
author: KumudD
manager: timlt
editor: 
ms.assetid: db1efbf6-6762-4c7a-ac99-675d4eeb54d0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2017
ms.author: kumud
ms.openlocfilehash: fe776e24a4f78b389c6096694055b38befa3c419
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-routing-methods"></a>Metodi di routing di Gestione traffico

Gestione traffico di Microsoft Azure supporta tre metodi di routing del traffico per determinare la modalità di instradamento del traffico di rete ai vari endpoint di servizio. Gestione traffico applica il metodo di routing a ogni query DNS ricevuta, per determinare l'endpoint da restituire nella risposta DNS.

In Gestione traffico sono disponibili quattro metodi di routing del traffico:

* **[Priorità](#priority):** selezionare **Priorità** quando si vuole usare un endpoint di servizio primario per tutto il traffico e prevedere endpoint di backup nel caso in cui l'endpoint primario o gli endpoint di backup non siano disponibili.
* **[Ponderato](#weighted):** selezionare **Ponderato** quando si vuole distribuire il traffico in un set di endpoint in modo uniforme o in base alle ponderazioni definite.
* **[Prestazioni](#performance):** selezionare **Prestazioni** quando gli endpoint si trovano in aree geografiche diverse e si vuole che gli utenti finali usino l'endpoint "più vicino" in termini di latenza di rete più bassa.
* **[Geografico](#geographic):** selezionare **Geografico** in modo che gli utenti vengono indirizzati a endpoint specifici (Azure, Esterno o Annidato) in base alla posizione geografica da cui provengono le query DNS. Questo consente ai clienti di Gestione traffico di abilitare scenari in cui è importante conoscere l'area geografica dell'utente ed eseguirne il routing in base a questa. Ne sono esempi la presenza di determinati requisiti sui dati, la localizzazione del contenuto e dell'esperienza dell'utente e la misurazione del traffico da aree diverse.

Tutti i profili di Gestione traffico includono il monitoraggio continuo dell'integrità degli endpoint e il failover automatico degli endpoint. Per altre informazioni, vedere [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md). Un singolo profilo di Gestione traffico può usare un solo metodo di routing del traffico. È possibile selezionare in qualsiasi momento un metodo di routing del traffico diverso per il profilo. Le modifiche vengono applicate entro un minuto e non si verificano periodi di inattività. I metodi di routing del traffico possono essere combinati usando profili nidificati di Gestione traffico. I profili annidati consentono di creare configurazioni di routing del traffico sofisticate e flessibili per soddisfare le esigenze di applicazioni più grandi e più complesse. Per altre informazioni, vedere [nested Traffic Manager profiles](traffic-manager-nested-profiles.md)(Profili nidificati di Gestione traffico).

## <a name = "priority"></a>Metodo di routing del traffico Priorità

Un'organizzazione vuole spesso offrire la massima affidabilità per i propri servizi dotandosi di uno o più servizi di backup in caso di inattività del servizio primario. Il metodo di routing del traffico "Priorità" consente ai clienti di Azure di implementare facilmente questo modello di failover.

![Metodo di routing del traffico "Priorità" di Gestione traffico di Azure][1]

Il profilo di Gestione traffico contiene un elenco con priorità di endpoint di servizio. Per impostazione predefinita, tutto il traffico viene inviato all'endpoint primario (o con priorità più elevata). Se l'endpoint primario non è disponibile, Gestione traffico indirizza il traffico verso il secondo endpoint. Se l'endpoint primario e secondario non sono disponibili, il traffico viene indirizzato al terzo e così via. La disponibilità dell'endpoint si basa sullo stato configurato (abilitato o disabilitato) e sul monitoraggio degli endpoint in corso.

### <a name="configuring-endpoints"></a>Configurazione degli endpoint

In Azure Resource Manager la priorità degli endpoint viene configurata in modo esplicito, definendo la proprietà "priority" di ogni endpoint. Questa proprietà accetta un valore compreso tra 1 e 1000, dove i valori più bassi rappresentano una priorità più elevata. Gli endpoint non possono avere lo stesso valore di priorità. La proprietà è facoltativa e, se viene omessa, viene usata una priorità predefinita in base all'ordine degli endpoint.

##<a name = "weighted"></a>Metodo di routing del traffico Ponderato
Il metodo di routing del traffico "Ponderato" consente di distribuire il traffico tra tutti gli endpoint in modo uniforme o con un peso predefinito.

![Metodo di routing del traffico "Ponderato" di Gestione traffico di Azure][2]

Nel metodo di routing del traffico "Ponderato" viene assegnato un peso a ogni endpoint come parte della configurazione del profilo di Gestione traffico. Ogni peso è un numero intero compreso tra 1 e 1000. Questo parametro è facoltativo e, se omesso, viene usato il valore di peso predefinito "1".

Per ogni query DNS ricevuta, viene scelto un endpoint disponibile in modo casuale, con una probabilità di scelta basata sul peso assegnato a tutti gli endpoint disponibili. L'uso dello stesso peso in tutti gli endpoint comporta una distribuzione uniforme del traffico. Se vengono usati pesi superiori o inferiori in specifici endpoint, questi verranno restituiti più o meno frequentemente nelle risposte DNS.

Il metodo "Ponderato" abilita alcuni scenari utili:

* Aggiornamento graduale dell'applicazione: allocare una percentuale di traffico da indirizzare a un nuovo endpoint, quindi incrementare gradualmente il traffico nel tempo fino al 100%.
* Migrazione dell'applicazione in Azure: creare un profilo con endpoint di Azure ed esterni. Regolare il peso degli endpoint per preferire i nuovi endpoint.
* Espansione del cloud per capacità aggiuntiva: espandere rapidamente una distribuzione locale nel cloud posizionandola dietro a un profilo di Gestione traffico. In caso di necessità di capacità aggiuntiva nel cloud, si possono aggiungere o abilitare più endpoint e si può specificare la quantità di traffico da indirizzare a ogni endpoint.

Il portale di Azure Resource Manager supporta la configurazione del traffico pesato.  I pesi possono essere configurati usando le versioni Resource Manager di Azure PowerShell, dell'interfaccia della riga di comando e dell'API REST.

È importante comprendere che le risposte DNS vengono memorizzate nella cache dai client e dai server ricorsivi usati per risolvere i nomi DNS. Questa memorizzazione ha un impatto sulle distribuzioni del traffico ponderate. Se il numero di client e di server DNS ricorsivi è elevato, la distribuzione del traffico funziona come previsto. Se invece il numero di client o di server DNS ricorsivi è limitato, la memorizzazione nella cache può modificare significativamente la distribuzione del traffico.

I casi d'uso comuni in cui può verificarsi questa situazione includono:

* Ambienti di sviluppo e test
* Comunicazioni tra applicazioni
* Applicazioni destinate a una base utenti ristretta che condivide un'infrastruttura DNS ricorsiva comune, ad esempio i dipendenti di un'organizzazione che si connettono tramite proxy.

Questi effetti della memorizzazione nella cache DNS non riguardano solo Gestione traffico di Azure, ma sono comuni a tutti i sistemi di routing del traffico basati su DNS. In alcuni casi, la cancellazione della cache DNS in modo esplicito può risolvere il problema. In altri casi, può essere più appropriato un metodo di routing del traffico alternativo.

## <a name = "performance"></a>Metodo di routing del traffico Prestazioni

La velocità di risposta di molte applicazioni può essere migliorata distribuendo gli endpoint in due o più posizioni a livello globale e indirizzando il traffico alla posizione più vicina. Questo è lo scopo del metodo di routing del traffico "Prestazioni".

![Metodo di routing del traffico "Prestazioni" di Gestione traffico di Azure][3]

L'endpoint più vicino non è necessariamente il più vicino in termini di distanza geografica. Il metodo di routing del traffico "Prestazioni" determina invece l'endpoint più vicino in termini di latenza di rete. La tabella della latenza di Internet indica il tempo di round trip tra intervalli di indirizzi IP e ciascun data center di Azure.

Gestione traffico individua nella tabella la riga relativa all'indirizzo IP di origine della richiesta DNS in ingresso e sceglie quindi nel data center di Azure l'endpoint disponibile con la latenza più bassa per l'intervallo di indirizzi IP specifico, e restituisce quindi l'endpoint nella risposta DNS.

Come spiegato in [Modalità di funzionamento di Gestione traffico](traffic-manager-overview.md#how-traffic-manager-works), le query DNS non vengono ricevute direttamente dai client, ma dal servizio DNS ricorsivo per il quale sono configurati. Pertanto, l'indirizzo IP usato per determinare l'endpoint più vicino non è l'indirizzo IP del client, ma l'indirizzo IP del servizio DNS ricorsivo. In pratica, questo indirizzo IP è un proxy valido a questo scopo per il client.


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
- Stato/provincia: ad esempio USA-California, Australia-Queensland, Canada-Alberta e così via (nota: questo livello di granularità è supportato solo per gli stati e province di Australia, Canada, Regno Unito e Stati Uniti).

Quando un'area o un gruppo di aree è assegnato a un endpoint, tutte le richieste da tali aree vengono instradate solo a tale endpoint. Gestione traffico usa l'indirizzo IP di origine della query DNS per determinare l'area da cui l'utente sta eseguendo la query: in genere si tratta dell'indirizzo IP del resolver DNS locale che esegue la query per conto dell'utente.  

![Metodo geografico di routing del traffico di Gestione traffico di Azure](./media/traffic-manager-routing-methods/geographic.png)

Gestione traffico legge l'indirizzo IP di origine della query DNS e decide da quale area geografica è stato originato. Verifica quindi se esiste un endpoint a cui è mappata questa area geografica. Questa ricerca viene avviata al livello di granularità più basso (stato/provincia dove è supportato, altrimenti a livello di paese/area geografica) e arriva fino al livello più alto che è il **mondo**. La prima corrispondenza trovata usando questo attraversamento viene indicata come endpoint da restituire nella risposta alla query. In caso di corrispondenza con un endpoint di tipo nidificato, viene restituito un endpoint all'interno di tale profilo figlio, in base al suo metodo di routing. I punti seguenti sono applicabili a questo comportamento:

- Un'area geografica può essere mappata a un solo endpoint in un profilo di Gestione traffico quando il tipo di routing è quello geografico. Ciò garantisce che il routing degli utenti sia deterministico e i clienti possano abilitare scenari che richiedono limiti geografici non ambigui.
- Se l'area geografica di un utente ricade entro il mapping geografico di due endpoint diversi, Gestione traffico seleziona l'endpoint con la granularità più bassa e non considera le richieste di routing di tale area all'altro endpoint. Ad esempio, si consideri un profilo con il tipo di routing geografico e con due endpoint: Endpoint1 ed Endpoint2. L'Endpoint1 è configurato per ricevere il traffico dall'Irlanda e l'Endpoint2 è configurato per ricevere il traffico dall'Europa. Se una richiesta proviene dall'Irlanda, viene sempre instradata all'Endpoint1.
- Poiché un'area può essere mappata a un solo endpoint, Gestione traffico la restituisce indipendentemente dal fatto che l'endpoint sia integro o meno.

    >[!IMPORTANT]
    >È consigliabile che i clienti usino il metodo di routing geografico associato con endpoint di tipo nidificato e profili figlio contenenti almeno due endpoint ciascuno.
- Se viene trovata una corrispondenza di endpoint e l'endpoint è nello stato **Arrestato**, Gestione traffico restituisce una risposta NODATA. In questo caso non vengono eseguite ulteriori ricerche più in alto nella gerarchia dell'area geografica. Questo comportamento è applicabile anche per i tipi di endpoint annidati quando il profilo figlio è nello stato **Arrestato** o **Disabilitato**.
- Se un endpoint si trova nello stato **Disabilitato**, non sarà incluso nel processo di ricerca di corrispondenza dell'area. Questo comportamento si applica anche per i tipi di endpoint annidati quando l'endpoint si trova nello stato **Disabilitato**.
- Se una query proviene da un'area geografica che non ha alcun mapping in quel profilo, Gestione traffico restituisce una risposta NODATA. Pertanto è consigliabile che i clienti usino il routing geografico con un solo endpoint, idealmente di tipo nidificato con almeno due endpoint all'interno del profilo figlio, con l'area **Mondo** assegnata. Questo garantisce inoltre che vengano gestiti tutti gli indirizzi IP che non sono mappati a un'area.

Come spiegato in [Modalità di funzionamento di Gestione traffico](traffic-manager-how-traffic-manager-works.md), le query DNS non vengono ricevute direttamente dai client, ma dal servizio DNS ricorsivo per il quale sono configurati. Pertanto l'indirizzo IP utilizzato per determinare l'area non è l'indirizzo IP del client ma l'indirizzo IP del servizio DNS ricorsivo. In pratica, questo indirizzo IP è un proxy valido a questo scopo per il client.


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come sviluppare applicazioni a disponibilità elevata tramite [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md)

Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png



