---
title: "Il proxy dell'applicazione di Azure AD: Cronologia del rilascio delle versioni | Microsoft Docs"
description: Questo articolo elenca tutte le versioni di Azure AD proxy di applicazione e descrive le nuove funzionalità e i problemi risolti
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
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693907"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Il proxy dell'applicazione di Azure AD: Cronologia delle versioni
Questo articolo elenca le versioni e le funzionalità del proxy di applicazione Azure Active Directory (Azure AD) rilasciate. Il team di Azure AD aggiorna regolarmente il proxy di applicazione con nuove caratteristiche e funzionalità. I connettori del proxy di applicazione vengono aggiornati automaticamente quando viene rilasciata una nuova versione. 

Si consiglia di verificare che gli aggiornamenti automatici siano abilitati per i connettori per assicurarsi di disporre delle funzionalità e delle correzioni di bug più recenti. Microsoft fornisce supporto diretto per la versione più recente del connettore e una versione precedente a.

Di seguito è riportato un elenco di risorse correlate:

Risorsa |  Dettagli
--------- | --------- |
Come abilitare il proxy di applicazione | In questa [esercitazione](application-proxy-add-on-premises-application.md)vengono descritti i prerequisiti per l'abilitazione del proxy di applicazione e l'installazione e la registrazione di un connettore.
Comprendere i connettori del proxy applicazione Azure AD | Scopri di più sulla [gestione dei](application-proxy-connectors.md) connettori e sull' [aggiornamento automatico](application-proxy-connectors.md#automatic-updates)dei connettori.
Download del connettore del proxy di applicazione Azure AD |  [Scaricare il connettore più recente](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Stato della versione

20 settembre 2018: resa disponibile per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

- Aggiunto il supporto WebSocket per l'applicazione Qlik Sense. Per ulteriori informazioni sull'integrazione di Qlik Sense con il proxy di applicazione, vedere questa [procedura dettagliata](application-proxy-qlik.md). 
- È stata migliorata l'installazione guidata per semplificare la configurazione di un proxy in uscita. 
- Impostare TLS 1,2 come protocollo predefinito per i connettori. 
- È stato aggiunto un nuovo contratto di licenza con l'utente finale (EULA).  

### <a name="fixed-issues"></a>Problemi risolti

- Correzione di un bug che causava alcune perdite di memoria nel connettore.
- Aggiornamento della versione del bus di servizio di Azure, che include una correzione di bug per i problemi di timeout del connettore.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Stato della versione

19 gennaio 2018: resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti

- Aggiunta del supporto per i domini personalizzati che richiedono la conversione del dominio nel cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Stato della versione 

25 maggio 2017: resa disponibile per il download 

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 

Miglioramento del controllo sui limiti delle connessioni in uscita dei connettori. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Stato della versione

15 aprile 2017: resa disponibile per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

- Gestione e gestione semplificate con meno porte obbligatorie. Il proxy dell'applicazione ora richiede l'apertura solo di due porte in uscita standard: 443 e 80. Il proxy dell'applicazione continua a usare solo le connessioni in uscita, pertanto non è ancora necessario alcun componente in una rete perimetrale. Per informazioni dettagliate, vedere la [documentazione di configurazione](application-proxy-add-on-premises-application.md).  
- Se supportato dal proxy esterno o dal firewall, è ora possibile aprire la rete in base al DNS anziché all'intervallo IP. I servizi proxy di applicazione richiedono solo connessioni a *. msappproxy.net e *. servicebus.windows.net.


## <a name="earlier-versions"></a>Versioni precedenti

Se si usa una versione del connettore del proxy di applicazione precedente a 1.5.36.0, eseguire l'aggiornamento alla versione più recente per assicurarsi di disporre delle funzionalità più recenti completamente supportate.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull' [accesso remoto alle applicazioni locali tramite Azure ad proxy di applicazione](application-proxy.md).
- Per iniziare a usare Application Proxy, vedere [Esercitazione: Aggiungere un'applicazione locale per l'accesso remoto tramite Application Proxy](application-proxy-add-on-premises-application.md).
