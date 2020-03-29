---
title: 'Proxy di applicazione di Azure AD: cronologia delle versioni Documenti Microsoft'
description: Questo articolo elenca tutte le versioni del proxy di applicazione di Azure AD e descrive le nuove funzionalità e i problemi risolti
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9ecd193282ed9b7333df44689530b4d057ad7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68693907"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Proxy di applicazione di Azure AD: cronologia delle versioniAzure AD Application Proxy: Version release history
Questo articolo elenca le versioni e le funzionalità del proxy di applicazione di Azure Active Directory (Azure AD) rilasciate. Il team di Azure AD aggiorna regolarmente il proxy di applicazione con nuove funzionalità e funzionalità. I connettori proxy di applicazione vengono aggiornati automaticamente quando viene rilasciata una nuova versione. 

È consigliabile assicurarsi che gli aggiornamenti automatici siano abilitati per i connettori per assicurarsi di disporre delle funzionalità e delle correzioni di bug più recenti. Microsoft fornisce supporto diretto per la versione del connettore più recente e una versione precedente.

Di seguito è riportato un elenco delle risorse correlate:Here is a list of related resources:

Risorsa |  Dettagli
--------- | --------- |
Come abilitare il proxy di applicazione | I prerequisiti per l'abilitazione del proxy di applicazione e l'installazione e la registrazione di un connettore sono descritti in questa [esercitazione.](application-proxy-add-on-premises-application.md)
Comprendere i connettori del proxy applicazione Azure AD | Ulteriori informazioni sulla gestione dei [connettori](application-proxy-connectors.md) e su come i connettori [vengono aggiornati automaticamente.](application-proxy-connectors.md#automatic-updates)
Azure AD Application Proxy Connector Download |  [Scaricare il connettore più recente](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Stato della versione

20 settembre 2018: Rilasciato per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

- Aggiunto il supporto WebSocket per l'applicazione QlikSense.Added WebSocket support for the QlikSense application. Per ulteriori informazioni sull'integrazione di QlikSense con il proxy di applicazione, vedere questa [procedura dettagliata](application-proxy-qlik.md). 
- Migliorata l'installazione guidata per semplificare la configurazione di un proxy in uscita. 
- Impostare TLS 1.2 come protocollo predefinito per i connettori. 
- Aggiunto un nuovo Contratto di Licenza con l'utente finale (EULA).  

### <a name="fixed-issues"></a>Problemi risolti

- Correzione di un bug che causava alcune perdite di memoria nel connettore.
- È stata aggiornata la versione del bus di servizio di Azure, che include una correzione di bug per i problemi di timeout del connettore.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Stato della versione

19 gennaio 2018: Rilasciato per il download

### <a name="fixed-issues"></a>Problemi risolti

- Aggiunto il supporto per i domini personalizzati che necessitano di traduzione del dominio nel cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Stato della versione 

25 maggio 2017: Rilasciato per il download 

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 

Migliorato il controllo sui limiti di connessione in uscita dei connettori. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Stato della versione

15 aprile 2017: Rilasciato per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

- Onboarding e gestione semplificati con meno porte necessarie. Il proxy di applicazione ora richiede solo l'apertura di due porte in uscita standard: 443 e 80. Il proxy di applicazione continua a utilizzare solo le connessioni in uscita, pertanto non sono ancora necessari componenti in una rete perimetrale. Per informazioni dettagliate, vedere la [documentazione relativa](application-proxy-add-on-premises-application.md)alla configurazione .  
- Se supportato dal proxy esterno o dal firewall, è ora possibile aprire la rete tramite DNS anziché l'intervallo IP. I servizi proxy di applicazione richiedono connessioni solo a i server msappproxy.net e servicebus.windows.net.


## <a name="earlier-versions"></a>Versioni precedenti

Se si utilizza una versione del connettore proxy di applicazione precedente alla 1.5.36.0, eseguire l'aggiornamento alla versione più recente per assicurarsi di disporre delle funzionalità completamente supportate più recenti.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni [sull'accesso remoto alle applicazioni locali tramite](application-proxy.md)il proxy di applicazione di Azure AD.
- Per iniziare a utilizzare il proxy di applicazione, vedere [Esercitazione: Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione](application-proxy-add-on-premises-application.md).
