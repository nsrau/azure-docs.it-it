---
title: Importanza delle notifiche push nelle app per dispositivi mobili con Visual Studio App Center e hub di notifica di Azure
description: Informazioni sui servizi, ad esempio Visual Studio App Center, che è possibile usare per interagire con gli utenti delle applicazioni per dispositivi mobili.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 1717bf6da849218f60bae7dafca20cb304286976
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453137"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Interagire con gli utenti dell'applicazione inviando notifiche push

Che si tratti di creare applicazioni consumer o aziendali, si vuole che gli utenti usino attivamente l'applicazione. Spesso è necessario condividere le ultime notizie, gli aggiornamenti dei giochi, le offerte interessanti, gli aggiornamenti del prodotto o altre informazioni rilevanti per gli utenti. Per aumentare il coinvolgimento degli utenti e tornare all'applicazione, è necessario un modo per comunicare con gli utenti in tempo reale.

Le notifiche push consentono di comunicare con gli utenti dell'applicazione in modo rapido ed efficiente. È possibile contattare gli utenti al momento giusto e notificare loro le informazioni desiderate, in genere in un elemento popup o in una finestra di dialogo in un dispositivo mobile, indipendentemente dalle operazioni eseguite.

## <a name="value-of-push-notifications"></a>Valore delle notifiche push
Le notifiche push forniscono valore a utenti e aziende.

Le aziende possono:
- Comunicare direttamente con gli utenti inviando messaggi sulle piattaforme supportate al momento giusto.
- Incrementare il coinvolgimento degli utenti inviando gli aggiornamenti in tempo reale e i promemoria agli utenti, incoraggiandoli a interagire con l'applicazione a intervalli regolari.
- Mantenere gli utenti e riinteragire con gli utenti inattivi che hanno scaricato l'applicazione, ma non usarla per riattivare l'applicazione.
- Aumenta i tassi di conversione analizzando il comportamento degli utenti, segmentando gli utenti e sfruttando le campagne basate sulle notifiche push per dispositivi mobili.
- Promuovi i prodotti e i servizi inviando messaggi push e aggiornamenti tempestivi agli utenti.
- Indirizzare gli utenti inviando messaggi push personalizzati.

Per gli utenti dell'applicazione, notifiche push:
- Fornire valore e informazioni in tempo reale.
- Ricordare agli utenti di usare l'applicazione.

Usare i servizi seguenti per abilitare le notifiche push nelle app per dispositivi mobili.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Con [App Center push](/appcenter/push/)è possibile inviare messaggi mirati agli utenti di iOS, Android e Windows senza dover gestire il processo di invio delle notifiche ai dispositivi usando i servizi di notifica push (PNS). Basato su Hub di notifica di Azure, questo servizio Elimina le complessità associate al push manuale delle notifiche fornendo un dashboard potente.

**Caratteristiche principali**
- Invia notifiche push ai dispositivi mobili in un'ampia gamma di piattaforme.
- Usare le notifiche per inviare dati a un'applicazione, visualizzare un messaggio all'utente o attivare un'azione da parte dell'applicazione.
- USA destinazioni di notifica per: 
    - Trasmettere messaggi a tutti i dispositivi registrati.
    - Inviare notifiche a gruppi di destinatari in base alle informazioni sul dispositivo e alle proprietà personalizzate.
    - Inviare notifiche a utenti specifici.
    - Inviare notifiche a dispositivi specifici.
- Usare i dati di telemetria avanzati su push, dispositivi ed errori disponibili nel portale di App Center.
- Ottieni supporto della piattaforma per iOS, Android, macOS, Novell, React native, Unity e Cordova.

**Riferimenti**
- [Iscriversi con Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introduzione a App Center push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Hub di notifica di Azure
[Hub di notifica](/azure/notification-hubs/notification-hubs-push-notification-overview) offre un motore push di facile utilizzo e con scalabilità orizzontale. È possibile usarlo per inviare notifiche a qualsiasi piattaforma e da qualsiasi back-end nel cloud o in locale.

**Caratteristiche principali**
- Invia notifiche push a qualsiasi piattaforma da qualsiasi back-end, nel cloud o in locale.
- Trasmissione veloce push a milioni di dispositivi mobili con una singola chiamata API.
- Personalizzare le notifiche push per cliente, lingua e località.
- Definire e notificare dinamicamente i segmenti dei clienti.
- Ridimensiona immediatamente a milioni di dispositivi mobili.
- Ottieni supporto della piattaforma per iOS, Android, Windows, Kindle e Baidu.
        
**Riferimenti**
- [Azure portal](https://portal.azure.com) 
- [Introduzione ad Hub di notifica di Azure](/azure/notification-hubs/)
- [Guide introduttive](/azure/notification-hubs/create-notification-hub-portal)
- [Esempi](/azure/notification-hubs/samples)
