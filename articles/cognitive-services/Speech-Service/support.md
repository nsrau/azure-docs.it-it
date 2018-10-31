---
title: Opzioni di supporto tecnico e assistenza
titlesuffix: Azure Cognitive Services
description: Informazioni su come ottenere assistenza e supporto tecnico per domande e problemi quando si creano applicazioni che si integrano con il servizio Voce
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: wolfma
ms.openlocfilehash: 58fb5d4be2137eed7d62e702f08bdd66c80be347
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468059"
---
# <a name="support-and-help-options"></a>Opzioni di supporto tecnico e assistenza

Se si sta iniziando a esplorare le funzionalità del servizio Voce o si sta per implementare una nuova funzionalità nell'applicazione, di seguito sono elencati i suggerimenti per gli sviluppatori su dove ottenere assistenza.

> [!div class="checklist"]
> * È possibile rimanere informati sui nuovi sviluppi in *Servizi cognitivi di Azure* o leggere le ultime novità relative al *servizio Voce*.
> * Eseguire una ricerca per verificare se la community ha già discusso il problema o se esiste già la documentazione della funzionalità che si vuole implementare.
> * Se non si riesce a trovare una risposta soddisfacente, si può porre una domanda in *Stack Overflow*.
> * Se si riscontra un problema relativo a uno degli esempi in GitHub, segnalare il problema in *GitHub*.
> * Cercare una soluzione nel *forum UserVoice*.

## <a name="stay-informed"></a>Rimani sempre aggiornato

Le novità su Servizi cognitivi sono raccolte nel[blog sui Servizi cognitivi](https://azure.microsoft.com/blog/topics/cognitive-services/). Per le informazioni più recenti sul servizio Voce, vedere il [blog sul servizio Voce](https://azure.microsoft.com/blog/tag/speech-service/).

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

* [Portale di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
* [Portale di Azure per il governo degli Stati Uniti](https://portal.azure.us)

## <a name="post-a-question-to-stack-overflow"></a>Pubblicare una domanda in Stack Overflow

Stack Overflow è il canale da scegliere per le domande correlate allo sviluppo. In questo canale, membri della community e membri dei team Microsoft sono direttamente coinvolti nell'assistenza agli utenti per la risoluzione dei problemi.

Se non si trova una risposta al problema tramite la ricerca, inviare una nuova domanda a Stack Overflow. Quando si formula la domanda, usare uno dei tag seguenti:

|Componente/Area  |Tag  |
|---------|---------|
|Riconoscimento vocale |[[microsoft-cognitive+speech-to-text]](http://stackoverflow.com/questions/tagged/microsoft-cognitive+speech-to-text)|
|Sintesi vocale |[[microsoft-cognitive+text-to-speech]](http://stackoverflow.com/questions/tagged/microsoft-cognitive+text-to-speech)|
|Traduzione vocale |[[microsoft-cognitive+translation]](http://stackoverflow.com/questions/tagged/microsoft-cognitive+translation)|
|Finalità voce |[[microsoft-cognitive+luis]](http://stackoverflow.com/questions/tagged/microsoft-cognitive+luis)|
|Speech SDK generale |[[microsoft-cognitive+microsoft-speech-api]](http://stackoverflow.com/questions/tagged/microsoft-cognitive+microsoft-speech-api)|

> [!TIP]
> I post di Stack Overflow seguenti contengono suggerimenti su come porre domande e su come aggiungere codice sorgente. Se ci si attiene a queste linee guida, aumenta la probabilità che i membri della community valutino le domande e rispondano rapidamente:  
> * [Come creare una domanda utile](https://stackoverflow.com/help/how-to-ask)
> * [Come creare un esempio minimo, completo e verificabile](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Segnalare un problema in GitHub

Gli esempi vengono spesso pubblicati come open source. Per domande e problemi, segnalare un *problema* nel corrispondente repository GitHub. Si può anche inviare anche una richiesta pull. L'elenco seguente contiene i collegamenti ai repository di esempio:

* [Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues)
* [Devices SDK](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/issues)

È possibile creare un report sui bug, una richiesta di funzionalità oppure porre una domanda di carattere generale e condividere le procedure consigliate. Per i report sui bug seguire il modello fornito:

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
 - Se si segnala un errore relativo a un'interazione con un servizio specifico, indicare SessionId e ora (incluso il fuso orario) degli incidenti segnalati. Il valore di SessionId è indicato in tutti i callback e gli eventi ricevuti.
 - Eventuali altre informazioni aggiuntive


## <a name="uservoice-forum"></a>Forum UserVoice

Condividere le idee per fare in modo che Servizi cognitivi e le API associate funzionino meglio per le applicazioni sviluppate. Usare la Knowledge Base in costante crescita per trovare le risposte alle domande più comuni:

[UserVoice](https://cognitive.uservoice.com/)
