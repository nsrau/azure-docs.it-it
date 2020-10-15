---
title: Assistente vocale in Windows-Panoramica
titleSuffix: Azure Cognitive Services
description: Panoramica degli assistenti vocali in Windows, incluse le funzionalità e le risorse di sviluppo disponibili.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: fbcb262fee6a2cc62bfe64e8a8589c92b4fe2b17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82997447"
---
# <a name="voice-assistants-on-windows"></a>Assistenti vocali in Windows

In Windows 10 versione 2004 e versioni più dettagliate, le applicazioni Voice Assistant possono sfruttare le API di Windows ConversationalAgent per ottenere un'esperienza di assistente completa abilitata per la voce.

## <a name="voice-assistant-features"></a>Funzionalità di Assistente vocale

Le applicazioni dell'agente vocale possono essere attivate da una parola chiave pronunciata per abilitare un'esperienza senza problemi e basata su voce. L'attivazione vocale funziona quando l'applicazione viene chiusa e quando lo schermo è bloccato.

Inoltre, in Windows è disponibile un set di impostazioni di privacy per l'attivazione vocale che consente agli utenti di controllare l'attivazione vocale e l'attivazione del blocco in base alle singole app.

Dopo l'attivazione vocale, Windows gestirà più agenti attivi in modo corretto e invierà una notifica a ogni Assistente vocale se vengono interrotti o disattivati. Ciò consente alle applicazioni di gestire correttamente le interruzioni e altri eventi tra agenti.

## <a name="how-does-voice-activation-work"></a>Come funziona l'attivazione vocale?

Agent Activation Runtime (AAR) è il processo continuo in Windows che gestisce l'attivazione dell'applicazione su una parola chiave o un pulsante premuto. Inizia con Windows purché sia presente almeno un'applicazione nel sistema registrata con il sistema. Le applicazioni interagiscono con AAR tramite le API ConversationalAgent nel Windows SDK.

Quando l'utente parla di una parola chiave, la parola chiave software o hardware spotter sul sistema informa AAR che è stata rilevata una parola chiave, fornendo un ID parola chiave. AAR Invia a sua volta una richiesta a BackgroundService per avviare l'applicazione con l'ID applicazione corrispondente.

### <a name="registration"></a>Registrazione

La prima volta che viene eseguita un'applicazione attivata tramite voce, registra le informazioni relative all'ID app e alla parola chiave tramite le API ConversationalAgent. AAR registra tutte le configurazioni nel mapping globale con la parola chiave hardware o software Spotter nel sistema, consentendo loro di rilevare la parola chiave dell'applicazione. L'applicazione viene inoltre [registrata con il servizio in background](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task).

Si noti che questo significa che un'applicazione non può essere attivata da Voice fino a quando non è stata eseguita una volta e che è stato consentito il completamento della registrazione.

### <a name="receiving-an-activation"></a>Ricezione di un'attivazione

Quando riceve la richiesta da AAR, il servizio in background avvia l'applicazione. L'applicazione riceve un segnale tramite il metodo del ciclo di vita OnBackgroundActivated in `App.xaml.cs` con un argomento di evento univoco. Questo argomento indica all'applicazione che è stata attivata da AAR e che deve avviare la verifica delle parole chiave.

Se la parola chiave viene verificata correttamente, l'applicazione può eseguire una richiesta di visualizzazione in primo piano. Quando la richiesta ha esito positivo, l'applicazione Visualizza l'interfaccia utente e continua l'interazione con l'utente.

AAR segnala ancora le applicazioni attive quando la parola chiave viene pronunciata. Anziché segnalare il metodo del ciclo di vita in `App.xaml.cs` , tuttavia, segnala un evento nelle API ConversationalAgent.

### <a name="keyword-verification"></a>Verifica parole chiave

La parola chiave spotter che attiva l'avvio dell'applicazione ha raggiunto un basso consumo di energia semplificando il modello di parola chiave. In questo modo, la parola chiave spotter può essere "always on" senza un elevato effetto di alimentazione, ma significa anche che la parola chiave spotter avrà probabilmente un numero elevato di "false accepts", in cui rileverà una parola chiave anche se non è stata pronunciata alcuna parola chiave. Questo è il motivo per cui il sistema di attivazione vocale avvia l'applicazione in background: per dare all'applicazione la possibilità di verificare che la parola chiave sia stata pronunciata prima di interrompere la sessione corrente dell'utente. AAR Salva l'audio da pochi secondi prima che la parola chiave sia stata individuata e la rende accessibile all'applicazione. L'applicazione può usare questa funzione per eseguire un spotter di parole chiave più affidabile sullo stesso audio.

## <a name="next-steps"></a>Passaggi successivi

- **Esaminare le linee guida per la progettazione:** Le [linee guida di progettazione](windows-voice-assistants-best-practices.md) definiscono il lavoro fondamentale necessario per fornire le migliori esperienze possibili per l'attivazione vocale in Windows 10.
- **Visitare la pagina Introduzione:** Iniziare da [qui](how-to-windows-voice-assistants-get-started.md) per i passaggi per iniziare a implementare gli assistenti vocali in Windows, dall'impostazione dell'ambiente di sviluppo tramite una guida introduttiva all'implementazione.
- **Provare l'app di esempio**: per sperimentare queste funzionalità in prima persona, visitare la pagina di [esempio UWP Voice Assistant](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) e seguire i passaggi per eseguire il client di esempio.
