---
title: Ripristino di emergenza e distribuzione geografica in Funzioni durevoli - Azure
description: Informazioni sul ripristino di emergenza e sulla distribuzione geografica in Funzioni durevoli.
services: functions
author: MS-Santi
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 1363dd3c620789b9f3c8ce1dbe0892ee61d66051
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741385"
---
# <a name="disaster-recovery-and-geo-distribution"></a>Ripristino di emergenza e distribuzione geografica

## <a name="overview"></a>Panoramica

In Durable Functions tutti gli stati vengono salvati in modo permanente in Archiviazione di Azure. Un [hub attività](durable-functions-task-hubs.md) è un contenitore logico per le risorse di Archiviazione di Azure che vengono usate per le orchestrazioni. Le funzioni attività e di orchestrazione possono interagire tra loro solo quando appartengono allo stesso hub attività.
Gli scenari descritti propongono opzioni di distribuzione per aumentare la disponibilità e ridurre al minimo il tempo di inattività durante le attività di ripristino di emergenza.

È importante notare che questi scenari sono basati sulle configurazioni attive-passive, dal momento che sono guidati dall'utilizzo di Archiviazione di Azure. Questo modello prevede la distribuzione di un'app per le funzioni di backup (passiva) in un'altra area. Gestione traffico monitorerà l'app per le funzioni primaria (attiva) per la disponibilità. In caso di errore dell'app primaria, verrà effettuato il failover all'app per le funzioni di backup. Per altre informazioni, vedere il [metodo di routing del traffico Priorità](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) di [Gestione traffico](https://azure.microsoft.com/services/traffic-manager/).

>[!NOTE]
>
> - La configurazione attiva-passiva proposta assicura che un client sia sempre in grado di attivare nuove orchestrazioni tramite HTTP. Tuttavia, disponendo di due app per le funzioni che condividono la stessa risorsa di archiviazione, l'elaborazione in background sarà di conseguenza distribuita tra entrambe, che si contenderanno i messaggi sulle stesse code. Questa configurazione comporta costi aggiuntivi in uscita per l'app per le funzioni secondaria.
> - L'hub attività e l'account di archiviazione sottostante vengono creati nell'area primaria e sono condivisi da entrambe le app per le funzioni.
> - Tutte le app per le funzioni che vengono distribuite in modo ridondante devono condividere le stesse chiavi di accesso della funzione in caso di attivazione tramite HTTP. Il runtime di Funzioni espone un'[API di gestione](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) che consente ai consumer di aggiungere, eliminare e aggiornare le chiavi di funzione a livello di codice.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scenario 1 - Calcolo con bilanciamento del carico con archiviazione condivisa

In caso di errore dell'infrastruttura di calcolo in Azure, l'app per le funzioni potrebbe diventare non disponibile. Per ridurre al minimo la possibilità di tale tempo di inattività, questo scenario usa due app per le funzioni distribuite in aree diverse.
Gestione traffico è configurato per rilevare i problemi nell'app per le funzioni primaria e reindirizzare automaticamente il traffico verso l'app per le funzioni nell'area secondaria. Questa app per le funzioni condivide lo stesso account di archiviazione di Azure e lo stesso hub attività. Pertanto, lo stato delle app per le funzioni non va perso e l'attività può riprendere normalmente. Una volta ripristinata l'integrità nell'area primaria, Gestione traffico di Azure avvierà automaticamente il routing delle richieste a tale app per le funzioni.

![Diagramma che illustra lo scenario 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

L'uso di questo scenario di distribuzione comporta diversi vantaggi:

- In caso di errore dell'infrastruttura di calcolo, l'attività può riprendere nell'area di failover senza perdita dello stato.
- Gestione traffico si occupa automaticamente del failover automatico all'app per le funzioni.
- Gestione traffico ristabilisce automaticamente il traffico all'app per le funzioni primaria dopo che l'interruzione è stata risolta.

Tuttavia, usando questo scenario, prendere in considerazione quanto segue:

- Se l'app per le funzioni viene distribuita tramite un piano di servizio app dedicato, l'infrastruttura di calcolo nel data center di failover aumenta i costi.
- Questo scenario riguarda le interruzioni nell'infrastruttura di calcolo, ma l'account di archiviazione continua a essere il singolo punto di guasto per l'app per le funzioni. Se si verifica un'interruzione nell'archiviazione, l'applicazione subirà un tempo di inattività.
- Se è stato effettuato il failover dell'app per le funzioni, si verificherà un aumento della latenza dal momento che accederà al relativo account di archiviazione tra le diverse aree.
- L'accesso al servizio di archiviazione da un'area diversa da quella in cui è situato comporta costi più elevati a causa del traffico di rete in uscita.
- Questo scenario dipende da Gestione traffico. Prendendo in considerazione la [modalità di funzionamento di Gestione traffico](../../traffic-manager/traffic-manager-how-it-works.md), potrebbe passare del tempo prima che un'applicazione client che utilizza una funzione durevole necessiti di eseguire nuovamente una query sull'indirizzo dell'app per le funzioni da Gestione traffico.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scenario 2 - Calcolo con bilanciamento del carico con archiviazione a livello di area

Lo scenario precedente riguarda solo i casi di errore nell'infrastruttura di calcolo. Se si verifica un errore nel servizio di archiviazione, comporterà un'interruzione dell'app per le funzioni.
Per garantire il funzionamento continuo delle funzioni durevoli, questo scenario usa un account di archiviazione locale in ogni area a cui sono distribuite le app per le funzioni.

![Diagramma che illustra lo scenario 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Questo approccio implica diversi miglioramenti rispetto allo scenario precedente:

- In caso di errore dell'app per le funzioni, Gestione traffico si occupa di effettuare il failover all'area secondaria. Tuttavia, poiché l'app per le funzioni si basa sul proprio account di archiviazione, le funzioni durevoli continueranno a funzionare.
- Durante un failover, non si riscontra alcuna latenza aggiuntiva nell'area di failover, poiché l'app per le funzioni e l'account di archiviazione hanno un percorso condiviso.
- Un errore a livello di archiviazione comporterà errori nelle funzioni durevoli che, a loro volta, attiveranno un reindirizzamento all'area di failover. Anche in questo caso, dal momento che l'app per le funzioni e l'archiviazione sono isolate per area, le funzioni durevoli continueranno a funzionare.

Considerazioni importanti per questo scenario:

- Se l'app per le funzioni viene distribuita tramite un piano di servizio app dedicato, l'infrastruttura di calcolo nel data center di failover aumenta i costi.
- Non viene effettuato il failover dello stato corrente, il che implica che si verificheranno errori nelle funzioni di esecuzione e con checkpoint. È compito dell'applicazione client riprovare/riavviare le operazioni.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scenario 3 - Calcolo con bilanciamento del carico con archiviazione con ridondanza geografica condivisa

Questo scenario costituisce una modifica rispetto al primo scenario e comporta l'implementazione di un account di archiviazione condiviso. La differenza principale è che l'account di archiviazione viene creato con la replica geografica abilitata.
Dal punto di vista funzionale, questo scenario offre gli stessi vantaggi dello Scenario 1, ma assicura altri benefici a livello di ripristino dei dati:

- L'archiviazione con ridondanza geografica (GRS) e l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) ottimizza la disponibilità per l'account di archiviazione.
- Se si verifica un'interruzione a livello di area del servizio di archiviazione, una delle possibilità è che le operazioni del data center determinino che debba essere effettuato il failover all'area secondaria. In questo caso, l'accesso all'account di archiviazione verrà reindirizzato in modo trasparente alla copia con replica geografica dell'account di archiviazione, senza l'intervento dell'utente.
- In questo caso, lo stato delle funzioni durevoli verrà conservato fino all'ultima replica dell'account di archiviazione, che si verifica ogni pochi minuti.

Come per gli altri scenari, ci sono considerazioni importanti di cui tenere conto:

- Il failover alla replica viene effettuato dagli operatori del data center e potrebbe richiedere del tempo. Fino a quel momento, l'app per le funzioni subirà un'interruzione.
- È previsto un costo aggiuntivo per l'uso degli account di archiviazione con replica geografica.
- L'archiviazione con ridondanza geografica avviene in modo asincrono. Alcune delle transazioni più recenti potrebbero andare perse a causa della latenza del processo di replica.

![Diagramma che illustra lo scenario 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Progettazione di applicazioni a disponibilità elevata con RA-GRS](../../storage/common/storage-designing-ha-apps-with-ragrs.md).
