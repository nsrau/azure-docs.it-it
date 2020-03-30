---
title: Hub di notifica di Azure e migrazione di Google Firebase Cloud Messaging (FCM)
description: Descrive in che modo gli hub di notifica di Azure indirizzano la migrazione da Google GCM a FCM.
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
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127034"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Migrazione di Hub di notifica di Azure e Google Firebase Cloud Messaging

## <a name="current-state"></a>Stato corrente

Quando Google ha annunciato la migrazione da Google Cloud Messaging (GCM) a Firebase Cloud Messaging (FCM), i servizi push come il nostro hanno dovuto modificare il modo in cui abbiamo inviato le notifiche ai dispositivi Android per adattarsi al cambiamento.

Abbiamo aggiornato il nostro back-end del servizio, quindi pubblicato gli aggiornamenti per le nostre API e SDK in base alle esigenze. Con la nostra implementazione, abbiamo deciso di mantenere la compatibilità con gli schemi di notifica GCM esistenti per ridurre al minimo l'impatto dei clienti. Ciò significa che attualmente inviamo notifiche ai dispositivi Android utilizzando FCM in modalità legacy FCM. In definitiva, vogliamo aggiungere un vero supporto per FCM, incluse le nuove funzionalità e il formato payload. Si tratta di una modifica a più lungo termine e la migrazione corrente è incentrata sul mantenimento della compatibilità con le applicazioni e gli SDK esistenti. Puoi usare gli SDK GCM o FCM nella tua app (insieme al nostro SDK) e ci assicuriamo che la notifica venga inviata correttamente.

Alcuni clienti hanno recentemente ricevuto un'email da Google che invia informazioni sulle app che utilizzano un endpoint GCM per le notifiche. Questo è stato solo un avviso, e nulla è rotto - le notifiche Android della tua applicazione sono ancora inviate a Google per l'elaborazione e Google le elabora ancora. Alcuni clienti che hanno specificato l'endpoint GCM in modo esplicito nella configurazione del servizio utilizzavano ancora l'endpoint deprecato. Avevamo già identificato questa lacuna e stavamo lavorando per risolvere il problema quando Google ha inviato l'e-mail.

L'endpoint deprecato è stato sostituito e la correzione viene distribuita.

## <a name="going-forward"></a>Andare avanti

Le DOMANDE frequenti FCM di Google dicono che non devi fare nulla. Nelle [FAQ FCM,](https://developers.google.com/cloud-messaging/faq)Google ha detto che "gli SDK client e i token GCM continueranno a funzionare a tempo indeterminato. Tuttavia, non sarai in grado di scegliere come target l'ultima versione di Google Play Services nella tua app Android a meno che non esegui la migrazione a FCM."

Se la tua app usa la libreria GCM, segui le istruzioni di Google per eseguire l'aggiornamento alla libreria FCM nella tua app. Il nostro SDK è compatibile con entrambi, quindi non dovrai aggiornare nulla nella tua app dalla nostra parte (a condizione che tu sia aggiornato con la nostra versione SDK).

## <a name="questions-and-answers"></a>Domande e risposte

Ecco alcune risposte alle domande più comuni che abbiamo sentito dai clienti:

**D:** Cosa devo fare per essere compatibile con la data limite (la data limite corrente di Google è il 29 maggio e potrebbe cambiare)?

**A:** Niente. Manterremo la compatibilità con lo schema di notifica GCM esistente. La chiave GCM continuerà a funzionare normalmente come tutti gli SDK GCM e le librerie utilizzate dall'applicazione.

Se/quando si decide di eseguire l'aggiornamento agli SDK e alle librerie FCM per sfruttare le nuove funzionalità, la chiave GCM continuerà a funzionare. È possibile passare all'utilizzo di una chiave FCM, se lo si desidera, ma assicurarsi di aggiungere Firebase al progetto GCM esistente durante la creazione del nuovo progetto Firebase. Ciò garantirà la compatibilità con le versioni precedenti con i clienti che eseguono versioni precedenti dell'app che usano ancora SDK e librerie GCM.

Se si sta creando un nuovo progetto FCM e non ci si collega al progetto GCM esistente, una volta aggiornato Notification Hubs con il nuovo segreto FCM si perderà la possibilità di inviare notifiche alle installazioni dell'app corrente, poiché la nuova chiave FCM non ha alcun collegamento al vecchio GCM Progetto.

**D:** Perché ricevo questo messaggio di posta elettronica sugli endpoint GCM precedenti in uso? Cosa devo fare?

**A:** Niente. È stata la migrazione ai nuovi endpoint e sarà completata a breve, quindi non è necessario alcun cambiamento. Nulla è rotto, il nostro unico endpoint perso semplicemente causato messaggi di avviso da Google.

**D:** Come posso passare ai nuovi SDK e librerie FCM senza interrompere gli utenti esistenti?

R: Aggiorna in qualsiasi momento. Google non ha ancora annunciato alcuna deprecazione degli SDK e delle librerie GCM esistenti. Per assicurarti di non interrompere le notifiche push agli utenti esistenti, assicurati che quando crei il nuovo progetto Firebase che stai associando al progetto GCM esistente. In questo modo i nuovi segreti Firebase funzioneranno per gli utenti che eseguono le versioni precedenti dell'app con SDK e librerie GCM, nonché per i nuovi utenti dell'app con SDK e librerie FCM.

**D:** Quando è possibile utilizzare le nuove funzionalità e gli schemi FCM per le notifiche?

**A:** Una volta pubblicato un aggiornamento per le nostre API e SDK, rimanete sintonizzati - ci aspettiamo di avere qualcosa per voi nei prossimi mesi.
