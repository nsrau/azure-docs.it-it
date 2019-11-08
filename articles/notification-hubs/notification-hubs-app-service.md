---
title: Integrazione con le app per dispositivi mobili del servizio app
description: Informazioni sul funzionamento di Hub di notifica di Azure con le app per dispositivi mobili del servizio app di Azure.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 09ecc258001dd735b920c3a7cb51d8ce7443ceb4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686535"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integrazione con le app per dispositivi mobili del servizio app

Per favorire un'esperienza lineare e uniforme nei servizi di Azure, le [app per dispositivi mobili del servizio app](../app-service-mobile/app-service-mobile-value-prop.md) comprendono il supporto predefinito per le notifiche push mediante Hub di notifica. [app per dispositivi mobili del servizio app](../app-service-mobile/app-service-mobile-value-prop.md) offrono una piattaforma di sviluppo di applicazioni mobili estremamente scalabile e disponibile a livello globale per sviluppatori aziendali e integratori di sistemi, che fornisce un set completo di funzionalità per gli sviluppatori di soluzioni per dispositivi mobili.

Gli sviluppatori di app per dispositivi mobili possono usare gli hub di notifica con il seguente flusso di lavoro:

1. Recuperare la gestione del dispositivo PNS
2. Registrare il dispositivo con gli Hub di notifica mediante il pratico API Register SDK client delle app per dispositivi mobili

    > [!NOTE]
    > Si noti che le app per dispositivi mobili consentono di eliminare tutti i tag nelle registrazioni per motivi di sicurezza. Usare le hub di notifica dal back-end direttamente per associare tag ai dispositivi.

3. Invio di notifiche dal back-end dell'app con hub di notifica

Ecco alcuni vantaggi per gli sviluppatori inclusi in questa integrazione:

- **SDK client delle app per dispositivi mobili.** :  Questi SDK multipiattaforma forniscono API semplici per la registrazione e per comunicare automaticamente con l'hub di notifica collegato all'app per dispositivi mobili. Gli sviluppatori non hanno bisogno di cercare tra le credenziali di Hub di notifica e lavorare con un servizio aggiuntivo.
  - *Push a utente*: Gli SDK assegnano automaticamente tag al dispositivo specificato con l'ID utente autenticato di App per dispositivi mobili per abilitare il push all'utente.
  - *Push a dispositivo*: L'ID di installazione di App per dispositivi mobili viene usato automaticamente dagli SDK come GUID per la registrazione in Hub di notifica. In questo modo gli sviluppatori non sono costretti a mantenere più GUID di servizio.
- **Modello di installazione**:  App per dispositivi mobili usa il modello push più recente di Hub di notifica per rappresentare tutte le proprietà push associate a un dispositivo in un'installazione JSON che sia in linea con i servizi di notifica push e facile da usare.
- **Flessibilità**:  Gli sviluppatori possono scegliere di usare direttamente Hub di notifica in qualsiasi momento, anche dopo l'integrazione.
- **Esperienza integrata nel [portale di Azure](https://portal.azure.com)** :  Il Push come funzionalità è rappresentato visivamente nelle app per dispositivi mobili e gli sviluppatori possono utilizzare facilmente l'hub di notifica associato tramite le App per dispositivi mobili.
