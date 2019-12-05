---
title: "Accesso condizionale: richiedere l'autenticazione a più fattori per gli amministratori-Azure Active Directory"
description: Creare un criterio di accesso condizionale personalizzato per richiedere agli amministratori di eseguire l'autenticazione a più fattori
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd3de14d8c82a3e51d38a1e32241cb106e0f95b3
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74804619"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Accesso condizionale: Richiedi autenticazione a più fattori per gli amministratori

Gli account a cui sono assegnati diritti amministrativi sono destinati da utenti malintenzionati. La richiesta di autenticazione a più fattori in questi account è un modo semplice per ridurre il rischio di compromissione di tali account.

Microsoft consiglia di richiedere l'autenticazione a più fattori sui seguenti ruoli:

* Amministratore globale
* Amministratore di SharePoint
* Amministratore di Exchange
* Amministratore di accesso condizionale
* Amministratore della sicurezza
* Amministratore helpdesk (password)
* Amministratore password
* Amministratore fatturazione
* Amministratore utenti

Le organizzazioni possono scegliere di includere o escludere i ruoli in base alle esigenze.

## <a name="user-exclusions"></a>Esclusioni utente

I criteri di accesso condizionale sono strumenti avanzati, quindi è consigliabile escludere dal criterio i seguenti account:

* Account di **accesso di emergenza** o **break-Glass** per impedire il blocco degli account a livello di tenant. Nello scenario improbabile che tutti gli amministratori siano bloccati dal tenant, è possibile usare l'account amministrativo di accesso di emergenza per accedere al tenant per recuperare l'accesso.
   * Altre informazioni sono disponibili nell'articolo [gestire gli account di accesso di emergenza in Azure ad](../users-groups-roles/directory-emergency-access.md).
* Gli **account del servizio** e i principi di **servizio**, ad esempio l'account Azure ad Connect Sync. Gli account del servizio sono account non interattivi che non sono collegati a un utente specifico. Vengono in genere usati dai servizi back-end e consentono l'accesso a livello di codice alle applicazioni. Gli account del servizio devono essere esclusi perché non è possibile completare l'autenticazione a livello di codice.
   * Se l'organizzazione dispone di questi account in uso negli script o nel codice, è consigliabile sostituirli con [identità gestite](../managed-identities-azure-resources/overview.md). Come soluzione temporanea, è possibile escludere questi account specifici dai criteri di base.

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

La procedura seguente consente di creare un criterio di accesso condizionale per richiedere che i ruoli amministrativi assegnati eseguano la funzionalità di autenticazione a più fattori.

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare a **Azure Active Directory** **sicurezza** >  > **l'accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **assegnazioni**selezionare **utenti e gruppi** .
   1. In **Includi**selezionare **ruoli della directory (anteprima)** e scegliere almeno i ruoli seguenti:
      * Amministratore globale
      * Amministratore di SharePoint
      * Amministratore di Exchange
      * Amministratore di accesso condizionale
      * Amministratore della sicurezza
      * Amministratore dell'help desk
      * Amministratore password
      * Amministratore fatturazione
      * Amministratore utenti
   1. In **Escludi**selezionare **utenti e gruppi** e scegliere l'accesso di emergenza dell'organizzazione o gli account break-Glass. 
   1. Selezionare **Operazione completata**.
1. In **app Cloud o azioni** > **includere**, selezionare **tutte le app Cloud**e selezionare **fine**.
1. In **controlli di accesso** > **concedere**Selezionare **Concedi accesso**, **Richiedi autenticazione**a più fattori e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** **su on**.
1. Selezionare **Crea** per creare per abilitare i criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

[Simulare il comportamento di accesso usando lo strumento di What If dell'accesso condizionale](troubleshoot-conditional-access-what-if.md)
