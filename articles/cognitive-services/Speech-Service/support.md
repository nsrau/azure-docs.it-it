---
title: Opzioni di supporto e Guida-servizio vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come ottenere assistenza e supporto tecnico per domande e problemi quando si creano applicazioni che si integrano con il servizio Voce
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: jhakulin
ms.openlocfilehash: f8793975d920e24924fca7c2ae366f70972e6ab7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468626"
---
# <a name="support-and-help-options"></a>Opzioni di supporto tecnico e assistenza

Si sta iniziando a esplorare solo le funzionalità dei servizi di riconoscimento vocale? o si sta per implementare una nuova funzionalità nell'applicazione, di seguito sono elencati i suggerimenti per gli sviluppatori su dove ottenere assistenza.

> [!div class="checklist"]
> * È possibile rimanere informati sui nuovi sviluppi in *Servizi cognitivi di Azure* o leggere le ultime novità relative al *servizio Voce*.
> * Le note sulla versione contengono informazioni per tutte le versioni.
> * Eseguire una ricerca per verificare se la community ha già discusso il problema o se esiste già la documentazione della funzionalità che si vuole implementare.
> * Se non si riesce a trovare una risposta soddisfacente, si può porre una domanda in *Stack Overflow*.
> * Se si riscontra un problema relativo a uno degli esempi in GitHub, segnalare il problema in *GitHub*.
> * Cercare una soluzione nel *forum UserVoice*.

## <a name="stay-informed"></a>Rimani sempre aggiornato

Le novità su Servizi cognitivi sono raccolte nel[blog sui Servizi cognitivi](https://azure.microsoft.com/blog/topics/cognitive-services/). Per informazioni aggiornate sui servizi vocali, tenere traccia del [Blog di servizi vocali](https://azure.microsoft.com/blog/tag/speech-service/).

## <a name="release-notes"></a>Note sulla versione

Le [Note sulla versione](https://aka.ms/csspeech/whatsnew) vengono aggiornate non appena è disponibile una nuova versione. Le note contengono informazioni su nuove funzionalità, miglioramenti e correzioni di bug.

## <a name="search"></a>Ricerca

È possibile trovare la risposta necessaria nella documentazione, negli esempi o nelle risposte alle domande poste in [Stack Overflow](https://www.stackoverflow.com).

### <a name="scoped-search"></a>Ricerca per ambito

Per risultati più veloci, delimitare l'ambito della ricerca in Stack Overflow, nella documentazione e negli esempi di codice usando la query seguente nel [motore di ricerca preferito](https://bing.com):

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples)
```

Dove *{Your Search Terms}* corrisponde alle parole chiave di ricerca.

## <a name="create-an-azure-support-request"></a>Creare una richiesta di supporto in Azure

I clienti di Azure possono creare e gestire le richieste di supporto nel portale di Azure.

* [Azure portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
* [Portale di Azure per il governo degli Stati Uniti](https://portal.azure.us)

## <a name="post-a-question-to-stack-overflow"></a>Pubblicare una domanda in Stack Overflow

Stack Overflow è il canale da scegliere per le domande correlate allo sviluppo. In questo canale, membri della community e membri dei team Microsoft sono direttamente coinvolti nell'assistenza agli utenti per la risoluzione dei problemi.

Se non si trova una risposta al problema tramite la ricerca, inviare una nuova domanda a Stack Overflow. Quando si formula la domanda, usare uno dei tag seguenti:

|Componente/Area  |Tag  |
|---------|---------|
|Riconoscimento vocale |[[microsoft-cognitive+speech-to-text]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+speech-to-text)|
|Sintesi vocale |[[microsoft-cognitive+text-to-speech]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+text-to-speech)|
|Traduzione vocale |[[microsoft-cognitive+translation]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+translation)|
|Finalità voce |[[microsoft-cognitive+luis]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+luis)|
|Speech SDK generale |[[microsoft-cognitive+microsoft-speech-api]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+microsoft-speech-api)|
|Assistenti vocali | [[Microsoft-cognitive + Virtual-Assistant + botframework (]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+virtual-assistant+botframework) |

> [!TIP]
> I post di Stack Overflow seguenti contengono suggerimenti su come porre domande e su come aggiungere codice sorgente. Se ci si attiene a queste linee guida, aumenta la probabilità che i membri della community valutino le domande e rispondano rapidamente:  
> * [Come creare una domanda utile](https://stackoverflow.com/help/how-to-ask)
> * [Come creare un esempio minimo, completo e verificabile](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Segnalare un problema in GitHub

Gli esempi vengono spesso pubblicati come open source. Per domande e problemi, segnalare un *problema* nel corrispondente repository GitHub. Si può anche inviare anche una richiesta pull. L'elenco seguente contiene i collegamenti ai repository di esempio:

* [Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues)
* [Speech Devices SDK](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/issues)

È possibile creare un report sui bug, una richiesta di funzionalità o porre una domanda generale e condividere le procedure consigliate. Per i report sui bug seguire il modello fornito:

**Descrizione del bug**

Descrizione chiara e concisa del bug.

**Riproduzione**

Passaggi per riprodurre il comportamento:
1. ...
2. ...

**Comportamento previsto**

Descrizione chiara e concisa del comportamento previsto.

**Versione di Speech SDK di Servizi cognitivi**

Versione dell'SDK usata.

**Piattaforma, sistema operativo e linguaggio di programmazione**

 - Sistema operativo, ad esempio Windows, Linux, Android, iOS, ... - specificare
 - Hardware: x64, x86, ARM, ...
 - Browser, ad esempio Chrome, Safari (se applicabile) - specificare

**Altre informazioni di contesto**

 - Messaggi di errore, informazioni del log, analisi dello stack, ...
 - Se si segnala un errore per un'interazione del servizio specifica, indicare il valore SessionId e l'ora (incl. TimeZone) degli eventi imprevisti segnalati. Il valore di SessionId è indicato in tutti i callback e gli eventi ricevuti.
 - Eventuali altre informazioni aggiuntive


## <a name="uservoice-forum"></a>Forum UserVoice

Condividere le idee per fare in modo che Servizi cognitivi e le API associate funzionino meglio per le applicazioni sviluppate. Usare la Knowledge Base in costante crescita per trovare le risposte alle domande più comuni:

[UserVoice](https://cognitive.uservoice.com/)
