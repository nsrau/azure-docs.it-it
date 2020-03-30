---
title: 'Agente di provisioning di Azure AD Connect: cronologia delle versioni Documenti Microsoft'
description: Questo articolo elenca tutte le versioni dell'agente di provisioning di Azure AD Connect e descrive le nuove funzionalità e i problemi risolti
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
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 559bca4f5020cebe06be7f24f7af5ec2e94bec0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183245"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agente di provisioning di Azure AD Connect: cronologia delle versioniAzure AD Connect Provisioning Agent: Version release history
Questo articolo elenca le versioni e le funzionalità dell'agente di provisioning di Azure Active Directory Connect che sono state rilasciate. Il team di Azure AD aggiorna regolarmente l'agente di provisioning con nuove funzionalità e funzionalità. L'agente di provisioning viene aggiornato automaticamente quando viene rilasciata una nuova versione. 

Microsoft fornisce supporto diretto per la versione più recente dell'agente e una versione precedente.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Stato della versione

4 dicembre 2019: Rilasciato per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Include il supporto per il provisioning cloud di [Azure AD Connect](../cloud-provisioning/what-is-cloud-provisioning.md) per sincronizzare i dati di utenti, contatti e gruppi da Active Directory locale ad Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Stato della versione

9 settembre 2019: Rilasciato per l'aggiornamento automatico

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Possibilità di configurare ulteriori problemi di traccia e registrazione per il debug dei problemi dell'agente di provisioning
* Possibilità di recuperare solo gli attributi di Azure AD configurati nel mapping per migliorare le prestazioni della sincronizzazione

### <a name="fixed-issues"></a>Problemi risolti

* Risolto un bug in cui l'agente è entrato in uno stato di mancata risposta in caso di problemi con gli errori di connessione di Azure ADFixed a bug where the agent went into an unresponsive state if there were issues with Azure AD connection failures
* Correzione di un bug che causava problemi durante la lettura di dati binari da Azure Active DirectoryFixed a bug that caused issues when binary data was read from Azure Active Directory
* Correzione di un bug in cui l'agente non è riuscito a rinnovare l'attendibilità con il servizio di identità ibrido cloudFixed a bug where the agent failed to renew trust with the cloud hybrid identity service

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Stato della versione

23 gennaio 2019: Rilasciato per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Rinnovato l'architettura dell'agente di provisioning e del connettore per migliorare le prestazioni, la stabilità e l'affidabilità 
* Semplificata la configurazione di Provisioning Agent utilizzando l'installazione guidata dell'interfaccia utente 
* Aggiunto il supporto per gli aggiornamenti automatici dell'agente

