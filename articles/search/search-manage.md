---
title: Amministrazione dei servizi nel portale
titleSuffix: Azure Cognitive Search
description: Gestire un servizio di ricerca cognitiva di Azure, un servizio di ricerca cloud ospitato in Microsoft Azure, usando il portale di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/24/2020
ms.openlocfilehash: 84ddc4b427f6dc168c044f34b41e81e3b0ff19e5
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935042"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Amministrazione dei servizi per Azure ricerca cognitiva nel portale di Azure

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Portale](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure ricerca cognitiva è un servizio di ricerca completamente gestito basato sul cloud, usato per creare un'esperienza di ricerca avanzata nelle app personalizzate. Questo articolo illustra le attività di amministrazione del servizio che è possibile eseguire nel [portale di Azure](https://portal.azure.com) per un servizio di ricerca di cui si è già effettuato il provisining. L'amministrazione del servizio è leggera come previsto dalla progettazione ed è limitata alle attività seguenti:

* Controllare lo spazio di archiviazione usando il collegamento di **utilizzo** a metà pagina.
* Controllare la latenza e i volumi di query usando il collegamento di **monitoraggio** a metà pagina e se le richieste sono state limitate.
* Gestire l'accesso con la pagina **chiavi** a sinistra.
* Modificare la capacità usando la pagina **scala** a sinistra.

Le stesse attività eseguite nel portale possono essere gestite anche a livello di codice tramite le [API di gestione](/rest/api/searchmanagement/) e il [modulo AZ. search PowerShell](search-manage-powershell.md). Le attività amministrative sono completamente rappresentate tra le interfacce del portale e a livello di codice. Nessuna attività amministrativa specifica disponibile in una sola modalità.

Azure ricerca cognitiva sfrutta altri servizi di Azure per un monitoraggio e una gestione più approfonditi. Di per sé, gli unici dati archiviati con un servizio di ricerca sono il contenuto, ovvero indici, indicizzatori e definizioni delle origini dati e altri oggetti. Le metriche segnalate alle pagine del portale vengono estratte da log interni in un ciclo di 30 giorni in sequenza. Per la conservazione dei log controllata dall'utente e per gli eventi aggiuntivi, è necessario [monitoraggio di Azure](../azure-monitor/index.yml). 

## <a name="fixed-service-properties"></a>Proprietà del servizio fisse

Alcuni aspetti di un servizio di ricerca sono determinati quando viene eseguito il provisioning del servizio e non possono essere modificati in un secondo momento:

* Nome del servizio (non è possibile rinominare un servizio)
* Posizione del servizio (attualmente non è possibile spostare un servizio intatto in un'altra area)
* Numero massimo di repliche e partizioni (determinate dal livello Basic o standard)

Se si inizia con Basic con il massimo di una partizione e sono ora necessarie più partizioni, sarà necessario [creare un nuovo servizio](search-create-service-portal.md) a un livello superiore e ricreare il contenuto nel nuovo servizio. 

## <a name="administrator-rights"></a>Diritti di amministratore

Il provisioning o il ritiro delle autorizzazioni per il servizio in sé può essere effettuato da un amministratore o da un coamministratore della sottoscrizione di Azure.

Per quanto riguarda l'accesso all'endpoint, chiunque disponga dell'accesso all'URL del servizio e una chiave API può accedere al contenuto. Per altre informazioni sulle chiavi, vedere [gestire le chiavi API](search-security-api-keys.md).

* L'accesso in sola lettura al servizio è costituito da diritti di query, in genere concessi a un'applicazione client assegnando l'URL e una chiave API di query.
* L'accesso in lettura/scrittura consente di aggiungere, eliminare o modificare oggetti server, tra cui chiavi API, indici, indicizzatori, origini dati e pianificazioni. L'accesso in lettura/scrittura viene concesso assegnando l'URL, una chiave API di amministrazione.

I diritti per l'apparecchio di provisioning del servizio vengono concessi tramite assegnazioni di ruolo. Il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md) è un sistema di autorizzazione basato su [Azure Resource Manager](../azure-resource-manager/management/overview.md) per il provisioning delle risorse di Azure. 

Nel contesto di Azure ricerca cognitiva le [assegnazioni di ruolo di Azure](search-security-rbac.md) determineranno chi può eseguire attività, indipendentemente dal fatto che stiano usando il [portale](search-manage.md), [PowerShell](search-manage-powershell.md)o le [API REST di gestione](/rest/api/searchmanagement/search-howto-management-rest-api):

* Creare o eliminare un servizio
* Ridimensionare il servizio
* Eliminare o rigenerare le chiavi API
* Abilitare la registrazione diagnostica (crea servizi)
* Abilitare analisi del traffico (creare servizi)

> [!TIP]
> Usando i meccanismi a livello di Azure, è possibile bloccare una sottoscrizione o una risorsa per evitare l'eliminazione accidentale o non autorizzata del servizio di ricerca da parte degli utenti con diritti di amministratore. Per altre informazioni, vedere [bloccare le risorse per impedire l'eliminazione imprevista](../azure-resource-manager/management/lock-resources.md).

## <a name="logging-and-system-information"></a>Informazioni di sistema e registrazione

Al livello Basic e superiore, Microsoft monitora tutti i servizi ricerca cognitiva di Azure per la disponibilità del 99,9% per ogni contratto di servizio. Se il servizio è lento o la velocità effettiva delle richieste è al di sotto delle soglie dei contratti di servizio, i team di supporto esaminano i file di log disponibili e risolvono il problema.

Azure ricerca cognitiva sfrutta [monitoraggio di Azure](../azure-monitor/index.yml) per raccogliere e archiviare le attività di indicizzazione e query. Un servizio di ricerca da solo archivia il proprio contenuto (indici, definizioni dell'indicizzatore, definizioni delle origini dati, definizioni di competenze, mappe sinonimi). La memorizzazione nella cache e le informazioni registrate vengono archiviate fuori servizio, spesso in un account di archiviazione di Azure. Per altre informazioni sulla registrazione dei carichi di lavoro di indicizzazione e query, vedere [raccogliere e analizzare i dati di log](search-monitor-logs.md).

In termini di informazioni generali sul servizio, usando solo le funzionalità predefinite di Azure ricerca cognitiva, è possibile ottenere informazioni nei modi seguenti:

* Utilizzo della pagina **Panoramica** servizio tramite notifiche, proprietà e messaggi di stato.
* Usare [PowerShell](search-manage-powershell.md) o l' [API REST di gestione](/rest/api/searchmanagement/) per [ottenere le proprietà del servizio](/rest/api/searchmanagement/services). Al livello programmatico non sono state fornite nuove informazioni o operazioni. Le interfacce sono disponibili per consentire la scrittura di script.

## <a name="monitor-resource-usage"></a>Monitorare l'uso delle risorse

Nel dashboard il monitoraggio delle risorse è limitato alle informazioni visualizzate nel dashboard del servizio e ad alcune metriche che è possibile ottenere effettuando query sul servizio. Nella sezione Utilizzo del Dashboard servizi è possibile determinare rapidamente se i livelli delle risorse di partizione sono adeguati per l'applicazione. È possibile effettuare il provisioning di risorse esterne, ad esempio monitoraggio di Azure, se si desidera acquisire e rendere permanente gli eventi registrati. Per altre informazioni, vedere [monitoraggio di Azure ricerca cognitiva](search-monitor-usage.md).

Usando l'API REST del servizio di ricerca, è possibile ottenere un conteggio dei documenti e degli indici a livello di codice: 

* [Ottenere le statistiche di un indice](/rest/api/searchservice/Get-Index-Statistics)
* [Contare i documenti](/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Interruzioni di servizio e ripristino di emergenza

Sebbene sia possibile recuperare i dati, Azure ricerca cognitiva non fornisce il failover immediato del servizio in caso di interruzione a livello di cluster o di data center. Se un cluster non funziona nel data center, il team operativo lo rileverà e provvederà a ripristinare il servizio. Si verificheranno tempi di inattività durante il ripristino del servizio ma è possibile richiedere i crediti del servizio per compensarne la non disponibilità in base al [Contratto di servizio (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Se un servizio continuo è necessario in caso di errori irreversibili che Microsoft non può controllare, è necessario [eseguire il provisioning di un servizio aggiuntivo](search-create-service-portal.md) in un'area diversa e implementare una strategia di replica geografica per garantire che gli indici siano completamente ridondanti in tutti i servizi.

I clienti che usano gli [indicizzatori](search-indexer-overview.md) per compilare e aggiornare gli indici possono gestire il ripristino di emergenza tramite gli indicizzatori specifici per l'area geografica che sfruttando la stessa origine dati. Due servizi in diverse aree, ognuno dei quali esegue un indicizzatore, possono indicizzare la stessa origine dati per ottenere la ridondanza geografica. Se si esegue l'indicizzazione da origini dati anche con ridondanza geografica, tenere presente che gli indicizzatori di Azure ricerca cognitiva possono eseguire solo l'indicizzazione incrementale (Unione di aggiornamenti da documenti nuovi, modificati o eliminati) dalle repliche primarie. In un evento di failover, assicurarsi di puntare di nuovo all'indicizzatore per la nuova replica primaria. 

Se non si usano gli indicizzatori, l'utente userà il codice dell'applicazione per eseguire il push sugli oggetti e i dati per diversi servizi in parallelo. Per altre informazioni, vedere [prestazioni e ottimizzazione in Azure ricerca cognitiva](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Backup e ripristino

Poiché ricerca cognitiva di Azure non è una soluzione di archiviazione dati primaria, non è disponibile un meccanismo formale per il backup e il ripristino self-service. Tuttavia, è possibile usare il codice di esempio **index-backup-restore** in questo [repository di esempio di Azure ricerca cognitiva .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) per eseguire il backup della definizione dell'indice e dello snapshot in una serie di file JSON e quindi usare questi file per ripristinare l'indice, se necessario. Questo strumento può inoltre spostare gli indici tra i livelli di servizio.

In caso contrario, il codice dell'applicazione utilizzato per creare e popolare un indice è l'opzione di ripristino de facto se si elimina un indice per errore. Per ricompilare un indice, è necessario eliminarlo (supponendo che sia presente), ricreare l'indice nel servizio e ricaricare recuperando i dati dall'archivio dati primario.

## <a name="scale-up-or-down"></a>Aumentare o ridurre la quantità di risorse

Ogni servizio di ricerca viene creato con un minimo di una replica e una partizione. Se è stata eseguita l'iscrizione a un [livello che supporta una maggiore capacità](search-limits-quotas-capacity.md), fare clic su **Ridimensiona** nel riquadro di spostamento a sinistra per modificare l'utilizzo delle risorse.

Quando si aggiunge capacità tramite l'una o l'altra risorsa, la risorsa aggiunta viene usata dal servizio in modo automatico. Non sono necessarie altre azioni da parte dell'utente, ma vi sarà un lieve ritardo prima che l'impatto delle nuove risorse sia apprezzabile. Possono essere necessari 15 o più minuti per il provisioning delle risorse aggiuntive.

### <a name="add-replicas"></a>Aggiungere repliche

L'aumento delle query al secondo o il raggiungimento della disponibilità elevata si ottengono mediante l'aggiunta di repliche. Ogni replica presenta una copia di un indice, quindi l'aggiunta di un'altra replica si traduce in un indice aggiuntivo disponibile per la gestione delle richieste di query del servizio. Per la disponibilità elevata sono necessari almeno 3 repliche. per informazioni dettagliate, vedere [regolazione della capacità](search-capacity-planning.md) .

Un servizio di ricerca con un numero superiore di repliche può eseguire il bilanciamento del carico delle richieste di query su un numero maggiore di indici. Per un determinato volume di query, la velocità effettiva delle query sarà maggiore in presenza di più copie dell'indice disponibili per soddisfare la richiesta. Se si verificano latenze delle query, ci si può aspettare un netto miglioramento delle prestazioni una volta portate online le repliche aggiuntive.

Sebbene la velocità effettiva delle query aumenti quando si aggiungono repliche, non viene effettivamente raddoppiata o triplicata in rapporto al numero di repliche aggiunte. Tutte le applicazioni di ricerca sono influenzate da fattori esterni che possono influire sulle prestazioni di query. Query complesse e latenza di rete sono due fattori che contribuiscono alle variazioni nei tempi di risposta alle query.

### <a name="add-partitions"></a>Aggiungere partizioni

È più comune aggiungere repliche, ma quando l'archiviazione è vincolata, è possibile aggiungere partizioni per ottenere una maggiore capacità. Il livello in cui è stato effettuato il provisioning del servizio determina se è possibile aggiungere partizioni. Il livello Basic è bloccato in una partizione. I livelli standard e versioni successive supportano partizioni aggiuntive.

Le partizioni vengono aggiunte in divisori di 12 (in particolare 1, 2, 3, 4, 6 o 12). Si tratta di un elemento del partizionamento orizzontale. Un indice viene creato in 12 sottopartizioni, tutte archiviabili a propria volta in 1 partizione o equamente distribuibili in 2, 3, 4, 6 o 12 partizioni (in quest'ultimo caso, una per partizione).

### <a name="remove-replicas"></a>Rimuovere repliche

Dopo periodi di alti volumi di query è possibile usare il dispositivo di scorrimento per ridurre le repliche dopo che i carichi di query di ricerca si sono normalizzati, ad esempio al termine di un periodo di vendite per le feste. Non vi sono altre azioni richieste da parte dell'utente. La riduzione del numero di repliche comporta il rilascio di macchine virtuali nel data center. Le operazioni di query e di inserimento dati verranno ora elaborate su un numero minore di VM rispetto a prima. Il requisito minimo è una replica.

### <a name="remove-partitions"></a>Rimuovere partizioni

A differenza della rimozione di repliche, che non richiede operazioni aggiuntive da parte dell'utente, potrebbe essere richiesto lavoro aggiuntivo se si occupa più archiviazione di quanta se ne vuole ottenere dopo una riduzione. Ad esempio, se la soluzione usa tre partizioni, il ridimensionamento a una o due partizioni genererà un errore se il nuovo spazio di archiviazione è meno di quello necessario per l'hosting dell'indice. Come è prevedibile, la scelta è di eliminare indici o documenti in un indice associato per liberare spazio oppure di mantenere la configurazione attuale.

Non è disponibile un metodo di rilevamento che indichi quante sottopartizioni di indice sono archiviate su una partizione specifica. Ogni partizione fornisce approssimativamente 25 GB di spazio di archiviazione, pertanto sarà necessario ridurre l'archiviazione a una dimensione che possa essere contenuta nel numero disponibile di partizioni. Se si vuole tornare a una partizione, questa dovrà contenere tutte e 12 le sottopartizioni.

Per pianificare in anticipo le esigenze future, è possibile verificare l'archiviazione (usando [Ottieni statistiche indice](/rest/api/searchservice/Get-Index-Statistics)) per verificare quanta archiviazione è stata effettivamente usata. 

## <a name="next-steps"></a>Passaggi successivi

* Automatizzare con [PowerShell](search-manage-powershell.md)

* Esaminare [le tecniche di ottimizzazione e prestazioni](search-performance-optimization.md)

* Esaminare le [funzionalità di sicurezza](search-security-overview.md) per proteggere il contenuto e le operazioni

* Abilitare la [registrazione diagnostica](search-monitor-logs.md) per monitorare i carichi di lavoro di query e indicizzazione