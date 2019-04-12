---
title: Hub di notifica di Azure e la migrazione di Google Firebase Cloud Messaging (FCM)
description: Viene descritto come hub di notifica di Azure risolve Google GCM migrazione da FCM.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: jowargo
ms.openlocfilehash: 4cbfc67bc66e84b4743f3326db40872241e5d474
ms.sourcegitcommit: f24b62e352e0512dfa2897362021b42e0cb9549d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59506407"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Hub di notifica di Azure e la migrazione di Google Firebase Cloud Messaging (FCM)

## <a name="current-state"></a>Stato corrente

Se Google ha annunciato la migrazione da Google Cloud Messaging (GCM) per l'interscambio di dati Firebase Cloud Messaging (FCM), servizi push, ad esempio le nostre era necessario modificare la modalità è inviate le notifiche a dispositivi Android per gestire la modifica.

Abbiamo aggiornato il servizio back-end e quindi aggiornamenti pubblicati per le API e SDK in base alle esigenze. Con la nostra implementazione, abbiamo preso la decisione di mantenere la compatibilità con gli schemi di notifica GCM esistenti per ridurre al minimo impatto sui clienti. Ciò significa che le notifiche vengono inviate attualmente per i dispositivi Android con FCM in modalità Legacy di FCM. Infine, si vuole aggiungere il supporto true per FCM, incluse le nuove funzionalità e il formato di payload. Che viene apportata una modifica a lungo termine e la migrazione corrente è incentrata su mantenendo la compatibilità con le applicazioni esistenti e gli SDK. È possibile usare GCM o FCM SDK nell'app (insieme a Windows SDK) e Microsoft si assicura che la notifica viene inviata correttamente.

Alcuni clienti ricevuto un messaggio di posta elettronica di avviso Google sulle App usando un endpoint GCM per le notifiche. Questo era solo un avviso e non viene interrotta: notifiche Android dell'app vengono comunque inviate a Google per l'elaborazione e Google li elabora comunque. Alcuni clienti che hanno specificato in modo esplicito l'endpoint GCM nella configurazione del servizio sono stati ancora usando l'endpoint deprecato. Si ha già identificato questo divario e sono state lavorando per risolvere il problema quando Google inviato il messaggio di posta elettronica.

È stata sostituita quell'endpoint deprecati e la correzione viene distribuita.

## <a name="going-forward"></a>In futuro

Domande frequenti di Google FCM afferma di che non dover eseguire alcuna operazione. Nel [domande frequenti su FCM](https://developers.google.com/cloud-messaging/faq), Google detto "SDK client e i token di GCM continueranno a funzionare per un periodo illimitato. Tuttavia, sarà possibile impostare come destinazione la versione più recente di Google Play Services nell'app per Android a meno che non si esegue la migrazione da FCM."

Se l'app Usa la libreria GCM, andare avanti e seguire le istruzioni di Google per eseguire l'aggiornamento alla libreria FCM nell'app. il SDK è compatibile con entrambi, in modo non sarà necessario aggiornare il contenuto l'app dipenda da noi (purché si è aggiornati con la versione del SDK).

## <a name="questions-and-answers"></a>Domande e risposte

Ecco alcune risposte alle domande più comuni che sono stati segnalati dai clienti:

**D:** Cosa bisogna fare per essere compatibile entro la data limite (Google corrente del valore di cambio data è 29 maggio e può cambiare)?

**R:** Niente. Si manterrà la compatibilità con lo schema di notifica GCM esistente. La chiave GCM continueranno a funzionare normalmente, così come qualsiasi SDK di GCM e le librerie usate dall'applicazione.

Se/quando si decide di eseguire l'aggiornamento a SDK FCM e librerie per sfruttare i vantaggi delle nuove funzionalità, la chiave GCM continuerà a funzionare. È possibile iniziare a usare una chiave FCM se si desidera, ma assicurarsi che si è aggiungere Firebase al progetto GCM esistente quando si crea il nuovo progetto Firebase. Ciò garantisce la compatibilità con i clienti che eseguono versioni precedenti dell'app che usano ancora GCM SDK e librerie.

Se si crea un nuovo progetto FCM e non associare al progetto GCM esistente, dopo aver aggiornato gli hub di notifica con il nuovo segreto FCM si perderà la possibilità per inviare notifiche push per le installazioni di app corrente, poiché la nuova chiave FCM non dispone di alcun collegamento al vecchio GCM progetto.

**D:** Perché viene visualizzato questo messaggio di posta elettronica sugli endpoint GCM precedente in uso? Cosa devo fare?

**R:** Niente. Eseguire la migrazione ai nuovi endpoint che sarà terminati a breve, pertanto è necessaria alcuna modifica. Nulla viene compromesso, un endpoint mancati causato semplicemente i messaggi di avviso da Google.

**D:** Come si attiva per il nuovo SDK di FCM e librerie senza interrompere gli utenti esistenti?

R: Eseguire l'aggiornamento in qualsiasi momento. Google non ha ancora annunciato alcun deprecazione delle librerie e SDK di GCM esistenti. Per assicurarsi non interrompere le notifiche push agli utenti esistenti, assicurarsi che quando si crea il nuovo progetto Firebase che si associano il progetto GCM esistente. Questa operazione assicurerà Firebase nuovi segreti funzioneranno per gli utenti che eseguono le versioni precedenti dell'app con gli SDK di GCM e librerie, nonché nuovi utenti dell'app con gli SDK di FCM e librerie.

**D:** Quando è possibile usare le nuove funzionalità FCM e schemi per notifiche personali?

**R:** Quando si pubblica un aggiornamento per le API e SDK, rimanete sintonizzati: si prevede di avere qualcosa per te nei prossimi mesi.
