---
title: Linee guida sulla privacy per gli assistenti vocali in Windows
titleSuffix: Azure Cognitive Services
description: Istruzioni per abilitare l'attivazione vocale per un agente vocale per impostazione predefinita.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 436367ede4f4be323b5334a201b1c9fb8f7f28e8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997513"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Linee guida sulla privacy per gli assistenti vocali in Windows

È importante che gli utenti dispongano di informazioni chiare sul modo in cui i dati vocali vengono raccolti e usati e che siano in grado di controllare se e come si verifica questa raccolta. Questi aspetti fondamentali della privacy, *divulgazione* e *consenso* , sono particolarmente importanti per gli assistenti vocali integrati in Windows in base alla natura sempre in ascolto del loro utilizzo.

Gli sviluppatori che creano Assistente vocale in Windows devono includere elementi di interfaccia utente cancellati all'interno delle applicazioni che riflettono le funzionalità di ascolto dell'assistente.

> [!NOTE]
> L'impossibilità di fornire la divulgazione e il consenso appropriati per un'applicazione Assistant, incluso dopo gli aggiornamenti dell'applicazione, può comportare l'indisponibilità dell'assistente per l'attivazione vocale fino alla risoluzione dei problemi di privacy. 

## <a name="minimum-requirements-for-feature-inclusion"></a>Requisiti minimi per l'inclusione delle funzionalità

Gli utenti di Windows possono visualizzare e controllare la disponibilità delle applicazioni Assistant **`Settings > Privacy > Voice activation`** in.

 > [!div class="mx-imgBorder"]
 > [![privacy-app-Listing](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Voce relativa all'impostazione della privacy per l'attivazione vocale di Windows per un'applicazione Assistant")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Per diventare idoneo per l'inclusione in questo elenco, un'applicazione deve:

1. Informare in modo prominente gli utenti che resterà in ascolto di una parola chiave, anche quando l'applicazione non è in esecuzione e qual è la parola chiave
1. Includere una descrizione della modalità di utilizzo dei dati vocali di un utente, incluso un collegamento o un riferimento a criteri di privacy pertinenti
1. Informare gli utenti che, oltre alle impostazioni in-app, gli utenti possono visualizzare e modificare le opzioni di privacy **`Settings > Privacy > Voice activation`** in, includendo facoltativamente un collegamento `ms-settings:privacy-voiceactivation` al protocollo per l'accesso diretto

Dopo aver soddisfatto questi requisiti e aver ottenuto l'approvazione da Microsoft, un'applicazione Assistant verrà visualizzata nell'elenco delle app per l'attivazione vocale dopo `Windows.ApplicationModel.ConversationalAgent` che è stata registrata con le API e gli utenti saranno in grado di concedere il consenso all'applicazione per l'attivazione delle parole chiave. Per impostazione predefinita, entrambe le impostazioni sono `Off` e richiedono che l'utente visiti manualmente la pagina delle impostazioni per abilitarla.

> [!NOTE]
> In tutti i casi, l'autorizzazione per l'attivazione vocale richiede l'autorizzazione del microfono. Se un'applicazione Assistant non dispone dell'accesso al microfono, non sarà idonea per l'attivazione vocale e verrà visualizzata nelle impostazioni relative alla privacy per l'attivazione vocale in uno stato disabilitato.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Requisiti aggiuntivi per l'inclusione del consenso del microfono

Gli autori degli assistenti che desiderano semplificare e rendere più semplice per gli utenti la scelta esplicita per l'attivazione vocale possono eseguire questa operazione rispettando alcuni requisiti aggiuntivi rispetto a quelli descritti in precedenza. Una volta soddisfatte queste impostazioni, l'opzione di attivazione vocale standard e sbloccata per il dispositivo `On` di un'applicazione Assistant verrà impostata su una sola volta (e una sola volta) l'accesso al microfono viene concesso all'applicazione. In questo modo viene eliminata la necessità di un ulteriore viaggio per le impostazioni prima dell'attivazione vocale di un assistente.

I requisiti aggiuntivi sono che un'applicazione Assistant deve:

1. **Prima** di richiedere il consenso del microfono (ad esempio, usando `AppCapability.RequestAccessAsync` l'API), fornire all'utente un'indicazione importante che l'applicazione Assistant vuole ascoltare la voce di un utente per una parola chiave, anche quando l'applicazione non è in esecuzione e vuole il consenso dell'utente
2. Includere tutte le informazioni rilevanti per l'utilizzo dei dati e i criteri di privacy **prima** di richiedere l' `Windows.ApplicationModel.ConversationalAgent` accesso al microfono o l'uso delle API
3. Evitare le direttive o le parole iniziali (ad esempio, "fare clic su Sì nel prompt seguente") nel flusso di esperienza che divulga il comportamento di acquisizione audio e richiedere l'autorizzazione

Una volta soddisfatti questi requisiti, un'applicazione di assistente idonea verrà visualizzata nell'elenco delle applicazioni idonee per l'attivazione `enabled` vocale in uno stato dopo che è stato concesso l'accesso al microfono.

> [!NOTE]
> L'attivazione vocale sopra il blocco non è idonea per l'abilitazione automatica con accesso al microfono e richiede comunque a un utente di visitare la pagina relativa alla privacy per l'attivazione vocale per abilitare l'accesso al blocco precedente per un assistente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulle procedure consigliate per gli assistenti vocali in Windows](windows-voice-assistants-best-practices.md)