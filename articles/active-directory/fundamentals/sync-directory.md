---
title: Sincronizzazione della directory con Azure Active Directory
description: Indicazioni sull'architettura per ottenere la sincronizzazione della directory con Azure Active Directory.
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
ms.openlocfilehash: 748f91b2fe77667969e9736f8084a9dd24018425
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172470"
---
# <a name="directory-synchronization"></a>Sincronizzazione delle directory

Molte organizzazioni hanno un'infrastruttura ibrida che comprende sia componenti locali che cloud. La sincronizzazione delle identità degli utenti tra directory locali e cloud consente agli utenti di accedere alle risorse con un unico set di credenziali. 

La sincronizzazione è il processo di 

* creazione di un oggetto in base a determinate condizioni
* aggiornamento dell'oggetto
* rimuovere l'oggetto quando le condizioni non vengono più soddisfatte. 

Il provisioning locale prevede il provisioning da origini locali (ad esempio Active Directory) a Azure Active Directory (Azure AD). 

## <a name="use-when"></a>Casi di utilizzo

Per Azure AD è necessario sincronizzare i dati di identità dagli ambienti Active Directory locali.

![diagramma dell'architettura](./media/authentication-patterns/dir-sync-auth.png)

## <a name="components-of-system"></a>Componenti del sistema

* **Utente**: accede a un'applicazione usando Azure ad.

* **Web browser**: il componente con cui l'utente interagisce per accedere all'URL esterno dell'applicazione.

* **Applicazione**: app Web che si basa sull'uso di Azure ad per scopi di autenticazione e autorizzazione.

* **Azure ad**: Sincronizza le informazioni di identità dalla directory locale dell'organizzazione tramite Azure ad Connect. 

* **Azure ad Connect**: uno strumento per la connessione di infrastrutture di identità locali a Microsoft Azure ad. La procedura guidata e le esperienze guidate consentono di distribuire e configurare i prerequisiti e i componenti necessari per la connessione, inclusa la sincronizzazione e l'accesso da Active Directory al Azure AD. 

* **Active Directory**: Active Directory è un servizio directory incluso nella maggior parte dei sistemi operativi Windows Server. I server che eseguono Active Directory Domain Services (AD DS) sono denominati controller di dominio. Autenticano e autorizzano tutti gli utenti e i computer nel dominio.

## <a name="implement-directory-synchronization-with-azure-ad"></a>Implementare la sincronizzazione della directory con Azure AD

* [Che cos'è il provisioning di identità?](../cloud-provisioning/what-is-provisioning.md) 

* [Strumenti di integrazione di directory di identità ibride](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Guida di orientamento per l'installazione Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md)