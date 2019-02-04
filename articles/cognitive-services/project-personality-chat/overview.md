---
title: Informazioni su Project Personality Chat
titlesuffix: Azure Cognitive Services
description: In questo articolo viene fornita una panoramica su Project Personality Chat di Azure, un'API basata su cloud per il miglioramento della funzionalità discorsiva del bot.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: overview
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: 053d43d0f17df2008106bda38318615e60778935
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207514"
---
# <a name="what-is-project-personality-chat"></a>Informazioni su Project Personality Chat

Project Personality Chat migliora la capacità discorsiva del bot tramite la gestione di brevi conversazioni in linea con una personalità distinta e scelta. Personality Chat usa classificatori di finalità per identificare i tipi più comuni di conversazioni brevi e genera risposte coerenti a una personalità. In base alla finalità e ai punteggi di attendibilità, il bot sceglie la migliore risposta da una base editoriale curata o ne genera una in tempo reale usando le Deep Neural Network (DNN). È possibile scegliere tra tre persone predefinite. Il modello di persona restituisce risposte che sono il più simili possibile a quelle della personalità scelta.

È disponibile un set editoriale personalizzabile per query di brevi conversazioni comuni. Può essere facilmente integrato con l'SDK Microsoft Bot Framework. Questo SDK consente di risparmiare il tempo e il costo di scriverle da zero.

## <a name="getting-started-with-project-personality-chat"></a>Introduzione a Project Personality Chat

È possibile visitare la pagina dei lab Project Personality Chat e interagire con la dimostrazione disponibile, oltre a richiedere in anteprima l'accesso al servizio quando sarà disponibile.
Attualmente, nei bot è anche possibile integrare la libreria personalizzabile composta esclusivamente da editoriali tramite l'SDK Microsoft Bot Framework. <br>
[Esempi: Integrare Personality Chat in un bot](https://github.com/Microsoft/BotBuilder-PersonalityChat/) <br>
[Provare la libreria Personality Chat](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp)

## <a name="generating-responses-using-neural-networks"></a>Generazione di risposte usando reti neurali

Personality Chat usa modelli di apprendimento avanzato per imparare i tratti generali di una conversazione e generare risposte. Il modello di generazione delle risposte è una rete neurale ricorrente (RNN). Questo modello viene sottoposto a training usando milioni di conversazioni, con cui riconosce e apprende i tratti dell'interazione umana. Con i tratti di struttura della frase imparati, le nuove query vengono create e le risposte vengono generate. Quando genera questa nuova risposta, il modello effettua una ricerca su un "vocabolario di scrittura" composto da parole e sceglie le prime migliori parole da utilizzare per la risposta. Eseguendo una ricerca con la tecnica beam search, continua a cercare le seconde migliori parole per ogni prima parola generata. Una risposta viene considerata completa quando il modello sceglie la parola di chiusura "End of sentence" (EOS). Appena riceve tutte le risposte, sceglie le risposte migliori in base al punteggio di attendibilità dell'intera risposta.

Il modello impara a generare risposte contestualmente appropriate composte dalla "struttura" corretta. Ad esempio, una domanda come "Desideri parlare ora?" fornisce diversi suggerimenti in merito alla struttura della risposta plausibile, che inizierà probabilmente con una parafrasi delle parole "sì" o "no" e un pronome, probabilmente "io". Un "no" è più probabile che includa una spiegazione cortese e così via.

Il sistema tenta di imparare un modello linguistico da usare per la struttura di base della conversazione. Questa struttura deve consentire alle risposte di essere influenzate in parte da vincoli esterni come argomenti, personalità e così via.  Poiché questi vincoli vengono appresi tramite tratti generali, essi sono adatti a (ma non è limitati a) applicazioni per conversazioni brevi.

![Modello da sequenza a sequenza per la generazione di risposte](./media/overview/sequence-to-sequence-model.png)

## <a name="personality-modeling"></a>Modellazione della personalità

 Con qualsiasi modello di conversazione basato su dati, si riscontra la difficoltà di fornire costantemente una "personalità" coerente. Per PERSONA si intende la personalità percepita durante le interazioni conversazionali. Una persona può essere vista come un insieme di elementi quali identità, comportamento linguistico e stile di interazione. Nella versione corrente di Personality Chat, l'attenzione è portata sul comportamento linguistico e sullo stile di interazione.

Questo modello rappresenta ogni singolo parlante come un vettore o un incorporamento. Consente di codificare le informazioni fornite dal parlante, che influenza il contenuto e lo stile delle relative risposte. Il modello apprende quindi le rappresentazioni del parlante in base al contenuto delle conversazioni fornite da diversi parlanti. I parlanti con risposte simili tendono ad avere incorporamenti simili, occupando posizioni adiacenti nello spazio vettoriale. In questo modo, i dati di training relativi ai parlanti adiacenti all'interno dello spazio vettoriale consentono di aumentare la capacità di generalizzazione del modello di parlante. Il modello raggruppa in modo efficace i parlanti caratterizzati da rappresentazioni simili nello spazio vettoriale in modo da formare un cluster di persona, a cui è assegnato un "ID persona".

Per le persona predefinite, gli attributi e le risposte curate sono usate per trovare il cluster di parlanti più adiacenti. Questo cluster viene quindi scelto come ID persona per ognuna delle personalità predefinite. La personalizzazione continua può verificarsi per qualsiasi tipo di personalità usando una serie di risposte del bot/marchio. Le conversazioni vengono quindi create in modo che emulino in modo accurato quella persona specifica, tra cui il comportamento linguistico e altre caratteristiche principali.

![Modellazione della persona usando cluster di parlanti](./media/overview/persona-modeling.png)

## <a name="small-talk-intent-understanding"></a>Riconoscimento della finalità di conversazioni brevi

Personality Chat dispone di classificatori di finalità per garantire una risposta per finalità di conversazioni brevi. Questi classificatori non interferiscono con le query di ricerca/attività. Un classificatore di alto livello stabilisce se la query riguarda una finalità di conversazione breve o di chiacchierata. Decide utilizzando classificatori lessicali e semantici e combinando i punteggi. Queste finalità vengono sottoposte a training usando dati conversazionali (come esempi di finalità positivi) e le query di ricerca/attività (per esempi di finalità negativi).

Altri classificatori di sub-finalità vengono usati per identificare le sottoclassi di conversazioni brevi per limitare i tipi di conversazioni brevi a cui il servizio risponde, ad esempio: saluti, complimenti, opinioni e così via. Questi classificatori di apprendimento avanzato, usando il Convolutional Deep Structure Semantic Model (CDSSM), convertono tutte le query in vettori. Sono sottoposti a training usando decine di migliaia di query curate per le sub-finalità. Il classificatore abbina quindi una query alle classi di finalità esistenti e identificate trovando la corrispondenza più simile all'interno dello spazio vettoriale.

Sono stati implementati numerosi controlli per evitare risposte sfavorevoli, sulla base delle conoscenze acquisite per agenti intelligenti come Zo. Per impostazione predefinita, Project Personality Chat è impostato per rispondere solo a finalità dell'utente riconosciute. È consigliabile verificare se Project Personality Chat è adatto alle proprie esigenze. I clienti sono invitati a inviare commenti e suggerimenti se riscontrano situazioni che richiedono ulteriore training.
