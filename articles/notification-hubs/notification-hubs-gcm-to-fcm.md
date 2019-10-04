---
title: Hub di notifica di Azure e migrazione di Google Firebase Cloud Messaging (FCM)
description: Descrive in che modo Hub di notifica di Azure risolve la migrazione da Google GCM a FCM.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 80eae09240bde61870995468485338db5f0b9c2d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212314"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Hub di notifica di Azure e migrazione di Google Firebase Cloud Messaging (FCM)

## <a name="current-state"></a>Stato corrente

Quando Google ha annunciato la migrazione da Google Cloud Messaging (GCM) a Firebase Cloud Messaging (FCM), i servizi push come il nostro hanno dovuto modificare il modo in cui abbiamo inviato le notifiche ai dispositivi Android per soddisfare le modifiche.

Il back-end del servizio è stato aggiornato, quindi sono stati pubblicati aggiornamenti per l'API e gli SDK in base alle esigenze. Con la nostra implementazione abbiamo deciso di mantenere la compatibilità con gli schemi di notifica GCM esistenti per ridurre al minimo l'incidenza dei clienti. Questo significa che attualmente si inviano notifiche a dispositivi Android con FCM in modalità legacy FCM. Infine, è necessario aggiungere il supporto vero per FCM, incluse le nuove funzionalità e il formato di payload. Si tratta di una modifica a lungo termine e la migrazione corrente è incentrata sulla gestione della compatibilità con le applicazioni e gli SDK esistenti. È possibile usare gli SDK GCM o FCM nell'app (insieme all'SDK) e assicurarsi che la notifica venga inviata correttamente.

Alcuni clienti hanno ricevuto di recente un messaggio di posta elettronica da Google Warning sulle app che usano un endpoint GCM per le notifiche. Si tratta di un messaggio di avviso e non viene interrotta alcuna operazione: le notifiche Android dell'app vengono ancora inviate a Google per l'elaborazione e Google le elabora. Alcuni clienti che hanno specificato in modo esplicito l'endpoint GCM nella configurazione del servizio stavano ancora usando l'endpoint deprecato. Abbiamo già identificato questo gap e abbiamo lavorato per risolvere il problema quando Google ha inviato il messaggio di posta elettronica.

L'endpoint deprecato è stato sostituito e la correzione è stata distribuita.

## <a name="going-forward"></a>In futuro

Domande frequenti su FCM di Google indica che non è necessario eseguire alcuna operazione. Nelle [domande frequenti su FCM](https://developers.google.com/cloud-messaging/faq), Google Said "SDK client e token GCM continueranno a funzionare per un periodo illimitato. Tuttavia, non sarà possibile usare come destinazione la versione più recente di Google Play Services nell'app Android, a meno che non si eseguirà la migrazione a FCM ".

Se l'app usa la libreria GCM, proseguire e seguire le istruzioni di Google per eseguire l'aggiornamento alla libreria FCM nell'app. L'SDK è compatibile con entrambi, quindi non sarà necessario aggiornare alcun elemento nell'app da parte dell'utente (purché si sia aggiornati con la versione dell'SDK).

## <a name="questions-and-answers"></a>Domande e risposte

Ecco alcune risposte alle domande comuni che abbiamo sentito dai clienti:

**D:** Che cosa è necessario fare per essere compatibile con la data di cutoff (il Data Cutoff corrente di Google è il 29 maggio e potrebbe cambiare)?

**R:** Niente. Si manterrà la compatibilità con lo schema di notifica GCM esistente. La chiave di GCM continuerà a funzionare normalmente come tutti gli SDK e le librerie di GCM usati dall'applicazione.

Se/quando si decide di eseguire l'aggiornamento agli SDK e alle librerie di FCM per sfruttare le nuove funzionalità, la chiave GCM funzionerà comunque. Se si vuole, è possibile passare all'uso di una chiave FCM, ma assicurarsi di aggiungere Firebase al progetto GCM esistente durante la creazione del nuovo progetto Firebase. Questo garantisce la compatibilità con i clienti che eseguono versioni precedenti dell'app che usano ancora le librerie e gli SDK di GCM.

Se si sta creando un nuovo progetto FCM e non ci si connette al progetto GCM esistente, dopo l'aggiornamento di hub di notifica con il nuovo segreto FCM si perderà la possibilità di inviare notifiche push alle installazioni correnti dell'app, perché la nuova chiave FCM non contiene collegamenti alla precedente GCM progetto.

**D:** Perché ricevo questo messaggio di posta elettronica sui vecchi endpoint GCM usati? Cosa devo fare?

**R:** Niente. È stata eseguita la migrazione ai nuovi endpoint e verrà completata a breve, quindi non è necessaria alcuna modifica. Nessun elemento è stato danneggiato, un endpoint mancante ha semplicemente causato messaggi di avviso da Google.

**D:** Come è possibile passare ai nuovi SDK e alle librerie FCM senza suddividere gli utenti esistenti?

R: Eseguire l'aggiornamento in qualsiasi momento. Google non ha ancora annunciato alcuna deprecazione di librerie e SDK GCM esistenti. Per assicurarsi di non interrompere le notifiche push agli utenti esistenti, assicurarsi quando si crea il nuovo progetto Firebase che si sta associando al progetto GCM esistente. In questo modo, i nuovi segreti Firebase funzioneranno per gli utenti che eseguono le versioni precedenti dell'app con gli SDK e le librerie di GCM, nonché per i nuovi utenti dell'app con gli SDK e le librerie di FCM.

**D:** Quando è possibile usare le nuove funzionalità e gli schemi di FCM per le notifiche?

**R:** Dopo aver pubblicato un aggiornamento per l'API e gli SDK, è possibile ottenere una soluzione per l'utente nei prossimi mesi.
