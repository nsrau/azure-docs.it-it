---
title: Servizi di Desktop remoto Gateway con Azure Active Directory
description: Indicazioni sull'architettura per il raggiungimento dei servizi del Gateway Desktop remoto con Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57466cccb71444d5711a9221c324a107757c5e82
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576796"
---
# <a name="remote-desktop-gateway-services"></a>Servizi del Gateway Desktop remoto

Una distribuzione di Servizi Desktop remoto standard (RDS) include diversi [servizi ruolo desktop remoto](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) in esecuzione in Windows Server. La distribuzione di Servizi Desktop remoto con il proxy di applicazione Azure Active Directory (Azure AD) dispone di una connessione in uscita permanente dal server che esegue il servizio del connettore. Altre distribuzioni lasciano le connessioni in ingresso aperte tramite un servizio di bilanciamento del carico. Questo modello di autenticazione consente di offrire più tipi di applicazioni pubblicando applicazioni locali tramite Servizi Desktop remoto. Consente inoltre di ridurre la superficie di attacco della distribuzione utilizzando Azure AD proxy di applicazione.

## <a name="use-when"></a>Casi di utilizzo

È necessario fornire l'accesso remoto e proteggere la distribuzione di Servizi Desktop remoto con la pre-autenticazione.

![diagramma dell'architettura](./media/authentication-patterns/rdp-auth.png)

## <a name="components-of-system"></a>Componenti del sistema

* **Utente** : accede a Servizi Desktop remoto serviti dal proxy di applicazione.

* **Web browser** : il componente con cui l'utente interagisce per accedere all'URL esterno dell'applicazione.

* **Azure ad** : autentica l'utente. 

* **Servizio proxy di applicazione** : funge da proxy inverso per l'inoltro della richiesta da parte dell'utente a Servizi Desktop remoto. Il proxy di applicazione può inoltre applicare eventuali criteri di accesso condizionale. 

* **Servizi Desktop remoto** : funge da piattaforma per le singole applicazioni virtualizzate, garantendo l'accesso protetto da desktop remoto e per dispositivi mobili e offrendo agli utenti finali la possibilità di eseguire le applicazioni e i desktop dal cloud. 

## <a name="implement-remote-desktop-gateway-services-with-azure-ad"></a>Implementare i servizi di Desktop remoto Gateway con Azure AD

* [Pubblicare desktop remoto con il proxy di applicazione Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services) 

* [Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
