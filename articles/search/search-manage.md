---
title: Amministrazione dei servizi per Azure ricerca cognitiva nel portale
titleSuffix: Azure Cognitive Search
description: Gestire un servizio di ricerca cognitiva di Azure, un servizio di ricerca cloud ospitato in Microsoft Azure, usando il portale di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 052d772c8ef885d594146d456ebb3cdcbbc0e383
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793514"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Amministrazione dei servizi per Azure ricerca cognitiva nel portale di Azure
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [di Microsoft Azure](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure ricerca cognitiva è un servizio di ricerca completamente gestito basato sul cloud, usato per creare un'esperienza di ricerca avanzata nelle app personalizzate. Questo articolo illustra le attività di amministrazione del servizio che è possibile eseguire nel [portale di Azure](https://portal.azure.com) per un servizio di ricerca di cui si è già effettuato il provisioning. L'amministrazione del servizio è leggera come previsto dalla progettazione ed è limitata alle attività seguenti:

> [!div class="checklist"]
> * Gestire l'accesso alle *chiavi API* usate per l'accesso in lettura o scrittura al servizio.
> * Regolare la capacità del servizio modificando l'allocazione di partizioni e repliche.
> * Monitorare l'uso della risorsa, relativamente ai limiti massimi del livello di servizio.

Tenere presente che l'*aggiornamento* non è un'attività amministrativa. Poiché le risorse vengono allocate quando viene eseguito il provisioning sul servizio, lo spostamento a un altro livello richiede un nuovo servizio. Per informazioni dettagliate, vedere [creare un servizio ricerca cognitiva di Azure](search-create-service-portal.md).

È possibile monitorare il volume di query e altre metriche e usare tali informazioni per modificare il servizio per tempi di risposta più rapidi. Per altre informazioni, vedere [Monitorare l'utilizzo e le statistiche in un servizio Ricerca di Azure](search-monitor-usage.md) e [Prestazioni e ottimizzazione](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Diritti di amministratore
Il provisioning o il ritiro delle autorizzazioni per il servizio in sé può essere effettuato da un amministratore o da un coamministratore della sottoscrizione di Azure.

All'interno di un servizio, chiunque disponga dell'accesso all'URL del servizio e una chiave API di amministrazione ha accesso in lettura/scrittura al servizio. L'accesso in lettura-scrittura offre la possibilità di aggiungere, eliminare o modificare gli oggetti del server, incluse le chiavi API, gli indici, gli indicizzatori, le origini dati, le pianificazioni e le assegnazioni di ruolo implementate tramite i [ruoli definiti dal controllo degli accessi in base al ruolo](search-security-rbac.md).

Tutte le interazioni dell'utente con ricerca cognitiva di Azure rientrano in una di queste modalità: accesso in lettura/scrittura al servizio (diritti di amministratore) o accesso in sola lettura al servizio (diritti di query). Per altre informazioni, vedere [Gestire le chiavi API](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Informazioni di sistema e registrazione
Azure ricerca cognitiva non espone i file di log per un singolo servizio tramite il portale o le interfacce programmatiche. Al livello Basic e superiore, Microsoft monitora tutti i servizi ricerca cognitiva di Azure per la disponibilità del 99,9% per ogni contratto di servizio. Se il servizio è lento o la velocità effettiva delle richieste è al di sotto delle soglie dei contratti di servizio, i team di supporto esaminano i file di log disponibili e risolvono il problema.

In termini di informazioni generali sul servizio, è possibile ottenere informazioni nei modi seguenti:

* Nel portale tramite notifiche, proprietà e messaggi di stato nel dashboard del servizio.
* Uso di [PowerShell](search-manage-powershell.md) o dell'interfaccia [API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/) per [ottenere le proprietà del servizio](https://docs.microsoft.com/rest/api/searchmanagement/services) oppure lo stato nell'uso della risorsa dell'indice.


<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorare l'uso delle risorse
Nel dashboard il monitoraggio delle risorse è limitato alle informazioni visualizzate nel dashboard del servizio e ad alcune metriche che è possibile ottenere effettuando query sul servizio. Nella sezione Utilizzo del Dashboard servizi è possibile determinare rapidamente se i livelli delle risorse di partizione sono adeguati per l'applicazione. È possibile effettuare il provisioning di risorse esterne, ad esempio monitoraggio di Azure, se si desidera acquisire e rendere permanente gli eventi registrati. Per altre informazioni, vedere [monitoraggio di Azure ricerca cognitiva](search-monitor-usage.md).

Usando l'API REST del servizio di ricerca, è possibile ottenere un conteggio dei documenti e degli indici a livello di codice: 

* [Ottenere le statistiche di indice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Contare i documenti](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Interruzioni di servizio e ripristino di emergenza

Sebbene sia possibile recuperare i dati, Azure ricerca cognitiva non fornisce il failover immediato del servizio in caso di interruzione a livello di cluster o di data center. Se un cluster non funziona nel data center, il team operativo lo rileverà e provvederà a ripristinare il servizio. Si verificheranno tempi di inattività durante il ripristino del servizio ma è possibile richiedere i crediti del servizio per compensarne la non disponibilità in base al [Contratto di servizio (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Se un servizio continuo è necessario in caso di errori irreversibili che Microsoft non può controllare, è necessario [eseguire il provisioning di un servizio aggiuntivo](search-create-service-portal.md) in un'area diversa e implementare una strategia di replica geografica per garantire che gli indici siano completamente ridondanti in tutti i servizi.

I clienti che usano gli [indicizzatori](search-indexer-overview.md) per compilare e aggiornare gli indici possono gestire il ripristino di emergenza tramite gli indicizzatori specifici per l'area geografica che sfruttando la stessa origine dati. Due servizi in diverse aree, ognuno dei quali esegue un indicizzatore, possono indicizzare la stessa origine dati per ottenere la ridondanza geografica. Se si esegue l'indicizzazione da origini dati anche con ridondanza geografica, tenere presente che gli indicizzatori di Azure ricerca cognitiva possono eseguire l'indicizzazione incrementale solo dalle repliche primarie. In un evento di failover, assicurarsi di puntare di nuovo all'indicizzatore per la nuova replica primaria. 

Se non si usano gli indicizzatori, l'utente userà il codice dell'applicazione per eseguire il push sugli oggetti e i dati per diversi servizi in parallelo. Per altre informazioni, vedere [prestazioni e ottimizzazione in Azure ricerca cognitiva](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Eseguire backup e ripristino

Poiché ricerca cognitiva di Azure non è una soluzione di archiviazione dati primaria, non è disponibile un meccanismo formale per il backup e il ripristino self-service. Tuttavia, è possibile usare il codice di esempio **index-backup-restore** in questo [repository di esempio di Azure ricerca cognitiva .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) per eseguire il backup della definizione dell'indice e dello snapshot in una serie di file JSON e quindi usare questi file per ripristinare l'indice, se necessario. Questo strumento può inoltre spostare gli indici tra i livelli di servizio.

In caso contrario, il codice dell'applicazione utilizzato per creare e popolare un indice è l'opzione di ripristino de facto se si elimina un indice per errore. Per ricompilare un indice, è necessario eliminarlo (supponendo che sia presente), ricreare l'indice nel servizio e ricaricare recuperando i dati dall'archivio dati primario.

<a id="scale"></a>

## <a name="scale-up-or-down"></a>Scalabilità verticale
Ogni servizio di ricerca viene creato con un minimo di una replica e una partizione. Se l'utente ha effettuato l'iscrizione a un [livello che offre risorse dedicate](search-limits-quotas-capacity.md), fare clic su sul riquadro **SCALA** nel dashboard del servizio per regolare l'uso delle risorse.

Quando si aggiunge capacità tramite l'una o l'altra risorsa, la risorsa aggiunta viene usata dal servizio in modo automatico. Non sono necessarie altre azioni da parte dell'utente, ma vi sarà un lieve ritardo prima che l'impatto delle nuove risorse sia apprezzabile. Possono essere necessari 15 o più minuti per il provisioning delle risorse aggiuntive.

 ![][10]

### <a name="add-replicas"></a>Aggiungere repliche
L'aumento delle query al secondo o il raggiungimento della disponibilità elevata si ottengono mediante l'aggiunta di repliche. Ogni replica presenta una copia di un indice, quindi l'aggiunta di un'altra replica si traduce in un indice aggiuntivo disponibile per la gestione delle richieste di query del servizio. Per una disponibilità elevata, sono necessarie almeno 3 repliche. Per informazioni dettagliate, vedere [Pianificazione della capacità](search-capacity-planning.md).

Un servizio di ricerca con un numero superiore di repliche può eseguire il bilanciamento del carico delle richieste di query su un numero maggiore di indici. Per un determinato volume di query, la velocità effettiva delle query sarà maggiore in presenza di più copie dell'indice disponibili per soddisfare la richiesta. Se si verificano latenze delle query, ci si può aspettare un netto miglioramento delle prestazioni una volta portate online le repliche aggiuntive.

Sebbene la velocità effettiva delle query aumenti quando si aggiungono repliche, non viene effettivamente raddoppiata o triplicata in rapporto al numero di repliche aggiunte. Tutte le applicazioni di ricerca sono influenzate da fattori esterni che possono influire sulle prestazioni di query. Query complesse e latenza di rete sono due fattori che contribuiscono alle variazioni nei tempi di risposta alle query.

### <a name="add-partitions"></a>Aggiungere partizioni
Per la maggior parte delle applicazioni di servizio è indispensabile un numero maggiore di repliche invece che di partizioni. Per i casi in cui è necessario un numero maggiore di documenti, l'iscrizione al servizio Standard permette di aggiungere partizioni. Il livello Basic non offre partizioni aggiuntive.

Al livello Standard le partizioni vengono aggiunte in multipli di 12 (ossia 1, 2, 3, 4, 6 o 12). Si tratta di un elemento del partizionamento orizzontale. Un indice viene creato in 12 sottopartizioni, tutte archiviabili a propria volta in 1 partizione o equamente distribuibili in 2, 3, 4, 6 o 12 partizioni (in quest'ultimo caso, una per partizione).

### <a name="remove-replicas"></a>Rimuovere repliche
Dopo periodi di alti volumi di query è possibile usare il dispositivo di scorrimento per ridurre le repliche dopo che i carichi di query di ricerca si sono normalizzati, ad esempio al termine di un periodo di vendite per le feste. Non vi sono altre azioni richieste da parte dell'utente. La riduzione del numero di repliche comporta il rilascio di macchine virtuali nel data center. Le operazioni di query e di inserimento dati verranno ora elaborate su un numero minore di VM rispetto a prima. Il requisito minimo è una replica.

### <a name="remove-partitions"></a>Rimuovere partizioni
A differenza della rimozione di repliche, che non richiede operazioni aggiuntive da parte dell'utente, potrebbe essere richiesto lavoro aggiuntivo se si occupa più archiviazione di quanta se ne vuole ottenere dopo una riduzione. Ad esempio, se la soluzione usa tre partizioni, il ridimensionamento a una o due partizioni genererà un errore se il nuovo spazio di archiviazione è meno di quello necessario per l'hosting dell'indice. Come è prevedibile, la scelta è di eliminare indici o documenti in un indice associato per liberare spazio oppure di mantenere la configurazione attuale.

Non è disponibile un metodo di rilevamento che indichi quante sottopartizioni di indice sono archiviate su una partizione specifica. Ogni partizione fornisce approssimativamente 25 GB di spazio di archiviazione, pertanto sarà necessario ridurre l'archiviazione a una dimensione che possa essere contenuta nel numero disponibile di partizioni. Se si vuole tornare a una partizione, questa dovrà contenere tutte e 12 le sottopartizioni.

Per pianificare in anticipo le esigenze future, è possibile verificare l'archiviazione (usando [Ottieni statistiche indice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) per verificare quanta archiviazione è stata effettivamente usata. 

<a id="next-steps"></a>

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso i concetti relativi all'amministrazione del servizio, è consigliabile usare [PowerShell](search-manage-powershell.md) per automatizzare le attività.

È anche consigliabile esaminare l'[articolo sulle prestazioni e l'ottimizzazione](search-performance-optimization.md).

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



