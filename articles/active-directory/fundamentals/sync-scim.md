---
title: Sincronizzazione SCIM con Azure Active Directory
description: Indicazioni sull'architettura per ottenere la sincronizzazione SCIM con Azure Active Directory.
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
ms.openlocfilehash: b95aac504bc6ee72c353faecad25384e2dc90840
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172419"
---
# <a name="scim-synchronization-with-azure-active-directory"></a>Sincronizzazione SCIM con Azure Active Directory

System for Cross-Domain Identity Management (SCIM) è un protocollo standard aperto per l'automazione dello scambio di informazioni sull'identità degli utenti tra domini di identità e sistemi IT. SCIM garantisce che i dipendenti aggiunti al sistema gestione dei capitali umani (HCM) abbiano automaticamente account creati in Azure Active Directory (Azure AD) o Windows Server Active Directory. Gli attributi e i profili utente vengono sincronizzati tra i due sistemi, aggiornando la rimozione degli utenti in base allo stato utente o alla modifica del ruolo.

SCIM è una definizione standardizzata di due endpoint: un endpoint/Users e un endpoint/groups. Usa verbi REST comuni per creare, aggiornare ed eliminare oggetti. USA inoltre uno schema predefinito per attributi comuni come nome del gruppo, nome utente, nome, cognome e indirizzo di posta elettronica. Le applicazioni che offrono un'API REST SCIM 2,0 possono ridurre o eliminare il dolore dovuto all'uso di API o prodotti di gestione utenti proprietari. Ad esempio, qualsiasi client conforme a SCIM può eseguire un POST HTTP di un oggetto JSON nell'endpoint/Users per creare una nuova voce utente. Invece di richiedere un'API leggermente diversa per le stesse azioni di base, le app conformi allo standard SCIM possono sfruttare immediatamente i vantaggi del codice, dei client e degli strumenti preesistenti. 

## <a name="use-when"></a>Utilizzare quando: 

Si desidera eseguire automaticamente il provisioning delle informazioni utente da un sistema HCM a Azure AD e Windows Server Active Directory, quindi ai sistemi di destinazione, se necessario. 

![diagramma dell'architettura](./media/authentication-patterns/scim-auth.png)


## <a name="components-of-system"></a>Componenti del sistema 

* **Sistema HCM**: applicazioni e tecnologie che consentono processi e procedure di gestione del capitale umano che supportano e automatizzano i processi HR durante tutto il ciclo di vita dei dipendenti. 

* **Servizio di provisioning Azure ad**: usa il protocollo scim 2,0 per il provisioning automatico. Il servizio si connette all'endpoint SCIM per l'applicazione e usa lo schema dell'oggetto utente SCIM e le API REST per automatizzare il provisioning e il deprovisioning di utenti e gruppi.  

* **Azure ad**: repository utente usato per gestire il ciclo di vita delle identità e i rispettivi diritti. 

* **Sistema di destinazione**: applicazione o sistema con endpoint scim e compatibile con il provisioning di Azure ad per abilitare il provisioning automatico di utenti e gruppi.  

## <a name="implement-scim-with-azure-ad"></a>Implementare SCIM con Azure AD 

* [Funzionamento del provisioning in Azure AD ](../app-provisioning/how-provisioning-works.md)

* [Gestione del provisioning degli account utente per le app aziendali nel portale di Azure ](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Compilare un endpoint SCIM e configurare il provisioning degli utenti con Azure AD  ](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [SCIM 2,0-conformità del protocollo del servizio di provisioning Azure AD](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md)