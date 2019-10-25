---
title: Importanza delle notifiche push nelle app per dispositivi mobili con Visual Studio App Center e hub di notifica di Azure
description: Informazioni sui servizi, ad esempio App Center che consentono di interagire con gli utenti delle applicazioni per dispositivi mobili.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 05a9bc6ccd26aaecd3e0bf615880e0543b8604f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795913"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Interagire con gli utenti dell'applicazione inviando notifiche push 

Che si stia compilando applicazioni consumer o aziendali, si vuole che gli utenti usino attivamente l'applicazione. Spesso è necessario condividere le ultime notizie, gli aggiornamenti dei giochi, le offerte interessanti, l'aggiornamento del prodotto o le informazioni rilevanti per gli utenti. Per aumentare il coinvolgimento degli utenti e tornare all'applicazione, è necessario un modo per comunicare con gli utenti in tempo reale.

Le notifiche push consentono di comunicare con gli utenti dell'applicazione in modo rapido ed efficiente. È possibile contattare gli utenti al momento giusto e notificare loro le informazioni desiderate, in genere in una finestra popup o in una finestra di dialogo in un dispositivo mobile, indipendentemente dalle operazioni eseguite.

## <a name="value-of-push-notifications"></a>Valore delle notifiche push
Le notifiche push forniscono valore agli utenti finali e all'azienda.

Le aziende possono:
- **Comunicare direttamente con gli utenti** inviando loro messaggi sulla piattaforma supportata al momento giusto.
- **Incrementa il coinvolgimento degli** utenti inviando gli aggiornamenti in tempo reale e i promemoria ai tuoi utenti, incoraggiando gli utenti a interagire con l'applicazione a intervalli regolari.
- **Mantenere gli utenti** e riattivare gli utenti inattivi che hanno scaricato l'applicazione, ma non usarla per riattivare l'applicazione.
- **Aumentare i tassi di conversione** analizzando il comportamento dell'utente finale, segmentando gli utenti finali e sfruttando le campagne basate sulle notifiche push per dispositivi mobili.
- **Promuovere i prodotti e i servizi** inviando messaggi push e aggiornamenti tempestivi agli utenti.
- **Indirizzare gli utenti** inviando messaggi push personalizzati.

Per gli utenti dell'applicazione, notifiche push:
- **Fornire valore e informazioni** in tempo reale.
- **Ricordare agli utenti** di usare l'applicazione.

Usare i servizi seguenti per abilitare le notifiche push nelle app per dispositivi mobili.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center servizio push](/appcenter/push/) consente di interagire con gli utenti inviando i messaggi di destinazione agli utenti di iOS, Android e Windows senza dover gestire il processo di invio delle notifiche ai dispositivi mediante l'Notification Services push (PNS). Basato su Hub di notifica di Azure, questo servizio Elimina le complessità associate al push manuale delle notifiche fornendo un dashboard potente.

**Funzionalità principali**
- **Inviare notifiche push a dispositivi mobili** su diverse piattaforme.
- Usare le notifiche per inviare dati a un'applicazione, visualizzare un messaggio all'utente o attivare un'azione da parte dell'applicazione.
- Destinazioni notifiche: 
    - Inviare messaggi broadcast a **tutti i dispositivi registrati**.
    - Inviare notifiche a gruppi di **destinatari** creati in base alle informazioni sul dispositivo e alle proprietà personalizzate.
    - Inviare notifiche a **utenti specifici**.
    - Inviare notifiche a **dispositivi specifici**.
- I dati di **telemetria avanzati** su push, dispositivo e errore sono disponibili nel portale di App Center.
- **Supporto della piattaforma** : iOS, Android, MacOS, Novell, React native, Unity, Cordova.

**Riferimenti**
- [Iscriversi con App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introduzione a App Center push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Hub di notifica di Azure
[Hub di notifica](/azure/notification-hubs/notification-hubs-push-notification-overview) offre un motore push di facile utilizzo e con scalabilità orizzontale che consente di inviare notifiche a qualsiasi piattaforma e da qualsiasi back-end (cloud o locale).

**Funzionalità principali**
- **Invia notifiche push** a qualsiasi piattaforma da qualsiasi back-end, nel cloud o in locale.
- **Trasmissione veloce** push a milioni di dispositivi mobili con una singola chiamata API.
- Personalizzare le notifiche push per cliente, lingua e località.
- Definire e notificare dinamicamente i **segmenti dei clienti**.
- **Ridimensiona immediatamente** a milioni di dispositivi mobili.
- **Supporto della piattaforma** : iOS, Android, Windows, Kindle, Baidu.
        
**Riferimenti**
- [Azure portal](https://portal.azure.com) 
- [Introduzione ad Hub di notifica di Azure](/azure/notification-hubs/)   
- [Guide introduttive](/azure/notification-hubs/create-notification-hub-portal)
- [Esempi](/azure/notification-hubs/samples)
