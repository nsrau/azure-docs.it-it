---
title: Procedure consigliate per Funzioni di Azure | Documentazione Microsoft
description: Informazioni su modelli e procedure consigliate per Funzioni di Azure.
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: funzioni di azure, modelli, procedure consigliate, funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/09/2016
ms.author: wesmc
translationtype: Human Translation
ms.sourcegitcommit: 182e28e37eb56c547e28524f2a3e13f042238cb4
ms.openlocfilehash: c638bf42b9adf906f195d77268637d056f7b00a9

---

# <a name="best-practices-for-azure-functions"></a>Procedure consigliate per Funzioni di Azure

##<a name="overview"></a>Panoramica

Questo articolo offre una raccolta di procedure consigliate da tenere in considerazione quando si implementano app per le funzioni. Ricordare che l'applicazione Funzioni di Azure è un servizio app di Azure. Pertanto, tali procedure consigliate sono valide.


## <a name="avoid-large-long-running-functions"></a>Evitare funzioni di grandi dimensioni con esecuzione prolungata

Le funzioni con esecuzione prolungata e di grandi dimensioni possono causare problemi di timeout imprevisti. Le dimensioni di una funzione possono essere grandi a causa della presenza di molte dipendenze di Node.js. L'importazione di tali dipendenze può fare aumentare i tempi di caricamento causando timeout imprevisti. Le dipendenze di Node.js possono essere caricate in modo esplicito da più istruzioni `require()` nel codice. Possono anche essere implicite, basate su un unico modulo caricato dal codice che ha le proprie dipendenze interne.  

Quando è possibile, suddividere le funzioni di grandi dimensioni in gruppi di funzioni più piccoli che possono interagire tra loro e restituire rapidamente le risposte. Ad esempio, un webhook o una funzione di trigger HTTP potrebbe richiedere una risposta di conferma entro un determinato limite di tempo. È possibile passare il payload del trigger HTTP in una coda perché venga elaborato da una funzione di trigger della coda. Questo approccio consente di rinviare l'operazione effettiva e di restituire una risposta immediata. È normale per i webhook richiedere una risposta immediata.


## <a name="cross-function-communication"></a>Comunicazioni tra funzioni.

Quando si integrano più funzioni, in genere è opportuno usare le code di archiviazione per la comunicazione tra funzioni.  Il motivo principale è che le code di archiviazione sono più economiche ed è molto più facile sottoporle a provisioning. 

Le dimensioni dei singoli messaggi in una coda di archiviazione sono limitate a 64 KB. Se è necessario passare i messaggi di dimensioni superiori tra le funzioni, è possibile usare una coda del bus di servizio di Azure per supportare dimensioni dei messaggi fino a 256 KB.

Gli argomenti del bus di servizio sono utili se è necessario filtrare i messaggi prima dell'elaborazione.

Gli hub eventi sono utili per supportare comunicazioni con volumi elevati.



## <a name="write-functions-to-be-stateless"></a>Scrivere le funzioni in modo che siano senza stato 

Le funzioni devono essere senza stato e idempotenti se possibile. Associare ai dati eventuali informazioni obbligatorie sullo stato. Ad esempio, un ordine in fase di elaborazione probabilmente ha un membro `state` associato. Una funzione può elaborare un ordine basato su tale stato rimanendo però una funzione senza stato. 

Le funzioni idempotenti sono consigliate in particolare con i trigger timer. Ad esempio, se si deve assolutamente eseguire un'azione una volta al giorno, scriverla in modo che possa essere eseguita a qualsiasi ora del giorno con gli stessi risultati. La funzione può essere chiusa quando non è presente alcun lavoro da svolgere per un determinato giorno. Anche se un'esecuzione precedente non è stata completata, l'esecuzione successiva riprenderà da dove era stata interrotta.


## <a name="write-defensive-functions"></a>Scrivere funzioni difensive.

Si supponga che la funzione possa rilevare un'eccezione in qualsiasi momento. Progettare le funzioni con la possibilità di continuare da un punto di errore precedente durante l'esecuzione successiva. Si consideri uno scenario che richiede le azioni seguenti:

1. Query per 10.000 righe in un database.
2. Creare un messaggio in coda per ognuna delle righe da elaborare ulteriormente in un secondo tempo.
 
In base alla complessità del sistema, è possibile che i servizi downstream coinvolti non funzionino correttamente, che si verifichino interruzioni della rete, che vengano raggiunti i limiti di quota e così via. Tutti questi elementi possono influire sulla funzione in qualsiasi momento. È necessario progettare le funzioni in modo che siano preparate.

Come reagisce il codice in caso di errore dopo l'inserimento di 5.000 di tali elementi in una coda per l'elaborazione? Tenere traccia degli elementi in un set già completato. In caso contrario, è possibile inserirli di nuovo la volta successiva. Ciò può influire in modo negativo sul flusso di lavoro. 

Se un elemento della coda è già stato elaborato, consentire alla funzione di essere no-op.

Sfruttare le misure difensive già messe a disposizione per i componenti usati nella piattaforma Funzioni di Azure. Ad esempio, vedere **Gestione di messaggi della coda non elaborabili** nella documentazione relativa ai [trigger della coda di Archiviazione di Azure](functions-bindings-storage-queue.md#trigger).
 



## <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Non combinare codice di test e di produzione nella stessa app per le funzioni.

Le funzioni all'interno di un'app per le funzioni condividono le risorse. Ad esempio, la memoria è condivisa. Se si usa un'app per le funzioni nell'ambiente di produzione, non aggiungere funzioni e risorse relative ai test, per evitare possibili sovraccarichi imprevisti durante l'esecuzione del codice di produzione.

Prestare attenzione a quello che si carica nelle app per le funzioni di produzione. La memoria viene calcolata in ogni funzione nell'app.

Se si usa un assembly condiviso a cui si fa riferimento in più funzioni di .Net, inserirlo in una cartella condivisa comune. Fare riferimento all'assembly con un'istruzione simile all'esempio seguente: 

    #r "..\Shared\MyAssembly.dll". 

In caso contrario, è facile distribuire accidentalmente più versioni di prova dello stesso binario che si comportano in modo diverso nelle varie funzioni.

Non usare la registrazione dettagliata nel codice di produzione. Ha un impatto negativo sulle prestazioni.



## <a name="use-async-code-but-avoid-taskresult"></a>Usare codice asincrono ma evitare Task.Result

La programmazione asincrona è una procedura consigliata. Tuttavia, evitare sempre di fare riferimento alla proprietà `Task.Result`. Questo approccio essenzialmente crea un "busy waiting" su un blocco di un altro thread. Il blocco potenzialmente può causare deadlock.




## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le seguenti risorse:

* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)
* [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-reference-csharp.md)
* [Guida di riferimento per gli sviluppatori di Funzioni di Azure in F#](functions-reference-fsharp.md)
* [Guida di riferimento per gli sviluppatori NodeJS di Funzioni di Azure](functions-reference-node.md)




<!--HONumber=Feb17_HO2-->


