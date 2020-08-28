---
title: Accesso condizionale-Blocca accesso-Azure Active Directory
description: Creare un criterio di accesso condizionale personalizzato per
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d05aaa7a6bd48c92e863d38dcee9f0bf925bafa
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049265"
---
# <a name="conditional-access-block-access"></a>Accesso condizionale: bloccare l'accesso

Per le organizzazioni con un approccio di migrazione cloud conservativa, i criteri di blocco tutti sono un'opzione che può essere usata. 

> [!CAUTION]
> La configurazione errata di un criterio di blocco può comportare la chiusura delle organizzazioni dal portale di Azure.

I criteri come questi possono avere effetti collaterali imprevisti. Il test e la convalida corretti sono fondamentali prima dell'abilitazione. Gli amministratori devono utilizzare strumenti come la [modalità solo report di accesso condizionale](concept-conditional-access-report-only.md) e [lo strumento What If nell'accesso condizionale](what-if-tool.md) durante l'esecuzione di modifiche.

## <a name="user-exclusions"></a>Esclusioni di utenti

Poiché i criteri di accesso condizionale sono strumenti avanzati, è consigliabile escludere dai criteri gli account seguenti:

* Gli account di **accesso di emergenza** o **critici** per impedire il blocco degli account a livello di tenant. In uno scenario improbabile in cui è bloccato l'accesso al tenant per tutti gli amministratori, è possibile usare l'account amministrativo di accesso di emergenza per accedere al tenant per recuperare l'accesso.
   * Altre informazioni sono disponibili nell'articolo [Gestire gli account di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md).
* Gli **account del servizio** e le **entità servizio**, ad esempio l'account di sincronizzazione di Azure AD Connect. Gli account del servizio sono account non interattivi che non sono collegati a un utente specifico. Vengono in genere usati dai servizi back-end che consentono l'accesso a livello di codice alle applicazioni, ma vengono usati anche per accedere ai sistemi per scopi amministrativi. Gli account del servizio di questo tipo dovrebbero essere esclusi poiché l'autenticazione a più fattori non può essere eseguita a livello di codice. Le chiamate effettuate dalle entità servizio non sono bloccate dall'accesso condizionale.
   * Se l'organizzazione usa questi account negli script o nel codice, è consigliabile sostituirli con [identità gestite](../managed-identities-azure-resources/overview.md). Come soluzione alternativa temporanea, è possibile escludere questi account specifici dai criteri di base.

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

La procedura seguente consente di creare criteri di accesso condizionale per bloccare l'accesso a tutte le app ad eccezione di [Office 365](concept-conditional-access-cloud-apps.md#office-365-preview) se gli utenti non si trovano in una rete attendibile. Questi criteri vengono inseriti in [modalità di sola segnalazione](howto-conditional-access-insights-reporting.md) per iniziare, in modo che gli amministratori possano determinare l'effetto che avranno sugli utenti esistenti. Quando gli amministratori hanno dimestichezza con l'applicazione dei criteri, possono impostarli **su on**.

Il primo criterio blocca l'accesso a tutte le app, ad eccezione delle applicazioni Office 365, se non in un percorso attendibile.

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome ai criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi** selezionare **Tutti gli utenti**.
   1. In **Escludi** selezionare **Utenti e gruppi** e scegliere gli account di accesso di emergenza o gli account critici dell'organizzazione. 
   1. Selezionare **Operazione completata**.
1. In **app Cloud o azioni**selezionare le opzioni seguenti:
   1. In **Includi**selezionare **tutte le app Cloud**.
   1. In **Escludi**selezionare **Office 365 (anteprima)**, selezionare **Seleziona**e quindi fare clic su **fine**.
1. In **Condizioni**:
   1. In **Condizioni** > **Posizione**
      1. impostare **Configura** su **Sì**
      1. In **Includi**selezionare **qualsiasi percorso**.
      1. In **Escludi**selezionare **tutti i percorsi attendibili**.
      1. Selezionare **Fine**.
   1. In **app client (anteprima)** impostare **Configura** su **Sì**, quindi fare clic su **fine**, quindi su **fine**.
1. In **controllo di accesso**  >  **concedere**selezionare **Blocca accesso**, quindi selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Attiva criterio** su **Solo report**.
1. Selezionare **Crea** per creare e abilitare il criterio.

Per richiedere l'autenticazione a più fattori o un dispositivo conforme per gli utenti di Office 365, viene creato un secondo criterio.

1. Selezionare **Nuovi criteri**.
1. Assegnare un nome ai criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi** selezionare **Tutti gli utenti**.
   1. In **Escludi** selezionare **Utenti e gruppi** e scegliere gli account di accesso di emergenza o gli account critici dell'organizzazione. 
   1. Selezionare **Operazione completata**.
1. In **app Cloud o azioni**  >  **includere**selezionare **Seleziona app**, scegliere **Office 365 (anteprima)**, quindi selezionare **selezionare**, quindi **fine**.
1. In **controllo di accesso**  >  **concedere**selezionare **Concedi accesso**.
   1. Selezionare **Richiedi autenticazione** a più fattori e **Richiedi che il dispositivo sia contrassegnato come conforme** Selezionare **Seleziona**.
   1. Assicurarsi che sia selezionata **l'opzione Richiedi tutti i controlli selezionati** .
   1. Scegliere **Seleziona**.
1. Confermare le impostazioni e impostare **Attiva criterio** su **Solo report**.
1. Selezionare **Crea** per creare e abilitare il criterio.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)

[Determinare l'impatto dell'uso della modalità di accesso condizionale solo report](howto-conditional-access-insights-reporting.md)

[Simulare il comportamento di accesso usando lo strumento What If per l'accesso condizionale](troubleshoot-conditional-access-what-if.md)
