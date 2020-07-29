---
title: Accesso condizionale - Richiedere l'autenticazione a più fattori per la gestione di Azure - Azure Active Directory
description: Creare criteri di accesso condizionale personalizzati per richiedere l'autenticazione a più fattori per le attività di gestione di Azure
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ee4c7776dbddf089d290d760d6c0689ba9c0102
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283028"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Accesso condizionale: Richiedere l'autenticazione a più fattori per la gestione di Azure

Le organizzazioni usano un'ampia gamma di servizi di Azure e li gestiscono da strumenti basati su Azure Resource Manager come:

* Portale di Azure
* Azure PowerShell
* Interfaccia della riga di comando di Azure

Questi strumenti possono offrire un accesso con privilegi elevati alle risorse che può modificare le configurazioni a livello di sottoscrizione, le impostazioni del servizio e la fatturazione della sottoscrizione. Per proteggere queste risorse con privilegi, Microsoft consiglia di richiedere l'autenticazione a più fattori per tutti gli utenti che accedono a queste risorse.

## <a name="user-exclusions"></a>Esclusioni di utenti

Poiché i criteri di accesso condizionale sono strumenti avanzati, è consigliabile escludere dai criteri gli account seguenti:

* Gli account di **accesso di emergenza** o **critici** per impedire il blocco degli account a livello di tenant. In uno scenario improbabile in cui è bloccato l'accesso al tenant per tutti gli amministratori, è possibile usare l'account amministrativo di accesso di emergenza per accedere al tenant per recuperare l'accesso.
   * Altre informazioni sono disponibili nell'articolo [Gestire gli account di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md).
* Gli **account del servizio** e le **entità servizio**, ad esempio l'account di sincronizzazione di Azure AD Connect. Gli account del servizio sono account non interattivi che non sono collegati a un utente specifico. Vengono in genere usati dai servizi back-end che consentono l'accesso a livello di codice alle applicazioni, ma vengono usati anche per accedere ai sistemi per scopi amministrativi. Gli account del servizio di questo tipo dovrebbero essere esclusi poiché l'autenticazione a più fattori non può essere eseguita a livello di codice. Le chiamate effettuate dalle entità servizio non sono bloccate dall'accesso condizionale.
   * Se l'organizzazione usa questi account negli script o nel codice, è consigliabile sostituirli con [identità gestite](../managed-identities-azure-resources/overview.md). Come soluzione alternativa temporanea, è possibile escludere questi account specifici dai criteri di base.

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

La procedura seguente consente di creare criteri di accesso condizionale per richiedere agli utenti con accesso all'app [Microsoft Azure Management](concept-conditional-access-cloud-apps.md#microsoft-azure-management) di eseguire l'autenticazione a più fattori.

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome a criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi** selezionare **Tutti gli utenti**.
   1. In **Escludi** selezionare **Utenti e gruppi** e scegliere gli account di accesso di emergenza o gli account critici dell'organizzazione. 
   1. Selezionare **Operazione completata**.
1. In **Applicazioni cloud o azioni** > **Includi** scegliere **Selezionare le app**, scegliere **Microsoft Azure Management** e selezionare **Seleziona** quindi **Chiudi**.
1. In **condizioni**  >  **app client (anteprima)**, in **selezionare le app client a cui verranno applicati i criteri per** lasciare selezionate tutte le impostazioni predefinite e selezionare **fine**.
1. In **Controlli di accesso** > **Concedi**, selezionare **Concedi accesso**, **Richiedi autenticazione a più fattori** e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

[Determinare l'impatto dell'uso della modalità di accesso condizionale solo report](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso usando lo strumento What If per l'accesso condizionale](troubleshoot-conditional-access-what-if.md)
