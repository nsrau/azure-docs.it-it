---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: nikuklic
ms.openlocfilehash: 63fd0af819fde7d78df289a1b8f5cefa2e415101
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779204"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Una risorsa di Servizi di comunicazione distribuita. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un numero di telefono acquisito nella risorsa di Servizi di comunicazione. [Come ottenere un numero di telefono](../../telephony-sms/get-phone-number.md).
- Un `User Access Token` per abilitare il client di chiamata. Per altre informazioni, vedere [come ottenere un `User Access Token`](../../access-tokens.md)
- Completare la guida di avvio rapido per [aggiungere funzionalità per chiamate all'applicazione](../getting-started-with-calling.md)

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

- Per visualizzare i numeri di telefono associati alla risorsa di Servizi di comunicazione, accedere al [portale di Azure](https://portal.azure.com/), individuare la risorsa di Servizi di comunicazione e aprire la scheda dei **numeri di telefono** nel riquadro di spostamento sinistro.
- È possibile compilare ed eseguire l'app con la libreria client Chiamate di Servizi di comunicazione per iOS:

## <a name="setting-up"></a>Configurazione

## <a name="start-a-call-to-phone"></a>Avviare una chiamata al telefono

Specificare il numero di telefono acquisito nella risorsa di Servizi di comunicazione, che verrà usato per avviare la chiamata:
> [!WARNING]
> Si noti che i numeri di telefono devono essere specificati nel formato standard internazionale E.164, ad esempio +12223334444.

Modificare il gestore dell'evento `startCall` che verrà eseguito quando si tocca il pulsante *Start Call* (Avvia chiamata):

```swift
func startCall() {
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            let startCallOptions = ACSStartCallOptions()
            startCallOptions!.alternateCallerID = PhoneNumber(phoneNumber: "+12223334444")
            self.call = self.callAgent!.call([PhoneNumber(phoneNumber: self.callee)], options: startCallOptions)
            self.callDelegate = CallDelegate(self)
            self.call!.delegate = self.callDelegate
        }
    }
}
```

## <a name="run-the-code"></a>Eseguire il codice

È possibile creare ed eseguire un'app nel simulatore iOS selezionando **Product** > **Run** (Prodotto > Esegui) o premendo i tasti di scelta rapida &#8984;+R.

![Aspetto finale dell'app di avvio rapido](../media/ios/quick-start-make-call.png)

È possibile effettuare una chiamata specificando un numero di telefono nel campo di testo aggiunto e facendo clic sul pulsante **Start Call** (Avvia chiamata).
> [!WARNING]
> Si noti che i numeri di telefono devono essere specificati nel formato standard internazionale E.164, ad esempio +12223334444.

> [!NOTE]
> La prima volta che si effettua una chiamata, il sistema chiederà l'accesso al microfono. In un'applicazione di produzione è consigliabile usare l'API `AVAudioSession` per [controllare lo stato delle autorizzazioni](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) e aggiornare normalmente il comportamento dell'applicazione quando l'autorizzazione non viene concessa.
