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
ms.openlocfilehash: a9fe083818c5850ad05556b18911c19fe19e0e72
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987401"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Linee guida sulla privacy per gli assistenti vocali in Windows

È importante che gli utenti dispongano di informazioni chiare sul modo in cui i dati vocali vengono raccolti e usati e che siano in grado di controllare se e come si verifica questa raccolta. Questi aspetti fondamentali della privacy, *divulgazione* e *consenso* , sono particolarmente importanti per gli assistenti vocali integrati in Windows in base alla natura sempre in ascolto del loro utilizzo.

Gli sviluppatori che creano Assistente vocale in Windows devono includere elementi di interfaccia utente cancellati all'interno delle applicazioni che riflettono le funzionalità di ascolto dell'assistente.

> [!NOTE]
> L'impossibilità di fornire la divulgazione e il consenso appropriati per un'applicazione Assistant, incluso dopo gli aggiornamenti dell'applicazione, può comportare l'indisponibilità dell'assistente per l'attivazione vocale fino alla risoluzione dei problemi di privacy.

## <a name="minimum-requirements-for-feature-inclusion"></a>Requisiti minimi per l'inclusione delle funzionalità

Gli utenti di Windows possono visualizzare e controllare la disponibilità delle applicazioni Assistant in **`Settings > Privacy > Voice activation`** .

 > [!div class="mx-imgBorder"]
 > [![Screenshot mostra le opzioni per controllare disponibilità di Cortana. ](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Voce relativa all'impostazione della privacy per l'attivazione vocale di Windows per un'applicazione Assistant")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Per diventare idoneo per l'inclusione in questo elenco, contattare Microsoft all'indirizzo winvoiceassistants@microsoft.com per iniziare. Per impostazione predefinita, gli utenti dovranno abilitare in modo esplicito l'attivazione vocale per un nuovo assistente in **`Settings > Privacy > Voice Activation`** , a cui un'applicazione può collegare il protocollo con `ms-settings:privacy-voiceactivation` . Un'applicazione consentita verrà visualizzata nell'elenco dopo l'esecuzione e l'uso delle `Windows.ApplicationModel.ConversationalAgent` API. Le impostazioni di attivazione vocale saranno modificabili dopo che l'applicazione ha ottenuto il consenso del microfono da parte dell'utente.

Poiché le impostazioni di privacy di Windows includono informazioni sul funzionamento dell'attivazione vocale e l'interfaccia utente standard per il controllo dell'autorizzazione, la divulgazione e il consenso sono entrambi soddisfatti. L'assistente rimarrà in questo elenco consentito a condizione che non sia:

* Fuorviare o informare l'utente sull'attivazione vocale o sulla gestione dei dati vocali da parte dell'assistente
* Interferire con un altro assistente
* Interrompi eventuali altri criteri Microsoft pertinenti

Se viene individuato uno dei precedenti, Microsoft può rimuovere un assistente dall'elenco dei consentiti fino a quando non vengono risolti i problemi.

> [!NOTE]
> In tutti i casi, l'autorizzazione per l'attivazione vocale richiede l'autorizzazione del microfono. Se un'applicazione Assistant non dispone dell'accesso al microfono, non sarà idonea per l'attivazione vocale e verrà visualizzata nelle impostazioni relative alla privacy per l'attivazione vocale in uno stato disabilitato.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Requisiti aggiuntivi per l'inclusione del consenso del microfono

Gli autori degli assistenti che desiderano rendere più semplice e più agevole la scelta degli utenti per l'attivazione vocale possono eseguire questa operazione soddisfacendo i requisiti aggiuntivi per soddisfare adeguatamente la divulgazione e il consenso senza un ulteriore viaggio alla pagina delle impostazioni. Una volta approvata, l'attivazione vocale diventerà disponibile immediatamente dopo che un utente concede l'autorizzazione al microfono per l'applicazione Assistant. Per essere idoneo, un'applicazione Assistant deve eseguire le operazioni seguenti **prima** di richiedere il consenso del microfono, ad esempio usando l' `AppCapability.RequestAccessAsync` API:

1. Fornire all'utente un'indicazione chiara e prominente che l'applicazione desidera ascoltare la voce dell'utente per una parola chiave, *anche quando l'applicazione non è in esecuzione*e desidera il consenso dell'utente
1. Includere le informazioni rilevanti sull'utilizzo dei dati e sui criteri di privacy, ad esempio un collegamento a un'informativa sulla privacy ufficiale
1. Evitare le direttive o le parole iniziali (ad esempio, "fare clic su Sì nel prompt seguente") nel flusso di esperienza che divulga il comportamento di acquisizione audio

Se un'applicazione esegue tutte le precedenti, è idonea ad abilitare la funzionalità di attivazione vocale insieme al consenso del microfono. winvoiceassistants@microsoft.comPer ulteriori informazioni e per esaminare una prima esperienza di utilizzo, contattare il.

> [!NOTE]
> L'attivazione vocale sopra il blocco non è idonea per l'abilitazione automatica con accesso al microfono e richiede comunque a un utente di visitare la pagina relativa alla privacy per l'attivazione vocale per abilitare l'accesso al blocco precedente per un assistente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulle procedure consigliate per gli assistenti vocali in Windows](windows-voice-assistants-best-practices.md)