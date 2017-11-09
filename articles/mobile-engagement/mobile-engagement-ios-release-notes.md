---
title: Note sulla versione di Azure Mobile Engagement iOS SDK | Documentazione Microsoft
description: Ultimi aggiornamenti e procedure relativi a iOS SDK per Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a43ff0f6-90d5-4b3c-8d7a-a1db21bc776b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 9bdaa57f9902373ccf796ff109332b64c66bf9e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Note sulla versione di Azure Mobile Engagement iOS SDK

## <a name="410-07172017"></a>4.1.0 (07/17/2017)
* Risolto il problema delle notifiche cancellate in background.
* Risolto il problema degli avvisi in XCode 9 sulle API non chiamate nella coda principale.
* Risolto un problema di memoria nei poll di copertura.
* Eliminazione del supporto per iOS 6.X. A partire da questa versione, la destinazione della distribuzione dell'applicazione deve essere almeno iOS 7.

## <a name="401-12132016"></a>4.0.1 (12/13/2016)
* Recapito del log migliorato in background.

## <a name="400-09122016"></a>4.0.0 (12/09/2016)
* Risolto il problema delle notifiche non prese in considerazione sui dispositivi iOS 10.
* Deprecare XCode 7.

## <a name="324-06302016"></a>3.2.4 (30/06/2016)
* Aggregazione fissa tra log tecnici e altri log.

## <a name="323-06072016"></a>3.2.3 (07/06/2016)
* Correzione del bug che causava la mancata segnalazione del feedback di recapito con l'applicazione in background.
* Ottimizzazione dell’invio di log tecnici.

## <a name="322-04072016"></a>3.2.2 (07/04/2016)
* Correzione del bug sull'annullamento della richiesta HTTP che a volte provoca l'arresto anomalo del sistema.

## <a name="321-12112015"></a>3.2.1 (12/11/2015)
* Correzione del ritardo durante l’attivazione di una nuova istanza dell’app tramite notifica con collegamenti diretti

## <a name="320-10082015"></a>3.2.0 (10/08/2015)
* Abilitato Bitcode nel SDK per utilizzarlo con **Xcode 7**.
* Bug corretti relativi a notifiche all'interno dell'applicazione.
* Apportate le notifiche all'interno dell'app più affidabili in caso di batteria scarica e altri scenari.
* Rimossi i registri aggiuntivi della console generati dalla libreria di terze parti 3.

## <a name="310-08262015"></a>3.1.0 (08/26/2015)
* Correzione del bug di compatibilità di iOS 9 con una libreria di terze parti. Provocava arresti anomali durante l'invio del polling dei risultati, di informazioni sull'applicazione o di dati aggiuntivi.

## <a name="300-06192015"></a>3.0.0 (19/06/2015)
* Mobile Enagement usa le notifiche push Silent.
* Eliminazione del supporto per iOS 4.X. A partire da questa versione, la destinazione della distribuzione dell'applicazione deve essere almeno iOS 6.

## <a name="220-05212015"></a>2.2.0 (21/05/2015)
* L'ID del dispositivo di Mobile Engagement per dispositivi < iOS 6 è ora basato su un GUID generato in fase di installazione.

## <a name="210-04242015"></a>2.1.0 (24/04/2015)
* È stata aggiunta la compatibilità per Swift.
* Quando si fa clic su una notifica, l'URL dell'azione viene ora eseguito subito dopo l'apertura dell'applicazione.
* È stato aggiunto il file di intestazione mancante nel pacchetto dell'SDK.
* È stato risolto un problema relativo alla disabilitazione del reporter di arresto anomalo di Mobile Engagement.

## <a name="200-02172015"></a>2.0.0 (17/02/2015)
* Versione iniziale di Azure Mobile Engagement
* La configurazione di appId/sdkKey viene sostituita da una configurazione della stringa di connessione.
* L'API non invia e non riceve più messaggi XMPP da entità XMPP arbitrarie.
* L'API non riceve e non invia più messaggi tra i dispositivi.
* Sono stati introdotti miglioramenti per la sicurezza.
* È stato eliminato il rilevamento di SmartAd.
