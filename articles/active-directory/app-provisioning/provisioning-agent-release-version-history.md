---
title: 'Agente di provisioning di Azure AD Connect: cronologia delle versioni | Microsoft Docs'
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
ms.date: 02/04/2020
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: cac9eac2d17fa0a4f1db487d4c7f8beb67de24a9
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066343"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agente di provisioning di Azure AD Connect: cronologia delle versioni
Questo articolo elenca le versioni e le funzionalità di Azure Active Directory Connect agente di provisioning che sono state rilasciate. Il team di Azure AD aggiorna periodicamente l'agente di provisioning con nuove caratteristiche e funzionalità. L'agente di provisioning viene aggiornato automaticamente quando viene rilasciata una nuova versione. 

È consigliabile abilitare l'aggiornamento automatico per gli agenti per assicurarsi di disporre delle funzionalità e delle correzioni di bug più recenti. Microsoft fornisce supporto diretto per la versione più recente dell'agente e per una versione precedente.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Stato della versione

4 dicembre 2019: rilasciato per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Include il supporto per il [provisioning del cloud Azure ad Connect](../cloud-provisioning/what-is-cloud-provisioning.md) per sincronizzare gli utenti, i contatti e i dati di gruppo dall'Active Directory locale al Azure ad


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Stato della versione

9 settembre 2019: rilasciata per l'aggiornamento automatico

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Possibilità di configurare la traccia e la registrazione aggiuntive per il debug dei problemi dell'agente di provisioning
* Possibilità di recuperare solo gli attributi Azure AD configurati nel mapping per migliorare le prestazioni di sincronizzazione

### <a name="fixed-issues"></a>Problemi risolti

* Correzione di un bug per cui l'agente si trovava in uno stato che non risponde se si sono verificati problemi con Azure AD errori di connessione
* Correzione di un bug che causava problemi durante la lettura dei dati binari da Azure Active Directory
* Correzione di un bug per cui l'agente non è riuscito a rinnovare l'attendibilità con il servizio di identità cloud ibrido

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Stato della versione

23 gennaio 2019: rilasciato per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Rinnovato l'agente di provisioning e l'architettura del connettore per ottenere prestazioni, stabilità e affidabilità migliori 
* Semplificazione della configurazione dell'agente di provisioning tramite l'installazione guidata guidata dall'interfaccia utente 
* Aggiunta del supporto per aggiornamenti automatici degli agenti

