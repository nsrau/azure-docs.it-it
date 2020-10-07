---
title: Integrazione di Hub di notifica con le app per dispositivi mobili del servizio app
description: Informazioni sul funzionamento di Hub di notifica di Azure con le app per dispositivi mobili del servizio app di Azure.
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 08/06/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e7042be7e407e8e0827e142ba6878dfff812e1f6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88004061"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integrazione con le app per dispositivi mobili del servizio app

Per favorire un'esperienza lineare e uniforme nei servizi di Azure, il servizio [App per dispositivi mobili del servizio app](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) include il supporto predefinito per le notifiche mediante Hub di notifica di Azure. Il servizio [App per dispositivi mobili del servizio app](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) offre una piattaforma di sviluppo di applicazioni mobili estremamente scalabile e disponibile a livello globale per sviluppatori aziendali e integratori di sistemi, che fornisce un set completo di funzionalità per gli sviluppatori di soluzioni per dispositivi mobili.

Gli sviluppatori di app per dispositivi mobili possono usare Hub di notifica con il flusso di lavoro seguente:

1. Recuperare la gestione del dispositivo PNS.
2. Registrare il dispositivo con Hub di notifica mediante le API di registrazione dell'SDK client di App per dispositivi mobili.

    > [!NOTE]
    > Si noti che le app per dispositivi mobili consentono di eliminare tutti i tag nelle registrazioni per motivi di sicurezza. Usare le hub di notifica dal back-end direttamente per associare tag ai dispositivi.

3. Invio di notifiche dal back-end dell'app con Hub di notifica.

Questa integrazione offre alcuni vantaggi, tra cui:

- **SDK client delle app per dispositivi mobili.** : questi SDK multipiattaforma forniscono API per la registrazione e le comunicazioni con l'hub di notifica collegato con l'app per dispositivi mobili. Non è necessario avere credenziali di Hub di notifica o usare un servizio aggiuntivo.
  - *Push a utente*: gli SDK assegnano automaticamente tag al dispositivo specificato con un ID utente autenticato di App per dispositivi mobili per abilitare lo scenario "push all'utente".
  - *Push a dispositivo*: l'ID di installazione di App per dispositivi mobili viene usato automaticamente dagli SDK come GUID per la registrazione in Hub di notifica. Non è quindi necessario mantenere più GUID di servizio.
- **Modello di installazione**: App per dispositivi mobili usa il modello push più recente di Hub di notifica per rappresentare tutte le proprietà push associate a un dispositivo in un'installazione JSON che sia in linea con i servizi di notifica push e facile da usare.
- **Flessibilità**: gli sviluppatori possono scegliere di usare direttamente Hub di notifica in qualsiasi momento, anche dopo l'integrazione.
- **Esperienza integrata nel [portale di Azure](https://portal.azure.com)** : il push come funzionalità è rappresentato visivamente in App per dispositivi mobili e gli sviluppatori possono usare facilmente l'hub di notifica associato tramite App per dispositivi mobili.
