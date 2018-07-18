---
title: Domande frequenti sul servizio di riconoscimento vocale in Azure | Microsoft Docs
description: Di seguito sono riportate le risposte alle domande più frequenti sul riconoscimento vocale.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 64e505889ef9472603471d67a961985c1290663a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054893"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Domande frequenti sul Servizio di riconoscimento vocale personalizzato

Se le risposte alle proprie domande non sono presenti in questo documento, provare a rivolgersi alla community del Servizio di riconoscimento vocale personalizzato in [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) e [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Generale

**Domanda**: Qual è la differenza tra i modelli del servizio di riconoscimento vocale di base e personalizzati?

**Risposta**: I modelli di base sono stati sottoposti a training con dati di proprietà di Microsoft e sono già stati distribuiti nel cloud. I modelli personalizzati consentono all'utente di adattare un modello a un determinato ambiente con una lingua o rumori di fondo particolari. Reparti di produzione aziendali, automobili o strade rumorose richiedono un modello acustico adattato, mentre argomenti specifici come biologia, fisica, radiologia, nomi di prodotti e acronimi personalizzati richiedono un modello linguistico.

**Domanda**: Da dove è necessario iniziare per usare un modello di base?

**Risposta**: Prima di tutto è necessario ottenere una [chiave di sottoscrizione](get-started.md). Se si desidera effettuare chiamate REST ai modelli di base predistribuiti, vedere i [dettagli qui](rest-apis.md). Se si desidera usare WebSocket, scaricare l'[SDK](speech-sdk.md).

**Domanda**: È sempre necessario compilare un modello conversione voce/testo personalizzato?

**Risposta**: No, se l'applicazione usa un linguaggio quotidiano generico, senza vocaboli personalizzati e senza termini rari, non è necessario personalizzare un modello. Se l'applicazione deve essere usata in un ambiente in cui il rumore di fondo è scarso o addirittura assente, non è necessario un modello personalizzato. Il portale consente agli utenti di distribuire modelli di base e personalizzati e di sottoporli a test di accuratezza. È possibile usare questa funzionalità per misurare l'accuratezza di un modello di base rispetto a un modello personalizzato.

**Domanda**: Come è possibile sapere quando l'elaborazione del set di dati o del modello è completa?

**Risposta**: Attualmente l'unica indicazione è lo stato del modello o del set di dati nella tabella.
Quando l'elaborazione è completata, lo stato è "Pronto".

**Domanda**: È possibile creare più modelli alla volta?

**Risposta**: Non c'è limite al numero di modelli presenti nella raccolta, ma in ogni pagina può essere creato un solo modello alla volta.
Non è possibile, ad esempio, avviare un processo di creazione di un modello linguistico se al momento un altro modello linguistico è in fase di elaborazione.
È tuttavia possibile elaborare un modello acustico e un modello linguistico allo stesso tempo. 

**Domanda**: Se ci si rende conto di aver commesso un errore, come è possibile annullare l'importazione dei dati o la creazione del modello in corso? 

**Risposta**: Al momento non è possibile eseguire il rollback di un processo di adattamento di un modello acustico o linguistico.
È possibile eliminare i dati dopo il completamento dell'importazione.

**Domanda**: Qual è la differenza tra i modelli di ricerca e dettatura e i modelli di conversazione?

**Risposta**: Nel Servizio di riconoscimento vocale personalizzato è possibile scegliere tra due modelli acustici e linguistici di base:
query di ricerca o dettatura. Il modello acustico di conversazione Microsoft è adatto per il riconoscimento vocale in uno stile di conversazione.
Questo modo di parlare è solitamente diretto a un'altra persona, come nei call center o nelle riunioni.

**Domanda**: È possibile aggiornare il proprio modello esistente (impilamento di modelli)?

**Risposta**: Non è possibile aggiornare i modelli esistenti. Come soluzione alternativa, combinare il set di dati precedente con quello nuovo e riadattarlo.

I set di dati nuovo e precedente devono essere combinati in un unico file con estensione zip (in caso di dati acustici) o txt (in caso di dati linguistici). Al termine dell'adattamento, per ottenere un nuovo endpoint è necessario che la distribuzione del nuovo modello aggiornato sia annullata.

**Domanda**: Che cosa è necessario fare se serve una concorrenza più elevata rispetto al valore predefinito o a ciò che viene offerto nel portale? 

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

**Risposta**: I dati devono essere trascritti. È possibile farlo personalmente o usando un servizio di trascrizione professionale. Alcuni di questi si avvalgono di trascrittori professionisti, altri invece usano il crowdsourcing.

**Domanda**:Quanto tempo occorre per creare un modello acustico personalizzato?

**Risposta**: Il tempo di elaborazione per creare un modello acustico personalizzato corrisponde circa alla lunghezza del set di dati acustici.
Di conseguenza, l'elaborazione di un modello acustico personalizzato creato da un set di dati di cinque ore richiederà circa cinque ore. 

## <a name="offline-testing"></a>Testing offline

**Domanda**: È possibile eseguire il testing offline del modello acustico personalizzato usando un modello linguistico personalizzato?

**Risposta**: Sì, è sufficiente selezionare il modello linguistico personalizzato nell'elenco a discesa durante la configurazione del test offline.

**Domanda**: È possibile eseguire il testing offline del modello linguistico personalizzato usando un modello acustico personalizzato?

**Risposta**: Sì, è sufficiente selezionare il modello acustico personalizzato nel menu a discesa durante la configurazione del test offline.

**Domanda**: Che cos'è la frequenza degli errori di parola e come viene calcolata?

**Risposta**: La frequenza degli errori di parola è la metrica di valutazione per il riconoscimento vocale. Viene calcolata come numero totale di errori, inclusi inserimenti, eliminazioni e sostituzioni, diviso per il numero totale di parole nella trascrizione di riferimento.

**Domanda**: Come si determina se i risultati di un test di accuratezza sono positivi?

**Risposta**: I risultati mostrano un confronto tra il modello di base e quello personalizzato.
Perché la personalizzazione sia proficua, è necessario che sia più efficiente del modello di base.

**Domanda**: Come si può scoprire la frequenza degli errori di parola nei modelli di base, per comprendere se c'è stato un miglioramento? 

**Risposta**: I risultati del test offline mostrano l'accuratezza di base del modello personalizzato e il miglioramento rispetto al modello di base.

## <a name="creating-lm"></a>Creazione di modelli linguistici

**Domanda**: Quanti dati di testo è necessario caricare?

**Risposta**: Dipende da quanto il vocabolario e le frasi usate nell'applicazione differiscono dai modelli linguistici iniziali. Per tutte le parole nuove è utile specificare il maggior numero possibile di esempi di utilizzo. Per le frasi frequenti usate nell'applicazione, è utile anche includere frasi nei dati linguistici, perché indicano al sistema di rimanere in ascolto anche di questi termini. È frequente che nel seti di dati linguistici siano presenti almeno 100 espressioni. Di solito ce ne sono almeno diverse centinaia. Se inoltre si prevede che alcuni tipi di query siano più comuni di altri, è possibile inserire nel set di dati varie copie delle query più comuni.

**Domanda**: È possibile caricare semplicemente un elenco di parole?

**Risposta**: Il caricamento di un elenco di parole consente di inserire le parole nel vocabolario ma non di insegnare al sistema come le parole stesse vengono generalmente usate.
Se si specificano espressioni complete o parziali (frasi o espressioni di probabile uso da parte degli utenti), il modello linguistico può apprendere le nuove parole e come vengono usate. Il modello linguistico personalizzato è utile non solo per inserire nuove parole nel sistema ma anche per modificare la probabilità di parole note per l'applicazione. L'indicazione di espressioni complete favorisce l'apprendimento da parte del sistema. 

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi](troubleshooting.md)
* [Note sulla versione](releasenotes.md)
