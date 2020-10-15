---
title: Query salvate in monitoraggio di Azure Log Analytics
description: Query che è possibile avviare e modificare in base alle proprie esigenze
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 06/16/2020
ms.openlocfilehash: 462358ab967d4ba26a265d7b5f401b995223815e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85959843"
---
# <a name="saved-queries-in-azure-monitor-log-analytics"></a>Query salvate in monitoraggio di Azure Log Analytics

Log Analytics offre set di query di esempio che è possibile eseguire autonomamente o da utilizzare come punto di partenza per le proprie query. Questo articolo descrive le query di esempio e il modo in cui usarle.

Se non si ha familiarità con Log Analytics o il linguaggio di query KQL, le query di esempio sono un ottimo modo per iniziare. Possono fornire informazioni dettagliate immediate su una risorsa e fornire un modo interessante per iniziare a imparare e usare KQL, riducendo così il tempo necessario per iniziare a usare Log Analytics. Abbiamo raccolto e curato più di 250 query di esempio progettate per fornire un valore immediato e che il numero di query di esempio è in continua crescita.

## <a name="in-context-queries"></a>Query nel contesto

La nuova esperienza filtra e suggerisce le query nel contesto. In altre parole, il sistema Mostra automaticamente solo le query rilevanti per l'ambito selezionato.

- Per una **singola risorsa** , le query vengono filtrate in base al tipo di risorsa.
- Per un **gruppo di risorse** , le query vengono filtrate in base alle risorse nel gruppo di risorse specifico.
- Per un' **area di lavoro** , le query vengono filtrate in base alle soluzioni installate nell'area di lavoro.

Questo comportamento è coerente per tutti gli ambiti di Log Analytics. Se non viene visualizzato un esempio di query per il tipo di risorsa desiderato, è possibile che si verifichino dei filtri a causa di un contesto. In una sezione successiva viene descritto come rimuovere l'ambito nel contesto in modo che sia possibile visualizzare tutte le query possibili.

> [!TIP]
> Maggiore è il numero di risorse disponibili nell'ambito, maggiore è il tempo necessario al portale per filtrare e visualizzare la finestra di dialogo di query di esempio.

## <a name="example-query-user-interface"></a>Interfaccia utente di query di esempio

È possibile ottenere query di esempio da due posizioni diverse.

### <a name="example-query-dialog"></a>Finestra di dialogo query di esempio

Quando si accede per la prima volta all'esperienza Log Analytics, viene visualizzata automaticamente la *finestra di dialogo query di esempio* .  È anche possibile accedervi facendo clic nella parte superiore destra della schermata sulle **query di esempio**.

![Barra laterale](media/saved-queries/sidebar-2.png)

La finestra di dialogo di esempio della query viene visualizzata come illustrato di seguito:  

![Schermata di query di esempio](media/saved-queries/example-query-start.png)

Lo screenshot precedente Mostra la schermata dei log per un'istanza di Azure Key Vault. Come indicato in precedenza in questo articolo, le query vengono visualizzate nel contesto.  Di conseguenza, lo screenshot mostra solo Key Vault esempi correlati. Se si seleziona un'intera sottoscrizione, verranno visualizzate le query per tutti i tipi di risorsa della sottoscrizione.  

Ogni query di esempio è rappresentata da una scheda. È possibile analizzare rapidamente le query per trovare le informazioni necessarie. È possibile eseguire la query direttamente dalla finestra di dialogo o scegliere di caricarla nell'editor di query per ottimizzare ulteriormente l'ottimizzazione.

### <a name="sidebar-query-experience"></a>Esperienza di query Sidebar

È possibile accedere a tutte le funzionalità dell'esperienza di dialogo dal riquadro query sul lato sinistro della barra laterale di Log Analytics. È possibile passare il puntatore del mouse su un nome di query per ottenere la descrizione della query e la funzionalità aggiuntiva.

![Barra laterale](media/saved-queries/sidebar-3.png)

## <a name="finding-and-filtering-queries"></a>Ricerca e filtro di query

Le opzioni di questa sezione sono disponibili sia nella finestra di dialogo che nell'esperienza della query Sidebar, ma con un'interfaccia utente leggermente diversa.  

### <a name="use-favorites"></a>USA Preferiti

È possibile aggiungere a Preferiti le query utilizzate di frequente per offrire un accesso più rapido.

> [!TIP]
> La raccolta e la visualizzazione delle query in un secondo momento è un modo efficace per iniziare. Trovare le query necessarie e fare clic sulla stella accanto alla query per aggiungerla ai Preferiti. Se più tardi si trova la query non è utile, è possibile annullare l'operazione.  

### <a name="filtering-and-group-by"></a>Applicazione di filtri e raggruppamento

Mentre nella finestra di dialogo query vengono visualizzati i filer per visualizzare solo le query con il contesto corretto, è possibile scegliere di rimuovere il filtro e visualizzare tutte le query.

### <a name="group-by"></a>Group by

Per modificare il raggruppamento delle query, fare clic sull'elenco a discesa *Raggruppa per* :

![Schermata di esempio della schermata di query](media/saved-queries/example-query-groupby.png)

La finestra di dialogo supporta il raggruppamento per:

- **Tipo di risorsa** : una risorsa definita in Azure, ad esempio una macchina virtuale. Vedere le informazioni di [riferimento sulla tabella di monitoraggio](/azure/azure-monitor/reference/tables/tables-resourcetype) di Azure per un mapping completo dei log di monitoraggio di Azure/tabelle log Analytics al tipo di risorsa.  
- **Categoria** : tipo di informazioni, ad esempio *sicurezza* o *controllo*. Le categorie sono identiche alle categorie definite nel riquadro laterale tabelle. Per un elenco completo delle categorie, vedere informazioni di [riferimento sulla tabella di monitoraggio di Azure](/azure/azure-monitor/reference/tables/tables-category) .  
- **Soluzione** : una soluzione di monitoraggio di Azure associata alle query
- **Argomento** : l'argomento della query di esempio, ad esempio i *log attività* o i *log delle app*. La proprietà topic è univoca per le query di esempio e può variare a seconda del tipo di risorsa specifico.

Anche i valori di raggruppamento fungono da un sommario attivo. Facendo clic su uno dei valori sul lato sinistro dello schermo, la visualizzazione query viene visualizzata a destra dell'elemento selezionato.

### <a name="filter"></a>Filtra

È anche possibile filtrare le query in base ai valori di GroupBy citati in precedenza. Nella finestra di dialogo query di esempio, i filtri si trovano nella parte superiore.

![Esempio di filtro della schermata query](media/saved-queries/example-query-filter.png)

### <a name="combining-group-by-and-filter"></a>Combinazione di Group by e Filter

Le funzionalità filtro e raggruppa per sono progettate per funzionare in tandem. Forniscono flessibilità nel modo in cui le query vengono disposte. Se ad esempio si usa un gruppo di risorse con più risorse, è possibile filtrare in base a un tipo di risorsa specifico e disporre le query risultanti per argomento.

## <a name="sample-query-dialog-appearance-behavior"></a>Comportamento dell'aspetto della finestra di dialogo query di esempio

Se si è un KQL Pro e si preferisce ottenere direttamente l'editor di query, è possibile attivare o disattivare la finestra di dialogo della query "off". Con l'interruttore disattivato, la finestra di dialogo di query di esempio non viene caricata quando Log Analytics schermata viene caricata.

![Esempi on-off](media/saved-queries/examples-on-off.png)

È sempre possibile accedere all'esperienza popup di query di esempio dal pulsante *query di esempio* nella barra superiore del log Analytics.

## <a name="query-explorer"></a>Esplora query

L'esperienza Esplora query per il salvataggio e la condivisione di query generate dall'utente rimane invariata per il momento.

## <a name="next-steps"></a>Passaggi successivi

[Introduzione alle query KQL](get-started-queries.md)

