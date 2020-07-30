---
title: Integrazione di Hub di notifica con le app per dispositivi mobili del servizio app
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
ms.openlocfilehash: 647c5f40c7b02b8d9b488ce0812f27c0c0dde1a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87022141"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integrazione con le app per dispositivi mobili del servizio app

Per favorire un'esperienza lineare e uniforme nei servizi di Azure, le [app per dispositivi mobili del servizio app](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) comprendono il supporto predefinito per le notifiche push mediante Hub di notifica. [App per dispositivi mobili del servizio app](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) offrono una piattaforma di sviluppo di applicazioni mobili estremamente scalabile e disponibile a livello globale per sviluppatori aziendali e integratori di sistemi, che fornisce un set completo di funzionalità per gli sviluppatori di soluzioni per dispositivi mobili.

Gli sviluppatori di app per dispositivi mobili possono usare gli hub di notifica con il seguente flusso di lavoro:

1. Recuperare la gestione del dispositivo PNS
2. Registrare il dispositivo con gli Hub di notifica mediante il pratico API Register SDK client delle app per dispositivi mobili

    > [!NOTE]
    > Si noti che le app per dispositivi mobili consentono di eliminare tutti i tag nelle registrazioni per motivi di sicurezza. Usare le hub di notifica dal back-end direttamente per associare tag ai dispositivi.

3. Invio di notifiche dal back-end dell'app con hub di notifica

Ecco alcuni vantaggi per gli sviluppatori inclusi in questa integrazione:

- **SDK client per app per dispositivi mobili**: questi SDK multipiattaforma forniscono API semplici per la registrazione e per comunicare automaticamente con l'Hub di notifica collegato all'app per dispositivi mobili. Gli sviluppatori non hanno bisogno di cercare tra le credenziali di Hub di notifica e lavorare con un servizio aggiuntivo.
  - *Push all'utente*: gli SDK assegnano automaticamente tag al dispositivo specificato con l'ID utente autenticato di App per dispositivi mobili per abilitare il push all'utente.
  - *Push al dispositivo*: l'ID di installazione di App per dispositivi mobili viene usato automaticamente dagli SDK come GUID per la registrazione in Hub di notifica. In questo modo gli sviluppatori non sono costretti a mantenere più GUID di servizio.
- **Modello di installazione**: le app per dispositivi mobili funzionano con il modello di push più recente degli Hub di notifica per rappresentare tutte le proprietà di push associate a un dispositivo in un'installazione JSON che sia in linea con i servizi di notifica push e che sia facile da usare.
- **Flessibilità**: gli sviluppatori possono scegliere di usare direttamente Hub di notifica in qualsiasi momento, anche dopo l'integrazione.
- **Esperienza integrata nel [Portale di Azure](https://portal.azure.com)** : il push come funzionalità è rappresentato visivamente nelle app per dispositivi mobili e gli sviluppatori possono utilizzare facilmente l'Hub di notifica associato tramite le App per dispositivi mobili.
