---
title: Accesso condizionale-Blocca accesso-Azure Active Directory
description: Creare un criterio di accesso condizionale personalizzato per
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2834fd3d4901b6394eabe000f9efc572c2efd497
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80755073"
---
# <a name="conditional-access-block-access"></a>Accesso condizionale: bloccare l'accesso

Per le organizzazioni con un approccio di migrazione cloud conservativa, i criteri di blocco tutti sono un'opzione che può essere usata. 

> [!CAUTION]
> La configurazione errata di un criterio di blocco può comportare la chiusura delle organizzazioni dal portale di Azure.

I criteri come questi possono avere effetti collaterali imprevisti. Il test e la convalida corretti sono fondamentali prima dell'abilitazione. Gli amministratori devono utilizzare strumenti come la [modalità solo report di accesso condizionale](concept-conditional-access-report-only.md) e [lo strumento What If nell'accesso condizionale](what-if-tool.md) durante l'esecuzione di modifiche.

## <a name="user-exclusions"></a>Esclusioni utente

I criteri di accesso condizionale sono strumenti avanzati, quindi è consigliabile escludere dal criterio i seguenti account:

* Account di **accesso di emergenza** o **break-Glass** per impedire il blocco degli account a livello di tenant. Nello scenario improbabile che tutti gli amministratori siano bloccati dal tenant, è possibile usare l'account amministrativo di accesso di emergenza per accedere al tenant per recuperare l'accesso.
   * Altre informazioni sono disponibili nell'articolo [gestire gli account di accesso di emergenza in Azure ad](../users-groups-roles/directory-emergency-access.md).
* Gli **account del servizio** e le **entità servizio**, ad esempio l'account Azure ad Connect Sync. Gli account del servizio sono account non interattivi che non sono collegati a un utente specifico. Vengono in genere usate dai servizi back-end che consentono l'accesso a livello di codice alle applicazioni, ma vengono usate anche per accedere ai sistemi per scopi amministrativi. Gli account del servizio come questi devono essere esclusi perché non è possibile completare l'autenticazione a livello di codice. Le chiamate effettuate dalle entità servizio non sono bloccate dall'accesso condizionale.
   * Se l'organizzazione dispone di questi account in uso negli script o nel codice, è consigliabile sostituirli con [identità gestite](../managed-identities-azure-resources/overview.md). Come soluzione temporanea, è possibile escludere questi account specifici dai criteri di base.

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

La procedura seguente consente di creare criteri di accesso condizionale per bloccare l'accesso a tutte le app ad eccezione di [Office 365](concept-conditional-access-cloud-apps.md#office-365-preview) se gli utenti non si trovano in una rete attendibile. Questi criteri vengono inseriti in [modalità di sola segnalazione](howto-conditional-access-report-only.md) per iniziare, in modo che gli amministratori possano determinare l'effetto che avranno sugli utenti esistenti. Quando gli amministratori hanno dimestichezza con l'applicazione dei criteri, possono impostarli **su on**.

Il primo criterio blocca l'accesso a tutte le app, ad eccezione delle applicazioni Office 365, se non in un percorso attendibile.

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare a **Azure Active Directory** > **Security** > **accesso condizionale**di sicurezza.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi**selezionare **tutti gli utenti**.
   1. In **Escludi**selezionare **utenti e gruppi** e scegliere l'accesso di emergenza dell'organizzazione o gli account break-Glass. 
   1. Seleziona **Chiudi**.
1. In **app Cloud o azioni**selezionare le opzioni seguenti:
   1. In **Includi**selezionare **tutte le app Cloud**.
   1. In **Escludi**selezionare **Office 365 (anteprima)**, selezionare **Seleziona**e quindi fare clic su **fine**.
1. In **condizioni**:
   1. In **Conditions** > **location**.
      1. Imposta **Configura** su **Sì**
      1. In **Includi**selezionare **qualsiasi percorso**.
      1. In **Escludi**selezionare **tutti i percorsi attendibili**.
      1. Seleziona **Chiudi**.
   1. In **app client (anteprima)** impostare **Configura** su **Sì**, quindi fare clic su **fine**, quindi su **fine**.
1. In **controllo** > di accesso**concedere**selezionare **Blocca accesso**, quindi selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** su **solo report**.
1. Selezionare **Crea** per creare per abilitare i criteri.

Per richiedere l'autenticazione a più fattori o un dispositivo conforme per gli utenti di Office 365, viene creato un secondo criterio.

1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi**selezionare **tutti gli utenti**.
   1. In **Escludi**selezionare **utenti e gruppi** e scegliere l'accesso di emergenza dell'organizzazione o gli account break-Glass. 
   1. Seleziona **Chiudi**.
1. In **app Cloud o azioni** > **includere**selezionare **Seleziona app**, scegliere **Office 365 (anteprima)**, quindi selezionare **selezionare**, quindi **fine**.
1. In **controllo** > di accesso**concedere**selezionare **Concedi accesso**.
   1. Selezionare **Richiedi autenticazione** a più fattori e **Richiedi che il dispositivo sia contrassegnato come conforme** Selezionare **Seleziona**.
   1. Assicurarsi che sia selezionata **l'opzione Richiedi tutti i controlli selezionati** .
   1. Selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** su **solo report**.
1. Selezionare **Crea** per creare per abilitare i criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

[Determinare l'effetto usando la modalità solo report di accesso condizionale](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso usando lo strumento di What If dell'accesso condizionale](troubleshoot-conditional-access-what-if.md)
