---
title: Accesso condizionale - Richiedere l'autenticazione a più fattori per tutti gli utenti - Azure Active DirectoryConditional Access - Require MFA for all users - Azure Active Directory
description: Creare criteri di accesso condizionale personalizzati per richiedere a tutti gli utenti di eseguire l'autenticazione a più fattoriCreate a custom Conditional Access policy to require all users to perform multi-factor authentication
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
ms.openlocfilehash: 0257f4f063361a96e6bb4560a6e101a383b02992
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631844"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Accesso condizionale: richiedere l'autenticazione a più fattori per tutti gli utentiConditional Access: Require MFA for all users

Come Alex Weinert, la Directory di sicurezza identità di Microsoft, cita nel suo post sul blog [il tuo $word non importa](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984):

> La password non ha importanza, ma l'autenticazione a più fattori lo fa! Sulla base dei nostri studi, il tuo account ha più del 99,9% di probabilità in meno di essere compromesso se utilizzi l'autenticazione a più fattori.

Le indicazioni contenute in questo articolo consentono all'organizzazione di creare criteri di autenticazione a più fattori bilanciati per l'ambiente.

## <a name="user-exclusions"></a>Esclusioni di utenti

I criteri di accesso condizionale sono strumenti potenti, è consigliabile escludere i seguenti account dai criteri:

* **Accesso di emergenza** o **account break-glass** per impedire il blocco degli account a livello di tenant. Nell'improbabile scenario in cui tutti gli amministratori sono bloccati dal tenant, l'account amministrativo di accesso di emergenza può essere usato per accedere al tenant per ripristinare l'accesso.
   * Altre informazioni sono disponibili nell'articolo Gestire gli account di [accesso di emergenza in Azure AD.](../users-groups-roles/directory-emergency-access.md)
* **Account di servizio** ed entità servizio, ad esempio l'account di sincronizzazione di Azure AD Connect.Service accounts and **service principals**, such as the Azure AD Connect Sync Account. Gli account di servizio sono account non interattivi che non sono legati ad alcun utente specifico. Vengono normalmente utilizzati dai servizi back-end che consentono l'accesso a livello di codice alle applicazioni, ma vengono utilizzati anche per accedere ai sistemi per scopi amministrativi. Gli account di servizio come questi devono essere esclusi poiché l'autenticazione a più fattori non può essere completata a livello di codice.
   * Se l'organizzazione dispone di questi account in uso negli script o nel codice, è consigliabile sostituirli con [identità gestite](../managed-identities-azure-resources/overview.md). Come soluzione temporanea, è possibile escludere questi account specifici dai criteri di base.

## <a name="application-exclusions"></a>Esclusioni di applicazioni

Le organizzazioni possono avere molte applicazioni cloud in uso. Non tutte queste applicazioni possono richiedere la stessa sicurezza. Ad esempio, le domande di retribuzione e partecipazione potrebbero richiedere l'autenticazione a più fattori, ma la caffetteria probabilmente no. Gli amministratori possono scegliere di escludere applicazioni specifiche dai propri criteri.

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

I passaggi seguenti consentono di creare un criterio di accesso condizionale per richiedere ai ruoli amministrativi assegnati di eseguire l'autenticazione a più fattori.

1. Accedere al **portale** di Azure come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare ad Accesso**condizionale**alla**sicurezza** > di **Azure Active Directory** > .
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni**selezionare **Utenti e gruppi**
   1. In **Includi**selezionare **Tutti gli utenti**
   1. In **Escludi**selezionare **Utenti e gruppi** e scegliere gli account di accesso di emergenza o di rottavetro dell'organizzazione. 
   1. Selezionare **Fatto**.
1. In **Includi app o azioni** > **Include**cloud selezionare Tutte le **app cloud**.
   1. In **Escludi**selezionare le applicazioni che non richiedono l'autenticazione a più fattori.
1. In **Condizioni** > **App client (anteprima)** impostare **Configura** su **Sì**e selezionare **Fatto**.
1. In **Controlli di** > accesso**Concedi**, selezionare **Concedi accesso**, Richiedi **autenticazione**a più fattori e selezionare **Seleziona**.
1. Confermare le impostazioni e **impostare Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare per abilitare i criteri.

### <a name="named-locations"></a>Posizioni specifiche

Le organizzazioni possono scegliere di incorporare percorsi di rete noti noti come **percorsi denominati** nei criteri di accesso condizionale. Questi percorsi denominati possono includere reti IPv4 attendibili come quelle per una sede della sede principale. Per altre informazioni sulla configurazione di percorsi denominati, vedere l'articolo Qual è la condizione del [percorso in Azure Active Directory Conditional Access?](location-condition.md)

Nel criterio di esempio precedente, un'organizzazione può scegliere di non richiedere l'autenticazione a più fattori se si accede a un'app cloud dalla rete aziendale. In questo caso è possibile aggiungere la configurazione seguente al criterio:In this case they could add the following configuration to the policy:

1. In **Assegnazioni**selezionare**Posizioni** **condizioni** > .
   1. Configura **Sì**.
   1. Includi **qualsiasi posizione**.
   1. Escludi **tutti i percorsi attendibili**.
   1. Selezionare **Fatto**.
1. Selezionare **Fatto**.
1. **Salvare** le modifiche ai criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)

[Determinare l'impatto utilizzando la modalità solo report di accesso condizionaleDetermine impact using Conditional Access report-only](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso con lo strumento Elementi di ricerca condizionali](troubleshoot-conditional-access-what-if.md)
