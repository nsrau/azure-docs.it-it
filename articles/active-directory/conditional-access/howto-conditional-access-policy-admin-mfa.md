---
title: Accesso condizionale - Richiedere l'autenticazione a più fattori per gli amministratori - Azure Active DirectoryConditional Access - Require MFA for administrators - Azure Active Directory
description: Creare criteri di accesso condizionale personalizzati per richiedere agli amministratori di eseguire l'autenticazione a più fattoriCreate a custom Conditional Access policy to require administrators to perform multi-factor authentication
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90e8a8b0926575b5a40a8c0ca7820e31827434ec
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755220"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Accesso condizionale: richiedere l'autenticazione a più fattori per gli amministratoriConditional Access: Require MFA for administrators

Gli account a cui sono assegnati diritti amministrativi sono presi di mira da utenti malintenzionati. La richiesta dell'autenticazione a più fattori (MFA) per tali account è un modo semplice per ridurre il rischio di compromissione di tali account.

Microsoft consiglia di richiedere l'autenticazione a più fattori per i ruoli seguenti almeno:

* Amministratore fatturazione
* Amministratore di accesso condizionale
* Amministratore di Exchange
* Amministratore globale
* Amministratore dell'helpdesk (password)
* Amministratore password
* Amministratore della sicurezza
* Amministratore di SharePoint
* Amministratore utenti

Le organizzazioni possono scegliere di includere o escludere i ruoli nel modo che ritiene opportuno.

## <a name="user-exclusions"></a>Esclusioni di utenti

I criteri di accesso condizionale sono strumenti potenti, è consigliabile escludere i seguenti account dai criteri:

* **Accesso di emergenza** o **account break-glass** per impedire il blocco degli account a livello di tenant. Nell'improbabile scenario in cui tutti gli amministratori sono bloccati dal tenant, l'account amministrativo di accesso di emergenza può essere usato per accedere al tenant per ripristinare l'accesso.
   * Altre informazioni sono disponibili nell'articolo Gestire gli account di [accesso di emergenza in Azure AD.](../users-groups-roles/directory-emergency-access.md)
* **Account di servizio** ed entità servizio, ad esempio l'account di sincronizzazione di Azure AD Connect.Service accounts and **service principals**, such as the Azure AD Connect Sync Account. Gli account di servizio sono account non interattivi che non sono legati ad alcun utente specifico. Vengono normalmente utilizzati dai servizi back-end che consentono l'accesso a livello di codice alle applicazioni, ma vengono utilizzati anche per accedere ai sistemi per scopi amministrativi. Gli account di servizio come questi devono essere esclusi poiché l'autenticazione a più fattori non può essere completata a livello di codice. Le chiamate effettuate dalle entità servizio non sono bloccate dall'accesso condizionale.
   * Se l'organizzazione dispone di questi account in uso negli script o nel codice, è consigliabile sostituirli con [identità gestite](../managed-identities-azure-resources/overview.md). Come soluzione temporanea, è possibile escludere questi account specifici dai criteri di base.

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

I passaggi seguenti consentono di creare un criterio di accesso condizionale per richiedere ai ruoli amministrativi assegnati di eseguire l'autenticazione a più fattori.

1. Accedere al **portale** di Azure come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare ad Accesso**condizionale**alla**sicurezza** > di **Azure Active Directory** > .
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni**selezionare **Utenti e gruppi**
   1. In **Includi**selezionare **Ruoli directory (anteprima)** e scegliere almeno i ruoli seguenti:
      * Amministratore dell'autenticazione
      * Amministratore fatturazione
      * Amministratore di accesso condizionale
      * Amministratore di Exchange
      * Amministratore globale
      * Amministratore dell'help desk
      * Amministratore password
      * Amministratore della sicurezza
      * Amministratore di SharePoint
      * Amministratore utenti
   1. In **Escludi**selezionare **Utenti e gruppi** e scegliere gli account di accesso di emergenza o di rottavetro dell'organizzazione. 
   1. Selezionare **Fatto**.
1. In **Includi app o azioni** > **Include**cloud selezionare Tutte le **app cloud**e **Infine**.
1. In **Condizioni** > **App client (anteprima)** impostare **Configura** su **Sì**e selezionare **Fatto**.
1. In **Controlli di** > accesso**Concedi**, selezionare **Concedi accesso**, Richiedi **autenticazione**a più fattori e selezionare **Seleziona**.
1. Confermare le impostazioni e **impostare Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare per abilitare i criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)

[Determinare l'impatto utilizzando la modalità solo report di accesso condizionaleDetermine impact using Conditional Access report-only](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso con lo strumento Elementi di ricerca condizionali](troubleshoot-conditional-access-what-if.md)
