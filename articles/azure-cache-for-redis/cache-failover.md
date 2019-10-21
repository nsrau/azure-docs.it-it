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
ms.openlocfilehash: 305511efe86d2b241ef5014d9c3f0501cfd3fbdc
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675902"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Failover e applicazione di patch per cache di Azure per Redis

Conoscere il contesto di un failover con la cache di Azure per il servizio Redis è fondamentale per la creazione di applicazioni client resilienti e con successo. Una delle cause comuni di un failover della cache deriva dal servizio di gestione che applica l'applicazione di patch ai file binari di Redis. Questo articolo descrive il tipo di failover, il modo in cui si verificano durante l'applicazione di patch e come creare un'applicazione client resiliente.

## <a name="what-is-a-failover"></a>Che cos'è un failover?

### <a name="a-quick-summary-of-our-architecture"></a>Riepilogo rapido della nostra architettura

Una cache è costituita da più macchine virtuali con indirizzi IP privati distinti. Ogni macchina virtuale, nota anche come nodo, è connessa a un servizio di bilanciamento del carico condiviso con un singolo IP virtuale. Ogni nodo esegue il processo del server Redis ed è accessibile tramite il nome host e le porte Redis. Ogni nodo viene considerato un nodo master o di replica. Quando un'applicazione client si connette a una cache, il traffico passa attraverso questo servizio di bilanciamento del carico e viene indirizzato automaticamente al nodo master.

In una cache di base, il singolo nodo è sempre un master. In una cache standard o Premium sono presenti due nodi in cui si sceglie il master e l'altro è la replica. Poiché le cache standard e Premium includono più nodi, è possibile che un nodo non sia disponibile mentre l'altro continua a elaborare le richieste. Le cache in cluster sono costituite da numerose partizioni, ognuna con nodi master e di replica distinti. Una partizione potrebbe essere inattiva mentre le altre restano disponibili.

> [!NOTE]
> Una cache di base non ha più nodi e non offre un contratto di contratto per la disponibilità. Le cache di base sono consigliate solo a scopo di sviluppo e test. Usare una cache standard o Premium per una distribuzione a più nodi per aumentare la disponibilità.

### <a name="a-failover-explained"></a>Spiegazione del failover

Si verifica un failover quando un nodo di replica promuove se stesso per diventare un nodo master e il nodo master precedente chiude le connessioni esistenti. Al termine del backup del nodo master, si noterà la modifica dei ruoli e il suo abbassamento di valore per diventare una replica. Si connetterà quindi al nuovo master e sincronizza i dati. Un failover può essere pianificato o non pianificato.

Si verifica un failover pianificato durante gli aggiornamenti del sistema, ad esempio l'applicazione di patch di redis o gli aggiornamenti del sistema operativo e di gestione, ad esempio la scalabilità e il riavvio. Poiché ai nodi viene assegnata una notifica avanzata dell'aggiornamento, possono scambiare ruoli in modo cooperativo e aggiornare rapidamente il servizio di bilanciamento del carico della modifica. Un failover pianificato deve essere completato in meno di un secondo.

È possibile che si verifichi un failover non pianificato a causa di errori hardware, errori di rete o altre interruzioni impreviste del nodo master. Il nodo di replica viene innalzato di livello al database master, ma il processo richiede più tempo. Prima di poter avviare il processo di failover, un nodo di replica deve prima rilevare che il nodo master non è disponibile. Il nodo della replica deve inoltre verificare che l'errore non pianificato non sia temporaneo o locale per evitare un failover troppo eager. Questo ritardo nel rilevamento indica che un failover non pianificato viene in genere completato entro 10-15 secondi.

## <a name="how-does-patching-occur"></a>In che modo si verifica l'applicazione di patch?

Il servizio cache di Azure per Redis esegue regolarmente la manutenzione per aggiornare la cache con le funzionalità e le correzioni più recenti della piattaforma. Per applicare patch a una cache, il servizio segue i passaggi seguenti:

1. Il servizio di gestione seleziona un nodo a cui applicare le patch.
1. Se il nodo selezionato è un nodo master, il relativo nodo di replica promuove in modo cooperativo. Questa promozione è considerata un failover pianificato.
1. Il nodo selezionato viene riavviato per eseguire le nuove modifiche e viene restituito come nodo di replica. I nodi di replica si connettono al nodo master e sincronizzano i dati.
1. Quando la sincronizzazione dei dati viene completata, il processo di applicazione delle patch si ripete per i nodi rimanenti.

Poiché l'applicazione di patch è un failover pianificato, il nodo di replica promuove rapidamente se stesso per diventare un master e inizia a gestire le richieste e le nuove connessioni. Le cache di base non hanno un nodo di replica e non sono disponibili fino al completamento dell'aggiornamento. Ogni partizione di una cache in cluster viene applicata separatamente e non chiuderà le connessioni a un'altra partizione.

> [!IMPORTANT]
> I nodi vengono patchati uno alla volta per evitare la perdita di dati. Le cache di base avranno una perdita di dati. Le cache in cluster vengono applicate a patch una partizione alla volta.

Viene anche applicata una patch per più cache nello stesso gruppo di risorse e area, una alla volta.  Le cache che si trovano in gruppi di risorse diversi o in aree diverse possono essere applicate simultaneamente.

Poiché la sincronizzazione completa dei dati viene eseguita prima della ripetizione del processo, è improbabile che si verifichi una perdita di dati quando si usa una cache standard o Premium. È possibile evitare la perdita di dati tramite l' [esportazione](cache-how-to-import-export-data.md#export) dei dati e l'abilitazione della [persistenza](cache-how-to-premium-persistence.md).

### <a name="additional-cache-load"></a>Caricamento cache aggiuntivo

Ogni volta che si verifica un failover, le cache standard e Premium devono replicare i dati da un nodo all'altro. Questa replica causa un aumento del carico sia nella memoria del server che nella CPU. Se l'istanza della cache è già caricata in modo elevato, le applicazioni client potrebbero riscontrare una latenza maggiore. In casi estremi, le applicazioni client possono ricevere eccezioni di timeout. [Configurare](cache-configure.md#memory-policies) l'impostazione `maxmemory-reserved` della cache per attenuare l'effetto di questo carico aggiuntivo.

## <a name="how-does-a-failover-impact-my-client-application"></a>In che modo un failover influisca sull'applicazione client?

Il numero di errori visualizzati dall'applicazione client dipende dal numero di operazioni in sospeso per tale connessione al momento del failover. Tutte le connessioni instradate tramite il nodo che le connessioni chiuse visualizzeranno errori. Molte librerie client possono generare tipi diversi di errori, ad esempio eccezioni di timeout, eccezioni di connessione o eccezioni di socket quando le connessioni si interrompono. Il numero e il tipo di eccezioni dipendono dalla posizione del percorso del codice in cui la richiesta è quando la cache chiude le connessioni. Ad esempio, un'operazione che invia una richiesta ma non ha ricevuto una risposta quando si verifica il failover può ottenere un'eccezione di timeout. Le nuove richieste sull'oggetto connessione chiusa riceveranno le eccezioni di connessione fino a quando la riconnessione non viene eseguita correttamente.

La maggior parte delle librerie client tenterà di riconnettersi alla cache se è configurata per eseguire questa operazione, ma i bug imprevisti possono occasionalmente inserire gli oggetti della libreria in uno stato irreversibile. Se gli errori permangono per più di un periodo di tempo preconfigurato, è necessario ricreare l'oggetto connessione. In .NET e altri linguaggi orientati agli oggetti, la ricreazione della connessione senza riavviare l'applicazione può essere eseguita usando [un modello di \> Lazy \<T](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="what-should-i-do-in-my-application"></a>Cosa devo fare nell'applicazione?

Poiché non è possibile evitare completamente un failover, le applicazioni client devono essere scritte per la resilienza per le interruzioni di connessione e le richieste non riuscite. Nonostante la maggior parte delle librerie client si riconnetta automaticamente all'endpoint della cache, alcune librerie client tentano di ritentare le richieste non riuscite. A seconda dello scenario dell'applicazione, la logica di ripetizione dei tentativi con il backup può avere senso.

Per testare la resilienza di un'applicazione client, usare un [riavvio](cache-administration.md#reboot) come trigger manuale per le interruzioni di connessione. Inoltre, è consigliabile [pianificare gli aggiornamenti](cache-administration.md#schedule-updates) in una cache per indicare al servizio di gestione di applicare le patch del runtime di redis durante le finestre settimanali specificate. Queste finestre vengono in genere scelte per i periodi in cui il traffico delle applicazioni client è inferiore per evitare potenziali incidenti.

### <a name="client-network-configuration-changes"></a>Modifiche alla configurazione della rete client

Alcune modifiche alla configurazione della rete sul lato client possono attivare errori di connessione non disponibili.  Lo scambio di un indirizzo IP virtuale di un'applicazione client tra slot di staging e di produzione o la scalabilità delle dimensioni o del numero di istanze dell'applicazione può causare un problema di connettività che dura meno di un minuto. In aggiunta a Redis, l'applicazione client perderà probabilmente la connessione ad altre risorse di rete esterne.

## <a name="next-steps"></a>Passaggi successivi

- [Pianificare gli aggiornamenti](cache-administration.md#schedule-updates) per la cache.
- Testare la resilienza dell'applicazione usando un [riavvio](cache-administration.md#reboot).
- [Configurare](cache-configure.md#memory-policies) le prenotazioni e i criteri di memoria.
