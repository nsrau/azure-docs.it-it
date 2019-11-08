---
title: 'Failover e applicazione di patch: cache di Azure per Redis | Microsoft Docs'
description: Informazioni sul failover, l'applicazione di patch e il processo di aggiornamento per cache di Azure per Redis.
services: cache
author: asasine
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 22c48441795e8aff9aba6540f15130452bcec2f7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819178"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Failover e applicazione di patch per cache di Azure per Redis

Per creare applicazioni client resilienti e con esito positivo, è fondamentale comprendere il failover nel contesto della cache di Azure per il servizio Redis. Un failover può essere una parte delle operazioni di gestione pianificate oppure può essere causato da errori hardware o di rete non pianificati. Quando il servizio di gestione esegue il patch della cache di Azure per i file binari di redis, viene comunemente usato il failover della cache. Questo articolo illustra il failover, il modo in cui si verifica durante l'applicazione di patch e come creare un'applicazione client resiliente.

## <a name="what-is-a-failover"></a>Che cos'è un failover?

Iniziamo con una panoramica del failover per cache di Azure per Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Riepilogo rapido dell'architettura della cache

Una cache è costituita da più macchine virtuali con indirizzi IP privati separati. Ogni macchina virtuale, nota anche come nodo, è connessa a un servizio di bilanciamento del carico condiviso con un singolo indirizzo IP virtuale. Ogni nodo esegue il processo del server Redis ed è accessibile tramite il nome host e le porte Redis. Ogni nodo viene considerato un nodo master o replica. Quando un'applicazione client si connette a una cache, il traffico passa attraverso questo servizio di bilanciamento del carico e viene indirizzato automaticamente al nodo master.

In una cache di base, il singolo nodo è sempre un master. In una cache standard o Premium sono presenti due nodi: uno viene scelto come master e l'altro è la replica. Poiché le cache standard e Premium includono più nodi, è possibile che un nodo non sia disponibile mentre l'altro continua a elaborare le richieste. Le cache in cluster sono costituite da numerose partizioni, ognuna con nodi master e di replica distinti. Una partizione potrebbe essere inattiva mentre le altre restano disponibili.

> [!NOTE]
> Una cache di base non ha più nodi e non offre un contratto di servizio (SLA) per la disponibilità. Le cache di base sono consigliate solo a scopo di sviluppo e test. Usare una cache standard o Premium per una distribuzione a più nodi, per aumentare la disponibilità.

### <a name="explanation-of-a-failover"></a>Spiegazione di un failover

Si verifica un failover quando un nodo di replica promuove se stesso per diventare un nodo master e il nodo master precedente chiude le connessioni esistenti. Al termine del backup del nodo master, si nota la modifica dei ruoli e viene abbassata di valore per diventare una replica. Si connette quindi al nuovo master e sincronizza i dati. Un failover potrebbe essere pianificato o non pianificato.

Durante gli aggiornamenti del sistema si verifica un *failover pianificato* , ad esempio l'applicazione di patch di redis o gli aggiornamenti del sistema operativo, nonché le operazioni di gestione, ad esempio il ridimensionamento e il riavvio. Poiché i nodi ricevono una notifica di avanzamento dell'aggiornamento, possono scambiare ruoli in modo cooperativo e aggiornare rapidamente il servizio di bilanciamento del carico della modifica. Un failover pianificato viene in genere completato in meno di un secondo.

È possibile che si verifichi un *failover non pianificato* a causa di errori hardware, errori di rete o altre interruzioni impreviste del nodo master. Il nodo di replica promuove se stesso al Master, ma il processo richiede più tempo. Un nodo di replica deve prima rilevare che il nodo master non è disponibile prima di poter avviare il processo di failover. Il nodo replica deve inoltre verificare che l'errore non pianificato non sia temporaneo o locale, per evitare un failover non necessario. Questo ritardo nel rilevamento indica che un failover non pianificato viene in genere completato entro 10-15 secondi.

## <a name="how-does-patching-occur"></a>In che modo si verifica l'applicazione di patch?

Il servizio cache di Azure per Redis aggiorna regolarmente la cache con le funzionalità e le correzioni più recenti della piattaforma. Per applicare una patch a una cache, il servizio segue i passaggi seguenti:

1. Il servizio di gestione seleziona un nodo a cui applicare le patch.
1. Se il nodo selezionato è un nodo master, il nodo di replica corrispondente promuove in modo cooperativo. Questa promozione è considerata un failover pianificato.
1. Il nodo selezionato viene riavviato per eseguire le nuove modifiche e viene restituito come nodo di replica.
1. Il nodo di replica si connette al nodo master e sincronizza i dati.
1. Al termine della sincronizzazione dei dati, il processo di applicazione delle patch si ripete per i nodi rimanenti.

Poiché l'applicazione di patch è un failover pianificato, il nodo di replica promuove rapidamente se stesso per diventare un master e inizia a gestire le richieste e le nuove connessioni. Le cache di base non dispongono di un nodo di replica e non sono disponibili fino al completamento dell'aggiornamento. Ogni partizione di una cache in cluster viene applicata separatamente e non chiuderà le connessioni a un'altra partizione.

> [!IMPORTANT]
> I nodi vengono patchati uno alla volta per evitare la perdita di dati. Le cache di base avranno una perdita di dati. Le cache in cluster vengono applicate a patch una partizione alla volta.

Viene anche applicata una patch per più cache nello stesso gruppo di risorse e area, una alla volta.  Le cache che si trovano in gruppi di risorse diversi o in aree diverse possono essere applicate simultaneamente.

Poiché la sincronizzazione completa dei dati viene eseguita prima della ripetizione del processo, è improbabile che si verifichi una perdita di dati quando si usa una cache standard o Premium. È possibile evitare la perdita di dati [esportando](cache-how-to-import-export-data.md#export) i dati e abilitando la [persistenza](cache-how-to-premium-persistence.md).

## <a name="additional-cache-load"></a>Caricamento cache aggiuntivo

Ogni volta che si verifica un failover, le cache standard e Premium devono replicare i dati da un nodo all'altro. Questa replica causa un aumento del carico sia nella memoria del server che nella CPU. Se l'istanza della cache è già caricata in modo eccessivo, le applicazioni client potrebbero riscontrare una latenza maggiore. In casi estremi, le applicazioni client potrebbero ricevere eccezioni di timeout. Per ridurre l'effetto di questo carico aggiuntivo, [configurare](cache-configure.md#memory-policies) l'impostazione della `maxmemory-reserved` della cache.

## <a name="how-does-a-failover-affect-my-client-application"></a>In che modo un failover influisce sull'applicazione client?

Il numero di errori visualizzati dall'applicazione client dipende dal numero di operazioni in sospeso sulla connessione al momento del failover. Eventuali connessioni instradate attraverso il nodo che ha chiuso le connessioni visualizzeranno errori. Molte librerie client possono generare tipi diversi di errori quando le connessioni si interrompono, incluse le eccezioni di timeout, le eccezioni di connessione o le eccezioni di socket. Il numero e il tipo di eccezioni dipendono dalla posizione del percorso del codice in cui la richiesta è quando la cache chiude le connessioni. Ad esempio, un'operazione che invia una richiesta ma non ha ricevuto una risposta quando si verifica il failover potrebbe ottenere un'eccezione di timeout. Le nuove richieste sull'oggetto connessione chiuso ricevono le eccezioni di connessione fino a quando la riconnessione non viene eseguita correttamente.

La maggior parte delle librerie client tenta di riconnettersi alla cache se sono state configurate a tale scopo. Tuttavia, in alcuni casi, i bug imprevisti possono mettere gli oggetti della libreria in uno stato irreversibile. Se gli errori permangono per più di un periodo di tempo preconfigurato, è necessario ricreare l'oggetto connessione. In Microsoft.NET e in altri linguaggi orientati a oggetti, la ricreazione della connessione senza riavviare l'applicazione può essere eseguita usando [un modello di\> Lazy\<t](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="how-do-i-make-my-application-resilient"></a>Ricerca per categorie garantire la resilienza dell'applicazione?

Poiché non è possibile evitare completamente i failover, scrivere le applicazioni client per la resilienza per le interruzioni di connessione e le richieste non riuscite. Sebbene la maggior parte delle librerie client riconnetta automaticamente l'endpoint della cache, alcuni di essi tentano di ritentare le richieste non riuscite. A seconda dello scenario dell'applicazione, potrebbe essere utile usare la logica di ripetizione dei tentativi con backoff.

Per testare la resilienza di un'applicazione client, usare un [riavvio](cache-administration.md#reboot) come trigger manuale per le interruzioni di connessione. Si consiglia inoltre di [pianificare gli aggiornamenti](cache-administration.md#schedule-updates) in una cache. Indicare al servizio di gestione di applicare le patch di runtime di redis durante le finestre settimanali specificate. Si tratta in genere di periodi in cui il traffico delle applicazioni client è basso per evitare potenziali incidenti.

### <a name="client-network-configuration-changes"></a>Rete client-modifiche di configurazione

Alcune modifiche alla configurazione della rete lato client possono attivare errori di connessione non disponibili. Tali modifiche possono includere:

- Scambio dell'indirizzo IP virtuale di un'applicazione client tra gli slot di staging e di produzione.
- Ridimensionamento delle dimensioni o del numero di istanze dell'applicazione.

Tali modifiche possono causare un problema di connettività che dura meno di un minuto. L'applicazione client perderà probabilmente la connessione ad altre risorse di rete esterne oltre alla cache di Azure per il servizio Redis.

## <a name="next-steps"></a>Passaggi successivi

- [Pianificare gli aggiornamenti](cache-administration.md#schedule-updates) per la cache.
- Testare la resilienza dell'applicazione usando un [riavvio](cache-administration.md#reboot).
- [Configurare](cache-configure.md#memory-policies) le prenotazioni e i criteri di memoria.
