---
title: Sincronizzazione LDAP con Azure Active Directory
description: Indicazioni sull'architettura per ottenere la sincronizzazione LDAP con Azure Active Directory.
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
ms.openlocfilehash: 1f34e734b315c7c05ce77f5e168a452fc1c1c547
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168679"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>Sincronizzazione LDAP con Azure Active Directory

LDAP (Lightweight Directory Access Protocol) è un protocollo di servizio directory che viene eseguito nello stack TCP/IP. Fornisce un meccanismo utilizzato per la connessione, la ricerca e la modifica delle directory Internet. Il servizio directory LDAP si basa su un modello client-server e la relativa funzione consente l'accesso a una directory esistente. Molte aziende dipendono da server LDAP locali per archiviare utenti e gruppi per le app aziendali critiche. 

Azure Active Directory (Azure AD) può sostituire la sincronizzazione LDAP con Azure AD Connect. Il servizio di sincronizzazione Azure AD Connect esegue tutte le operazioni correlate alla sincronizzazione dei dati di identità tra gli ambienti locali e Azure AD. 

## <a name="use-when"></a>Casi di utilizzo

È necessario sincronizzare i dati di identità tra le directory LDAP v3 locali e Azure AD. 

![diagramma dell'architettura](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>Componenti del sistema

* **Utente**: accede a un'applicazione che si basa sull'uso di una directory LDAP v3 per l'ordinamento di utenti e password.

* **Web browser**: il componente con cui l'utente interagisce per accedere all'URL esterno dell'applicazione

* **App Web**: applicazione con dipendenze da directory LDAP v3.

* **Azure ad**: Azure ad sincronizza le informazioni di identità (utenti, gruppi e password) dalle directory LDAP locali dell'organizzazione tramite Azure ad Connect. 

* **Azure ad Connect**: è uno strumento per la connessione di infrastrutture di identità locali a Microsoft Azure ad. La procedura guidata e le esperienze guidate consentono di distribuire e configurare i prerequisiti e i componenti necessari per la connessione. 

* **Connettore personalizzato**: un connettore Generic LDAP consente di integrare il servizio di sincronizzazione Azure ad Connect con un server LDAP v3. Si trova in Azure AD Connect.

* **Active Directory**: Active Directory è un servizio directory incluso nella maggior parte dei sistemi operativi Windows Server. I server che eseguono Active Directory servizi directory sono detti controller di dominio e autenticano e autorizzano tutti gli utenti e i computer in un dominio Windows.

* **Server LDAP v3**: directory conforme al protocollo LDAP che archivia utenti e password aziendali usati per l'autenticazione dei servizi directory.

## <a name="implement-ldap-synchronization-with-azure-ad"></a>Implementare la sincronizzazione LDAP con Azure AD

* [Strumenti di integrazione di directory di identità ibride](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Guida di orientamento per l'installazione Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md) 

* [Panoramica e creazione di un connettore LDAP](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > I connettori LDAP sono una configurazione avanzata che richiede una certa familiarità con Forefront Identity Manager e/o Microsoft Identity Manager. Se usato nell'ambiente di produzione, è consigliabile consultare le domande su questa configurazione per [supporto tecnico Premier](https://support.microsoft.com/premier) o Microsoft Partner Network.

