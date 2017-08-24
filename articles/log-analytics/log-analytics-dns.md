---
title: Soluzione DNS Analytics in Azure Log Analytics | Microsoft Docs
description: Configurare e usare la soluzione DNS Analytics in Log Analytics per raccogliere informazioni dettagliate nell'infrastruttura DNS relative a sicurezza, prestazioni e operazioni.
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: 1b2f9c3fc59a83b384a63db00d2d039cab8679e8
ms.contentlocale: it-it
ms.lasthandoff: 06/09/2017

---

# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Raccogliere informazioni dettagliate sull'infrastruttura DNS con la soluzione DNS Analytics (anteprima)

![Simbolo di Analisi DNS](./media/log-analytics-dns/dns-analytics-symbol.png)

Questo articolo descrive come configurare e usare la soluzione Azure DNS Analytics in Azure Log Analytics per raccogliere informazioni dettagliate in DNS relative a sicurezza, prestazioni e operazioni.

DNS Analytics consente di:

- Identificare i client che tentano di risolvere nomi di dominio dannosi.
- Identificare i record di risorse non aggiornati.
- Identificare i nomi di dominio sottoposti frequentemente a query e i client DNS loquaci.
- Visualizzare il carico di richieste nei server DNS.
- Visualizzare gli errori di registrazione di DNS dinamici.

La soluzione raccoglie, analizza e mette in relazione i log di controllo e analisi DNS di Windows e altri dati correlati dei server DNS.

## <a name="connected-sources"></a>Origini connesse

La tabella seguente descrive le origini connesse che sono supportate da questa soluzione:

| **Origine connessa** | **Supporto** | **Descrizione** |
| --- | --- | --- |
| [Agenti di Windows](log-analytics-windows-agents.md) | Sì | La soluzione raccoglie le informazioni DNS dagli agenti Windows. |
| [Agenti Linux](log-analytics-linux-agents.md) | No | La soluzione non raccoglie le informazioni DNS dagli agenti Linux diretti. |
| [Gruppo di gestione di System Center Operations Manager](log-analytics-om-agents.md) | Sì | La soluzione raccoglie le informazioni DNS dagli agenti di un gruppo di gestione di Operations Manager connesso. Non è necessaria una connessione diretta dall'agente Operations Manager a Operations Management Suite. I dati vengono inoltrati dal gruppo di gestione al repository Operations Management Suite. |
| [Account di archiviazione di Azure](log-analytics-azure-storage.md) | No | La soluzione non usa le risorse di archiviazione di Azure. |

### <a name="data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati

La soluzione raccoglie i dati relativi all'inventario e agli eventi DNS dai server DNS in cui è installato un agente Log Analytics. Tali dati vengono quindi caricati in Log Analytics e visualizzati nel dashboard della soluzione. I dati relativi all'inventario, come il numero di server, zone e record di risorse DNS, vengono raccolti eseguendo i cmdlet di PowerShell per DNS. I dati vengono aggiornati ogni due giorni. I dati relativi agli eventi vengono raccolti quasi in tempo reale dai [log di analisi e di controllo](https://technet.microsoft.com/library/dn800669.aspx#enhanc) offerti dalle funzionalità avanzate di registrazione e diagnostica DNS di Windows Server 2012 R2.

## <a name="configuration"></a>Configurazione

Per configurare la soluzione, usare le informazioni seguenti:

- È necessario un agente [Windows](log-analytics-windows-agents.md) o [Operations Manager](log-analytics-om-agents.md) in ogni server DNS che si vuole monitorare.
- È possibile aggiungere la soluzione DNS Analytics nell'area di lavoro di Operations Management Suite da [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). È anche possibile seguire la procedura descritta nell'articolo relativo all'[aggiunta di soluzioni di Log Analytics dalla raccolta soluzioni](log-analytics-add-solutions.md).

La soluzione avvia la raccolta dati senza che siano necessarie operazioni di configurazione aggiuntive. È tuttavia possibile usare la configurazione seguente per personalizzare la raccolta.

### <a name="configure-the-solution"></a>Configurare la soluzione

Nel dashboard della soluzione fare clic su **Configurazione** per aprire la pagina Configurazione di DNS Analytics. È possibile apportare due tipi di modifiche di configurazione.

- **Nomi di dominio consentiti**. La soluzione non elabora tutte le query di ricerca, ma gestisce un elenco elementi consentiti per i suffissi dei nomi di dominio. Le query di ricerca che si risolvono nei nomi di dominio corrispondenti ai suffissi di questo elenco non vengono elaborate dalla soluzione. L'esclusione dall'elaborazione dei nomi di dominio consentiti consente di ottimizzare i dati inviati a Log Analytics. L'elenco elementi consentiti predefinito include i nomi di dominio pubblici più diffusi, come www.google.com e www.facebook.com. L'intero elenco predefinito può essere visualizzato tramite lo scorrimento.

 È possibile modificare l'elenco per aggiungere qualsiasi suffisso di nome di dominio per cui non si vogliono visualizzare informazioni dettagliate relative alla ricerca. È anche possibile rimuovere qualsiasi suffisso di nome di dominio per cui non si vogliono visualizzare informazioni dettagliate relative alla ricerca.

- **Soglia client loquaci**. I client DNS che superano la soglia relativa al numero di richieste di ricerca vengono evidenziati nel pannello **Client DNS**. La soglia predefinita è 1000. È possibile modificare la soglia.

    ![Nomi di dominio consentiti](./media/log-analytics-dns/dns-config.png)

## <a name="management-packs"></a>Management Pack

Se per la connessione all'area di lavoro di Operations Management Suite si usa Microsoft Monitoring Agent, viene installato il Management Pack seguente:

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)

Se il gruppo di gestione di Operations Manager è connesso all'area di lavoro di Operations Management Suite, in Operations Manager vengono installati i Management Pack seguenti quando si aggiunge questa soluzione. Per i Management Pack seguenti non è necessaria alcuna configurazione o manutenzione:

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)
- Microsoft System Center Advisor DNS Analytics Configuration (Microsoft.IntelligencePack.Dns.Configuration)

Per maggiori informazioni sulla modalità di aggiornamento dei Management Pack, vedere [Connettere Operations Manager a Log Analytics](log-analytics-om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Usare la soluzione DNS Analytics

In questa sezione vengono illustrate tutte le funzioni del dashboard e si spiega come usarle.

Dopo aver aggiunto la soluzione all'area di lavoro, il riquadro della soluzione nella pagina di panoramica di Operations Management Suite offre un breve riepilogo dell'infrastruttura DNS, che include il numero di server DNS in cui vengono raccolti i dati e il numero di richieste effettuato dai client per risolvere domini dannosi nelle ultime 24 ore. Quando si fa clic sul riquadro, viene aperto il dashboard della soluzione.

![Riquadro DNS Analytics](./media/log-analytics-dns/dns-tile.png)

### <a name="solution-dashboard"></a>Dashboard della soluzione

Il dashboard della soluzione visualizza informazioni di riepilogo relative alle varie funzionalità e include anche collegamenti alla visualizzazione dettagliata per l'analisi e la diagnosi per scopi legali. Per impostazione predefinita, vengono visualizzati i dati per gli ultimi sette giorni. È possibile modificare l'intervallo di tempo con il **controllo di selezione di data e ora**, come nell'immagine seguente:

![Controllo di selezione di data e ora](./media/log-analytics-dns/dns-time.png)

Il dashboard visualizza i pannelli descritti di seguito.

**Sicurezza DNS**. Segnala i client DNS che tentano di comunicare con domini dannosi. Usando i feed di intelligence per le minacce di Microsoft, DNS Analytics può rilevare gli indirizzi IP client che tentano di accedere ai domini dannosi. In molti casi, i dispositivi infetti da malware "stabiliscono la connessione" con il "centro di comando e controllo" del dominio dannoso risolvendo il nome di dominio del malware.

![Pannello Sicurezza DNS](./media/log-analytics-dns/dns-security-blade.png)

Facendo clic su un indirizzo IP client nell'elenco si apre Ricerca log con i dettagli della ricerca della rispettiva query. Nell'esempio seguente, DNS Analytics ha rilevato una comunicazione con [IRCbot](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=Win32/IRCbot):

![Ricerca log: risultati con IRCbot](./media/log-analytics-dns/ircbot.png)

Le informazioni consentono di identificare quanto segue:

- IP client che ha avviato la comunicazione.
- Nome di dominio che viene risolto nell'indirizzo IP dannoso.
- Indirizzi IP in cui viene risolto il nome di dominio.
- Indirizzo IP dannoso.
- Gravità del problema.
- Motivo per non consentire l'indirizzo IP dannoso.
- Data e ora di rilevamento.

**Domini sottoposti a query**. Contiene i nomi di dominio su cui i client DNS eseguono più frequentemente query nell'ambiente. È possibile visualizzare l'elenco di tutti i nomi di dominio sottoposti a query ed eseguire il drill-down nei dettagli delle richieste di ricerca per un nome di dominio specifico in Ricerca log.

![Pannello Domini sottoposti a query](./media/log-analytics-dns/domains-queried-blade.png)

**Client DNS**. Segnala i client che *violano la soglia* relativa al numero di query nel periodo di tempo selezionato. È possibile visualizzare l'elenco di tutti i client DNS e i dettagli delle query che hanno eseguito in Ricerca log.

![Pannello Client DNS](./media/log-analytics-dns/dns-clients-blade.png)

**Registrazioni di DNS dinamici**. Segnala gli errori di registrazione dei nomi. Vengono evidenziati tutti gli errori di registrazione relativi a [record di risorse](https://en.wikipedia.org/wiki/List_of_DNS_record_types) indirizzo (tipo A e AAAA), con gli indirizzi IP client che hanno effettuato le richieste di registrazione. È quindi possibile usare queste informazioni per trovare la causa radice dell'errore di registrazione seguendo questa procedura:

1. Trovare la zona rilevante per il nome che il client sta tentando di aggiornare.

2. Usare la soluzione per controllare le informazioni di inventario di tale zona.

3. Verificare che per la zona sia abilitato l'aggiornamento dinamico.

4. Controllare se la zona è configurata o meno per l'aggiornamento dinamico sicuro.

    ![Pannello Registrazioni di DNS dinamici](./media/log-analytics-dns/dynamic-dns-reg-blade.png)

**Richieste di registrazione del nome**. Il riquadro superiore visualizza la tendenza del numero di richieste di aggiornamento dinamico di DNS riuscite e non riuscite. Il riquadro inferiore contiene l'elenco dei primi 10 client che hanno inviato richieste di aggiornamento di DNS non riuscite ai server DNS, ordinato in base al numero di errori.

![Pannello Richieste di registrazione del nome ](./media/log-analytics-dns/name-reg-req-blade.png)

**Query DDI Analytics di esempio**. Contiene un elenco delle query di ricerca più comuni che recuperano direttamente dati di analisi non elaborati.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Query di esempio](./media/log-analytics-dns/queries.png)

È possibile usare queste query come punto di partenza per creare le proprie query per l'elaborazione di report personalizzati. Le query si collegano alla pagina di Ricerca log di DNS Analytics in cui vengono visualizzati i risultati:

- **Elenco di server DNS**. Visualizza un elenco di tutti i server DNS con i nomi di dominio completo, i nomi di dominio, i nomi foresta e gli IP server associati.
- **Elenco di zone DNS**. Visualizza un elenco di tutte le zone DNS con il nome della zona, lo stato di aggiornamento dinamico, i server dei nomi e lo stato della firma DNSSEC associati.
- **Record di risorse inutilizzati**. Visualizza un elenco di tutti i record di risorse non usati o non aggiornati. L'elenco contiene il nome e il tipo dei record di risorse, il server DNS associato, la data e l'ora di generazione dei record e il nome della zona. È possibile usare questo elenco per identificare i record di risorse DNS non più in uso e quindi intervenire sulla base di tali informazioni per rimuovere tali voci dai server DNS.
- **Carico query server DNS**. Visualizza informazioni che consentono di ottenere una prospettiva del carico DNS sui server DNS. Queste informazioni consentono di pianificare la capacità per i server. È possibile passare alla scheda **Metrica** per ottenere una visualizzazione grafica, che consente di comprendere la distribuzione del carico DNS nei server DNS e mostra le tendenze nella frequenza di query DNS per ogni server.

    ![Ricerca log: risultati relativi alle query nei server DNS](./media/log-analytics-dns/dns-servers-query-load.png)

- **Carico query zone DNS**. Visualizza le statistiche al secondo delle query di tutte le zone DNS nei server DNS gestiti dalla soluzione. Fare clic sulla scheda **Metrica** per passare dalla visualizzazione di record dettagliati a una visualizzazione grafica dei risultati.
- **Eventi di configurazione**. Visualizza tutti gli eventi di modifica configurazione DNS e i messaggi associati. È quindi possibile filtrare questi eventi in base a data e ora dell'evento, ID evento, server DNS o categoria di attività. I dati consentono di controllare le modifiche apportate a specifici server DNS in date e ore specifiche.
- **Log analitico DNS**. Visualizza tutti gli eventi analitici in tutti i server DNS gestiti dalla soluzione. È quindi possibile filtrare questi eventi in base a data e ora dell'evento, ID evento, server DNS, IP client che ha eseguito la query di ricerca e categoria di attività del tipo di query. Gli eventi di analisi dei server DNS consentono il rilevamento delle attività nel server DNS. Viene registrato un evento di analisi ogni volta che il server invia o riceve informazioni DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Eseguire una ricerca usando la Ricerca log di DNS Analytics

Nella pagina Ricerca log è possibile creare una query. È possibile filtrare i risultati usando i controlli facet. È anche possibile creare query avanzate per trasformare, filtrare e creare report sui risultati. Per iniziare, usare le query seguenti:

1. Nella **casella della query di ricerca** digitare `Type=DnsEvents` per visualizzare tutti gli eventi DNS generati dai server DNS gestiti dalla soluzione. Nei risultati sono elencati i dati di log per tutti gli eventi relativi a query di ricerca, registrazioni dinamiche e modifiche di configurazione.

    ![Ricerca log: eventi DNS](./media/log-analytics-dns/log-search-dnsevents.png)  

    a. Per visualizzare i dati di log per query di ricerca, selezionare **LookUpQuery** come filtro **sottotipo** nel controllo facet sul lato sinistro. Viene visualizzata una tabella contenente gli eventi relativi a query di ricerca per il periodo di tempo selezionato.

    b. Per visualizzare i dati di log per registrazioni dinamiche, selezionare  **DynamicRegistration** come filtro **sottotipo** nel controllo facet sul lato sinistro. Viene visualizzata una tabella contenente tutti gli eventi relativi a registrazioni dinamiche per il periodo di tempo selezionato.

    c. Per visualizzare i dati di log per modifiche di configurazione, selezionare **ConfigurationChange** come filtro **sottotipo** nel controllo facet sul lato sinistro. Viene visualizzata una tabella contenente tutti gli eventi relativi a modifiche di configurazione per il periodo di tempo selezionato.

2. Nella **casella della query di ricerca** digitare `Type=DnsInventory` per visualizzare tutti i dati relativi all'inventario DNS per i server DNS gestiti dalla soluzione. Nei risultati vengono elencati i dati di log relativi ai server DNS, alle zone DNS e ai record di risorse.

    ![Ricerca log: inventario DNS](./media/log-analytics-dns/log-search-dnsinventory.png)

## <a name="feedback"></a>Commenti e suggerimenti

È possibile indicare commenti e suggerimenti in diversi modi:

- **UserVoice**. È possibile pubblicare idee sulle funzionalità di DNS Analytics che possono essere migliorate. Visitare la pagina [UserVoice relativa a Operations Management Suite](https://aka.ms/dnsanalyticsuservoice).
- **Partecipare alla coorte**. Microsoft è sempre interessata a includere nelle coorti nuovi clienti, che otterranno un accesso in anteprima alle nuove funzionalità e contribuiranno al miglioramento di DNS Analytics. Per partecipare, compilare questo [rapido sondaggio](https://aka.ms/dnsanalyticssurvey).

## <a name="next-steps"></a>Passaggi successivi

[Eseguire ricerche nei log](log-analytics-log-searches.md) per visualizzare record di log dettagliati per DNS.

