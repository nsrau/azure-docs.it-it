---
title: L'importanza delle notifiche push nelle app per dispositivi mobili con Visual Studio App Center e Hub di notifica di AzureThe importance of push notifications in your mobile apps with Visual Studio App Center and Azure Notification Hubs
description: Informazioni su servizi come Visual Studio App Center che è possibile usare per interagire con gli utenti delle applicazioni mobili.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: bc59857cdb843dfff54e69f2226f13cfe70df8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235339"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Interagisci con gli utenti dell'applicazione inviando notifiche push

Che si stiano creando applicazioni consumer o aziendali, si desidera che gli utenti utilizzino attivamente l'applicazione. Ci sono spesso momenti in cui si desidera condividere le ultime notizie, gli aggiornamenti dei giochi, offerte emozionanti, aggiornamenti dei prodotti o qualsiasi altra informazione rilevante con gli utenti. Per aumentare il coinvolgimento con gli utenti e farli tornare all'applicazione, è necessario un modo per comunicare con gli utenti in tempo reale.

Le notifiche push forniscono un modo rapido ed efficiente per comunicare con gli utenti dell'applicazione. È possibile contattare gli utenti al momento giusto e notificare loro le informazioni desiderate, in genere in un elemento popup o in una finestra di dialogo su un dispositivo mobile, indipendentemente da ciò che stanno facendo.

## <a name="value-of-push-notifications"></a>Valore delle notifiche push
Le notifiche push forniscono valore agli utenti e alle aziende.

Le aziende possono:
- Comunica direttamente con gli utenti inviando messaggi sulle piattaforme supportate al momento giusto.
- Aumenta il coinvolgimento degli utenti inviando aggiornamenti e promemoria in tempo reale agli utenti, incoraggiandoli a interagire regolarmente con l'applicazione.
- Mantenere gli utenti e reinserirsi con gli utenti inattivi che hanno scaricato l'applicazione ma non utilizzarla per diventare nuovamente attivi.
- Aumenta i tassi di conversione analizzando il comportamento degli utenti, segmentando gli utenti e sfruttando le campagne in base alle notifiche push per dispositivi mobili.
- Promuovi prodotti e servizi inviando messaggi push e aggiornamenti tempestivi agli utenti.
- Scegli come target gli utenti inviando messaggi push personalizzati.

Per gli utenti dell'applicazione, le notifiche push:For application users, push notifications:
- Fornisci valore e informazioni in tempo reale.
- Ricordare agli utenti di utilizzare l'applicazione.

Usa i servizi seguenti per abilitare le notifiche push nelle tue app per dispositivi mobili.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Con [App Center Push,](/appcenter/push/)è possibile inviare messaggi mirati agli utenti iOS, Android e Windows senza dover gestire il processo di invio di notifiche ai dispositivi utilizzando i servizi di notifica push (PNS). Basato su Hub di notifica di Azure, questo servizio elimina manualmente le complessità associate al push delle notifiche fornendo un dashboard potente.

**Caratteristiche principali**
- Invia notifiche push ai dispositivi mobili su una varietà di piattaforme.
- Utilizzare le notifiche per inviare dati a un'applicazione, visualizzare un messaggio all'utente o attivare un'azione dall'applicazione.
- Usa gli obiettivi di notifica per: 
    - Trasmettere messaggi a tutti i dispositivi registrati.
    - Invia notifiche ai segmenti di pubblico in base alle informazioni sul dispositivo e alle proprietà personalizzate.
    - Inviare notifiche a utenti specifici.
    - Inviare notifiche a dispositivi specifici.
- Usare i dati di telemetria avanzati su push, dispositivi ed errori disponibili nel portale App Center.
- Ottieni il supporto della piattaforma per iOS, Android, macOS, Xamarin, React Native, Unity e Cordova.

**Riferimenti**
- [Iscriversi a Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introduzione a App Center Push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Hub di notifica di Azure
[Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) offre un motore push facile da usare e scalato. È possibile utilizzarlo per inviare notifiche a qualsiasi piattaforma e da qualsiasi back-end nel cloud o in locale.

**Caratteristiche principali**
- Invia notifiche push a qualsiasi piattaforma da qualsiasi back-end, nel cloud o in locale.
- Push broadcast veloce a milioni di dispositivi mobili con una singola chiamata API.
- Personalizza le notifiche push in base al cliente, alla lingua e alla posizione.
- Definire e notificare dinamicamente i segmenti di cliente.
- Scalabilità immediata su milioni di dispositivi mobili.
- Ottieni il supporto della piattaforma per iOS, Android, Windows, Kindle e Baidu.
        
**Riferimenti**
- [Portale di Azure](https://portal.azure.com) 
- [Introduzione ad Hub di notifica di Azure](/azure/notification-hubs/)
- [Avvi rapidi](/azure/notification-hubs/create-notification-hub-portal)
- [Esempi](/azure/notification-hubs/samples)
