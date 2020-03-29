---
title: Che cosa sono i dispositivi registrati di Azure AD?
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67462759"
---
# <a name="azure-ad-registered-devices"></a>Dispositivi registrati in Azure AD

L'obiettivo dei dispositivi registrati di Azure AD è fornire agli utenti il supporto per gli scenari BYOD (Bring Your Own Device) o per i dispositivi mobili. In questi scenari, un utente può accedere alle risorse controllate da Azure Active Directory dell'organizzazione usando un dispositivo personale.

|   | Azure AD registrato |
| --- | --- |
| **Definizione** | Registrato in Azure AD senza richiedere l'account dell'organizzazione per accedere al dispositivo |
| **Destinatari principali** | Applicabile a tutti gli utenti con i seguenti criteri: |
|   | Bring Your Own Device (BYOD) |
|   | Dispositivi mobili |
| **Proprietà del dispositivo** | Utente o organizzazione |
| **Sistemi operativi** | Windows 10, iOS, Android e MacOS |
| **Provisioning** | Windows 10 – Impostazioni |
|   | iOS/Android – Portale aziendale o app Microsoft Authenticator |
|   | MacOS – Portale aziendale |
| **Opzioni di accesso al dispositivo** | Credenziali locali dell'utente finaleEnd-user local credentials |
|   | Password |
|   | Windows Hello |
|   | PIN |
|   | Biometria o modello per altri dispositivi |
| **Gestione del dispositivo** | Gestione dei dispositivi mobili (esempio: Microsoft Intune)Mobile Device Management (example: Microsoft Intune) |
|   | gestione di applicazioni mobili |
| **Funzionalità principali** | SSO per le risorse cloud |
|   | Accesso condizionale quando è registrato in IntuneConditional Access when enrolled into Intune |
|   | Accesso condizionale tramite criteri di protezione delle appConditional Access via App protection policy |
|   | Consente l'accesso al telefono con l'app Microsoft Authenticator |

![Dispositivi registrati in Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

I dispositivi registrati di Azure AD hanno eseguito l'accesso all'uso di un account locale, ad esempio un account Microsoft in un dispositivo Windows 10, ma dispongono inoltre di un account Azure AD collegato per l'accesso alle risorse dell'organizzazione. L'accesso alle risorse nell'organizzazione può essere ulteriormente limitato in base all'account di Azure AD e ai criteri di accesso condizionale applicati all'identità del dispositivo.

Gli amministratori possono proteggere e controllare ulteriormente questi dispositivi registrati di Azure AD usando gli strumenti di gestione dei dispositivi mobili (MDM) come Microsoft Intune.Administrators can secure and further control these Azure AD registered devices using Mobile Device Management (MDM) tools like Microsoft Intune. MDM fornisce un mezzo per applicare le configurazioni richieste dall'organizzazione, ad esempio richiedere la crittografia dell'archiviazione, la complessità delle password e il software di sicurezza mantenuto aggiornato. 

La registrazione di Azure AD può essere eseguita quando si accede a un'applicazione di lavoro per la prima volta o manualmente usando il menu Impostazioni di Windows 10.Azure AD registration can be accomplished when accessing a work application for the first time or manually using the Windows 10 Settings menu. 

## <a name="scenarios"></a>Scenari

Un utente dell'organizzazione desidera accedere agli strumenti per la posta elettronica, la segnalazione delle intime e la registrazione dei vantaggi dal PC di casa. L'organizzazione dispone di questi strumenti dietro un criterio di accesso condizionale che richiede l'accesso da un dispositivo compatibile con Intune.Your organization has these tools behind a Conditional Access policy that requires access from an Intune compliant device. L'utente aggiunge l'account dell'organizzazione e registra il PC principale con Azure AD e i criteri di Intune necessari vengono applicati con l'accesso dell'utente alle risorse.

Un altro utente vuole accedere alla posta elettronica dell'organizzazione sul proprio telefono Android personale che è stato radicato. L'azienda richiede un dispositivo conforme e ha creato un criterio di conformità di Intune per bloccare tutti i dispositivi radice. Al dipendente viene impedito di accedere alle risorse dell'organizzazione in questo dispositivo.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire le identità dei dispositivi tramite il portale di AzureManage device identities using the Azure portal](device-management-azure-portal.md)
- [Gestire i dispositivi non aggiornati in Azure AD](manage-stale-devices.md)
