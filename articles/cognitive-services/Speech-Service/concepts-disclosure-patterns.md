---
title: Schemi di progettazione della divulgazione
titleSuffix: Azure Cognitive Services
description: Modelli di progettazione e procedure consigliate per la divulgazione.
services: cognitive-services
author: angle
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: angle
ms.openlocfilehash: e0cb73f224f53441ad4f23298a035f95d59b9f7c
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559000"
---
# <a name="disclosure-design-patterns"></a>Schemi progettuali per le informative
Ora che hai&#39;determinato il giusto [livello di divulgazione](concepts-disclosure-guidelines.md#disclosure-assessment) per la tua esperienza vocale sintetica, è&#39;opportuno esplorare i modelli di progettazione potenziali.
## <a name="overview"></a>Panoramica
Esiste una vasta gamma di modelli di progettazione di divulgazione che è possibile applicare all'esperienza vocale sintetica. Se il risultato della valutazione della divulgazione era "divulgazione elevata", si consiglia la [**divulgazione esplicita**](#explicit-disclosure), che significa comunicare le origini della voce sintetica. La [**divulgazione implicita**](#implicit-disclosure) include suggerimenti e modelli di interazione che traggono vantaggio dall'esperienza vocale, indipendentemente dal fatto che i livelli di divulgazione siano elevati o bassi.
![gamma di modelli di divulgazione](media/responsible-ai/disclosure-patterns/affordances.png)






| Modelli di divulgazione esplicita                                                                                                                                                                                    | Modelli di divulgazione implicita                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Introduzione trasparente](#transparent-introduction)<br> [Introduzione trasparente verbale](#verbal-transparent-introduction)<br>  [Sottotitolo esplicito](#explicit-byline)<br>  [Personalizzazione e calibrazione](#customization-and-calibration)<br> [Divulgazione parentale](#parental-disclosure)<br> [Possibilità di ottenere ulteriori informazioni su come è stata creata la voce](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Divulgazione delle funzionalità](#capability-disclosure)<br>[Suggerimenti e commenti impliciti](#implicit-cues--feedback)<br> [Trasparenza conversazione](#conversational-transparency) |



Usare il grafico seguente per fare riferimento direttamente ai modelli applicabili alla voce sintetica. Alcune delle altre condizioni di questo grafico possono essere valide anche per lo scenario:<br/>



| Se l'esperienza di Voice sintetico... | Raccomandazioni | Modelli di progettazione |
| --- | --- | --- |
| Richiede la divulgazione elevata  | Usare almeno un modello esplicito e i segnali impliciti in primo piano per aiutare gli utenti a compilare le associazioni. |[Divulgazione esplicita](#explicit-disclosure)<br>[Divulgazione implicita](#implicit-disclosure)  |
| Richiede la divulgazione limitata | La divulgazione può essere minima o superflua, ma può trarre vantaggio da alcuni modelli impliciti. | [Divulgazione delle funzionalità](#capability-disclosure)<br>[Trasparenza conversazione](#conversational-transparency)  |
| Ha un elevato livello di engagement | Compilare per il lungo termine e offrire più punti di ingresso per la divulgazione durante il percorso utente. Si consiglia vivamente di eseguire un'esperienza di onboarding. | [Introduzione trasparente](#transparent-introduction)<br>[Personalizzazione e calibrazione](#customization-and-calibration)<br>[Divulgazione delle funzionalità](#capability-disclosure) |
| Include elementi figlio come destinatari primari | Rivolgersi ai genitori come destinatari della divulgazione principale e assicurarsi che possano comunicare in modo efficace la divulgazione agli elementi figlio.  | [Divulgazione parentale](#parental-disclosure)<br>[Introduzione trasparente verbale](#verbal-transparent-introduction)<br> [Divulgazione implicita](#implicit-disclosure)<br> [Trasparenza conversazione](#conversational-transparency)  |
| Include utenti ciechi o persone con ipovisione come destinatari primari  | Essere inclusi tutti gli utenti e assicurarsi che qualsiasi forma di divulgazione visiva abbia associato testo alternativo o effetti audio. Rispettare gli standard di accessibilità per il rapporto di contrasto e le dimensioni di visualizzazione. Usare i segnali acustici per comunicare la divulgazione.  | [Introduzione trasparente verbale](#verbal-transparent-introduction) <br>[Segnali uditivi](#implicit-cues--feedback)<br>[Suggerimenti tattili](#implicit-cues--feedback)<br>[Trasparenza conversazione](#conversational-transparency)<br>[Standard di accessibilità](https://www.microsoft.com/accessibility) |
| È senza schermo, senza dispositivo o usa la voce come modalità di interazione primaria o unica | Usare i segnali acustici per comunicare la divulgazione. | [Introduzione trasparente verbale](#verbal-transparent-introduction) <br> [Segnali uditivi](#implicit-cues--feedback)  |
| Potenzialmente include più utenti/listener (ad esempio, Personal Assistant in più famiglie)  | Tenere conto dei diversi contesti e dei livelli di comprensione degli utenti e offrire più opportunità per la divulgazione nel percorso utente.  | [Introduzione trasparente (utente restituito)](#transparent-introduction)<br> [Possibilità di ottenere ulteriori informazioni su come è stata creata la voce](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Trasparenza conversazione](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Divulgazione esplicita
Se l'esperienza della voce sintetica richiede la divulgazione elevata, è consigliabile usare almeno uno dei seguenti modelli espliciti per indicare chiaramente la natura sintetica.
### <a name="transparent-introduction"></a>Introduzione trasparente

Prima dell'inizio dell'esperienza vocale, l'assistente digitale è stato completamente trasparente sulle origini della sua voce e sulle sue funzionalità. Il momento ottimale per usare questo modello è quando si carica un nuovo utente o quando si introducono nuove funzionalità per un utente che restituisce un nuovo utente. L'implementazione di suggerimenti impliciti durante un'introduzione consente agli utenti di creare un modello mentale sulla natura sintetica dell'agente digitale.

#### <a name="first-time-user-experience"></a>Esperienza utente per la prima volta

![Introduzione trasparente durante la prima esecuzione](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*La voce sintetica viene introdotta durante l'onboarding di un nuovo utente.*

Raccomandazioni
- Descrivere che la voce è artificiale, ad esempio &quot;&quot;digitali
- Descrivere le operazioni che l'agente è in grado di eseguire
- Dichiarare in modo esplicito&#39;le origini vocali
- Offrire un punto di ingresso per ulteriori informazioni sulla voce sintetica

#### <a name="returning-user-experience"></a>Restituzione dell'esperienza utente

Se un utente ignora l'esperienza di onboarding, continuare a offrire punti di ingresso all'esperienza di introduzione trasparente fino a quando l'utente non attiverà la voce per la prima volta.
<br/>

![Introduzione trasparente durante la restituzione dell'esperienza utente](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Fornire un punto di ingresso coerente all'esperienza vocale sintetica. Consente all'utente di tornare all'esperienza di onboarding quando avvia la voce per la prima volta in qualsiasi punto del percorso utente.*


### <a name="verbal-transparent-introduction"></a>Introduzione trasparente verbale

Una richiesta vocale che indica le origini della voce di assistente&#39;digitale è sufficientemente esplicita per ottenere la divulgazione. Questo modello è ideale per scenari di divulgazione elevata in cui Voice è l'unica modalità di interazione disponibile.
<br/>

![Introduzione trasparente pronunciata oralmente](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Utilizzare un'introduzione trasparente quando si verificano istanti nell'esperienza utente in cui è già possibile introdurre o attribuire una&#39;voce a una persona.*


![Introduzione trasparente pronunciata oralmente nel primo utente](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*Per una maggiore trasparenza, l'attore vocale può divulgare le origini della voce sintetica nella prima persona.*

### <a name="explicit-byline"></a>Sottotitolo esplicito

Usare questo modello se l'utente interagisce con un lettore audio o un componente interattivo per attivare la voce.


![la firma esplicita in uno scenario multimediale di notizie](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Un sottotitolo esplicito è l'attribuzione da cui proviene la voce.*

Raccomandazioni

- Punto di ingresso dell'offerta per ulteriori informazioni sulla voce sintetizzata

### <a name="customization-and-calibration"></a>Personalizzazione e calibrazione

Fornire agli utenti il controllo sulla modalità di risposta dell'assistente digitale (ad esempio, come funziona la voce).  Quando un utente interagisce con un sistema in base alle proprie esigenze e con specifici obiettivi, quindi, per definizione, ha già compreso che non è&#39;una persona reale.

#### <a name="user-control"></a>Controllo utente

Offrire scelte che hanno un effetto significativo ed evidente sull'esperienza di voce sintetica.

![preferenze utente](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*Le preferenze utente consentono agli utenti di personalizzare e migliorare l'esperienza.*

Raccomandazioni

- Consenti agli utenti di personalizzare la voce (ad esempio, selezionare la lingua e il tipo di voce)
- Fornire agli utenti un modo per insegnare al sistema di rispondere alla propria voce univoca (ad esempio, la calibrazione vocale, i comandi personalizzati)
- Ottimizzare le interazioni generate dall'utente o contestuali, ad esempio i promemoria

#### <a name="persona-customization"></a>Personalizzazione di tipo persona

Offrire modi per personalizzare la voce degli&#39;assistenti digitali. Se la voce si basa su una celebrità o su una persona ampiamente riconoscibile, è consigliabile usare sia l'introduzione visiva che quella parlata quando gli utenti visualizzano in anteprima la voce.

personalizzazione della voce ![](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Offrire la possibilità di selezionare un set di voci consente di fornire la natura artificiale.*

Raccomandazioni
- Consenti agli utenti di visualizzare in anteprima il suono di ogni voce
- Usa un'introduzione autentica per ogni voce
- Fornire punti di ingresso per ulteriori informazioni sulla voce sintetizzata

### <a name="parental-disclosure"></a>Divulgazione parentale

Oltre a rispettare le normative della COPPA, fornire la divulgazione agli elementi padre se i destinatari primari sono figli e il livello di esposizione è elevato. Per gli usi riservati, provare a eseguire il controllo dell'esperienza fino a quando un adulto non ha riconosciuto l'uso della voce sintetica. Incoraggiare i genitori a comunicare il messaggio agli elementi figlio.

divulgazione ![per gli elementi padre](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*Un'introduzione trasparente ottimizzata per i genitori garantisce che un adulto abbia reso conto della natura sintetica della voce prima che un figlio interagisce con esso.*

Raccomandazioni

- Indirizzare gli elementi padre come destinatari primari per la divulgazione
- Incoraggiare i genitori a comunicare la divulgazione ai propri figli
- Fornire punti di ingresso per ulteriori informazioni sulla voce sintetizzata
- Controllare l'esperienza chiedendo ai genitori una semplice domanda di &quot;SafeGuard&quot; per mostrare che hanno letto la divulgazione

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Possibilità di ottenere ulteriori informazioni su come è stata creata la voce

Offrire punti di ingresso sensibili al contesto a una pagina, a un popup o a un sito esterno che fornisce ulteriori informazioni sulla tecnologia voce sintetica. Ad esempio, è possibile visualizzare un collegamento per ottenere ulteriori informazioni durante il caricamento o quando l'utente richiede ulteriori informazioni durante la conversazione.

![punto di ingresso per ulteriori informazioni](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Esempio di punto di ingresso per offrire l'opportunità di ottenere ulteriori informazioni sulla voce sintetizzata.*

Quando un utente richiede ulteriori informazioni sulla voce sintetica, l'obiettivo principale è quello di istruirle sulle origini della voce sintetica e di essere trasparenti sulla tecnologia.

![fornire agli utenti ulteriori informazioni sulla voce sintetica](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Ulteriori informazioni possono essere disponibili in un sito esterno della guida del sito.*

Raccomandazioni

- Semplifica i concetti complessi ed evita l'uso di un gergo tecnico e legale
- Non seppellire questo contenuto nelle istruzioni sulla privacy e sulle condizioni per l'utilizzo
- Mantieni conciso il contenuto e usa le immagini quando disponibili

## <a name="implicit-disclosure"></a>Divulgazione implicita

La coerenza è la chiave per ottenere la divulgazione implicita in tutto il percorso utente. L'uso coerente di suggerimenti visivi e uditivi tra dispositivi e modalità di interazione può aiutare a creare associazioni tra modelli impliciti e divulgazione esplicita.

![Coerenza dei segnali impliciti](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Suggerimenti impliciti & Commenti

Il antropomorfismo può manifestarsi in modi diversi, dalla rappresentazione visiva effettiva dell'agente, alla voce, ai suoni, ai modelli di luce, alle forme rimbalzanti o persino alla vibrazione di un dispositivo. Quando si definisce la persona, sfruttare i suggerimenti e i modelli di feedback impliciti anziché puntare a un avatar molto simile a quello umano. Questo è un modo per ridurre al minimo la necessità di divulgazione più esplicita.

![segnali visivi e commenti e suggerimenti](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Questi suggerimenti aiutano a antropomorfizzare l'agente senza essere troppo umani. Possono anche diventare propri meccanismi di divulgazione efficaci quando vengono usati in modo coerente nel tempo.*

Prendere in considerazione le diverse modalità di interazione dell'esperienza quando si incorporano i tipi di indicazioni seguenti:

| Segnali visivi                                                                                                                                                               | Segnali uditivi                                                      | Suggerimenti tattili |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>Suggerimenti in tempo reale reattivi (ad esempio, animazioni)<br> Suggerimenti non sullo schermo (ad esempio, luci e modelli in un dispositivo)<br>  | Sonicon (ad esempio, un breve suono distintivo, serie di note musicali) | Vibrazione   |

### <a name="capability-disclosure"></a>Divulgazione delle funzionalità

La divulgazione può essere eseguita in modo implicito impostando previsioni accurate su ciò che è in grado di supportare Digital Assistant. Fornire comandi di esempio in modo che gli utenti possano imparare a interagire con l'assistente digitale e offrire la guida contestuale per ottenere altre informazioni sulla voce sintetica durante le fasi iniziali dell'esperienza.

![Suggerimenti visivi e commenti e suggerimenti](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Trasparenza conversazione

Quando le conversazioni rientrino in percorsi imprevisti, è consigliabile creare risposte predefinite che consentono di reimpostare le aspettative, rafforzare la trasparenza e indirizzare gli utenti verso i percorsi riusciti. Esistono anche opportunità di utilizzare la divulgazione esplicita nella conversazione.

![Gestione di percorsi imprevisti](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Non attività o &quot;le domande&quot; personali indirizzate all'agente sono il momento giusto per ricordare agli utenti la natura sintetica dell'agente e indirizzarle in modo appropriato o reindirizzarle a una persona reale.

![Gestione delle domande sulle attività](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Quando divulgare

Sono disponibili molte opportunità per la divulgazione durante il percorso utente. Progettazione per il primo utilizzo, secondo utilizzo, ennesimo utilizzo..., ma anche momenti di &quot;errore&quot; per evidenziare la trasparenza, ad esempio quando il sistema commette un errore o quando l'utente individua una limitazione delle funzionalità dell'&#39;agente.

![Opportunità di divulgazione in un percorso utente](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Esempio di un percorso utente di Digital Assistant standard che evidenzia le varie opportunità di divulgazione.

### <a name="up-front"></a>In primo piano

Il momento ottimale per la divulgazione è la prima volta che una persona interagisce con la voce sintetica.  In uno scenario di Assistente vocale personale, questo sarebbe durante il processo di onboarding o la prima volta che l'utente si è praticamente decassettato dall'esperienza. In altri scenari, potrebbe essere la prima volta che una voce sintetica legge il contenuto in un sito Web o la prima volta che un utente interagisce con un carattere virtuale.

- [Introduzione trasparente](#transparent-introduction)
- [Divulgazione delle funzionalità](#capability-disclosure)
- [Personalizzazione e calibrazione](#customization-and-calibration)
- [Suggerimenti impliciti](#implicit-cues--feedback)

### <a name="upon-request"></a>Su richiesta

Gli utenti devono essere in grado di accedere facilmente a informazioni aggiuntive, controllare le preferenze e ricevere comunicazione trasparente in qualsiasi momento durante il percorso utente quando richiesto.

- [Possibilità di ottenere ulteriori informazioni su come è stata creata la voce](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Personalizzazione e calibrazione](#customization-and-calibration)
- [Trasparenza conversazione](#conversational-transparency)

### <a name="continuously"></a>Continuamente

Usare i modelli di progettazione impliciti che migliorano costantemente l'esperienza utente.

- [Divulgazione delle funzionalità](#capability-disclosure)
- [Suggerimenti impliciti](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>Quando il sistema ha esito negativo

Utilizzare la divulgazione come opportunità per eseguire correttamente l'errore.

- [Trasparenza conversazione](#conversational-transparency)
- [Possibilità di ottenere ulteriori informazioni su come è stata creata la voce](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Passaggio a una persona](#conversational-transparency)



## <a name="additional-resources"></a>Risorse aggiuntive
- [Linee guida per bot Microsoft](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Linee guida per la progettazione di Cortana](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Linee guida per la progettazione di sintesi vocale di Microsoft Windows UWP](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Linee guida per i comandi vocali della realtà mista di Microsoft Windows](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>Documenti di riferimento

* [Divulgazione per il talento vocale](https://aka.ms/disclosure-voice-talent)
* [Linee guida per la distribuzione responsabile della tecnologia Voice sintetica](concepts-guidelines-responsible-deployment-synthetic.md)
* [Cenni preliminari sul controllo](concepts-gating-overview.md)
* [Come divulgare](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>Passaggi successivi

* [Divulgazione per il talento vocale](https://aka.ms/disclosure-voice-talent)
