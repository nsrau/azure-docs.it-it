---
title: Assistenti vocali per le linee guida di progettazione di Windows
titleSuffix: Azure Cognitive Services
description: Linee guida per le procedure consigliate per la progettazione di un'esperienza agente vocale.
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: 948fbcd57514f4ef77483b05c60324e867e0e3ed
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89293643"
---
# <a name="design-assistant-experiences-for-windows-10"></a>Esperienze di assistente progettazione per Windows 10

Gli assistenti vocali sviluppati in Windows 10 devono implementare le linee guida per l'esperienza utente seguenti per fornire le migliori esperienze possibili per l'attivazione vocale in Windows 10. Questo documento consente agli sviluppatori di comprendere il lavoro fondamentale necessario per l'integrazione di un assistente vocale con la shell di Windows 10.

## <a name="contents"></a>Contenuto

- [Riepilogo delle visualizzazioni di attivazione vocale supportate in Windows 10](#summary-of-voice-activation-views-supported-in-windows-10)
- [Riepilogo requisiti](#requirements-summary)
- [Procedure consigliate per esperienze di ascolto ottimali](#best-practices-for-good-listening-experiences)
- [Linee guida di progettazione per l'attivazione vocale in-app](#design-guidance-for-in-app-voice-activation)
- [Linee guida di progettazione per l'attivazione vocale sopra il blocco](#design-guidance-for-voice-activation-above-lock)
- [Linee guida di progettazione per l'attivazione vocale anteprima](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Riepilogo delle visualizzazioni di attivazione vocale supportate in Windows 10

Windows 10 deduce un'esperienza di attivazione per il contesto del cliente in base al contesto del dispositivo. La tabella di riepilogo seguente è una panoramica di alto livello delle diverse visualizzazioni disponibili quando la schermata è attiva.

| Visualizzazione (disponibilità) | Contesto di dispositivo | Obiettivo del cliente | Viene visualizzato quando | Esigenze di progettazione |
| --- | --- | --- | --- | --- |
| **In-app (19H1)** | Al di sotto del blocco, assistente ha lo stato attivo | Interagire con l'app Assistant | Assistente elabora la richiesta in-app | Esperienza di ascolto della visualizzazione principale in-app |
| **Blocco precedente (19H2)** | Blocco precedente, non autenticato | Interagire con l'assistente, ma da una distanza | Il sistema è bloccato e richiede l'attivazione dell'assistente | Oggetti visivi a schermo intero per l'interfaccia utente di campo lontano. Implementare i criteri di dismissione per non bloccare lo sblocco. |
| **Anteprima attivazione voce (20H1)** | Al di sotto del blocco, l'assistente non ha lo stato attivo | Interagisci con l'assistente, ma in modo meno intrusivo | Il sistema è sotto l'attivazione in background delle richieste di blocco e assistente | Area di disegno minima. Ridimensionare o passare alla visualizzazione principale dell'app in base alle esigenze. |

## <a name="requirements-summary"></a>Riepilogo requisiti

Per accedere alle diverse esperienze è necessario un impegno minimo. Tuttavia, gli assistenti devono implementare le linee guida di progettazione appropriate per ogni visualizzazione. Nella tabella seguente viene fornito un elenco di controllo dei requisiti che devono essere seguiti.

| **Visualizzazione attivazione voce** | **Riepilogo requisiti assistente** |
| --- | --- |
| **In-app** | <ul><li>Elaborare la richiesta in-app</li><li>Fornisce indicatori dell'interfaccia utente per gli Stati di ascolto</li><li>L'interfaccia utente si adatta alla modifica delle dimensioni della finestra</li></ul> |
| **Blocco precedente** | <ul><li>Rilevare lo stato di blocco e richiedere l'attivazione</li><li>Non fornire un'esperienza utente sempre persistente che blocca l'accesso alla schermata di blocco di Windows</li><li>Fornire oggetti visivi a schermo intero e un'esperienza vocale</li><li>Rispetta le indicazioni di chiusura seguenti</li><li>Segui le considerazioni sulla privacy e sulla sicurezza</li></ul> |
| **Anteprima attivazione vocale** | <ul><li>Rilevare lo stato di sblocco e richiedere l'attivazione in background</li><li>Creare un'esperienza utente di ascolto minima nel riquadro di anteprima</li><li>Crea una X di chiusura in alto a destra e ignora e Interrompi lo streaming audio quando premuto</li><li>Ridimensionare o passare alla visualizzazione dell'app Assistant principale in base alle esigenze per fornire risposte</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>Procedure consigliate per esperienze di ascolto ottimali

Gli assistenti devono creare un'esperienza di ascolto per fornire un feedback critico, in modo che il cliente possa comprendere lo stato dell'assistente. Di seguito sono riportati alcuni stati possibili da considerare quando si crea un'esperienza di assistente. Si tratta solo di suggerimenti possibili, non di linee guida obbligatorie.

- Assistant è disponibile per l'input vocale
- È in corso l'attivazione dell'assistente (premere una parola chiave o un pulsante mic)
- Assistente che esegue attivamente lo streaming di audio nel cloud Assistant
- Assistant è pronto per il cliente per iniziare a parlare
- L'assistente ha udito che parole
- Assistenti comprende che il cliente ha finito di parlare
- Assistente che elabora e prepara una risposta
- Assistente risponde

Anche se gli Stati cambiano rapidamente, è opportuno considerare la possibilità di fornire l'esperienza utente per gli Stati, poiché le durate sono variabili nell'ecosistema Windows. Il feedback visivo, nonché brevi Campanelli audio o cinguetti, denominati anche &quot; earcons &quot; , possono far parte della soluzione. Analogamente, le schede visive abbinate alle descrizioni audio rendono valide le opzioni di risposta.

## <a name="design-guidance-for-in-app-voice-activation"></a>Linee guida di progettazione per l'attivazione vocale in-app

Quando l'app Assistant ha lo stato attivo, la finalità del cliente è chiaramente interagire con l'app, quindi tutte le esperienze di attivazione vocale devono essere gestite dalla visualizzazione principale dell'app. Questa vista può essere ridimensionata dal cliente. Per semplificare la spiegazione delle interazioni della shell degli assistenti, nella parte restante di questo documento viene usato l'esempio concreto di assistente di servizi finanziari denominato contoso. In questo e nei diagrammi successivi, ciò che il cliente dice verrà visualizzato nelle bolle di riconoscimento vocale dei cartoni a sinistra con le risposte degli assistenti nelle bolle dei cartoni a destra.

**Visualizzazione in-app. Stato iniziale quando viene avviata l'attivazione vocale:** 
 ![ screenshot dell'Assistente vocale in Windows prima dell'attivazione](media/voice-assistants/windows_voice_assistant/initial_state.png)

**Visualizzazione in-app. Al termine dell'attivazione vocale, viene avviata l'esperienza di ascolto:** ![ screenshot dell'Assistente vocale in Windows mentre l'Assistente vocale è in ascolto](media/voice-assistants/windows_voice_assistant/listening.png)

**Visualizzazione in-app. Tutte le risposte rimangono nell'esperienza dell'app.** ![ Screenshot dell'Assistente vocale in Windows come risposte dell'assistente](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>Linee guida di progettazione per l'attivazione vocale sopra il blocco

Disponibile con 19H2, gli assistenti basati sulla piattaforma di attivazione di Windows Voice sono disponibili per rispondere al blocco.

### <a name="customer-opt-in"></a>Consenso esplicito per il cliente

L'attivazione vocale sopra il blocco è sempre disabilitata per impostazione predefinita. I clienti scelgono di usare le impostazioni di Windows>privacy>attivazione vocale. Per informazioni dettagliate sul monitoraggio e la richiesta di questa impostazione, vedere la [Guida all'implementazione del blocco precedente](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference).

### <a name="not-a-lock-screen-replacement"></a>Sostituzione della schermata di blocco

Sebbene le notifiche o altri punti di integrazione dello schermo di blocco delle app standard rimangano disponibili per l'assistente, la schermata di blocco di Windows definisce sempre l'esperienza iniziale del cliente fino a quando non si verifica un'attivazione vocale. Quando viene rilevata l'attivazione vocale, l'app Assistant viene visualizzata temporaneamente sopra la schermata di blocco. Per evitare confusione con i clienti, quando si attiva il blocco, l'app Assistant non deve mai presentare l'interfaccia utente per richiedere qualsiasi tipo di credenziali o eseguire l'accesso.

![Screenshot di una schermata di blocco di Windows](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>Sopra l'esperienza di blocco dopo l'attivazione vocale

Quando la schermata è attiva, l'app Assistant è a schermo intero senza barra del titolo sopra la schermata di blocco. Gli oggetti visivi più grandi e le descrizioni forti con l'interfaccia primaria avanzata consentono i casi in cui il cliente è troppo lontano per leggere l'interfaccia utente o è occupato da un'altra attività (non PC).

Quando la schermata rimane spenta, l'app Assistant potrebbe riprodurre un earcon per indicare che l'Assistente viene attivato e fornisce un'esperienza di sola voce.

![Screenshot dell'Assistente vocale sopra il blocco](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>Criteri di chiusura

Per consentire ai clienti di accedere in modo più semplice alla prossima volta che desiderano utilizzare il PC Windows, l'assistente deve implementare le indicazioni in questa sezione. Di seguito sono elencati i requisiti specifici che devono essere implementati dall'assistente:

- **Tutte le Canvas degli assistenti che mostrano il blocco precedente devono contenere una X** nella parte superiore destra che respinge l'assistente.
- **Premendo un tasto qualsiasi è necessario chiudere anche l'app Assistant**. L'input da tastiera è un segnale tradizionale dell'app lock che il cliente vuole accedere. Pertanto, qualsiasi input di tastiera/testo non deve essere indirizzato all'app. Al contrario, l'app deve essere autoignorata quando viene rilevato un input da tastiera, in modo che il cliente possa accedere facilmente al dispositivo.
- **Se lo schermo si spegne, l'app deve essere autoignorata.** In questo modo si garantisce che alla successiva utilizzo del PC da parte del cliente, la schermata di accesso sarà pronta e in attesa.
- Se l'app è &quot; in uso &quot; , può continuare con il blocco. &quot;in uso &quot; costituisce qualsiasi input o output. Ad esempio, quando si esegue il flusso di musica o video, l'app può continuare sopra il blocco. &quot;In &quot; e in altri passaggi della finestra di dialogo multigiro è consentito il blocco dell'app.
- I **Dettagli di implementazione sulla chiusura dell'applicazione** sono disponibili [nella Guida all'implementazione del blocco precedente](windows-voice-assistants-implementation-guide.md#closing-the-application).

![Screenshot dell'Assistente vocale in Windows prima dell'attivazione](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![Screenshot dell'Assistente vocale in Windows prima dell'attivazione](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>Considerazioni sulla sicurezza della privacy &amp; sopra il blocco

Molti PC sono portabili ma non sempre entro la portata del cliente. Possono essere brevemente lasciati nelle sale di Hotel, nelle postazioni aeree o nelle aree di lavoro, in cui altri utenti hanno accesso fisico. Se gli assistenti abilitati sopra il blocco non sono preparati, possono diventare soggetti alla classe di attacchi di &quot; [cattiva cameriera](https://en.wikipedia.org/wiki/Evil_maid_attack) &quot; .

Gli assistenti devono quindi seguire le istruzioni di questa sezione per garantire la protezione dell'esperienza. L'interazione sopra il blocco si verifica quando l'utente di Windows non è autenticato. Ciò significa che, in generale, **l'input per l'assistente deve essere considerato come non autenticato**.

- Gli assistenti devono **implementare un elenco di competenze consentite per identificare le competenze che vengono confermate sicure e sicure per l'** accesso al blocco.
- Le tecnologie di ID altoparlante possono svolgere un ruolo per alleviare alcuni rischi, ma l'ID altoparlante non è un sostituto adatto per l'autenticazione di Windows.
- L'elenco delle competenze consentite deve considerare tre classi di azioni o competenze:

| **Classe Action** | **Descrizione** | **Esempi (non è un elenco completo)** |
| --- | --- | --- |
| Sicuro senza autenticazione | Informazioni generali o comando e controllo dell'app di base | &quot;Qual è il tempo? &quot; , &quot; riprodurre la traccia successiva&quot; |
| Sicuro con ID altoparlante | Rischio di rappresentazione, che rivela informazioni personali. | &quot;Quali sono&#39;prossimo appuntamento? &quot; , &quot; rivedere l'elenco degli acquisti &quot; , &quot; rispondere alla chiamata&quot; |
| Sicuro solo dopo l'autenticazione di Windows | Azioni ad alto rischio che possono essere usate da un utente malintenzionato per danneggiare il cliente | &quot;Acquistare altre drogherie &quot; , &quot; eliminare l'appuntamento (importante) &quot; , &quot; inviare un messaggio di testo (Mean) &quot; , &quot; avviare una pagina Web (nefasto)&quot; |

Per il caso di Contoso, le informazioni generali relative alle scorte pubbliche sono sicure senza autenticazione. È probabile che le informazioni specifiche del cliente, ad esempio il numero di condivisioni possedute, siano sicure con ID altoparlante. Tuttavia, l'acquisto o la vendita di scorte non dovrebbe mai essere consentito senza l'autenticazione di Windows.

Per proteggere ulteriormente l'esperienza, i **collegamenti weblink o altri lanci da app a app verranno sempre bloccati da Windows fino a quando il cliente non effettuerà l'accesso.** Come ultima soluzione per la mitigazione, Microsoft si riserva il diritto di rimuovere un'applicazione dall'elenco degli assistenti abilitati in caso di problemi di sicurezza che non vengono risolti in modo tempestivo.

## <a name="design-guidance-for-voice-activation-preview"></a>Linee guida di progettazione per l'attivazione vocale anteprima

Al di sotto del blocco, quando l'app Assistant _non ha lo stato attivo_ , Windows fornisce un'interfaccia utente per l'attivazione vocale meno intrusiva che consente di far fluire il cliente. Ciò vale soprattutto per il caso di false attivazioni che potrebbero essere estremamente problematiche se hanno avviato l'intera applicazione. Il concetto di base è che ogni assistente ha un'altra casa nella shell, l'icona della barra delle applicazioni dell'assistente. Quando si verifica la richiesta di attivazione in background, viene visualizzata una piccola visualizzazione sopra l'icona della barra delle applicazioni dell'assistente. Gli assistenti dovrebbero fornire un'esperienza di ascolto ridotta in questa area di disegno. Al termine dell'elaborazione delle richieste, gli assistenti possono scegliere di ridimensionare questa visualizzazione per mostrare una risposta nel contesto o di passare dalla visualizzazione principale dell'app per mostrare oggetti visivi più grandi e dettagliati.

- Per rimanere minimo, l'anteprima non dispone di una barra del titolo, quindi **l'assistente deve creare una X in alto a destra per consentire ai clienti di ignorare la visualizzazione.** Vedere [chiusura dell'applicazione](windows-voice-assistants-implementation-guide.md#closing-the-application) per le API specifiche da chiamare quando viene premuto il pulsante Ignora.
- Per supportare le anteprime per l'attivazione vocale, gli assistenti possono invitare i clienti a aggiungere l'assistente alla barra delle applicazioni durante la prima esecuzione.

**Anteprima attivazione vocale: stato iniziale**

Contoso Assistant ha una Home page sulla barra delle applicazioni: la relativa icona circolare vorticosa.

![Screenshot dell'Assistente vocale in Windows come pre-attivazione dell'icona della barra delle applicazioni](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

**Con l'avanzamento dell'attivazione**, l'assistente richiede l'attivazione in background. All'assistente viene assegnato un piccolo riquadro di anteprima (larghezza predefinita 408 e altezza: 248). Se l'attivazione vocale sul lato server determina che il segnale è un falso positivo, questa visualizzazione potrebbe essere annullata per un'interruzione minima.

![Screenshot dell'Assistente vocale in Windows nella visualizzazione compatta durante la verifica dell'attivazione](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

**Quando viene confermata l'attivazione finale**, l'assistente presenta il proprio UX di ascolto. L'assistente deve sempre creare una X Ignora nella parte superiore destra dell'anteprima di attivazione vocale.

![Screenshot dell'Assistente vocale in Windows in attesa in Compact View](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

Le **risposte rapide** possono essere visualizzate nell'anteprima dell'attivazione vocale. Un TryResizeView consentirà agli assistenti di richiedere dimensioni diverse.

![Screenshot dell'Assistente vocale in Windows per la risposta in Compact View](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**Hand-off**. In qualsiasi momento, l'assistente può passare alla visualizzazione principale dell'app per fornire altre informazioni, dialoghi o risposte che richiedono una maggiore quantità di spazio sullo schermo. Per informazioni dettagliate sull'implementazione, vedere la sezione relativa alla [transizione dalla vista compatta alla visualizzazione completa](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view) .

![Screenshot di Assistente vocale in Windows prima e dopo l'espansione della vista compatta](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione allo sviluppo di un assistente vocale](how-to-windows-voice-assistants-get-started.md)