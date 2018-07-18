---
title: Domande frequenti sul Servizio di riconoscimento vocale personalizzato in Azure | Microsoft Docs
description: Di seguito sono riportate le risposte alle domande più frequenti sul Servizio di riconoscimento vocale personalizzato.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/21/2016
ms.author: panosper
ms.openlocfilehash: a929869b36387b3257b672308ceca36c84ff8cae
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373372"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Domande frequenti sul Servizio di riconoscimento vocale personalizzato

Se le risposte alle proprie domande non sono presenti in questo documento, provare a rivolgersi alla community del Servizio di riconoscimento vocale personalizzato in [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) e [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Generale

**Domanda**: Come è possibile sapere quando l'elaborazione del set di dati o del modello è completa?

**Risposta**: Attualmente l'unica indicazione è lo stato del modello o del set di dati nella tabella.
Quando l'elaborazione è completata, lo stato è "Pronto".
Stiamo lavorando per migliorare i metodi di comunicazione dello stato di elaborazione, come la notifica tramite posta elettronica.

**Domanda**: È possibile creare più modelli alla volta?

**Risposta**: Non c'è limite al numero di modelli presenti nella raccolta, ma in ogni pagina può essere creato un solo modello alla volta.
Non è possibile, ad esempio, avviare un processo di creazione di un modello linguistico se al momento un altro modello linguistico è in fase di elaborazione.
È tuttavia possibile elaborare un modello acustico e un modello linguistico allo stesso tempo. 

**Domanda**: Se ci si rende conto di aver commesso un errore, come è possibile annullare l'importazione dei dati o la creazione del modello in corso? 

**Risposta**: Al momento non è possibile eseguire il rollback di un processo di adattamento di un modello acustico o linguistico.
È possibile eliminare i dati dopo il completamento dell'importazione.

**Domanda**: Qual è la differenza tra i modelli di ricerca e dettatura e i modelli colloquiali?

**Risposta**: Nel Servizio di riconoscimento vocale personalizzato è possibile scegliere tra due modelli acustici e linguistici di base:
query di ricerca o dettatura. Il modello acustico colloquiale Microsoft è adatto per il riconoscimento vocale in uno stile colloquiale.
Questo modo di parlare è solitamente diretto a un'altra persona, come nei call center o nelle riunioni.

**Domanda**: È possibile aggiornare il proprio modello esistente (impilamento di modelli)?

**Risposta**: Non è possibile aggiornare un modello esistente con nuovi dati.
Se si ha un nuovo set di dati e si vuole personalizzare un modello esistente, è necessario riadattarlo con i nuovi dati e il vecchio set di dati usato.
Il vecchio set di dati e quello nuovo devono essere uniti in un unico file ZIP (nel caso di dati acustici) o TXT (nel caso di dati linguistici). Una volta eseguito l'adattamento, il nuovo modello aggiornato deve essere ridistribuito per ottenere un nuovo endpoint.

**Domanda**: Che cosa è necessario fare se serve una concorrenza più elevata rispetto al valore predefinito? 

**Risposta**: È possibile aumentare le prestazioni del modello in incrementi di 5 richieste simultanee, ossia unità di scala. Ogni unità di scala garantisce che il modello possa elaborare 5 flussi audio contemporaneamente. È possibile acquistare fino a 100 unità di scala (o 500 richieste simultanee).

Se è necessario un aumento ancora superiore, rivolgersi a Microsoft.

**Domanda**: È possibile scaricare il modello ed eseguirlo in locale?

**Risposta**: Il download e l'esecuzione in locale dei modelli non sono consentiti.

**Domanda**: Le richieste vengono registrate?

**Risposta**: Durante la creazione di una distribuzione, è possibile scegliere di disattivare la traccia. A questo punto non vengono effettuate registrazioni di audio o di trascrizioni. In caso contrario, le richieste vengono in genere registrate nel servizio di archiviazione sicura in Azure. In caso di altri problemi di privacy che impediscono l'uso del Servizio di riconoscimento vocale personalizzato, contattare Microsoft.

## <a name="importing-data"></a>Importazione di dati

**Domanda**: Qual è il limite per le dimensioni del set di dati? Perché? 

**Risposta**:Il limite corrente per un set di dati è di 2 GB, a causa delle restrizioni relative alle dimensioni dei file per il caricamento HTTP. 

**Domanda**: È possibile comprimere i file di testo per poter caricare file più grandi? 

**Risposta**: No, attualmente sono consentiti solo file di testo non compressi.

**Domanda**: Il report sui dati segnala che alcune espressioni hanno avuto esito negativo. È un problema?

**Risposta**: Se l'importazione non è riuscita solo per un numero limitato di espressioni, ciò non costituisce un problema.
Se la grande maggioranza (ad esempio, più del 95%) delle espressioni in un set di dati acustico o linguistico viene importata correttamente, il set di dati può essere usato. È tuttavia consigliabile cercare di comprendere il motivo dell'esito negativo delle espressioni e risolvere i problemi.
I problemi più comuni, ad esempio gli errori di formattazione, sono facili da risolvere. 

## <a name="creating-am"></a>Creazione di modelli acustici

**Domanda**: Quanti dati acustici sono necessari?

**Risposta**: È consigliabile iniziare con dati acustici di lunghezza compresa tra 30 minuti e un'ora.

**Domanda**:Quale tipo di dati è necessario raccogliere?

**Risposta**: È consigliabile raccogliere dati più vicini possibile allo scenario e al caso d'uso dell'applicazione.
Questo significa che la raccolta dei dati deve corrispondere all'applicazione e agli utenti di destinazione in termini di dispositivo o dispositivi, ambienti e tipi di parlanti. In genere, è consigliabile raccogliere dati dalla più ampia varietà di parlanti possibile. 

**Domanda**: Come si devono raccogliere? 

**Risposta**: È possibile creare un'applicazione di raccolta dati autonoma o usare un software di registrazione audio standard.
È anche possibile creare una versione dell'applicazione in grado di registrare e usare i dati audio. 

**Domanda**: È necessario trascrivere personalmente i dati di adattamento? 

**Risposta**: I dati devono essere trascritti. È possibile farlo personalmente o usando un servizio di trascrizione professionale. Alcuni di questi si avvalgono di trascrittori professionisti, altri invece usano il crowdsourcing. Microsoft può inoltre consigliare un servizio di trascrizione su richiesta.

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

**Domanda**: Come si capisce se i risultati del test del modello personalizzato sono positivi o negativi?

**Risposta**: I risultati mostrano un confronto tra il modello di base e quello personalizzato.
Perché la personalizzazione sia proficua, è necessario che sia più efficiente del modello di base.

**Domanda**: Come si può scoprire la frequenza degli errori di parola nei modelli di base, per comprendere se c'è stato un miglioramento? 

**Risposta**: I risultati del test offline mostrano l'accuratezza di base del modello personalizzato e il miglioramento rispetto al modello di base.

## <a name="creating-lm"></a>Creazione di modelli linguistici

**Domanda**: Quanti dati di testo è necessario caricare?

**Risposta**: È una domanda a cui è difficile dare una risposta precisa, poiché dipende da quanto il vocabolario e le frasi usate nell'applicazione differiscono dai modelli linguistici iniziali. Per tutte le parole nuove è utile specificare il maggior numero possibile di esempi di utilizzo. Per le frasi frequenti usate nell'applicazione, è utile anche includere frasi nei dati linguistici, perché indicano al sistema di rimanere in ascolto anche di questi termini.
È frequente che nel seti di dati linguistici siano presenti almeno cento espressioni. Di solito ce ne sono almeno diverse centinaia.
Se inoltre si prevede che alcuni tipi di query siano più comuni di altri, è possibile inserire nel set di dati varie copie delle query più comuni.

**Domanda**: È possibile caricare semplicemente un elenco di parole?

**Risposta**: Il caricamento di un elenco di parole consente di inserire le parole nel vocabolario ma non di insegnare al sistema come le parole stesse vengono generalmente usate.
Se si specificano espressioni complete o parziali (frasi o espressioni di probabile uso da parte degli utenti), il modello linguistico può apprendere le nuove parole e come vengono usate. Il modello linguistico personalizzato è utile non solo per inserire nuove parole nel sistema ma anche per modificare la probabilità di parole note per l'applicazione. L'indicazione di espressioni complete favorisce l'apprendimento da parte del sistema. 

-----

 * [Panoramica](cognitive-services-custom-speech-home.md)
 * [Introduzione](cognitive-services-custom-speech-get-started.md)
 * [Glossario](cognitive-services-custom-speech-glossary.md)
