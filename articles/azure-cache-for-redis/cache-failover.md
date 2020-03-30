---
title: Failover e applicazione di patch - Cache Redis
description: Informazioni su failover, applicazione di patch e processo di aggiornamento per la cache di Azure per Redis.Learn about failover, patching, and the update process for Azure Cache for Redis.
author: asasine
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 6ff33bd594181aabc4fd7d55ce33f780a0d06086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74122198"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Failover e applicazione di patch per la cache di Azure per Redis

Per creare applicazioni client resilienti e di successo, è fondamentale comprendere il failover nel contesto del servizio Cache di Azure per Redis.To build resilient and successful client applications, it's critical to understand failover in the context of the Azure Cache for Redis service. Un failover può essere parte delle operazioni di gestione pianificate o potrebbe essere causato da errori hardware o di rete non pianificati. Un uso comune del failover della cache viene quando il servizio di gestione applica patch alla cache di Azure per i file binari Redis.A common use of cache failover comes when the management service patches the Azure Cache for Redis binaries. In questo articolo viene illustrato che cos'è un failover, come si verifica durante l'applicazione di patch e come creare un'applicazione client resiliente.

## <a name="what-is-a-failover"></a>Che cos'è un failover?

Iniziamo con una panoramica del failover per la cache di Azure per Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Un breve riepilogo dell'architettura della cache

Una cache è costituita da più macchine virtuali con indirizzi IP privati separati. Ogni macchina virtuale, nota anche come nodo, è connessa a un servizio di bilanciamento del carico condiviso con un singolo indirizzo IP virtuale. Ogni nodo esegue il processo server Redis ed è accessibile tramite il nome host e le porte Redis. Ogni nodo è considerato un nodo master o un nodo di replica. Quando un'applicazione client si connette a una cache, il relativo traffico passa attraverso questo servizio di bilanciamento del carico e viene automaticamente instradato al nodo master.

In una cache Basic, il singolo nodo è sempre un master. In una cache Standard o Premium sono presenti due nodi: uno viene scelto come master e l'altro è la replica. Poiché le cache Standard e Premium hanno più nodi, un nodo potrebbe non essere disponibile mentre l'altro continua a elaborare le richieste. Le cache cluster sono costituite da molte partizioni, ognuna con nodi master e replica distinti. Un frammento potrebbe essere inattivo mentre gli altri rimangono disponibili.

> [!NOTE]
> Una cache di base non dispone di più nodi e non offre un contratto di servizio (SLA) per la disponibilità. Le cache di base sono consigliate solo a scopo di sviluppo e test. Usare una cache Standard o Premium per una distribuzione a più nodi, per aumentare la disponibilità.

### <a name="explanation-of-a-failover"></a>Spiegazione di un failover

Un failover si verifica quando un nodo di replica promuove se stesso a diventare un nodo master e il nodo master precedente chiude le connessioni esistenti. Dopo che il nodo master viene riprodotto, rileva la modifica dei ruoli e si abbassa di livello per diventare una replica. Si connette quindi al nuovo master e sincronizza i dati. Un failover potrebbe essere pianificato o non pianificato.

Durante gli aggiornamenti di sistema, ad esempio l'applicazione di patch a redis o gli aggiornamenti del sistema operativo, viene eseguito un *failover,* ad esempio il ridimensionamento e il riavvio. Poiché i nodi ricevono una notifica anticipata dell'aggiornamento, possono scambiare i ruoli in modo cooperativo e aggiornare rapidamente il servizio di bilanciamento del carico della modifica. Un failover pianificato viene in genere completato in meno di 1 secondo.

Un *failover non pianificato* potrebbe verificarsi a causa di un errore hardware, errore di rete o altre interruzioni impreviste del nodo master. Il nodo di replica promuove se stesso al master, ma il processo richiede più tempo. Un nodo di replica deve prima rilevare che il nodo master non è disponibile prima di poter avviare il processo di failover. Il nodo di replica deve inoltre verificare che l'errore non pianificato non sia temporaneo o locale, per evitare un failover non necessario. Questo ritardo nel rilevamento significa che un failover non pianificato viene in genere completato entro 10-15 secondi.

## <a name="how-does-patching-occur"></a>Come avviene l'applicazione delle patch?

Il servizio Cache di Azure per Redis aggiorna regolarmente la cache con le funzionalità e le correzioni della piattaforma più recenti. Per applicare una patch a una cache, il servizio segue questi passaggi:

1. Il servizio di gestione seleziona un nodo da applicare alla patch.
1. Se il nodo selezionato è un nodo master, il nodo replica corrispondente si promotea in modo cooperativo. Questa promozione è considerata un failover pianificato.
1. Il nodo selezionato si riavvia per accettare le nuove modifiche e ne viene ritornato come nodo di replica.
1. Il nodo di replica si connette al nodo master e sincronizza i dati.
1. Al termine della sincronizzazione dei dati, il processo di gestione delle patch si ripete per i nodi rimanenti.

Poiché l'applicazione di patch è un failover pianificato, il nodo di replica si promuove rapidamente a diventare un master e inizia la manutenzione delle richieste e delle nuove connessioni. Le cache di base non dispongono di un nodo di replica e non sono disponibili fino al completamento dell'aggiornamento. Ogni partizione di una cache cluster viene crittografata separatamente e non chiuderà le connessioni a un'altra partizione.

> [!IMPORTANT]
> I nodi vengono patch ai dati uno alla volta per evitare la perdita di dati. Le cache di base avranno una perdita di dati. Le cache cluster sono sottoposte a patch una partizione alla volta.

Più cache nello stesso gruppo di risorse e nello stesso area vengono inoltre patchate una alla volta.  Le cache che si trovano in gruppi di risorse diversi o aree diverse potrebbero essere patch contemporaneamente.

Poiché la sincronizzazione completa dei dati avviene prima che il processo si ripeta, è improbabile che si verifichi una perdita di dati quando si utilizza una cache Standard o Premium. È possibile proteggersi ulteriormente dalla perdita di dati [esportando i](cache-how-to-import-export-data.md#export) dati e abilitando [la persistenza.](cache-how-to-premium-persistence.md)

## <a name="additional-cache-load"></a>Carico della cache aggiuntivo

Ogni volta che si verifica un failover, le cache Standard e Premium devono replicare i dati da un nodo all'altro. Questa replica causa un certo aumento del carico sia nella memoria del server che nella CPU. Se l'istanza della cache è già caricata in modo intensivo, le applicazioni client potrebbero riscontrare un aumento della latenza. In casi estremi, le applicazioni client potrebbero ricevere eccezioni di timeout. Per ridurre l'impatto di questo carico aggiuntivo, `maxmemory-reserved` [configurare](cache-configure.md#memory-policies) l'impostazione della cache.

## <a name="how-does-a-failover-affect-my-client-application"></a>In che modo un failover influisce sull'applicazione client?

Il numero di errori rilevati dall'applicazione client dipende dal numero di operazioni in sospeso sulla connessione al momento del failover. Qualsiasi connessione instradata attraverso il nodo che ha chiuso le connessioni visualizzerà errori. Molte librerie client possono generare diversi tipi di errori quando le connessioni si interrompono, incluse le eccezioni di timeout, le eccezioni di connessione o le eccezioni socket. Il numero e il tipo di eccezioni dipende da dove si trova nel percorso del codice la richiesta quando la cache chiude le connessioni. Ad esempio, un'operazione che invia una richiesta ma non ha ricevuto una risposta quando si verifica il failover potrebbe ottenere un'eccezione di timeout. Le nuove richieste sull'oggetto connessione chiuso ricevono eccezioni di connessione finché la riconnessione non viene eseguita correttamente.

La maggior parte delle librerie client tenta di riconnettersi alla cache se sono configurate per eseguire questa operazione. Tuttavia, i bug imprevisti possono occasionalmente inserire gli oggetti della libreria in uno stato irreversibile. Se gli errori persistono per un periodo di tempo superiore a quello preconfigurato, l'oggetto connessione deve essere ricreato. In Microsoft.NET e in altri linguaggi orientati agli oggetti, è possibile ricreare la connessione senza riavviare l'applicazione utilizzando [un modello\<T\> lazy](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="how-do-i-make-my-application-resilient"></a>Come è possibile rendere resiliente l'applicazione?

Poiché non è possibile evitare completamente i failover, scrivere le applicazioni client per la resilienza in interruzioni di connessione e richieste non riuscite. Sebbene la maggior parte delle librerie client si riconnetta automaticamente all'endpoint della cache, alcune di esse tentano di ritentare le richieste non riuscite. A seconda dello scenario dell'applicazione, potrebbe essere opportuno usare la logica di ripetizione dei tentativi con backoff.

Per testare la resilienza di un'applicazione client, usare un [riavvio](cache-administration.md#reboot) come trigger manuale per le interruzioni di connessione. Inoltre, è consigliabile [pianificare gli aggiornamenti](cache-administration.md#schedule-updates) in una cache. Indicare al servizio di gestione di applicare le patch di runtime Redis durante le finestre settimanali specificate. Queste finestre sono in genere periodi in cui il traffico delle applicazioni client è basso, per evitare potenziali eventi imprevisti.

### <a name="client-network-configuration-changes"></a>Modifiche alla configurazione della rete client

Alcune modifiche alla configurazione di rete lato client possono generare errori "Nessuna connessione disponibile". Tali modifiche potrebbero includere:

- Scambio dell'indirizzo IP virtuale di un'applicazione client tra gli slot di gestione temporanea e di produzione.
- Ridimensionamento delle dimensioni o del numero di istanze dell'applicazione.

Tali modifiche possono causare un problema di connettività che dura meno di un minuto. L'applicazione client probabilmente perderà la connessione ad altre risorse di rete esterne oltre al servizio Cache di Azure per Redis.Your client application will probably lose its connection to other external network resources in addition to the Azure Cache for Redis service.

## <a name="next-steps"></a>Passaggi successivi

- [Pianificare gli aggiornamenti](cache-administration.md#schedule-updates) per la cache.
- Verificare la resilienza dell'applicazione utilizzando un [riavvio](cache-administration.md#reboot).
- [Configurare](cache-configure.md#memory-policies) le prenotazioni e i criteri di memoria.
