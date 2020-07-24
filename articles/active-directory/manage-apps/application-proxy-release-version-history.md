---
title: "Proxy dell'applicazione Azure AD: cronologia delle versioni | Microsoft Docs"
description: Questo articolo elenca tutte le versioni di Azure AD proxy di applicazione e descrive le nuove funzionalità e i problemi risolti
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/22/2020
ms.subservice: app-mgmt
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042509240eb2b88446d3ac1956d9056d5c39dfc8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019387"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Proxy dell'applicazione Azure AD: cronologia delle versioni
Questo articolo elenca le versioni e le funzionalità del proxy di applicazione Azure Active Directory (Azure AD) rilasciate. Il team di Azure AD aggiorna regolarmente il proxy di applicazione con nuove caratteristiche e funzionalità. I connettori del proxy di applicazione vengono aggiornati automaticamente quando viene rilasciata una nuova versione. 

Si consiglia di verificare che gli aggiornamenti automatici siano abilitati per i connettori per assicurarsi di disporre delle funzionalità e delle correzioni di bug più recenti. Microsoft fornisce supporto diretto per la versione più recente del connettore e una versione precedente a.

Di seguito è riportato un elenco di risorse correlate:

Resource |  Dettagli
--------- | --------- |
Come abilitare il proxy di applicazione | In questa [esercitazione](application-proxy-add-on-premises-application.md)vengono descritti i prerequisiti per l'abilitazione del proxy di applicazione e l'installazione e la registrazione di un connettore.
Comprendere i connettori del proxy applicazione Azure AD | Scopri di più sulla [gestione dei](application-proxy-connectors.md) connettori e sull' [aggiornamento automatico](application-proxy-connectors.md#automatic-updates)dei connettori.
Download del connettore del proxy di applicazione Azure AD |  [Scaricare il connettore più recente](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="1519750"></a>1.5.1975.0

### <a name="release-status"></a>Stato della versione

22 luglio 2020: rilasciata per il download questa versione è disponibile solo per l'installazione tramite la pagina di download. Una versione di aggiornamento automatico di questa versione verrà rilasciata in un secondo momento.

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità
-   Supporto migliorato per gli ambienti cloud di Azure per enti pubblici. Per la procedura di installazione corretta del connettore per il cloud di Azure per enti pubblici, vedere i [prerequisiti](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-government-cloud#allow-access-to-urls) e le [procedure di installazione](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-government-cloud#install-the-agent-for-the-azure-government-cloud).
- Supporto per l'uso del client Web di Servizi Desktop remoto con il proxy di applicazione. Per altri dettagli, vedere [pubblicare desktop remoto con Azure ad proxy di applicazione](application-proxy-integrate-with-remote-desktop-services.md) .
- Negoziazione migliorata dell'estensione WebSocket. 

### <a name="fixed-issues"></a>Problemi risolti
- Correzione di un problema WebSocket che forzava le stringhe minuscole.
- È stato risolto un problema che causava un occasionale mancato risposta ai connettori.

## <a name="1516260"></a>1.5.1626.0

### <a name="release-status"></a>Stato della versione

17 luglio 2020: rilasciato per il download. Questa versione è disponibile solo per l'installazione tramite la pagina di download. Una versione di aggiornamento automatico di questa versione verrà rilasciata in un secondo momento.

### <a name="fixed-issues"></a>Problemi risolti
- Problema di perdita di memoria risolto presente nella versione precedente
- Miglioramenti generali per il supporto di WebSocket

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>Stato della versione

07 aprile 2020: rilasciata per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità
-   I connettori usano solo TLS 1,2 per tutte le connessioni. Per altri dettagli, vedere [prerequisiti del connettore](application-proxy-add-on-premises-application.md#before-you-begin) .
- Segnalazione migliorata tra il connettore e i servizi di Azure. Questo include il supporto di sessioni affidabili per la comunicazione WCF tra il connettore e i servizi di Azure e i miglioramenti della cache DNS per le comunicazioni WebSocket.
- Supporto per la configurazione di un proxy tra il connettore e l'applicazione back-end. Per altre informazioni, vedere [usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="fixed-issues"></a>Problemi risolti
- Rimosso il fallback alla porta 8080 per le comunicazioni dal connettore ai servizi di Azure.
- Sono state aggiunte tracce di debug per le comunicazioni WebSocket. 
- È stata risolta la conservazione dell'attributo navigava sullostesso sito quando è impostata su cookie dell'applicazione back-end.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Stato della versione

20 settembre 2018: rilasciato per il download

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

19 gennaio 2018: rilasciato per il download

### <a name="fixed-issues"></a>Problemi risolti

- Aggiunta del supporto per i domini personalizzati che richiedono la conversione del dominio nel cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Stato della versione 

25 maggio 2017: rilasciato per il download 

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 

Miglioramento del controllo sui limiti delle connessioni in uscita dei connettori. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Stato della versione

15 aprile 2017: rilasciato per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

- Gestione e gestione semplificate con meno porte obbligatorie. Il proxy di applicazione ora richiede l'apertura solo di due porte in uscita standard: 443 e 80. Il proxy dell'applicazione continua a usare solo le connessioni in uscita, pertanto non è ancora necessario alcun componente in una rete perimetrale. Per informazioni dettagliate, vedere la [documentazione di configurazione](application-proxy-add-on-premises-application.md).  
- Se supportato dal proxy esterno o dal firewall, è ora possibile aprire la rete in base al DNS anziché all'intervallo IP. I servizi proxy di applicazione richiedono solo connessioni a *. msappproxy.net e *. servicebus.windows.net.


## <a name="earlier-versions"></a>Versioni precedenti

Se si usa una versione del connettore del proxy di applicazione precedente a 1.5.36.0, eseguire l'aggiornamento alla versione più recente per assicurarsi di disporre delle funzionalità più recenti completamente supportate.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull' [accesso remoto alle applicazioni locali tramite Azure ad proxy di applicazione](application-proxy.md).
- Per iniziare a usare il proxy di applicazione, vedere [esercitazione: aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione](application-proxy-add-on-premises-application.md).
