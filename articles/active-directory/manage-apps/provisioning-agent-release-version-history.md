---
title: 'Agente di provisioning di Azure AD Connect: Cronologia del rilascio delle versioni | Microsoft Docs'
description: Questo articolo elenca tutte le versioni di Azure AD Connect agente di provisioning e descrive le nuove funzionalità e i problemi risolti
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ce9549765f6a912b3e95f99d11da20347b82ad8
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326457"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agente di provisioning di Azure AD Connect: Cronologia delle versioni
Questo articolo elenca le versioni e le funzionalità di Azure Active Directory Connect agente di provisioning che sono state rilasciate. Il team di Azure AD aggiorna periodicamente l'agente di provisioning con nuove caratteristiche e funzionalità. L'agente di provisioning viene aggiornato automaticamente quando viene rilasciata una nuova versione. 

È consigliabile abilitare l'aggiornamento automatico per gli agenti per assicurarsi di disporre delle funzionalità e delle correzioni di bug più recenti. Microsoft fornisce supporto diretto per la versione più recente dell'agente e per una versione precedente.

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Stato della versione

9 settembre 2019: Rilasciato per l'aggiornamento automatico

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Possibilità di configurare la traccia e la registrazione aggiuntive per il debug dei problemi dell'agente di provisioning
* Possibilità di recuperare solo gli attributi Azure AD configurati nel mapping per migliorare le prestazioni di sincronizzazione

### <a name="fixed-issues"></a>Problemi risolti

* Correzione di un bug per cui l'agente si trovava in uno stato che non risponde se si sono verificati problemi con Azure AD errori di connessione
* Correzione di un bug che causava problemi durante la lettura dei dati binari da Azure Active Directory
* Correzione di un bug per cui l'agente non è riuscito a rinnovare l'attendibilità con il servizio di identità cloud ibrido

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Stato della versione

23 gennaio 2019: resa disponibile per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Rinnovato l'agente di provisioning e l'architettura del connettore per ottenere prestazioni, stabilità e affidabilità migliori 
* Semplificazione della configurazione dell'agente di provisioning tramite l'installazione guidata guidata dall'interfaccia utente 
* Aggiunta del supporto per aggiornamenti automatici degli agenti

