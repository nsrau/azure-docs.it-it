---
title: Ripristino di emergenza e distribuzione geografica di Azure Durable Functions
description: Informazioni sul ripristino di emergenza e sulla distribuzione geografica in Funzioni durevoli.
author: MS-Santi
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: azfuncdf
ms.openlocfilehash: 01c400f51cce85ef39e9d39bcad1221253c6942d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89071211"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Ripristino di emergenza e distribuzione geografica in Azure Durable Functions

Microsoft si impegna per fare in modo che i servizi di Azure siano sempre disponibili. Possono tuttavia verificarsi interruzioni dei servizi non pianificate. Se l'applicazione richiede la resilienza, Microsoft consiglia di configurare l'app per la ridondanza geografica. I clienti dovrebbero anche predisporre un piano di ripristino di emergenza per gestire un'interruzione dei servizi a livello di area. Una parte importante di un piano di ripristino di emergenza è la preparazione del failover alla replica secondaria dell'app e dell'archiviazione nel caso in cui la replica primaria diventi non disponibile.

In Durable Functions, per impostazione predefinita, tutto lo stato viene reso permanente in archiviazione di Azure. Un [Hub attività](durable-functions-task-hubs.md) è un contenitore logico per le risorse di archiviazione di Azure usate per le [orchestrazioni](durable-functions-types-features-overview.md#orchestrator-functions) e le [entità](durable-functions-types-features-overview.md#entity-functions). Le funzioni dell'agente di orchestrazione, dell'attività e dell'entità possono interagire tra loro solo quando appartengono allo stesso hub attività. Questo documento si riferisce agli hub attività quando si descrivono scenari per mantenere le risorse di archiviazione di Azure a disponibilità elevata.

Le orchestrazioni e le entità possono essere attivate usando le [funzioni client](durable-functions-types-features-overview.md#client-functions) che vengono avviate tramite http o uno degli altri tipi di trigger di funzioni di Azure supportate. Possono anche essere attivati usando le [API HTTP predefinite](durable-functions-http-features.md#built-in-http-apis). Per semplicità, questo articolo è incentrato sugli scenari che coinvolgono l'archiviazione di Azure e i trigger di funzione basati su HTTP e le opzioni per aumentare la disponibilità e ridurre al minimo i tempi di inattività durante le attività di ripristino di emergenza Altri tipi di trigger, ad esempio il bus di servizio o Cosmos DB trigger, non verranno analizzati in modo esplicito.

Gli scenari seguenti sono basati sulle configurazioni di Active-Passive, perché sono guidate dall'uso di archiviazione di Azure. Questo modello prevede la distribuzione di un'app per le funzioni di backup (passiva) in un'altra area. Gestione traffico eseguirà il monitoraggio dell'app per le funzioni primaria (attiva) per la disponibilità HTTP. In caso di errore dell'app primaria, verrà effettuato il failover all'app per le funzioni di backup. Per altre informazioni, vedere [priorità Traffic-Routing metodo](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) di [Gestione traffico di Azure](https://azure.microsoft.com/services/traffic-manager/).

> [!NOTE]
> - La configurazione attiva-passiva proposta assicura che un client sia sempre in grado di attivare nuove orchestrazioni tramite HTTP. Tuttavia, come conseguenza della presenza di due app per le funzioni che condividono lo stesso hub attività nell'archivio, alcune transazioni di archiviazione in background verranno distribuite tra di esse. Questa configurazione comporta pertanto alcuni costi aggiuntivi in uscita per l'app per le funzioni secondarie.
> - L'hub attività e l'account di archiviazione sottostante vengono creati nell'area primaria e sono condivisi da entrambe le app per le funzioni.
> - Tutte le app per le funzioni che vengono distribuite in modo ridondante devono condividere le stesse chiavi di accesso della funzione nel caso in cui vengano attivate tramite HTTP. Il runtime di Funzioni espone un'[API di gestione](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) che consente ai consumer di aggiungere, eliminare e aggiornare le chiavi di funzione a livello di codice. È possibile usare la gestione delle chiavi anche tramite le [api Azure Resource Manager](https://www.markheath.net/post/managing-azure-functions-keys-2).

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scenario 1 - Calcolo con bilanciamento del carico con archiviazione condivisa

In caso di errore dell'infrastruttura di calcolo in Azure, l'app per le funzioni potrebbe diventare non disponibile. Per ridurre al minimo la possibilità di tale tempo di inattività, questo scenario usa due app per le funzioni distribuite in aree diverse.
Gestione traffico è configurato per rilevare i problemi nell'app per le funzioni primaria e reindirizzare automaticamente il traffico verso l'app per le funzioni nell'area secondaria. Questa app per le funzioni condivide lo stesso account di archiviazione di Azure e lo stesso hub attività. Pertanto, lo stato delle app per le funzioni non va perso e l'attività può riprendere normalmente. Una volta ripristinata l'integrità nell'area primaria, Gestione traffico di Azure avvierà automaticamente il routing delle richieste a tale app per le funzioni.

![Diagramma che illustra lo scenario 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

L'uso di questo scenario di distribuzione comporta diversi vantaggi:

- Se l'infrastruttura di calcolo ha esito negativo, è possibile riprendere il lavoro nell'area di failover senza perdita di dati.
- Gestione traffico gestisce automaticamente il failover automatico per l'app per le funzioni integre.
- Gestione traffico ristabilisce automaticamente il traffico all'app per le funzioni primaria dopo che l'interruzione è stata risolta.

Tuttavia, usando questo scenario, prendere in considerazione quanto segue:

- Se l'app per le funzioni viene distribuita usando un piano di servizio App dedicato, la replica dell'infrastruttura di calcolo nel Data Center di failover aumenta i costi.
- Questo scenario riguarda le interruzioni nell'infrastruttura di calcolo, ma l'account di archiviazione continua a essere il singolo punto di guasto per l'app per le funzioni. Se si verifica un'interruzione dell'archiviazione, l'applicazione subisce tempi di inattività.
- Se è stato effettuato il failover dell'app per le funzioni, si verificherà un aumento della latenza dal momento che accederà al relativo account di archiviazione tra le diverse aree.
- L'accesso al servizio di archiviazione da un'area diversa da quella in cui è situato comporta costi più elevati a causa del traffico di rete in uscita.
- Questo scenario dipende da Gestione traffico. Prendendo in considerazione la [modalità di funzionamento di Gestione traffico](../../traffic-manager/traffic-manager-how-it-works.md), potrebbe passare del tempo prima che un'applicazione client che utilizza una funzione durevole necessiti di eseguire nuovamente una query sull'indirizzo dell'app per le funzioni da Gestione traffico.

> [!NOTE]
> A partire da **v 2.3.0** dell'estensione Durable Functions, è possibile eseguire in modo sicuro due app per le funzioni con lo stesso account di archiviazione e la stessa configurazione dell'hub attività. La prima app da avviare acquisisce un lease di BLOB a livello di applicazione che impedisce ad altre app di sottrarre messaggi dalle code dell'hub attività. Se questa prima app viene arrestata, il lease scadrà e potrà essere acquisito da una seconda app, che procederà quindi all'elaborazione dei messaggi dell'hub attività.
> 
> Prima della v 2.3.0, le app per le funzioni configurate per l'uso dello stesso account di archiviazione elaborano i messaggi e aggiornano gli artefatti di archiviazione contemporaneamente, ottenendo latenze complessive e costi in uscita più elevati. Se le app primarie e di replica hanno sempre distribuito codice diverso, anche temporaneamente, le orchestrazioni potrebbero non essere eseguite correttamente a causa delle incoerenze delle funzioni dell'agente di orchestrazione nelle due app. È quindi consigliabile che tutte le app che richiedono la distribuzione geografica per scopi di ripristino di emergenza usino v 2.3.0 o versione successiva dell'estensione durevole.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scenario 2 - Calcolo con bilanciamento del carico con archiviazione a livello di area

Lo scenario precedente riguarda solo i casi di errore nell'infrastruttura di calcolo. Se si verifica un errore nel servizio di archiviazione, comporterà un'interruzione dell'app per le funzioni.
Per garantire il funzionamento continuo delle funzioni durevoli, questo scenario usa un account di archiviazione locale in ogni area a cui sono distribuite le app per le funzioni.

![Diagramma che illustra lo scenario 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Questo approccio implica diversi miglioramenti rispetto allo scenario precedente:

- In caso di errore dell'app per le funzioni, Gestione traffico si occupa di effettuare il failover all'area secondaria. Tuttavia, poiché l'app per le funzioni si basa sul proprio account di archiviazione, le funzioni durevoli continueranno a funzionare.
- Durante un failover, non è presente alcuna latenza aggiuntiva nell'area di failover Poiché l'app per le funzioni e l'account di archiviazione si trovano in un percorso condiviso.
- L'errore del livello di archiviazione provocherà errori nelle funzioni permanenti, che a loro volta attiveranno un reindirizzamento nell'area di failover. Anche in questo caso, dal momento che l'app per le funzioni e l'archiviazione sono isolate per area, le funzioni durevoli continueranno a funzionare.

Considerazioni importanti per questo scenario:

- Se l'app per le funzioni viene distribuita usando un piano di servizio App dedicato, la replica dell'infrastruttura di calcolo nel Data Center di failover aumenta i costi.
- Non è stato eseguito il failover dello stato corrente. ciò implica che le orchestrazioni e le entità esistenti verranno sospese in modo efficace e non saranno disponibili fino al ripristino dell'area primaria.

Per riepilogare, il compromesso tra il primo e il secondo scenario è che la latenza viene mantenuta e i costi in uscita sono ridotti al minimo ma le orchestrazioni e le entità esistenti non saranno disponibili durante il tempo di inattività. Il fatto che questi compromessi siano accettabili dipende dai requisiti dell'applicazione.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scenario 3 - Calcolo con bilanciamento del carico con archiviazione con ridondanza geografica condivisa

Questo scenario costituisce una modifica rispetto al primo scenario e comporta l'implementazione di un account di archiviazione condiviso. La differenza principale consiste nel fatto che l'account di archiviazione viene creato con la replica geografica abilitata.
Dal punto di vista funzionale, questo scenario offre gli stessi vantaggi dello Scenario 1, ma assicura altri benefici a livello di ripristino dei dati:

- L'archiviazione con ridondanza geografica (GRS) e l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) ottimizza la disponibilità per l'account di archiviazione.
- Se si verifica un'interruzione a livello di area del servizio di archiviazione, è possibile [avviare manualmente un failover alla replica secondaria](../../storage/common/storage-initiate-account-failover.md). In casi estremi, in cui un'area va persa a causa di una grave emergenza, Microsoft potrebbe avviare un failover a livello di area. In tal caso, non è necessaria alcuna azione da parte dell'utente.
- Quando si verifica un failover, lo stato delle funzioni permanenti verrà mantenuto fino all'ultima replica dell'account di archiviazione, che in genere si verifica a intervalli di pochi minuti.

Come per gli altri scenari, ci sono considerazioni importanti di cui tenere conto:

- Un failover per la replica potrebbe richiedere del tempo. Finché non viene completato il failover e i record DNS di archiviazione di Azure non sono stati aggiornati, l'app per le funzioni subirà un'interruzione del servizio.
- È previsto un costo aggiuntivo per l'uso degli account di archiviazione con replica geografica.
- La replica GRS copia i dati in modo asincrono. Alcune delle transazioni più recenti potrebbero andare perse a causa della latenza del processo di replica.

![Diagramma che illustra lo scenario 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

> [!NOTE]
> Come descritto nello scenario 1, si consiglia vivamente alle app per le funzioni distribuite con questa strategia di usare **v 2.3.0** o versione successiva dell'estensione Durable Functions.

Per ulteriori informazioni, vedere la documentazione relativa al [ripristino di emergenza e al failover dell'account di archiviazione di Azure](../../storage/common/storage-disaster-recovery-guidance.md) .

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sulla progettazione di applicazioni a disponibilità elevata in archiviazione di Azure](../../storage/common/geo-redundant-design.md)
