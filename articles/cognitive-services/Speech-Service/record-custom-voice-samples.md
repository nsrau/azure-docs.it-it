---
title: Esempi di campioni vocali per la creazione di una voce personalizzata
titleSuffix: Microsoft Cognitive Services
description: Creare una voce personalizzata di qualità di produzione grazie alla preparazione di uno script solido, all'assunzione di speaker di alto livello e a una registrazione professionale.
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/5/2018
ms.author: erhopf
ms.openlocfilehash: 0051cc480a26ffc3668f922d1af9fc9cd7af1f49
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167172"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Esempi di campioni vocali per la creazione di una voce personalizzata

La creazione di una voce personalizzata di qualità elevata partendo da zero non è un impegno da sottovalutare. Il componente di base di una voce personalizzata è costituito dalla raccolta di un numero elevato di campioni audio di voci umane. È fondamentale che queste registrazioni audio siano di alta qualità. È necessario pertanto scegliere speaker con esperienza in questi tipi di registrazioni e affidare la registrazione a un tecnico del suono competente che usi apparecchiature professionali.

Prima di eseguire queste registrazioni, tuttavia, è necessario creare uno script costituito dalle parole che vengono lette dallo speaker per realizzare i campioni audio. Per ottenere risultati ottimali, lo script deve essere caratterizzato da una copertura fonetica adeguata e da una varietà sufficiente per eseguire il training del modello vocale personalizzato.

Per creare una registrazione vocale di livello professionale, è necessario fare attenzione a molti piccoli dettagli, che sono tuttavia estremamente importanti. Questa guida è una roadmap di un processo che consente di ottenere risultati validi e coerenti.

> [!TIP]
> Per ottenere risultati ottimali nello sviluppo di una voce personalizzata, è consigliabile coinvolgere Microsoft, che può vantare una vasta esperienza nella produzione di voci di alta qualità per i propri prodotti, ad esempio Cortana e Office.

## <a name="voice-recording-roles"></a>Ruoli nella registrazione vocale

In un progetto di registrazione di voce personalizzata, sono coinvolti quattro ruoli di base:

Ruolo|Scopo
-|-
Speaker        |La voce di questa persona costituirà la base della voce personalizzata.
Tecnico di registrazione  |Controlla gli aspetti tecnici della registrazione e fa funzionare le apparecchiature di registrazione.
Responsabile            |Prepara lo script e sovrintende alle prestazioni dello speaker.
Editor              |Finalizza i file audio e li prepara per il caricamento nel portale per la voce personalizzata.

Una singola persona può ricoprire più di un ruolo. In questa guida si presuppone che l'utente ricopra il ruolo di responsabile e che recluti sia lo speaker che un tecnico di registrazione. Nel caso in cui si desideri eseguire in modo autonomo le registrazioni, nell'articolo sono presenti alcune informazioni sul ruolo di tecnico di registrazione. Il ruolo editor di testo non è necessario fino al termine della sessione, quindi può essere eseguito dal tecnico di registrazione responsabile.

## <a name="choose-your-voice-talent"></a>Scegliere lo speaker

Gli attori con esperienza di doppiatori o di caratteristi sono ottimi esempi di speaker. È spesso possibile trovare speaker adatti anche tra annunciatori e presentatori.

Scegliere uno speaker con la voce naturale che si preferisce. È possibile creare campioni con voci di caratterista univoci, ma per la maggior parte degli speaker è molto più difficile riprodurli in modo uniforme e lo sforzo può provocare tensione nella voce.

> [!TIP]
> È consigliabile evitare, in genere, di usare voci riconoscibili per creare una voce personalizzata, a meno che, naturalmente, l'obiettivo non consista nel produrre una voce celebre. Le voci meno note, generalmente, provocano meno distrazione agli utenti.

Il fattore più importante per la scelta di uno speaker è l'uniformità. Tutte le registrazioni devono sembrare come eseguite nello stesso giorno e nella stessa stanza. È possibile avvicinarsi a questa situazione ideale tramite procedure e tecniche ottimali di registrazione. 

Lo speaker rappresenta la seconda parte dell'equazione. Deve essere in grado di parlare con velocità, livello di volume e tono costanti e una dizione perfetta è naturalmente indispensabile. Lo speaker deve essere anche in grado di controllare rigorosamente la propria variazione di tono, l'effetto emotivo e i modi di parlare.

La registrazione di campioni vocali personalizzati può essere più difficoltosa rispetto ad altri tipi di attività legate alla voce. La maggior parte degli speaker può registrare per due o tre ore al giorno. Limitare le sessioni a tre o quattro alla settimana, con un giorno di riposo tra l'una e l'altra, se possibile.

Le registrazioni eseguite per un modello vocale devono essere neutre dal punto di vista emotivo, ovvero un'espressione triste non deve essere letta in modo triste. Una certa intonazione può essere aggiunta in un secondo momento alla sintesi vocale tramite controlli di prosodia. È opportuno collaborare con lo speaker per sviluppare un utente tipo che definisca il suono complessivo e il tono emotivo della voce personalizzata. In questo processo è possibile individuare con precisione qual è il tono neutro per tale utente.

Un utente tipo, ad esempio, potrebbe avere una personalità naturalmente allegra. Quindi la voce potrebbe essere caratterizzata da una nota di ottimismo anche quando si parla in tono neutro. Questo aspetto della personalità, tuttavia, deve essere poco evidente e costante. Per acquisire un'idea degli obiettivi da raggiungere, ascoltare brani letti da voci esistenti.

> [!TIP]
> In genere si desidera essere proprietari delle registrazioni vocali eseguite. Lo speaker deve essere pertanto disponibile a prestare la propria opera su commissione per il progetto.

## <a name="create-a-script"></a>Creare uno script

Il punto di partenza di qualsiasi sessione di registrazione di voci personalizzate è lo script, che contiene le espressioni che lo speaker deve leggere. Con il termine "espressioni" si indicano sia le frasi complete che quelle più brevi.

Le espressioni nello script possono provenire da origini diverse, ad esempio romanzi, pubblicazioni, trascrizioni di discorsi, notiziari e qualsiasi altra origine disponibile in forma stampata. Se si vuole garantire che la voce restituisca buoni risultati su tipi specifici di parole (ad esempio la terminologia medica o il gergo di programmazione), è possibile includere frasi da pubblicazioni scolastiche o documenti tecnici. Per una breve discussione sui potenziali problemi legali, vedere la sezione ["Aspetti legali"](#legalities). È anche possibile scrivere un testo personalizzato.

Le espressioni non devono provenire necessariamente dalla stessa origine o dallo stesso tipo di origine. e possono anche non essere correlate tra loro. Se tuttavia nell'applicazione di riconoscimento vocale si usano frasi standard, ad esempio "Accesso eseguito", assicurasi di includerle nello script. In questo modo la voce personalizzata avrà una migliore possibilità di pronunciare tali frasi correttamente. Se si decide di usare una registrazione anziché la sintesi vocale, si disporrà già della registrazione nella stessa voce.

Se l'uniformità è il fattore chiave nella scelta dello speaker, la varietà è la caratteristica di base di uno script valido. Lo script deve includere molte parole e frasi diverse, con un'ampia gamma di lunghezze, strutture e intonazioni. Tutti i suoni del linguaggio devono essere rappresentati più volte e in numerosi contesti (condizione definita *copertura fonetica)*. 

Il testo deve anche incorporare tutti i modi in cui un suono specifico può essere rappresentato in forma scritta e posizionare ogni suono in punti diversi nelle frasi. È necessario includere sia frasi dichiarative che domande ed entrambe devono essere lette con l'intonazione appropriata.

La scrittura di uno script caratterizzato da un numero *sufficiente* di dati per consentire al portale del Servizio di riconoscimento vocale personalizzato di creare una voce valida non è semplice. In pratica, il modo più semplice di creare uno script che consenta di ottenere una solida copertura fonetica consiste nell'includere un elevato numero di campioni. Le voci standard fornite da Microsoft sono state ottenute da decine di migliaia di espressioni. Per creare una voce personalizzata di qualità, è opportuno essere preparati almeno a registrare alcune migliaia di espressioni.

Controllare lo script con attenzione per verificare la presenza di errori. Se possibile, far eseguire il controllo anche a un'altra persona. Quando si rivede lo script con lo speaker, è possibile che vengano rilevati altri errori.

### <a name="script-format"></a>Formato dello script

Per scrivere lo script, è possibile usare Microsoft Word. Lo script viene usato durante la sessione di registrazione e di conseguenza è possibile crearlo in modo da consentirne l'uso più semplice. Creare separatamente il file di testo richiesto dal portale per la voce personalizzata.

Un formato di script di base include le tre colonne indicate di seguito:

* Numero di espressioni, a partire da 1. La numerazione semplifica a tutte le persone nello studio il riferimento a un'espressione specifica ("riproviamo l'espressione numero 356"). Per numerare le righe della tabella in modo automatico, è possibile usare la funzionalità di numerazione dei paragrafi di Word.
* Una colonna vuota in cui scrivere il numero del take o il time code di ogni espressione per individuarla nella registrazione completata.
* Testo dell'espressione.

![Script di esempio](media/custom-voice/script.png)

> [!NOTE]
> La maggior parte degli studi esegue la registrazione in brevi segmenti noti come *take*. Ogni take contiene in genere un numero di espressioni compreso tra 10 e 24. Si noti che il numero del take è sufficiente per trovare un'espressione in un secondo momento. Se si esegue la registrazione in uno studio che preferisce registrare segmenti più lunghi, è opportuno prendere nota del time code. Nello studio sarà in tal caso disponibile uno schermo per la visualizzazione cronologica appropriato.

Lasciare spazio sufficiente dopo ogni riga per scrivere le note. Verificare che nessuna espressione sia suddivisa in più pagine. Numerare le pagine e stampare lo script su un lato del foglio.

Stampare tre copie dello script, una per lo speaker, una per il tecnico e una per il responsabile. Usare graffette anziché punti metallici, dato che uno speaker con esperienza separa le pagine per evitare di fare rumore quando le gira.

### <a name="legalities"></a>Aspetti legali

In base alle norme sul copyright, la lettura di un testo protetto da copyright da parte di un attore può essere una prestazione per cui l'autore del lavoro dovrebbe essere ricompensato. Tale prestazione non sarà riconoscibile nel prodotto finale, ovvero la voce personalizzata. Tuttavia, la legittimità dell'uso di un testo protetto da copyright per questo scopo non è comunque definita in modo adeguato. Microsoft non può offrire consulenza legale in relazione a questo problema e consiglia pertanto di rivolgersi al proprio legale.

È possibile tuttavia evitare completamente questi problemi. Sono disponibili molte origini di testo che si possono usare senza licenza né autorizzazione.

|Origine del testo|DESCRIZIONE|
|-|-|
|[CMU Arctic corpus](http://festvox.org/cmu_arctic/)|Circa 1100 frasi selezionate da testi non protetti da copyright per l'uso specifico in progetti di sintesi vocale. Si tratta di un punto di partenza eccellente.|
|Testi non più protetti<br>da copyright|Si tratta in genere di testi pubblicati prima del 1923. Per l'inglese, sul sito [Project Gutenberg](https://www.gutenberg.org/) sono disponibili decine di migliaia di tali testi. Può essere opportuno, tuttavia, concentrarsi su testi più recenti perché la lingua si avvicina di più all'inglese moderno.|
|Testi&nbsp;di enti pubblici|I testi creati dagli enti pubblici degli Stati Uniti non sono protetti da copyright negli Stati Uniti, ma possono esserlo in altri paesi.|
|Pubblico dominio|Testi per cui è stata fatta rinuncia esplicita al diritto di copyright o che sono stati definiti di pubblico dominio. In alcune giurisdizioni può non essere possibile rinunciare completamente al copyright.|
|Testi concessi in licenza|Testi distribuiti in base a una licenza, ad esempio Creative Commons o la GNU Free Documentation License (GFDL). Wikipedia usa la licenza GFDL. Alcune licenze, tuttavia, possono imporre restrizioni sulle prestazioni correlate al contenuto concesso in licenza, condizione che può influire sulla creazione di un modello di voce personalizzata. È consigliabile pertanto leggere attentamente la licenza.|

## <a name="recording-your-script"></a>Registrazione dello script

Registrare lo script in uno studio di registrazione professionale specializzato in attività vocali. In uno studio di questo tipo sono disponibili un banco di registrazione, le apparecchiature adeguate e le persone esperte per farle funzionare. Quando di tratta di eseguire una registrazione, è opportuno non risparmiare.

Discutere il progetto con il tecnico di registrazione di studio e ascoltarne i consigli. La registrazione deve avere minima o nessuna compressione di intervallo dinamico (massimo 4:1). È fondamentale che l'audio sia caratterizzato da un volume uniforme e da un rapporto segnale/rumore elevato e che sia privo di suoni non desiderati.

### <a name="do-it-yourself"></a>Provare

Se si vuole eseguire la registrazione in modo autonomo anziché in uno studio di registrazione, di seguito vengono indicate alcuni istruzioni. Grazie all'aumento della home recording e del podcasting, attualmente è più facile trovare consigli e risorse online.

Il "banco di registrazione" deve essere una piccola stanza senza eco o rumori di fondo evidenti. La stanza deve essere silenziosa e insonorizzata il più possibile. È possibile usare tendaggi sulle pareti per ridurre l'eco e per attenuare i suoni della stanza.

Usare un microfono unidirezionale da studio di qualità elevata (abbreviato in "mic") previsto per la registrazione vocale. I microfoni Sennheiser, AKG e anche i nuovi microfoni Zoom consentono di ottenere ottimi risultati. È possibile acquistare un microfono unidirezionale o noleggiarne uno da una società di apparecchiature audiovisive. Cercare un microfono con un'interfaccia USB. Questo tipo di microfono combina in modo efficiente l'elemento microfono, il preamplificatore e il convertitore analogico-digitale in un unico componente, semplificando il collegamento.

È anche possibile usare un microfono analogico. Molte società di noleggio offrono microfoni meno recenti rinnovati per il carattere voce. Si noti che un dispositivo analogico professionale usa connettori XLR bilanciati anziché il connettore da 1/4" usato nelle apparecchiature consumer. Se si decide di usare un dispositivo analogico, è necessario procurarsi anche un preamplificatore e un'interfaccia audio per computer con tali connettori.

Installare il microfono su un supporto o su un'asta e installare un filtro pop davanti al microfono per eliminare il rumore derivante da consonanti occlusive come "p" e "b". Alcune microfoni sono dotati di un supporto a sospensione che li isola dalle vibrazioni del supporto, condizione estremamente utile.

Lo speaker deve mantenere una distanza costante dal microfono. Con del nastro, contrassegnare sul pavimento i punti in cui deve trovarsi lo speaker. Se lo speaker vuole sedersi, prestare particolare attenzione a monitorare la distanza del microfono ed evitare rumori della sedia.

Usare un supporto per lo script. Evitare l'angolazione del supporto in modo che possa riflettere il suono verso il microfono.

La persona che fa funzionare le apparecchiature di registrazione, ovvero il tecnico, deve trovarsi una stanza separata rispetto allo speaker nel banco di registrazione ( *circuito di talkback).*

La registrazione deve contenere il minor rumore possibile, con l'obiettivo di un rapporto segnale/rumore di 80 db o superiore.

Ascoltare con attenzione una registrazione di silenzio presso il banco, cercare di capire da dove proviene il rumore ed eliminare la causa. Fonti comuni di rumore sono i condotti dell'aria, gli alimentatori delle lampade fluorescenti, il traffico nelle vicinanze e le ventole della apparecchiature (presenti anche nei PC notebook). Cavi e microfoni possono trasmettere rumori elettrici dai cavi CA, in genere interferenze. Un buzz può anche essere causato da un *ground loop*, dovuto alla presenza di apparecchiature collegate a più di un circuito elettrico.

> [!TIP]
> In alcuni casi è possibile usare un equalizzatore o un software di riduzione del rumore per rimuovere il rumore dalle registrazioni, anche se è sempre consigliabile eliminarlo all'origine.

I livelli devono essere impostati in modo che la maggior parte dell'intervallo dinamico disponibile della registrazione digitale venga usato senza overdriving. Ciò significa impostare l'audio ad alto volume, ma non così alto da renderlo distorto. Nella figura seguente è riportato un esempio della forma d'onda di una registrazione valida:

![Una forma d'onda di una registrazione valida](media/custom-voice/good-recording.png)

In questo caso viene usata la maggior parte dell'intervallo (altezza), ma i picchi più alti del segnale non raggiungono la parte superiore o inferiore dell'intervallo stesso. È anche possibile vedere che il silenzio nella registrazione è approssimabile a una linea orizzontale sottile, che indica un basso livello di rumore. Questa registrazione è caratterizzata da un intervallo dinamico e da un rapporto segnale/rumore accettabili.

Registrare direttamente nel computer usando un'interfaccia audio di alta qualità o una porta USB, a seconda del microfono in uso. Se si usa un dispositivo analogico, mantenere semplice la catena audio: microfono, preamplificatore, interfaccia audio, computer. È possibile assegnare mensilmente licenze a entrambe [Avid Pro Tools](http://www.avid.com/en/pro-tools) e [Adobe Audition](https://www.adobe.com/products/audition.html) a un costo ragionevole. Se il budget è estremamente ridotto, provare l'utilità gratuita [Audacity](https://www.audacityteam.org/).

Registrare a una qualità monofonica di 44,1 KHz 16 bit (qualità CD) o superiore. Lo standard attuale è di 48 KHz 24 bit, se l'apparecchiatura in uso lo supporta. Sarà possibile ricampionare l'audio a 16 KHz 16 bit prima di inviarlo al portale per la voce personalizzata. Sottolineiamo che è importante disporre di una registrazione originale di qualità elevata nel caso in cui sia necessario apportare modifiche.

È consigliabile che persone diverse ricoprano i ruoli di responsabile, tecnico e speaker. Non provare a eseguire tutte le operazioni in modo autonomo. In caso di necessità, una sola persona può ricoprire il ruolo di responsabile e di tecnico.

### <a name="before-the-session"></a>Prima della sessione

Per evitare di sprecare tempo in studio, esaminare lo script con lo speaker prima della sessione di registrazione. Lo speaker deve acquisire familiarità con il testo nonché chiarire la pronuncia delle parole che non conosce.

> [!NOTE]
> La maggior parte degli studi di registrazione consente di visualizzare in modo elettronico gli script nel banco di registrazione. In questo caso digitare le note direttamente nel documento dello script. È tuttavia opportuno disporre di una copia cartacea per prendere appunti durante la sessione. Anche la maggior parte dei tecnici vuole in genere una copia cartacea. Predisporre anche una terza copia stampata come backup per lo speaker qualora il computer si arresti.

Lo speaker potrebbe chiedere quale parola deve essere enfatizzata in un'espressione ("parola operativa"). Comunicare allo speaker che si desidera una lettura naturale senza alcuna enfasi particolare. L'enfasi può essere aggiunta in fase di sintesi vocale, ma non deve far parte della registrazione originale.

Indicare allo speaker di pronunciare distintamente le parole. Ogni parola dello script deve essere pronunciata come è stata scritta. I suoni non devono essere omessi né confusi, come talvolta avviene in un discorso casuale, *a meno che non siano stati scritti in tal modo nello script.*

|Testo scritto|Pronuncia casuale indesiderata|
|-|-|
|lo hanno eliminato|l'hanno eliminato|
|le luci erano quattro|le luci erano quattro|
|come è il tempo oggi|com'è il tempo oggi|
|non avere paura|non aver paura|

Lo speaker *non* deve aggiungere pause distinte tra le parole. La frase deve fluire in modo naturale, anche se suona un po' formale. Per raggiungere questo risultato, è necessaria una certa pratica.

### <a name="the-recording-session"></a>Sessione di registrazione

Creare una registrazione di riferimento, denominata *file di corrispondenza,* di un'espressione tipica all'inizio della sessione. Chiedere allo speaker di ripetere questa riga all'incirca a ogni pagina. Confrontare ogni volta la nuova registrazione al riferimento. Questa procedura consente allo speaker di mantenere l'uniformità in volume, tempo, tono e intonazione. Nel frattempo il tecnico può usare il file di corrispondenza come riferimento per controllare i livelli e l'uniformità complessiva del suono.

Il file di corrispondenza è particolarmente importante quando si riprende la registrazione dopo un'interruzione o un altro giorno. È opportuno eseguirlo più volte per lo speaker e farlo ripetere ogni volta fino a quando la corrispondenza non sarà perfetta.

Invitare lo speaker a respirare profondamente e a fare una pausa prima di ogni espressione. Registrare un paio di secondi di silenzio tra le espressioni. Le parole devono essere pronunciate allo stesso modo ogni volta che vengono visualizzate, tenendo conto del contesto. Ad esempio, "viola" come verbo è pronunciato in modo diverso da "viola" come sostantivo.

Registrare cinque secondi di silenzio prima che la prima registrazione acquisisca il rumore di fondo della stanza. In questo modo, il portale per la voce personalizzata può compensare eventuali rumori rimanenti nelle registrazioni.

> [!TIP]
> Ciò che è effettivamente necessario è lo speaker, pertanto è possibile creare una registrazione monofonica (a singolo canale) delle sole righe. Se tuttavia si registra in formato stereo, è possibile usare il secondo canale per registrare la conversazione nella sala di controllo per acquisire le discussioni di righe o take particolari. Rimuovere questa traccia dalla versione caricata nel portale per la voce personalizzata.

Ascoltare con attenzione, usando le cuffie, la prestazione dello speaker. Verificare che la dizione sia buona, ma naturale, che la pronuncia sia corretta e che non siano presenti suoni non desiderati. Non esitare a chiedere allo speaker di registrare nuovamente un'espressione che non soddisfa gli standard. 

> [!TIP] 
> Quando si usa un gran numero di espressioni, una singola espressione potrebbe non avere un effetto evidente sulla voce personalizzata risultante. Può risultare pertanto più vantaggioso prendere nota di eventuali espressioni con problemi, escluderle dal set di dati e scoprire com'è la voce personalizzata risultante. È sempre possibile tornare nello studio e registrare i campioni mancanti in un secondo momento.

Prendere nota del numero del take o del time code per ogni espressione. Chiedere al tecnico se può contrassegnare ogni espressione nei metadati o nel foglio della segnalazione della registrazione.

Concedere momenti di pausa e offrire bevande per consentire allo speaker di mantenere la voce in buono stato.

### <a name="after-the-session"></a>Al termine della sessione

Gli studi di registrazione moderni si avvalgono dell'uso dei computer. Al termine della sessione vengono restituiti uno o più file audio, non un nastro. Tali file sono probabilmente in formato WAV o AIFF in qualità CD (44,1 KHz 16 bit) o superiore. La frequenza di 48 KHz 24 bit è comune e consigliata. In genere, non sono necessarie frequenze di campionamento maggiori, come 96 KHz.

Il portale per la voce personalizzata richiede che ogni espressione si trovi nel proprio file. Ogni file audio fornito dallo studio contiene più espressioni. L'attività di post-produzione primaria, di conseguenza, consiste nel suddividere le registrazioni e prepararle per l'invio. Il tecnico di registrazione potrebbe aver posizionato i marcatori nel file (oppure aver fornito un foglio separato) per indicare dove inizia ogni espressione.

Usare le note per trovare i take esatti desiderati, quindi usare un'utilità di modifica del suono, ad esempio [Avid Pro Tools](http://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html) o l'[Audacity](https://www.audacityteam.org/) gratuita per copiare ogni espressione in un nuovo file.

Lasciare solo circa 0,2 secondi di silenzio all'inizio e alla fine di ogni clip, ad eccezione del primo. Tale file deve iniziare con cinque secondi completi di silenzio. Non usare l'editor audio per azzerare le parti silenziose del file. L'inclusione del rumore di fondo della stanza consente agli algoritmi per la voce personalizzata di compensare eventuali rumori di sfondo residui.

Ascoltare attentamente ogni file. In questa fase, è possibile modificare i disturbi meno evidenti indesiderati non rilevati durante la registrazione, ad esempio un lieve rumore delle labbra prima di una riga, ma prestare attenzione a rimuovere alcuna parola effettiva. Se non è possibile correggere un file, rimuoverlo dal set di dati, prendendo nota dell'operazione eseguita.

Convertire ogni file a 16 bit e a una frequenza di campionamento di 16 KHz prima del salvataggio e, se sono state registrate conversazioni in studio, rimuovere il secondo canale. Salvare ogni file in formato WAV, denominando i file con il numero di espressione presente nello script.

Creare infine la *trascrizione* che associa ogni file con estensione wav con la versione in formato testo dell'espressione corrispondente. In [Creazione di caratteri voce personalizzati](how-to-customize-voice-font.md) sono incluse informazioni dettagliate sul formato necessario. È possibile copiare il testo direttamente dallo script. Creare quindi un file ZIP dei file WAV e la trascrizione del testo.

Archiviare le registrazioni originali in un luogo sicuro nel caso in cui siano necessarie in un secondo momento. Conservare anche lo script e le note.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è possibile caricare le registrazioni e creare la voce personalizzata.

> [!div class="nextstepaction"]
> [Creare caratteri voce personalizzati](how-to-customize-voice-font.md)
