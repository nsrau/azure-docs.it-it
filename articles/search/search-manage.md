---
title: Amministrazione del servizio per Ricerca di Azure nel portale di Azure
description: Gestire Ricerca di Azure, un servizio di ricerca cloud ospitato in Microsoft Azure, usando il portale di Azure.
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: c87d1fdd-b3b8-4702-a753-6d7e29dbe0a2
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/18/2017
ms.author: heidist
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: c293de5b43103c8cbec01f61a26b8b28ac7e9116
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Amministrazione del servizio per Ricerca di Azure nel portale di Azure
> [!div class="op_single_selector"]
> * [Portale](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Ricerca di Azure è un servizio di ricerca basato sul cloud completamente gestito usato per offrire un'esperienza di ricerca avanzata nelle app personalizzate. Questo articolo illustra le attività di *amministrazione del servizio* che è possibile eseguire nel [portale di Azure](https://portal.azure.com) per un servizio di ricerca di cui si è già effettuato il provisining. *Service administration* è leggera e limitata alle attività seguenti:

* Gestire e proteggere l'accesso alle *chiavi API* usate per l'accesso in lettura o scrittura al servizio.
* Regolare la capacità del servizio modificando l'allocazione di partizioni e repliche.
* Monitorare l'uso della risorsa, relativamente ai limiti massimi del livello del servizio.

**Non nell'ambito** 

*gestione dei contenuti* (o gestione degli indici) si riferisce a operazioni come l'analisi del traffico di ricerca per conoscere il volume delle query, trovare i termini cercati dalle persone e sapere se dai risultati della ricerca i clienti riescono ad arrivare a specifici documenti dell'indice. Per avere assistenza in quest'area, visitare [Analisi del traffico di ricerca per Ricerca di Azure](search-traffic-analytics.md).

*prestazioni delle query* non rientrano nell'ambito di questi argomento. Per altre informazioni, vedere [Monitorare l'utilizzo e le statistiche in un servizio Ricerca di Azure](search-monitor-usage.md) e [Prestazioni e ottimizzazione](search-performance-optimization.md).

*L'aggiornamento* non è un'attività amministrativa. Poiché le risorse vengono allocate quando viene eseguito il provisioning sul servizio, lo spostamento a un altro livello richiede un nuovo servizio. Per informazioni dettagliate vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Diritti di amministratore
Il provisioning o il ritiro delle autorizzazioni per il servizio in sé può essere effettuato da un amministratore o da un coamministratore della sottoscrizione di Azure.

All'interno di un servizio, chiunque disponga dell'accesso all'URL del servizio e una chiave API di amministrazione ha accesso in lettura/scrittura al servizio. L'accesso in lettura-scrittura offre la possibilità di aggiungere, eliminare o modificare gli oggetti del server, incluse le chiavi API, gli indici, gli indicizzatori, le origini dati, le pianificazioni e le assegnazioni di ruolo implementate tramite i [ruoli definiti dal controllo degli accessi in base al ruolo](#rbac).

Ogni interazione dell'utente con Ricerca di Azure rientra in una di queste modalità: accesso in lettura/scrittura al servizio (diritti di amministratore) o accesso di sola lettura al servizio (diritti di query). Per altre informazioni, vedere [Gestire le chiavi API](#manage-keys).

<a id="sys-info"></a>

## <a name="set-rbac-roles-for-administrative-access"></a>Impostare i ruoli RBAC per l'accesso amministrativo
Azure offre un [modello di autorizzazione basata sui ruoli globali](../active-directory/role-based-access-control-configure.md) per tutti i servizi gestiti tramite il portale o le API di Resource Manager. I ruoli Proprietario, Collaboratore e Lettore determinano il livello di amministrazione del servizio per gli utenti, i gruppi e le entità di sicurezza di Active Directory assegnati a ogni ruolo. 

Per Ricerca di Azure, le autorizzazioni del controllo degli accessi in base al ruolo determinano le attività amministrative seguenti:

| Ruolo | Attività |
| --- | --- |
| Proprietario |Creare o eliminare il servizio o qualsiasi oggetto nel servizio, inclusi chiavi API, indici, indicizzatori, origini dati di un indicizzatore e pianificazioni di indicizzatore.<p>Visualizzare lo stato del servizio, inclusi conteggi e dimensioni.<p>Aggiunta o eliminazione dell'appartenenza al ruolo, che può essere gestita solo da un Proprietario.<p>Gli amministratori delle sottoscrizioni e i proprietari del servizio vengono aggiunti automaticamente al ruolo proprietario. |
| Collaboratore |Stesso livello di accesso del Proprietario, tranne la gestione dei ruoli Controllo degli accessi in base al ruolo. Ad esempio, un Collaboratore può visualizzare e rigenerare `api-key`, ma non può modificare le appartenenze ai ruoli. |
| Reader |Può visualizzare lo stato e le chiavi di query. I membri di questo ruolo non possono modificare la configurazione del servizio né visualizzare le chiavi amministratore. |

I ruoli non concedono diritti di accesso all'endpoint di servizio. Le operazioni del servizio di ricerca, ad esempio la gestione e il popolamento degli indici e le query sui dati di ricerca, sono controllate tramite le chiavi API, non tramite i ruoli. Per altre informazioni, vedere "Autorizzazioni per le operazioni di gestione e per le operazioni di dati" in [Che cos'è il controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md).

<a id="secure-keys"></a>
## <a name="logging-and-system-information"></a>Informazioni di sistema e registrazione
Ricerca di Azure non espone i file di log di un singolo servizio né tramite il portale né tramite interfacce programmatiche. Nel piano Basic Microsoft monitora tutti i servizi di Ricerca di Azure per verificare la disponibilità del 99,9% per ogni contratto di servizio. Se il servizio è lento o la velocità effettiva delle richieste è al di sotto delle soglie dei contratti di servizio, i team di supporto esaminano i file di log disponibili e risolvono il problema.

In termini di informazioni generali sul servizio, è possibile ottenere informazioni nei modi seguenti:

* Nel portale tramite notifiche, proprietà e messaggi di stato nel dashboard del servizio.
* Uso di [PowerShell](search-manage-powershell.md) o dell'interfaccia [API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/) per [ottenere le proprietà del servizio](https://docs.microsoft.com/rest/api/searchmanagement/services) oppure lo stato nell'uso della risorsa dell'indice.
* Tramite l' [analisi del traffico di ricerca](search-traffic-analytics.md), come indicato prima.

<a id="manage-keys"></a>

## <a name="manage-api-keys"></a>Gestire le chiavi API
Tutte le richieste indirizzate a un servizio di ricerca necessitano di una chiave API generata specificamente per il proprio servizio. Questa chiave API è l'unico meccanismo di autenticazione dell'accesso all'endpoint di servizio di ricerca. 

Una chiave API è una stringa composta da lettere e numeri generati casualmente. Tramite le [autorizzazioni del controllo degli accessi in base al ruolo](#rbac), è possibile eliminare o leggere le chiavi, ma non è possibile sostituire una chiave con una password definita dall'utente. 

Per accedere al servizio di ricerca vengono usati due tipi di chiavi:

* Amministratore (valida per qualsiasi operazione in lettura/scrittura sul servizio)
* Di query (valida per operazioni di sola lettura, ad esempio le query su un indice)

Una chiave API amministratore viene creata quando viene effettuato il provisioning del servizio. Esistono due chiavi amministratore, designate come *primaria* e *secondaria* per distinguerle, ma di fatto sono intercambiabili. Ogni servizio ha due chiavi amministratore in modo che sia possibile eseguire il rollover di una senza perdere l'accesso al servizio. È possibile rigenerare qualsiasi delle due chiavi amministrative ma non è possibile aggiungere chiavi al totale delle chiavi amministrative disponibili. È consentito un massimo di due chiavi amministratore per ogni servizio di ricerca.

Le chiavi di query sono progettate per le applicazioni client che chiamano il servizio di ricerca direttamente. È possibile creare fino a 50 chiavi di query. Nel codice dell'applicazione si specificano l'URL di ricerca e una chiave API di query per consentire l'accesso di sola lettura al servizio. Il codice dell'applicazione specifica anche l'indice usato dall'applicazione. L'endpoint, una chiave API per l'accesso di sola lettura e un indice di destinazione definiscono insieme l'ambito e il livello di accesso della connessione dall'applicazione client.

Per ottenere o rigenerare le chiavi API, aprire il Dashboard servizi. Fare clic su **CHIAVI** per aprire la pagina di gestione delle chiavi con effetto scorrimento. I comandi per rigenerare o creare le chiavi sono visualizzati nella parte superiore della pagina. Per impostazione predefinita, vengono create solo le chiavi amministratore. Le chiavi API di query devono essere create manualmente.

 ![][9]

<a id="rbac"></a>

## <a name="secure-api-keys"></a>Proteggere le chiavi API
La sicurezza delle chiavi viene garantita limitando l'accesso tramite il portale o le interfacce di Resource Manager (PowerShell o interfaccia della riga di comando). Come indicato, gli amministratori delle sottoscrizioni possono visualizzare e rigenerare tutte le chiavi API. Per precauzione, esaminare le assegnazioni di ruolo per sapere chi ha accesso alle chiavi amministratore.

1. Nel dashboard del servizio fare clic sull'icona Accesso per aprire il pannello Utenti con effetto scorrimento.
   ![][7]
2. In Utenti esaminare le assegnazioni di ruoli esistenti. Come previsto, gli amministratori della sottoscrizione hanno già accesso completo al servizio tramite il ruolo Proprietario.
3. Per altre informazioni, fare clic su **Amministratori della sottoscrizione** e quindi espandere l'elenco di assegnazioni di ruolo per visualizzare chi ha diritti di coamministrazione sul servizio di ricerca.

Per visualizzare le autorizzazioni di accesso, è anche possibile fare clic su **Ruoli** nel pannello Utenti. In questo modo vengono visualizzati i ruoli disponibili e il numero di utenti o gruppi assegnati a ogni ruolo.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorare l'uso delle risorse
Nel dashboard il monitoraggio delle risorse è limitato alle informazioni visualizzate nel dashboard del servizio e ad alcune metriche che è possibile ottenere effettuando query sul servizio. Nella sezione Utilizzo del Dashboard servizi è possibile determinare rapidamente se i livelli delle risorse di partizione sono adeguati per l'applicazione.

Usando l'API del servizio di ricerca, è possibile ottenere un conteggio dei documenti e degli indici. Esistono limiti rigidi associati a questi conteggi in base al livello di prezzo. Per altre informazioni, vedere [Limiti del servizio di ricerca](search-limits-quotas-capacity.md). 

* [Ottenere le statistiche di un indice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Conteggio documenti](https://docs.microsoft.com/rest/api/searchservice/count-documents)

> [!NOTE]
> Il comportamento della cache può determinare la dichiarazione di un limite più alto. Ad esempio, quando si usa il servizio condiviso, è possibile che venga visualizzato un conteggio documenti superiore al limite rigido di 10.000. Questa stima in eccesso è temporanea e verrà rilevata al successivo controllo di imposizione del limite. 
> 
> 

## <a name="disaster-recovery-and-service-outages"></a>Interruzioni di servizio e ripristino di emergenza

Sebbene sia possibile recuperare i dati, Ricerca di Azure non offre il failover immediato del servizio se è presente un'interruzione a livello di data center o di cluster. Se un cluster non funziona nel data center, il team operativo lo rileverà e provvederà a ripristinare il servizio. Si verificheranno tempi di inattività durante il ripristino del servizio. È possibile richiedere i crediti del servizio per compensarne la non disponibilità per il [Contratto di servizio (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Se un servizio continuo è necessario in caso di errori irreversibili che Microsoft non può controllare, è necessario [eseguire il provisioning di un servizio aggiuntivo](search-create-service-portal.md) in un'area diversa e implementare una strategia di replica geografica per garantire che gli indici siano completamente ridondanti in tutti i servizi.

I clienti che usano gli [indicizzatori](search-indexer-overview.md) per compilare e aggiornare gli indici possono gestire il ripristino di emergenza tramite gli indicizzatori specifici per l'area geografica che sfruttando la stessa origine dati. Due servizi in diverse aree, ognuno dei quali esegue un indicizzatore, possono indicizzare dalla stessa origine dati per ottenere la ridondanza geografica. Se si esegue l'indicizzazione da origini dati che hanno anche una ridondanza geografica, tenere presente che gli indicizzatori di Ricerca di Azure possono eseguire solo l'indicizzazione incrementale da repliche primarie. In un evento di failover, assicurarsi di puntare di nuovo all'indicizzatore per la nuova replica primaria. 

Se non si usano gli indicizzatori, l'utente userà il codice dell'applicazione per eseguire il push sugli oggetti e i dati per diversi servizi in parallelo. Per altre informazioni, vedere [Prestazioni e ottimizzazione in Ricerca di Azure](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Backup e ripristino

Poiché Ricerca di Azure non è una soluzione di archiviazione dati primaria, Microsoft non fornisce un meccanismo formale per il backup e il ripristino self-service. Il codice dell'applicazione usato per la creazione e la compilazione di un indice è l'opzione di ripristino di fatto se si elimina un indice per errore. 

Per ricompilare un indice, è necessario eliminarlo (supponendo che sia presente), ricreare l'indice nel servizio e ricaricare recuperando i dati dall'archivio dati primario. In alternativa, è possibile raggiungere [il supporto tecnico]() per salvare gli indici di recupero se è presente un'interruzione nell'area.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Aumentare o ridurre la quantità di risorse
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
Dopo periodi di alti volumi di query è possibile ridurre le repliche dopo che i carichi di query di ricerca si sono normalizzati, ad esempio al termine di un periodo di vendite per le feste.

Per fare ciò, spostare il dispositivo di scorrimento relativo alle repliche su un numero inferiore. Non vi sono altre azioni richieste da parte dell'utente. La riduzione del numero di repliche comporta il rilascio di macchine virtuali nel data center. Le operazioni di query e di inserimento dati verranno ora elaborate su un numero minore di VM rispetto a prima. Il limite minimo è di una replica.

### <a name="remove-partitions"></a>Rimuovere partizioni
A differenza della rimozione di repliche, che non richiede operazioni aggiuntive da parte dell'utente, potrebbe essere richiesto lavoro aggiuntivo se si occupa più archiviazione di quanta se ne vuole ottenere dopo una riduzione. Ad esempio, se la soluzione usa tre partizioni, il downsizing a una o due partizioni genererà un errore se il nuovo spazio di archiviazione è meno del necessario. Come è prevedibile, la scelta è di eliminare indici o documenti in un indice associato per liberare spazio oppure di mantenere la configurazione attuale.

Non è disponibile un metodo di rilevamento che indichi quante sottopartizioni di indice sono archiviate su una partizione specifica. Ogni partizione fornisce approssimativamente 25 GB di spazio di archiviazione, pertanto sarà necessario ridurre l'archiviazione a una dimensione che possa essere contenuta nel numero disponibile di partizioni. Se si vuole tornare a una partizione, questa dovrà contenere tutte e 12 le sottopartizioni.

Per pianificare in anticipo le esigenze future, è possibile verificare l'archiviazione (usando [Ottieni statistiche indice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) per verificare quanta archiviazione è stata effettivamente usata. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Procedure consigliate su scalabilità e distribuzione
Questo video di 30 minuti esamina le procedure consigliate per gli scenari di distribuzione avanzata, inclusi i carichi di lavoro con distribuzione geografica. Per le pagine della guida che illustrano gli stessi argomenti, è anche possibile vedere [Prestazioni e ottimizzazione in Ricerca di Azure](search-performance-optimization.md) .

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso i concetti relativi all'amministrazione del servizio, è consigliabile usare [PowerShell](search-manage-powershell.md) per automatizzare le attività.

È anche consigliabile esaminare l'[articolo sulle prestazioni e l'ottimizzazione](search-performance-optimization.md).

Si consiglia anche di guardare il video indicato nella sezione precedente che offre un approfondimento sulle tecniche indicate in questa sezione.

<!--Image references-->
[7]: ./media/search-manage/rbac-icon.png
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png




