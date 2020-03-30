---
title: Accesso condizionale - Blocca accesso - Azure Active DirectoryConditional Access - Block access - Azure Active Directory
description: Creare criteri di accesso condizionale personalizzati per
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdbf4f352bc7d6874cab9c12adf5f1717d4b8f9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295728"
---
# <a name="conditional-access-block-access"></a>Accesso condizionale: Bloccare l'accessoConditional Access: Block access

Per le organizzazioni con un approccio conservativo di migrazione cloud, il criterio Blocca tutti i criteri è un'opzione che può essere usata. 

> [!CAUTION]
> Una configurazione errata di un criterio di blocco può causare il blocco delle organizzazioni dal portale di Azure.Misconfiguration of a block policy can lead to organizations being locked out of the Azure portal.

Criteri come questi possono avere effetti collaterali indesiderati. Prima di abilitare, i test e la convalida appropriati sono fondamentali. Gli amministratori devono utilizzare strumenti quali la [modalità di solo report di accesso condizionale](concept-conditional-access-report-only.md) e lo strumento What If in Accesso [condizionale](what-if-tool.md) quando si apportano modifiche.

## <a name="user-exclusions"></a>Esclusioni di utenti

I criteri di accesso condizionale sono strumenti potenti, è consigliabile escludere i seguenti account dai criteri:

* **Accesso di emergenza** o **account break-glass** per impedire il blocco degli account a livello di tenant. Nell'improbabile scenario in cui tutti gli amministratori sono bloccati dal tenant, l'account amministrativo di accesso di emergenza può essere usato per accedere al tenant per ripristinare l'accesso.
   * Altre informazioni sono disponibili nell'articolo Gestire gli account di [accesso di emergenza in Azure AD.](../users-groups-roles/directory-emergency-access.md)
* **Account di servizio** ed entità servizio, ad esempio l'account di sincronizzazione di Azure AD Connect.Service accounts and **service principals**, such as the Azure AD Connect Sync Account. Gli account di servizio sono account non interattivi che non sono legati ad alcun utente specifico. Vengono normalmente utilizzati dai servizi back-end e consentono l'accesso a livello di codice alle applicazioni. Gli account di servizio devono essere esclusi poiché l'autenticazione a più fattori non può essere completata a livello di codice.
   * Se l'organizzazione dispone di questi account in uso negli script o nel codice, è consigliabile sostituirli con [identità gestite](../managed-identities-azure-resources/overview.md). Come soluzione temporanea, è possibile escludere questi account specifici dai criteri di base.

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

La procedura seguente consente di creare criteri di accesso condizionale per bloccare l'accesso a tutte le app ad eccezione [di Office 365](concept-conditional-access-cloud-apps.md#office-365-preview) se gli utenti non fanno parte di una rete attendibile. Questi criteri vengono attivati in [modalità Solo report,](howto-conditional-access-report-only.md) in modo che gli amministratori possano determinare l'impatto che avranno sugli utenti esistenti. Quando gli amministratori sono a proprio agio nel fatto che i criteri si applichino come vogliono, possono passare a **On**.

Il primo criterio blocca l'accesso a tutte le app, ad eccezione delle applicazioni di Office 365, se non in un percorso attendibile.

1. Accedere al **portale** di Azure come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare ad Accesso**condizionale**alla**sicurezza** > di **Azure Active Directory** > .
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi**selezionare **Tutti gli utenti**.
   1. In **Escludi**selezionare **Utenti e gruppi** e scegliere gli account di accesso di emergenza o di rottavetro dell'organizzazione. 
   1. Selezionare **Fatto**.
1. In **Azioni o app cloud**selezionare le opzioni seguenti:
   1. In **Includi**selezionare **Tutte le app cloud**.
   1. In **Escludi**selezionare **Office 365 (anteprima)**, **selezionare Seleziona**, quindi **Fatto**.
1. In **Condizioni**:
   1. In**Posizione** **condizioni** > .
      1. Impostare **Configura su** **Sì**
      1. In **Includi**selezionare **Qualsiasi posizione**.
      1. In **Escludi**selezionare **Tutti i percorsi attendibili**.
      1. Selezionare **Fatto**.
   1. In **App client (anteprima)** impostare **Configura** su **Sì**e selezionare **Fatto**, quindi **Fine**.
1. In **Controlli di** > accesso**Concedi**selezionare **Blocca accesso**, quindi **selezionare Seleziona**.
1. Confermare le impostazioni e **impostare Abilita criterio** su **Solo report**.
1. Selezionare **Crea** per creare per abilitare i criteri.

Un secondo criterio viene creato di seguito per richiedere l'autenticazione a più fattori o un dispositivo conforme per gli utenti di Office 365.

1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi**selezionare **Tutti gli utenti**.
   1. In **Escludi**selezionare **Utenti e gruppi** e scegliere gli account di accesso di emergenza o di rottavetro dell'organizzazione. 
   1. Selezionare **Fatto**.
1. In **Includi app o azioni** > **Include**cloud **selezionare Seleziona app**, Office **365 (anteprima)** e selezionare **Seleziona**, quindi **Fine**.
1. In **Controlli di** > accesso**Concedi**, selezionare **Concedi accesso**.
   1. Selezionare **Richiedi autenticazione** a più fattori e Richiedi che il **dispositivo sia contrassegnato come conforme,** **selezionare Seleziona**.
   1. Assicurarsi che **l'opzione Richiedi tutti i controlli selezionati** sia selezionata.
   1. Selezionare **Seleziona**.
1. Confermare le impostazioni e **impostare Abilita criterio** su **Solo report**.
1. Selezionare **Crea** per creare per abilitare i criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)

[Determinare l'impatto utilizzando la modalità solo report di accesso condizionaleDetermine impact using Conditional Access report-only](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso con lo strumento Elementi di ricerca condizionali](troubleshoot-conditional-access-what-if.md)
