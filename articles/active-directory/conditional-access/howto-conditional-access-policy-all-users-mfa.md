---
title: "Accesso condizionale: richiedere l'autenticazione a più fattori per tutti gli utenti-Azure Active Directory"
description: Creare un criterio di accesso condizionale personalizzato per richiedere a tutti gli utenti di eseguire l'autenticazione a più fattori
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
ms.openlocfilehash: 7af1347ac57e1a5c5ae99744924ea04fe9757581
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83995346"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Accesso condizionale: richiedere l'autenticazione a più fattori per tutti gli utenti

Come Alex Weinert, la directory di Identity Security di Microsoft, cita nel suo Blog il post di Blog della [PA $ $Word non è rilevante](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984):

> La password non è rilevante, ma l'autenticazione a più fattori. In base ai nostri studi, il tuo account è più del 99,9% meno probabile che venga compromesso se usi l'autenticazione a più fattori.

Le indicazioni fornite in questo articolo consentiranno all'organizzazione di creare criteri di autenticazione a più fattori bilanciati per l'ambiente.

## <a name="user-exclusions"></a>Esclusioni di utenti

Poiché i criteri di accesso condizionale sono strumenti avanzati, è consigliabile escludere dai criteri gli account seguenti:

* Gli account di **accesso di emergenza** o **critici** per impedire il blocco degli account a livello di tenant. In uno scenario improbabile in cui è bloccato l'accesso al tenant per tutti gli amministratori, è possibile usare l'account amministrativo di accesso di emergenza per accedere al tenant per recuperare l'accesso.
   * Altre informazioni sono disponibili nell'articolo [Gestire gli account di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md).
* Gli **account del servizio** e le **entità servizio**, ad esempio l'account di sincronizzazione di Azure AD Connect. Gli account del servizio sono account non interattivi che non sono collegati a un utente specifico. Vengono in genere usati dai servizi back-end che consentono l'accesso a livello di codice alle applicazioni, ma vengono usati anche per accedere ai sistemi per scopi amministrativi. Gli account del servizio di questo tipo dovrebbero essere esclusi poiché l'autenticazione a più fattori non può essere eseguita a livello di codice. Le chiamate effettuate dalle entità servizio non sono bloccate dall'accesso condizionale.
   * Se l'organizzazione usa questi account negli script o nel codice, è consigliabile sostituirli con [identità gestite](../managed-identities-azure-resources/overview.md). Come soluzione alternativa temporanea, è possibile escludere questi account specifici dai criteri di base.

## <a name="application-exclusions"></a>Esclusioni delle applicazioni

Le organizzazioni possono avere molte applicazioni cloud in uso. Non tutte le applicazioni possono richiedere la stessa sicurezza. Ad esempio, le applicazioni per le retribuzioni e la partecipazione possono richiedere l'autenticazione a più fattori, ma la caffetteria probabilmente no. Gli amministratori possono scegliere di escludere applicazioni specifiche dal criterio.

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

La procedura seguente consente di creare un criterio di accesso condizionale per richiedere a tutti gli utenti di eseguire l'autenticazione a più fattori.

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome ai criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi**selezionare **tutti gli utenti**
   1. In **Escludi** selezionare **Utenti e gruppi** e scegliere gli account di accesso di emergenza o gli account critici dell'organizzazione. 
   1. Selezionare **Operazione completata**.
1. In **app Cloud o azioni**  >  **Includi**selezionare **tutte le app Cloud**.
   1. In **Escludi**selezionare le applicazioni che non richiedono l'autenticazione a più fattori.
1. In **condizioni**  >  **app client (anteprima)** impostare **Configura** su **Sì**. In **selezionare le app client a cui verranno applicati i criteri per** lasciare selezionati tutti i valori predefiniti e selezionare **fine**.
1. In **Controlli di accesso** > **Concedi**, selezionare **Concedi accesso**, **Richiedi autenticazione a più fattori** e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

### <a name="named-locations"></a>Posizioni specifiche

Le organizzazioni possono scegliere di incorporare i percorsi di rete noti, detti **località denominate** , ai criteri di accesso condizionale. Queste località denominate possono includere reti IPv4 attendibili come quelle per una sede centrale. Per ulteriori informazioni sulla configurazione delle località denominate, vedere l'articolo [Qual è la condizione di posizione in Azure Active Directory l'accesso condizionale?](location-condition.md)

Nei criteri di esempio precedenti, un'organizzazione può scegliere di non richiedere l'autenticazione a più fattori per l'accesso a un'app Cloud dalla propria rete aziendale. In questo caso, è possibile aggiungere la configurazione seguente ai criteri:

1. In **assegnazioni**selezionare i **Conditions**  >  **percorsi**delle condizioni.
   1. Configurare **Sì**.
   1. Includere **Tutte le località**.
   1. Escludere **Tutte le posizioni attendibili**.
   1. Selezionare **Operazione completata**.
1. Selezionare **Operazione completata**.
1. **Salvare** le modifiche apportate ai criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)

[Determinare l'impatto dell'uso della modalità di accesso condizionale solo report](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso usando lo strumento What If per l'accesso condizionale](troubleshoot-conditional-access-what-if.md)
