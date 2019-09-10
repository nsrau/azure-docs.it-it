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
ms.openlocfilehash: de6ef5dedb0779536cfbc1e6a3440e748ff78524
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862109"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agente di provisioning di Azure AD Connect: Cronologia delle versioni
Questo articolo elenca le versioni e le funzionalità di Azure AD Connect agente di provisioning che sono state rilasciate. Il team di Azure AD aggiorna periodicamente l'agente di provisioning con nuove caratteristiche e funzionalità. Gli agenti di provisioning vengono aggiornati automaticamente quando viene rilasciata una nuova versione. 

È consigliabile abilitare l'aggiornamento automatico per gli agenti per assicurarsi di disporre delle funzionalità e delle correzioni di bug più recenti. Microsoft fornisce supporto diretto per la versione più recente dell'agente e per una versione precedente.

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Stato della versione

9 settembre 2019: Rilasciato per l'aggiornamento automatico

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Possibilità di configurare la traccia e la registrazione aggiuntive per il debug dei problemi dell'agente di provisioning
* Possibilità di recuperare solo gli attributi di Active Directory configurati nel mapping per migliorare le prestazioni di sincronizzazione

### <a name="fixed-issues"></a>Problemi risolti

* Correzione di un bug per cui l'agente si trovava in uno stato che non risponde se si sono verificati problemi relativi AD errori di connessione AD
* Correzione di un bug che causava problemi durante la lettura dei dati binari da Active Directory
* Correzione di un bug per cui l'agente non è riuscito a rinnovare l'attendibilità con il servizio cloud Hybrid Identity

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Stato della versione

23 gennaio 2019: resa disponibile per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Revamping Agent & l'architettura del connettore per ottenere prestazioni, stabilità e affidabilità migliori 
* Configurazione semplificata dell'agente di provisioning tramite l'installazione guidata basata sull'interfaccia utente 
* Aggiunta del supporto per aggiornamenti automatici degli agenti

