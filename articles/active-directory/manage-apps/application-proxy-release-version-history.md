---
title: "Il proxy dell'applicazione di Azure AD: Cronologia del rilascio delle versioni | Microsoft Docs"
description: Questo articolo elenca tutte le versioni di Azure AD Application Proxy e descrive le nuove funzionalità e problemi risolti
services: active-directory
documentationcenter: ''
author: msmimart
manager: celested
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: manage-apps
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ab280b4647c0e8eb7083797ec00965cbaec6f8e
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502339"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Il proxy dell'applicazione di Azure AD: Cronologia delle versioni
Questo articolo elenca le versioni e le funzionalità del Proxy dell'applicazione Azure Active Directory (Azure AD) che sono state rilasciate. Il team di Azure AD aggiorna regolarmente il Proxy di applicazione con nuove caratteristiche e funzionalità. I connettori del Proxy dell'applicazione vengono aggiornati automaticamente quando viene rilasciata una nuova versione.

Ecco un elenco di risorse correlate:

Risorsa |  Dettagli
--------- | --------- |
Come abilitare il Proxy di applicazione | Prerequisiti per l'abilitazione del Proxy di applicazione e l'installazione e la registrazione di un connettore sono descritte in questo [esercitazione](application-proxy-add-on-premises-application.md).
Comprendere i connettori del proxy applicazione Azure AD | Altre informazioni sulle [gestione connettori](application-proxy-connectors.md) e come connettori di aggiornamento automatico.
Azure AD Application Proxy Connector Download |  [Scaricare il connettore più recente](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Stato della versione

20 settembre 2018: resa disponibile per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

- Aggiunta del supporto di WebSocket per l'applicazione qlik Sense. Per altre informazioni su come integrare qlik Sense con Proxy dell'applicazione, vedere questo [walkthrough](application-proxy-qlik.md). 
- Migliorata l'installazione guidata per renderne più semplice configurare un proxy in uscita. 
- Impostare TLS 1.2 come il protocollo predefinito per i connettori. 
- Aggiungere un nuovo contratto (licenza).  

### <a name="fixed-issues"></a>Problemi risolti

- Risolto un bug che ha causato alcuni problemi di memoria nel connettore.
- Aggiornata la versione del Bus di servizio di Azure, che include una correzione di bug per problemi di timeout del connettore.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Stato della versione

19 gennaio 2018: resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti

- Aggiunta del supporto per domini personalizzati che richiedono la traduzione di dominio nel cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Stato della versione 

25 maggio 2017: resa disponibile per il download 

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 

Controllo migliorato dei limiti di connessione in uscita dei connettori. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Stato della versione

15 aprile 2017: resa disponibile per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

- Processo di onboarding semplificato e la gestione con un minor numero di porte necessarie. Il Proxy di applicazione richiede ora l'apertura solo due porte in uscita standard: 443 e 80. Il Proxy di applicazione continua a usare le connessioni solo in uscita, in modo non occorre comunque tutti i componenti in una rete Perimetrale. Per informazioni dettagliate, vedere la [documentazione relativa alla configurazione](application-proxy-add-on-premises-application.md).  
- Se supportato dal firewall o proxy esterni, è ora possibile aprire la rete da DNS invece di intervallo di indirizzi IP. Servizi Proxy dell'applicazione richiedono connessioni a *. msappproxy.net e *. servicebus.windows.net solo.


## <a name="earlier-versions"></a>Versioni precedenti

Se si usa una versione del connettore Proxy di applicazione precedenti a 1.5.36.0, l'aggiornamento alla versione più recente per assicurarsi di aver la versione più recente funzionalità completamente supportate.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [di accesso remoto alle applicazioni locali tramite Azure AD Application Proxy](application-proxy.md).
- Per iniziare a usare Application Proxy, vedere [Esercitazione: Aggiungere un'applicazione locale per l'accesso remoto tramite Application Proxy](application-proxy-add-on-premises-application.md).