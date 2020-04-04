---
title: Accesso condizionale - Richiedere l'autenticazione a più fattori per la gestione di Azure - Azure Active DirectoryConditional Access - Require MFA for Azure management - Azure Active Directory
description: Creare criteri di accesso condizionale personalizzati per richiedere l'autenticazione a più fattori per le attività di gestione di AzureCreate a custom Conditional Access policy to require multi-factor authentication for Azure management tasks
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
ms.openlocfilehash: a1dd2da51cb33582c90ff592e0061b5c1ebf8ee1
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631831"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Accesso condizionale: richiedere l'autenticazione a più fattori per la gestione di AzureConditional Access: Require MFA for Azure management

Le organizzazioni usano un'ampia gamma di servizi di Azure e li gestiscono da strumenti basati su Azure Resource Manager, ad esempio:Organizations use a variety of Azure services and manage them from Azure Resource Manager based tools like:

* Portale di Azure
* Azure PowerShell
* Interfaccia della riga di comando di Azure

Questi strumenti possono fornire accesso con privilegi elevati alle risorse, che possono modificare le configurazioni a livello di sottoscrizione, le impostazioni del servizio e la fatturazione delle sottoscrizioni. Per proteggere queste risorse con privilegi, Microsoft consiglia di richiedere l'autenticazione a più fattori per qualsiasi utente che accede a queste risorse.

## <a name="user-exclusions"></a>Esclusioni di utenti

I criteri di accesso condizionale sono strumenti potenti, è consigliabile escludere i seguenti account dai criteri:

* **Accesso di emergenza** o **account break-glass** per impedire il blocco degli account a livello di tenant. Nell'improbabile scenario in cui tutti gli amministratori sono bloccati dal tenant, l'account amministrativo di accesso di emergenza può essere usato per accedere al tenant per ripristinare l'accesso.
   * Altre informazioni sono disponibili nell'articolo Gestire gli account di [accesso di emergenza in Azure AD.](../users-groups-roles/directory-emergency-access.md)
* **Account di servizio** ed entità servizio, ad esempio l'account di sincronizzazione di Azure AD Connect.Service accounts and **service principals**, such as the Azure AD Connect Sync Account. Gli account di servizio sono account non interattivi che non sono legati ad alcun utente specifico. Vengono normalmente utilizzati dai servizi back-end che consentono l'accesso a livello di codice alle applicazioni, ma vengono utilizzati anche per accedere ai sistemi per scopi amministrativi. Gli account di servizio come questi devono essere esclusi poiché l'autenticazione a più fattori non può essere completata a livello di codice.
   * Se l'organizzazione dispone di questi account in uso negli script o nel codice, è consigliabile sostituirli con [identità gestite](../managed-identities-azure-resources/overview.md). Come soluzione temporanea, è possibile escludere questi account specifici dai criteri di base.

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

I passaggi seguenti consentono di creare un criterio di accesso condizionale per richiedere ai ruoli amministrativi assegnati di eseguire l'autenticazione a più fattori.

1. Accedere al **portale** di Azure come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare ad Accesso**condizionale**alla**sicurezza** > di **Azure Active Directory** > .
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni**selezionare **Utenti e gruppi**
   1. In **Includi**selezionare **Tutti gli utenti**.
   1. In **Escludi**selezionare **Utenti e gruppi** e scegliere gli account di accesso di emergenza o di rottavetro dell'organizzazione. 
   1. Selezionare **Fatto**.
1. In **Includi app o azioni** > **Include**cloud **selezionare Seleziona app**, Gestione di **Microsoft Azure**e **selezionare Seleziona,** quindi **Fine**.
1. In **Condizioni** > **App client (anteprima)** impostare **Configura** su **Sì**e selezionare **Fatto**.
1. In **Controlli di** > accesso**Concedi**, selezionare **Concedi accesso**, Richiedi **autenticazione**a più fattori e selezionare **Seleziona**.
1. Confermare le impostazioni e **impostare Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare per abilitare i criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)

[Determinare l'impatto utilizzando la modalità solo report di accesso condizionaleDetermine impact using Conditional Access report-only](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso con lo strumento Elementi di ricerca condizionali](troubleshoot-conditional-access-what-if.md)
