---
title: Che cosa sono i dispositivi Azure AD registrati?
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67462759"
---
# <a name="azure-ad-registered-devices"></a>Dispositivi registrati in Azure AD

L'obiettivo di Azure AD dispositivi registrati è fornire agli utenti il supporto per gli scenari BYOD (Bring Your Own Device) o per dispositivi mobili. In questi scenari, un utente può accedere alle risorse controllate Azure Active Directory dell'organizzazione usando un dispositivo personale.

|   | Azure AD registrato |
| --- | --- |
| **Definizione** | Registrato per Azure AD senza richiedere all'account aziendale di accedere al dispositivo |
| **Destinatari primari** | Applicabile a tutti gli utenti con i criteri seguenti: |
|   | Bring Your Own Device (BYOD) |
|   | Dispositivi mobili |
| **Proprietà del dispositivo** | Utente o organizzazione |
| **Sistemi operativi** | Windows 10, iOS, Android e MacOS |
| **Provisioning** | Windows 10-impostazioni |
|   | iOS/Android: app Portale aziendale o Microsoft Authenticator |
|   | MacOS-Portale aziendale |
| **Opzioni di accesso del dispositivo** | Credenziali locali dell'utente finale |
|   | Password |
|   | Windows Hello |
|   | PIN |
|   | Biometria o modello per altri dispositivi |
| **Gestione dei dispositivi** | Gestione dei dispositivi mobili (ad esempio: Microsoft Intune) |
|   | gestione di applicazioni mobili |
| **Funzionalità principali** | Da SSO a risorse cloud |
|   | Accesso condizionale quando viene registrato in Intune |
|   | Accesso condizionale tramite criteri di protezione delle app |
|   | Abilita l'accesso tramite telefono con Microsoft Authenticator app |

![Dispositivi registrati in Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD dispositivi registrati hanno eseguito l'accesso con un account locale come un account Microsoft in un dispositivo Windows 10, ma hanno anche un account Azure AD collegato per l'accesso alle risorse dell'organizzazione. L'accesso alle risorse dell'organizzazione può essere ulteriormente limitato in base al Azure AD account e ai criteri di accesso condizionale applicati all'identità del dispositivo.

Gli amministratori possono proteggere e controllare ulteriormente questi Azure AD dispositivi registrati usando strumenti di gestione di dispositivi mobili (MDM) come Microsoft Intune. MDM fornisce un mezzo per applicare le configurazioni necessarie per l'organizzazione, ad esempio la necessità di crittografare l'archiviazione, la complessità della password e il software di sicurezza mantenuto aggiornato. 

Azure AD registrazione può essere eseguita quando si accede a un'applicazione di lavoro per la prima volta o manualmente usando il menu impostazioni di Windows 10. 

## <a name="scenarios"></a>Scenari

Un utente dell'organizzazione desidera accedere agli strumenti per la posta elettronica, segnalare il timeout e i vantaggi della registrazione dal PC di casa. L'organizzazione dispone di questi strumenti dietro un criterio di accesso condizionale che richiede l'accesso da un dispositivo compatibile con Intune. L'utente aggiunge il proprio account aziendale e registra il PC di casa con Azure AD e i criteri di Intune necessari vengono applicati per concedere all'utente l'accesso alle risorse.

Un altro utente vuole accedere alla posta elettronica aziendale sul proprio telefono Android che ha avuto una radice. La società richiede un dispositivo conforme e ha creato un criterio di conformità di Intune per bloccare tutti i dispositivi rooted. Il dipendente viene arrestato dall'accesso alle risorse dell'organizzazione in questo dispositivo.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire le identità dei dispositivi usando il portale di Azure](device-management-azure-portal.md)
- [Gestire i dispositivi non aggiornati in Azure AD](manage-stale-devices.md)
