---
title: Disclosure Design Patterns
titleSuffix: Azure Cognitive Services
description: Progettare modelli e best practice per la divulgazione.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: 3e7d8ee2b156a30b11cda79798a8af8a8ecf4f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776623"
---
# <a name="disclosure-design-patterns"></a>Schemi progettuali per le informative
Ora che si&#39;determinato il giusto [livello di divulgazione](concepts-disclosure-guidelines.md#disclosure-assessment) per la vostra esperienza vocale sintetica,&#39;è un buon momento per esplorare potenziali modelli di progettazione.
## <a name="overview"></a>Panoramica
C'è uno spettro di modelli di progettazione di divulgazione che è possibile applicare alla vostra esperienza vocale sintetica. Se l'esito della tua valutazione di divulgazione è stata "High Disclosure", raccomandiamo la [**divulgazione esplicita,**](#explicit-disclosure)il che significa comunicare le origini della voce sintetica a titolo definitivo. [**La divulgazione implicita**](#implicit-disclosure) include segnali e modelli di interazione che beneficiano le esperienze vocali indipendentemente dal fatto che i livelli di divulgazione richiesti siano alti o bassi.
![Spettro di modelli di divulgazione](media/responsible-ai/disclosure-patterns/affordances.png)






| Modelli di divulgazione esplicitaExplicit disclosure patterns                                                                                                                                                                                    | Modelli di informativa impliciti                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Introduzione trasparente](#transparent-introduction)<br> [Introduzione verbale trasparente](#verbal-transparent-introduction)<br>  [Esplicito Byline](#explicit-byline)<br>  [Personalizzazione e calibrazione](#customization-and-calibration)<br> [Divulgazione dei genitori](#parental-disclosure)<br> [Offrire opportunità per saperne di più su come è stata fatta la voce](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Divulgazione delle capacità](#capability-disclosure)<br>[Cue implicite e feedback](#implicit-cues--feedback)<br> [Trasparenza conversazionale](#conversational-transparency) |



Utilizzare il grafico seguente per fare riferimento direttamente ai modelli che si applicano alla voce sintetica. Alcune delle altre condizioni in questo grafico possono essere applicate anche allo scenario:<br/>



| Se la tua esperienza di voce sintetica... | Consigli | Modelli di progettazione |
| --- | --- | --- |
| Richiede un'elevata divulgazione  | Usare almeno un modello esplicito e segnali impliciti in anticipo per aiutare gli utenti a creare associazioni. |[Divulgazione esplicita](#explicit-disclosure)<br>[Divulgazione implicita](#implicit-disclosure)  |
| Richiede scarsa divulgazione | La divulgazione può essere minima o non necessaria, ma potrebbe trarre vantaggio da alcuni modelli impliciti. | [Divulgazione delle capacità](#capability-disclosure)<br>[Trasparenza conversazionale](#conversational-transparency)  |
| Ha un alto livello di impegno | Costruisci a lungo termine e offri più punti di ingresso alla divulgazione lungo il percorso dell'utente. Si consiglia vivamente di avere un'esperienza di onboarding. | [Introduzione trasparente](#transparent-introduction)<br>[Personalizzazione e calibrazione](#customization-and-calibration)<br>[Divulgazione delle capacità](#capability-disclosure) |
| Include i bambini come pubblico primario | Scegli come target per i genitori come pubblico principale per la divulgazione e assicurati che possano comunicare efficacemente la divulgazione ai bambini.  | [Divulgazione dei genitori](#parental-disclosure)<br>[Introduzione verbale trasparente](#verbal-transparent-introduction)<br> [Divulgazione implicita](#implicit-disclosure)<br> [Trasparenza conversazionale](#conversational-transparency)  |
| Include utenti non vedenti o con problemi di vista come destinatario principale  | Essere inclusivo di tutti gli utenti e assicurarsi che qualsiasi forma di divulgazione visiva ha associato testo alternativo o effetti sonori. Rispettare gli standard di accessibilità per il rapporto di contrasto e le dimensioni del display. Usa i segnali uditivi per comunicare la divulgazione.  | [Introduzione verbale trasparente](#verbal-transparent-introduction) <br>[Cue Uditrie](#implicit-cues--feedback)<br>[Cuche aptiche](#implicit-cues--feedback)<br>[Trasparenza conversazionale](#conversational-transparency)<br>[Standard di accessibilità](https://www.microsoft.com/accessibility) |
| È senza schermo, senza dispositivo o utilizza la voce come modalità primaria o unica di interazione | Usa i segnali uditivi per comunicare la divulgazione. | [Introduzione verbale trasparente](#verbal-transparent-introduction) <br> [Cue Uditrie](#implicit-cues--feedback)  |
| Potenzialmente include più utenti/ascoltatori (ad esempio, assistente personale in più famiglie)  | Essere consapevoli dei vari contesti utente e livelli di comprensione e offrire molteplici opportunità di divulgazione nel percorso dell'utente.  | [Introduzione trasparente (Restituzione utente)](#transparent-introduction)<br> [Offrire opportunità per saperne di più su come è stata fatta la voce](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Trasparenza conversazionale](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Divulgazione esplicita
Se l'esperienza vocale sintetica richiede un'elevata divulgazione, è consigliabile utilizzare almeno uno dei seguenti modelli espliciti per indicare chiaramente la natura sintetica.
### <a name="transparent-introduction"></a>Introduzione trasparente

Prima dell'inizio dell'esperienza vocale, introduci l'assistente digitale essendo completamente trasparente sulle origini della sua voce e sulle sue capacità. Il momento ottimale per utilizzare questo modello è quando si esegue l'onboarding di un nuovo utente o quando si introducono nuove funzionalità per un utente di ritorno. L'implementazione di segnali impliciti durante un'introduzione aiuta gli utenti a formare un modello mentale sulla natura sintetica dell'agente digitale.

#### <a name="first-time-user-experience"></a>Esperienza utente per la prima volta

![Introduzione trasparente durante l'esperienza di prima esecuzione](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*La voce sintetica viene introdotta durante l'onboarding di un nuovo utente.*

Consigli
- Descrivi che la voce è &quot;artificiale&quot;(ad es. digitale)
- Descrivere ciò che l'agente è in grado di fare
- Indicare in modo esplicito la voce&#39;s origini
- Offrire un punto di ingresso per saperne di più sulla voce sintetica

#### <a name="returning-user-experience"></a>Restituzione dell'esperienza utente

Se un utente ignora l'esperienza di onboarding, continuare a offrire punti di ingresso all'esperienza Introduzione trasparente fino a quando l'utente non attiva la voce per la prima volta.
<br/>

![Introduzione trasparente durante l'esperienza utente di ritorno](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Fornire un punto di ingresso coerente all'esperienza vocale sintetica. Consentire all'utente di tornare all'esperienza di onboarding quando attiva la voce per la prima volta in qualsiasi punto del percorso utente.*


### <a name="verbal-transparent-introduction"></a>Introduzione verbale trasparente

Un prompt parlato che indica le origini della voce dell'assistente digitale&#39;è abbastanza esplicito da solo per ottenere la divulgazione. Questo modello è ideale per scenari di divulgazione elevata in cui la voce è l'unica modalità di interazione disponibile.
<br/>

![Introduzione trasparente pronunciata verbalmente](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Usa un'introduzione trasparente quando ci sono momenti nell'esperienza utente in cui potresti già introdurre o attribuire una persona&#39;s voce.*


![Verbalmente parlato introduzione trasparente in prima persona](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*Per una maggiore trasparenza, il doppiatore può rivelare le origini della voce sintetica in prima persona.*

### <a name="explicit-byline"></a>Esplicito Byline

Usa questo modello se l'utente interagirà con un lettore audio o un componente interattivo per attivare la voce.


![Esplicito byline in uno scenario di news media](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Un byline esplicito è l'attribuzione della provenicina della voce.*

Consigli

- Offrire un punto di ingresso per saperne di più sulla voce sintetizzata

### <a name="customization-and-calibration"></a>Personalizzazione e calibrazione

Fornire agli utenti il controllo su come l'assistente digitale risponde a loro (cioè, come la voce suona).  Quando un utente interagisce con un sistema alle proprie condizioni e con obiettivi specifici in mente, quindi per definizione, hanno già capito che&#39;non è una persona reale.

#### <a name="user-control"></a>Controllo utente

Offrite scelte che hanno un impatto significativo e notevole sull'esperienza vocale sintetica.

![Preferenze dell'utente](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*Le preferenze utente consentono agli utenti di personalizzare e migliorare la propria esperienza.*

Consigli

- Consentire agli utenti di personalizzare la voce (ad esempio, selezionare la lingua e il tipo di voce)
- Fornire agli utenti un modo per insegnare al sistema a rispondere alla sua voce unica (ad esempio, calibrazione vocale, comandi personalizzati)
- Ottimizza per interazioni contestuali o generate dall'utente (ad es. promemoria)

#### <a name="persona-customization"></a>Personalizzazione Persona

Offrire modi per personalizzare la voce dell'assistente digitale&#39;. Se la voce è basata su una celebrità o una persona ampiamente riconoscibile, prendere in considerazione l'utilizzo di introduzioni sia visive che vocali quando gli utenti visualizzano l'anteprima della voce.

![Personalizzazione vocale](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Offrire la possibilità di scegliere da un insieme di voci aiuta a trasmettere la natura artificiale.*

Consigli
- Consentire agli utenti di visualizzare in anteprima il suono di ogni voce
- Usare un'introduzione autentica per ogni voceUse an authentic introduction for each voice
- Offrire punti di ingresso per saperne di più sulla voce sintetizzata

### <a name="parental-disclosure"></a>Divulgazione dei genitori

Oltre a rispettare le normative COPPA, fornire divulgazione ai genitori se il pubblico previsto è i bambini piccoli e il livello di esposizione è alto. Per usi sensibili, prendere in considerazione l'atto di gating l'esperienza fino a quando un adulto ha riconosciuto l'uso della voce sintetica. Incoraggia i genitori a comunicare il messaggio ai loro figli.

![Divulgazione per i genitori](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*Un'introduzione trasparente ottimizzata per i genitori assicura che un adulto sia stato informato della natura sintetica della voce prima che un bambino interagisca con essa.*

Consigli

- Targeting dei genitori come il pubblico principale per la divulgazione
- Incoraggiare i genitori a comunicare la divulgazione ai propri figli
- Offrire punti di ingresso per saperne di più sulla voce sintetizzata
- Cancellare l'esperienza ponendo &quot;&quot; ai genitori una semplice domanda di salvaguardia per dimostrare di aver letto la divulgazione

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Offrire opportunità per saperne di più su come è stata fatta la voce

Offrire punti di ingresso sensibili al contesto a una pagina, un popup o un sito esterno che fornisce ulteriori informazioni sulla tecnologia vocale sintetica. Ad esempio, è possibile visualizzare un collegamento per ulteriori informazioni durante l'onboarding o quando l'utente richiede ulteriori informazioni durante la conversazione.

![Punto di ingresso per saperne di più](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Esempio di punto di ingresso per offrire l'opportunità di saperne di più sulla voce sintetizzata.*

Una volta che un utente richiede ulteriori informazioni sulla voce sintetica, l'obiettivo principale è quello di educare loro circa le origini della voce sintetica e di essere trasparente circa la tecnologia.

![Fornire agli utenti ulteriori informazioni sulla voce sintetica](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Ulteriori informazioni possono essere offerte in un sito di assistenza esterno.*

Consigli

- Semplificare concetti complessi ed evitare l'uso di legalese e gergo tecnico
- Non seppellire questo contenuto nelle dichiarazioni sulla privacy e le condizioni d'uso
- Mantenere i contenuti concisi e utilizzare le immagini quando disponibili

## <a name="implicit-disclosure"></a>Divulgazione implicita

La coerenza è la chiave per ottenere la divulgazione implicitamente durante tutto il percorso dell'utente. L'uso coerente di segnali visivi e uditivi tra dispositivi e modalità di interazione può aiutare a creare associazioni tra modelli impliciti e divulgazione esplicita.

![Coerenza dei segnali impliciti](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Cue implicite & Feedback

L'antropomorfismo può manifestarsi in modi diversi, dalla rappresentazione visiva effettiva dell'agente, alla voce, ai suoni, ai modelli di luce, alle forme che rimbalzano o persino alla vibrazione di un dispositivo. Quando definisci la tua persona, sfrutta i segnali impliciti e i modelli di feedback piuttosto che mirare a un avatar molto umano. Questo è un modo per ridurre al minimo la necessità di una divulgazione più esplicita.

![Segnali visivi e feedback](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Questi segnali aiutano ad antropomorfizzare l'agente senza essere troppo umani. Possono anche diventare meccanismi di divulgazione efficaci da soli se utilizzati in modo coerente nel tempo.*

Considera le diverse modalità di interazione della tua esperienza quando incorpori i seguenti tipi di segnali:

| Visivi                                                                                                                                                               | Cue Uditrie                                                      | Cuche aptiche |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>Segnali reattivi in tempo reale (ad es. animazioni)<br> Segnali non su schermo (ad es. luci e motivi su un dispositivo)<br>  | Sonicon (ad es. un breve suono distintivo, serie di note musicali) | Vibrazione   |

### <a name="capability-disclosure"></a>Divulgazione delle capacità

La divulgazione può essere ottenuta in modo implicito impostando aspettative precise su ciò che l'assistente digitale è in grado di fare. Fornire comandi di esempio in modo che gli utenti possano imparare a interagire con l'assistente digitale e offrire assistenza contestuale per saperne di più sulla voce sintetica durante le prime fasi dell'esperienza.

![Segnali visivi e feedback](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Trasparenza conversazionale

Quando le conversazioni cadono in percorsi imprevisti, valuta la possibilità di creare risposte predefinite che possono aiutare a ripristinare le aspettative, rafforzare la trasparenza e indirizzare gli utenti verso percorsi di successo. Ci sono opportunità di utilizzare la divulgazione esplicita anche nella conversazione.

![Gestione di percorsi imprevistiHandling unexpected paths](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Domande off-task o &quot;personali&quot; indirizzate all'agente sono un buon momento per ricordare agli utenti della natura sintetica dell'agente e guidarli a impegnarsi con esso in modo appropriato o per reindirizzarli a una persona reale.

![Gestione delle domande sulle attività](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Quando divulgare

Ci sono molte opportunità per la divulgazione durante il percorso dell'utente. Progettare per il primo utilizzo, secondo utilizzo, ennesimo uso..., ma anche abbracciare momenti di &quot;errore&quot; per evidenziare la trasparenza, ad esempio quando il sistema commette un errore o quando l'utente rileva una limitazione delle capacità dell'agente&#39;.

![Opportunità di divulgazione durante il percorso dell'utente](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Esempio di un percorso utente assistente digitale standard che evidenzia varie opportunità di divulgazione.

### <a name="up-front"></a>Iniziale

Il momento ottimale per la divulgazione è la prima volta che una persona interagisce con la voce sintetica.In uno scenario di assistente vocale personale, questo sarebbe durante l'onboarding, o la prima volta che l'utente unbox virtualmente l'esperienza. In altri scenari, potrebbe essere la prima volta che una voce sintetica legge il contenuto in un sito Web o la prima volta che un utente interagisce con un carattere virtuale.

- [Introduzione trasparente](#transparent-introduction)
- [Divulgazione delle capacità](#capability-disclosure)
- [Personalizzazione e calibrazione](#customization-and-calibration)
- [Cue implicite](#implicit-cues--feedback)

### <a name="upon-request"></a>Su richiesta

Gli utenti devono essere in grado di accedere facilmente a informazioni aggiuntive, controllare le preferenze e ricevere comunicazioni trasparenti in qualsiasi momento durante il percorso dell'utente quando richiesto.

- [Offrire opportunità per saperne di più su come è stata fatta la voce](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Personalizzazione e calibrazione](#customization-and-calibration)
- [Trasparenza conversazionale](#conversational-transparency)

### <a name="continuously"></a>Continuamente

Usare i modelli di progettazione impliciti che migliorano continuamente l'esperienza utente.

- [Divulgazione delle capacità](#capability-disclosure)
- [Cue implicite](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>Quando il sistema si guasta

Utilizzare la divulgazione come un'opportunità per fallire con grazia.

- [Trasparenza conversazionale](#conversational-transparency)
- [Offrire opportunità per saperne di più su come è stata fatta la voce](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Passaggio a una persona](#conversational-transparency)



## <a name="additional-resources"></a>Risorse aggiuntive
- [Linee guida per i bot Microsoft](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Linee guida per la progettazione di Cortana](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Linee guida per la progettazione del riconoscimento vocale Di Microsoft Windows UWP](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Linee guida per i comandi vocali di Microsoft Windows Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>Documentazione di riferimento

* [Divulgazione per il talento vocale](https://aka.ms/disclosure-voice-talent)
* [Linee guida per la distribuzione responsabile della tecnologia vocale sintetica](concepts-guidelines-responsible-deployment-synthetic.md)
* [Panoramica di Gating](concepts-gating-overview.md)
* [Come divulgare](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>Passaggi successivi

* [Divulgazione per il talento vocale](https://aka.ms/disclosure-voice-talent)
