---
title: Domande frequenti sul servizio di riconoscimento vocale in Azure
description: Di seguito sono riportate le risposte alle domande più frequenti sul riconoscimento vocale.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: e5ba01c25646578da22f054659051be3515e9e4b
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281831"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Domande frequenti sul servizio di riconoscimento vocale

Se le risposte alle proprie domande non sono presenti in questo documento, vedere le altre opzioni di supporto [qui](support.md).

## <a name="general"></a>Generale

**Domanda**: Qual è la differenza tra i modelli del servizio di riconoscimento vocale di base e personalizzato?

**Risposta**: I modelli di base sono stati sottoposti a training con dati di proprietà di Microsoft e sono già stati distribuiti nel cloud. I modelli personalizzati consentono all'utente di adattare un modello a un determinato ambiente con una lingua o rumori di fondo particolari. Reparti di produzione aziendali, automobili o strade rumorose richiedono un modello acustico adattato, mentre argomenti specifici come biologia, fisica, radiologia, nomi di prodotti e acronimi personalizzati richiedono un modello linguistico adattato.

**Domanda**: Da dove è necessario iniziare per usare un modello di base?

**Risposta**: Prima di tutto è necessario ottenere una [chiave di sottoscrizione](get-started.md). Per effettuare chiamate REST ai modelli di base predistribuiti, vedere i [dettagli qui](rest-apis.md). Se si desidera usare WebSocket, scaricare l'[SDK](speech-sdk.md).

**Domanda**: È sempre necessario compilare un modello conversione voce/testo personalizzato?

**Risposta**: No, se l'applicazione usa un linguaggio quotidiano generico, non è necessario personalizzare un modello. Se inoltre l'applicazione viene usata in un ambiente in cui il rumore di fondo è scarso o addirittura assente, non è necessario usare un modello personalizzato. Il portale consente agli utenti di distribuire modelli di base e personalizzati e di sottoporli a test di accuratezza. È possibile usare questa funzionalità per misurare l'accuratezza di un modello di base rispetto a un modello personalizzato.

**Domanda**: Come è possibile sapere quando l'elaborazione del set di dati o del modello è completa?

**Risposta**: Attualmente l'unica indicazione è lo stato del modello o del set di dati nella tabella.
Quando l'elaborazione è completata, lo stato è "Succeded" (Operazione completata).

**Domanda**: È possibile creare più modelli?

**Risposta**: Non sono previsti limiti al numero di modelli nella raccolta.

**Domanda**: Se ci si rende conto di aver commesso un errore, come è possibile annullare l'importazione dei dati o la creazione del modello in corso? 

**Risposta**: Al momento non è possibile eseguire il rollback di un processo di adattamento di un modello acustico o linguistico. I modelli e i dati importati possono essere eliminati dopo che sono in uno stato terminale.

**Domanda**: Qual è la differenza tra i modelli di ricerca e dettatura e i modelli colloquiali?

**Risposta**: Sono disponibili più modelli di base tra cui scegliere nel servizio di riconoscimento vocale. Il modello Conversational (Modello colloquiale) è adatto per il riconoscimento vocale di una conversazione. Questo modello sarebbe ideale per la trascrizione di chiamate durante la ricerca e la dettatura è ideale per le app ad attivazione vocale. Universal è un nuovo modello che contribuisce a risolvere entrambi questi scenari.

**Domanda**: È possibile aggiornare il proprio modello esistente (impilamento di modelli)?

**Risposta**: Non è possibile aggiornare i modelli esistenti. Come soluzione alternativa, combinare il set di dati precedente con quello nuovo e riadattarlo.

I set di dati nuovo e precedente devono essere combinati in un unico file con estensione zip (in caso di dati acustici) o txt (in caso di dati linguistici). Al termine dell'adattamento, per ottenere un nuovo endpoint è necessario che la distribuzione del nuovo modello aggiornato sia annullata.

**Domanda**: Che cosa è necessario fare se serve una concorrenza più elevata per il modello distribuito rispetto a ciò che viene offerto nel portale? 

**Risposta**: È possibile aumentare le prestazioni del modello in incrementi di 20 richieste simultanee. 

Se è necessario un aumento ancora superiore, rivolgersi a Microsoft.

**Domanda**: È possibile scaricare il modello ed eseguirlo in locale?

**Risposta**: Non è possibile scaricare i modelli ed eseguirli in locale.

**Domanda**: Le richieste vengono registrate?

**Risposta**: Durante la creazione di una distribuzione, è possibile scegliere di disattivare la traccia. A questo punto non vengono effettuate registrazioni di audio o di trascrizioni. In caso contrario, le richieste vengono in genere registrate nel servizio di archiviazione sicura in Azure. In caso di altri problemi di privacy che impediscono l'uso del Servizio di riconoscimento vocale personalizzato, contattare uno dei canali di supporto.

## <a name="importing-data"></a>Importazione di dati

**Domanda**: Qual è il limite per le dimensioni del set di dati? Perché? 

**Risposta**:Il limite corrente per un set di dati è di 2 GB, a causa delle restrizioni relative alle dimensioni dei file per il caricamento HTTP. 

**Domanda**: È possibile comprimere i file di testo per poter caricare file più grandi? 

**Risposta**: No, attualmente sono consentiti solo file di testo non compressi.

**Domanda**: Il report sui dati segnala che alcune espressioni hanno avuto esito negativo. Qual è il problema?

**Risposta**: Se non viene caricato il 100% delle espressioni di un file, non è un problema.
Se la grande maggioranza (ad esempio, più del 95%) delle espressioni in un set di dati acustico o linguistico viene importata correttamente, il set di dati può essere usato. È tuttavia consigliabile cercare di comprendere il motivo dell'esito negativo delle espressioni e risolvere i problemi. I problemi più comuni, ad esempio gli errori di formattazione, sono facili da risolvere. 

## <a name="creating-am"></a>Creazione di modelli acustici

**Domanda**: Quanti dati acustici sono necessari?

**Risposta**: È consigliabile iniziare con dati acustici di lunghezza compresa tra 30 minuti e un'ora.

**Domanda**:Quali dati è necessario raccogliere?

**Risposta**: Raccogliere dati più vicini possibile allo scenario e al caso d'uso dell'applicazione.
La raccolta dei dati deve corrispondere all'applicazione e agli utenti di destinazione in termini di dispositivo o dispositivi, ambienti e tipi di parlanti. In genere, è consigliabile raccogliere dati dalla più ampia varietà di parlanti possibile. 

**Domanda**: Come si devono raccogliere? 

**Risposta**: È possibile creare un'applicazione di raccolta dati autonoma o usare un software di registrazione audio standard.
È anche possibile creare una versione dell'applicazione in grado di registrare e usare i dati audio. 

**Domanda**: È necessario trascrivere personalmente i dati di adattamento? 

**Risposta**: Sì. È possibile farlo personalmente o usando un servizio di trascrizione professionale. Alcuni utenti preferiscono usare sistemi di trascrizione professionali, mentre altri usano il crowdsourcing o eseguono le trascrizioni autonomamente.

## <a name="accuracy-testing"></a>Test di accuratezza

**Domanda**: È possibile eseguire il testing offline del modello acustico personalizzato usando un modello linguistico personalizzato?

**Risposta**: Sì, è sufficiente selezionare il modello linguistico personalizzato nell'elenco a discesa durante la configurazione del test offline.

**Domanda**: È possibile eseguire il testing offline del modello linguistico personalizzato usando un modello acustico personalizzato?

**Risposta**: Sì, è sufficiente selezionare il modello acustico personalizzato nel menu a discesa durante la configurazione del test offline.

**Domanda**: Che cos'è la frequenza degli errori di parola (WER) e come viene calcolata?

**Risposta**: La frequenza degli errori di parola (WER) è la metrica di valutazione per il riconoscimento vocale. Viene calcolata come numero totale di errori, inclusi inserimenti, eliminazioni e sostituzioni, diviso per il numero totale di parole nella trascrizione di riferimento. Altri dettagli sono disponibili [qui](https://en.wikipedia.org/wiki/Word_error_rate).

**Domanda**: Come si determina se i risultati di un test di accuratezza sono positivi?

**Risposta**: I risultati mostrano un confronto tra il modello di base e quello personalizzato.
Perché la personalizzazione sia proficua, è necessario che sia più efficiente del modello di base.

**Domanda**: Come si può scoprire la frequenza degli errori di parola nei modelli di base per comprendere se c'è stato un miglioramento? 

**Risposta**: I risultati dei test offline mostrano l'accuratezza di base del modello personalizzato e il miglioramento rispetto al modello di base.

## <a name="creating-lm"></a>Creazione di modelli linguistici

**Domanda**: Quanti dati di testo è necessario caricare?

**Risposta**: Dipende da quanto il vocabolario e le frasi usate nell'applicazione differiscono dai modelli linguistici iniziali. Per tutte le parole nuove è utile specificare il maggior numero possibile di esempi di utilizzo. Per le frasi frequenti usate nell'applicazione, è utile anche includere frasi nei dati linguistici, perché indicano al sistema di rimanere in ascolto anche di questi termini. È frequente che nel seti di dati linguistici siano presenti almeno cento espressioni. Di solito ce ne sono almeno diverse centinaia. Se inoltre si prevede che alcuni tipi di query siano più comuni di altri, è possibile inserire nel set di dati varie copie delle query più comuni.

**Domanda**: È possibile caricare semplicemente un elenco di parole?

**Risposta**: Il caricamento di un elenco di parole consente di inserire le parole nel vocabolario ma non di insegnare al sistema come le parole stesse vengono generalmente usate.
Se si specificano espressioni complete o parziali (frasi o espressioni di probabile uso da parte degli utenti), il modello linguistico può apprendere le nuove parole e come vengono usate. Il modello linguistico personalizzato è utile non solo per inserire nuove parole nel sistema ma anche per modificare la probabilità di parole note per l'applicazione. L'indicazione di espressioni complete favorisce l'apprendimento da parte del sistema. 

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi](troubleshooting.md)
* [Note sulla versione](releasenotes.md)
