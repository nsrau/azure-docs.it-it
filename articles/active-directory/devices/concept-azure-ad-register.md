---
title: Dispositivi registrati in che cosa sono Azure AD?
description: Informazioni su come le identità dei dispositivi semplificano la gestione dei dispositivi che accedono alle risorse nell'ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462759"
---
# <a name="azure-ad-registered-devices"></a>Dispositivi registrati in Azure AD

L'obiettivo dei dispositivi registrati in Azure AD è fornire agli utenti con il supporto per il BYOD Bring Your Own Device () o gli scenari dei dispositivi mobili. In questi scenari, un utente può accedere alle risorse di Azure Active Directory controllata dell'organizzazione usando un dispositivo personale.

|   | Registrati in Azure AD |
| --- | --- |
| **Definizione** | Registrati in Azure AD senza la necessità di un account aziendale per accedere al dispositivo |
| **Destinatari principali** | Applicabile a tutti gli utenti con i criteri seguenti: |
|   | Portare il proprio dispositivo (BYOD) |
|   | Dispositivi mobili |
| **Proprietà del dispositivo** | Utente o l'organizzazione |
| **Sistemi operativi** | Windows 10, iOS, Android e MacOS |
| **Provisioning** | Windows 10: le impostazioni |
|   | iOS/Android – app Microsoft Authenticator o portale aziendale |
|   | Portale aziendale MacOS |
| **Opzioni di accesso dispositivo** | Credenziali locali dell'utente finale |
|   | Password |
|   | Windows Hello |
|   | PIN |
|   | La biometria o un modello per altri dispositivi |
| **Gestione del dispositivo** | Gestione dei dispositivi mobili (esempio: Microsoft Intune) |
|   | gestione di applicazioni mobili |
| **Funzionalità principali** | Accesso SSO alle risorse cloud |
|   | Accesso condizionale quando registrato in Intune |
|   | Accesso condizionale tramite criteri di protezione delle App |
|   | Consente l'accesso tramite telefono con app Microsoft Authenticator |

![Dispositivi registrati in Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

I dispositivi registrati AD Azure sono connessi con un account locale, ad esempio un account Microsoft in un dispositivo Windows 10, ma inoltre avere un account Azure AD collegato per l'accesso alle risorse dell'organizzazione. Accesso alle risorse dell'organizzazione può essere ulteriormente limitato basato sull'account Azure AD e i criteri di accesso condizionale applicati all'identità del dispositivo.

Gli amministratori possono proteggere e controllare ulteriormente questi dispositivi registrati in Azure AD usando strumenti di gestione dei dispositivi mobili (MDM) come Microsoft Intune. MDM offre un modo per applicare le configurazioni necessarie dell'organizzazione, ad esempio che richiedono archiviazione deve essere crittografato, la complessità delle password e software per la sicurezza mantenuti aggiornati. 

Registrazione con Azure AD può essere eseguita quando si accede a un'applicazione di lavoro per la prima volta o manualmente tramite il menu delle impostazioni di Windows 10. 

## <a name="scenarios"></a>Scenari

Un utente nell'organizzazione desidera accedere agli strumenti per la posta elettronica, reporting indisponibilità e registrazione di vantaggi dal loro PC di casa. L'organizzazione dispone di questi strumenti dietro a un criterio di accesso condizionale che richiede l'accesso da un dispositivo conforme con Intune. L'utente aggiunge l'account dell'organizzazione e registra il PC di casa con Azure AD e vengono applicati i criteri di Intune necessari concedere all'utente l'accesso alle risorse.

Un altro utente vuole accedere alla posta elettronica dell'organizzazione sul telefono Android personale che sono rooted. La società richiede un dispositivo conforme e creato un criterio di conformità di Intune per bloccare i dispositivi rooted. Il dipendente viene arrestato l'accesso alle risorse dell'organizzazione su questo dispositivo.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire le identità dei dispositivi nel portale di Azure](device-management-azure-portal.md)
- [Gestire i dispositivi non aggiornati in Azure AD](manage-stale-devices.md)
