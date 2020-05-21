---
title: Assistenti vocali su Windows-sopra le linee guida per l'implementazione del blocco
titleSuffix: Azure Cognitive Services
description: Istruzioni per implementare l'attivazione vocale e le funzionalità di blocco superiore per un'applicazione dell'agente vocale.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 726dd4e18565174c8bbf49b204af64129e607db5
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714730"
---
# <a name="implementing-voice-assistants-on-windows"></a>Implementazione di Assistente vocale in Windows

Questa guida illustra i dettagli di implementazione importanti per la creazione di un assistente vocale in Windows.

## <a name="implementing-voice-activation"></a>Implementazione dell'attivazione vocale

Dopo aver configurato [l'ambiente](how-to-windows-voice-assistants-get-started.md) e aver appreso [come funziona l'attivazione vocale](windows-voice-assistants-overview.md#how-does-voice-activation-work), è possibile iniziare a implementare l'attivazione vocale per la propria applicazione di Assistente vocale.

### <a name="registration"></a>Registrazione

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>Verificare che il microfono sia disponibile e accessibile, quindi monitorarne lo stato

MVA richiede che un microfono sia presente e accessibile per poter rilevare un'attivazione vocale. Usare le classi [AppCapability](https://docs.microsoft.com/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability?view=winrt-18362), [DeviceWatcher](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher?view=winrt-18362)e [MediaCapture](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture?view=winrt-18362) per verificare rispettivamente l'accesso alla privacy del microfono, la presenza del dispositivo e lo stato del dispositivo (ad esempio volume e mute).

### <a name="register-the-application-with-the-background-service"></a>Registrare l'applicazione con il servizio in background

Per consentire a MVA di avviare l'applicazione in background, l'applicazione deve essere registrata con il servizio in background. Vedere una guida completa per la registrazione del servizio in background [qui](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task).

### <a name="unlock-the-limited-access-feature"></a>Sbloccare la funzionalità di accesso limitato

Usare la chiave della funzionalità di accesso limitato fornita da Microsoft per sbloccare la funzionalità di Assistente vocale. Per eseguire questa operazione, usare la classe [LimitedAccessFeature](https://docs.microsoft.com/uwp/api/windows.applicationmodel.limitedaccessfeatures?view=winrt-18362) dalla Windows SDK.

### <a name="register-the-keyword-for-the-application"></a>Registrare la parola chiave per l'applicazione

L'applicazione deve registrarsi, il modello di parole chiave e il relativo linguaggio con Windows.

Iniziare recuperando la parola chiave detector. In questo esempio di codice viene recuperato il primo rilevatore, ma è possibile selezionare un determinato rilevatore selezionandola da `configurableDetectors` .

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

Dopo aver recuperato l'oggetto ActivationSignalDetector, chiamare il relativo `ActivationSignalDetector.CreateConfigurationAsync` metodo con l'ID del segnale, l'ID del modello e il nome visualizzato per registrare la parola chiave e recuperare l'applicazione `ActivationSignalDetectionConfiguration` . Gli ID del segnale e del modello devono essere GUID decisi dallo sviluppatore e rimanere coerenti per la stessa parola chiave.

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>Verificare che l'impostazione di attivazione vocale sia abilitata

Per usare l'attivazione vocale, un utente deve abilitare l'attivazione vocale per il sistema e abilitare l'attivazione vocale per la propria applicazione. È possibile trovare l'impostazione in "impostazioni di privacy per l'attivazione vocale" in impostazioni di Windows. Per controllare lo stato dell'impostazione di attivazione vocale nell'applicazione, usare l'istanza di `ActivationSignalDetectionConfiguration` dalla registrazione della parola chiave. Il campo [AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) nell'oggetto `ActivationSignalDetectionConfiguration` contiene un valore enum che descrive lo stato dell'impostazione di attivazione vocale.

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>Recuperare un ConversationalAgentSession per registrare l'app con il sistema MVA

`ConversationalAgentSession`È una classe del Windows SDK che consente all'app di aggiornare Windows con lo stato dell'app (inattivo, rilevamento, ascolto, utilizzo, conversazione) e ricezione di eventi, ad esempio il rilevamento dell'attivazione e le modifiche dello stato del sistema, ad esempio il blocco dello schermo. Il recupero di un'istanza di AgentSession serve anche per registrare l'applicazione con Windows come attivabile tramite Voice. È consigliabile mantenere un riferimento a `ConversationalAgentSession` . Per recuperare la sessione, usare l' `ConversationalAgentSession.GetCurrentSessionAsync` API.

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>Ascolto dei due segnali di attivazione: OnBackgroundActivated e OnSignalDetected

Windows segnalerà l'app quando rileverà una parola chiave in uno dei due modi. Se l'app non è attiva (ovvero, non si dispone di un riferimento a un'istanza non eliminata di `ConversationalAgentSession` ), l'app verrà avviata e verrà chiamato il metodo OnBackgroundActivated nel file app.XAML.cs dell'applicazione. Se il campo degli argomenti dell'evento `BackgroundActivatedEventArgs.TaskInstance.Task.Name` corrisponde alla stringa "AgentBackgroundTrigger", l'avvio dell'applicazione è stato attivato dall'attivazione vocale. L'applicazione deve eseguire l'override di questo metodo e recuperare un'istanza di ConversationalAgentSession per segnalare a Windows che è ora attiva. Quando l'applicazione è attiva, Windows segnalerà l'occorrenza di un'attivazione vocale usando l' `ConversationalAgentSession.OnSignalDetected` evento. Aggiungere un gestore eventi all'evento non appena viene recuperato `ConversationalAgentSession` .

## <a name="keyword-verification"></a>Verifica parole chiave

Quando un'applicazione dell'agente vocale viene attivata tramite voce, il passaggio successivo consiste nel verificare che il rilevamento delle parole chiave sia valido. Windows non fornisce una soluzione per la verifica delle parole chiave, ma consente agli assistenti vocali di accedere all'audio dall'attivazione ipotizzata e di completare la verifica autonomamente.

### <a name="retrieve-activation-audio"></a>Recuperare l'audio di attivazione

Creare un [AudioGraph](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph) e passarlo a `CreateAudioDeviceInputNodeAsync` del `ConversationalAgentSession` . Il buffer audio del grafo verrà caricato con l'audio *a partire da circa 3 secondi prima che la parola chiave sia stata rilevata*. Questo audio leader aggiuntivo è incluso per supportare un'ampia gamma di lunghezze delle parole chiave e velocità del parlante. Quindi, gestire l'evento [QuantumStarted](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph.quantumstarted?view=winrt-18362) dal grafico audio per recuperare i dati audio.

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

Nota: l'audio principale incluso nel buffer audio può causare un errore di verifica delle parole chiave. Per risolvere il problema, tagliare l'inizio del buffer audio prima di inviare l'audio per la verifica della parola chiave. Questo trim iniziale deve essere personalizzato per ogni assistente.

### <a name="launch-in-the-foreground"></a>Avvia in primo piano

Quando la verifica delle parole chiave ha esito positivo, l'applicazione deve spostarsi in primo piano per visualizzare l'interfaccia utente. Chiamare l' `ConversationalAgentSession.RequestForegroundActivationAsync` API per spostare l'applicazione in primo piano.

### <a name="transition-from-compact-view-to-full-view"></a>Transizione dalla vista compatta alla visualizzazione completa

Quando l'applicazione viene attivata per la prima volta da Voice, viene avviata in una vista compatta. Leggere le [linee guida di progettazione per l'anteprima dell'attivazione vocale](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) per istruzioni sulle diverse visualizzazioni e transizioni tra di essi per gli assistenti vocali in Windows.

Per passare dalla visualizzazione compatta alla visualizzazione completa dell'app, usare l'API ApplicationView `TryEnterViewModeAsync` :

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>Implementazione dell'attivazione del blocco precedente

I passaggi seguenti illustrano i requisiti per abilitare un assistente vocale in Windows per eseguire il blocco precedente, inclusi i riferimenti a codice di esempio e linee guida per la gestione del ciclo di vita dell'applicazione.

Per indicazioni sulla progettazione di precedenti esperienze di blocco, vedere la [Guida alle procedure consigliate](windows-voice-assistants-best-practices.md).

Quando un'app Visualizza un blocco di visualizzazione, viene considerato in modalità tutto schermo. Per altre informazioni sull'implementazione di un'app che usa la modalità tutto schermo, vedere la [documentazione relativa alla modalità](https://docs.microsoft.com/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access)tutto schermo.

### <a name="transitioning-above-lock"></a>Transizione sopra il blocco

Un blocco di attivazione precedente è simile a un blocco di attivazione. Se non sono presenti istanze attive dell'applicazione, verrà avviata una nuova istanza in background e `OnBackgroundActivated` verrà chiamato app.XAML.cs. Se è presente un'istanza dell'applicazione, l'istanza riceverà una notifica tramite l' `ConversationalAgentSession.SignalDetected` evento.

Se l'applicazione non ha già visualizzato il blocco precedente, deve chiamare `ConversationalAgentSession.RequestForegroundActivationAsync` . Viene attivato il `OnLaunched` metodo in app.XAML.cs che dovrebbe passare alla visualizzazione che verrà visualizzata sopra il blocco.

### <a name="detecting-lock-screen-transitions"></a>Rilevamento delle transizioni della schermata di blocco

La libreria ConversationalAgent nel Windows SDK fornisce un'API per rendere lo stato della schermata di blocco e le modifiche apportate allo stato della schermata di blocco facilmente accessibili. Per rilevare lo stato corrente della schermata di blocco, controllare il `ConversationalAgentSession.IsUserAuthenticated` campo. Per rilevare le modifiche nello stato di blocco, aggiungere un gestore eventi all' `ConversationalAgentSession` evento dell'oggetto `SystemStateChanged` . Viene attivato ogni volta che lo schermo cambia da sbloccato a bloccato o viceversa. Se il valore degli argomenti dell'evento è `ConversationalAgentSystemStateChangeType.UserAuthentication` , lo stato della schermata di blocco è stato modificato.

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>Rilevamento preferenza utente di attivazione blocco precedente

La voce dell'applicazione nella pagina delle impostazioni di privacy per l'attivazione vocale dispone di un interruttore per la funzionalità di blocco sopra indicata. Per consentire all'app di avviare il blocco, l'utente dovrà attivare questa impostazione. Lo stato delle autorizzazioni di blocco precedente viene inoltre archiviato nell'oggetto ActivationSignalDetectionConfiguration. Lo stato AvailabilityInfo. HasLockScreenPermission indica se l'utente ha specificato le autorizzazioni di blocco sopra indicate. Se questa impostazione è disabilitata, un'applicazione vocale può richiedere all'utente di passare alla pagina delle impostazioni appropriate nel collegamento "MS-Settings: privacy-voiceactivation" con le istruzioni su come abilitare l'attivazione del blocco precedente per l'applicazione.

## <a name="closing-the-application"></a>Chiusura dell'applicazione

Per chiudere correttamente l'applicazione a livello di codice, al di sopra o al di sotto del blocco, usare l' `WindowService.CloseWindow()` API. Vengono attivati tutti i metodi del ciclo di vita UWP, incluso onsuspend, che consente all'applicazione di eliminare l' `ConversationalAgentSession` istanza prima della chiusura.

> [!NOTE]
> L'applicazione può essere chiusa senza chiudere l' [istanza di blocco seguente](https://docs.microsoft.com/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-). In questo caso, la visualizzazione di blocco precedente deve essere "pulita", assicurandosi che una volta sbloccata la schermata non siano presenti gestori eventi o attività che tenteranno di modificare la visualizzazione di blocco sopra indicata.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Vedere l'app di esempio UWP Voice Assistant per esempi e procedure dettagliate di codice](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
