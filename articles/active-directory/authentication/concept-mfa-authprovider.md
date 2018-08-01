---
title: Quando e come usare un provider di Azure Multi-Factor Authentication?
description: Quando è necessario usare un provider di autenticazione con Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 4a6ce07bfe641d9efdbe0eac841bb4f27f468b34
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161465"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Quando usare un provider di Azure Multi-Factor Authentication

La verifica in due passaggi è disponibile per impostazione predefinita per gli amministratori globali che si occupano di utenti di Azure Active Directory e Office 365. Per sfruttare le [funzionalità avanzate](howto-mfa-mfasettings.md), è tuttavia consigliabile acquistare la versione completa di Azure Multi-Factor Authentication (MFA).

Un provider di Azure Multi-Factor Authentication viene usato per sfruttare le funzionalità fornite dalla versione completa di Azure MFA. È destinato agli utenti che **non dispongono di licenze mediante Azure MFA, Azure AD Premium o bundle che includono Azure AD Premium e Azure MFA**. Azure MFA e Azure AD Premium includono la versione completa di Azure MFA per impostazione predefinita.

Se si hanno licenze che coprono tutti gli utenti dell'organizzazione, non è necessario un provider di Azure Multi-Factor Authentication. Creare un provider di Azure Multi-Factor Authentication solo se è necessario fornire la verifica in due passaggi anche per alcuni utenti privi di licenze.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Avvertenze relative ad Azure MFA SDK

È necessario un provider di Azure Multi-Factor Authentication per scaricare l'SDK. Si noti che l'SDK è stato deprecato, non è più supportato per i nuovi clienti e continuerà a funzionare solo fino al 14 novembre 2018. Dopo tale periodo, le chiamate all'SDK avranno esito negativo.

Per scaricare l'SDK, creare un provider di Azure Multi-Factor Authentication, anche se si dispone di licenze di Azure MFA, AAD Premium o altre licenze in bundle. Se si crea un provider di Azure Multi-Factor Authentication a tale scopo e si dispone già di licenze, creare il provider con il modello **Per utente abilitato**. Collegare quindi il provider alla directory contenente le licenze di Azure MFA, Azure AD Premium o altre licenze in bundle. Con questa configurazione verranno eseguiti addebiti solo se il numero di utenti singoli che eseguono la verifica in due passaggi è maggiore del numero di licenze possedute.

## <a name="what-is-an-mfa-provider"></a>Informazioni sui provider MFA

Se non si dispone di licenze per Azure Multi-Factor Authentication, è possibile creare un provider di autenticazione per richiedere la verifica in due passaggi per gli utenti.

Esistono due tipi di provider di autenticazione, la cui differenza consiste nella modalità di addebito per la sottoscrizione di Azure. L'opzione in base al numero di autenticazioni calcola il numero di autenticazioni eseguite sul tenant in un mese. Questa opzione è consigliata se si ha un numero di utenti che eseguono l'autenticazione solo occasionalmente. L'opzione in base al numero di utenti calcola il numero di persone nel tenant che eseguono la verifica in due passaggi in un mese. Questa opzione è consigliata se alcuni utenti possiedono già una licenza, ma è necessario estendere MFA ad altri utenti oltre ai termini di licenza.

## <a name="create-an-mfa-provider"></a>Creare un provider MFA

Per creare un provider Azure Multi-Factor Authentication nel portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.
2. Selezionare **Azure Active Directory** > **Server MFA** > **Provider**.

   ![Provider][Providers]

3. Selezionare **Aggiungi**.
4. Compilare i campi seguenti e quindi selezionare **Aggiungi**.
   - **Nome**: nome del provider.
   - **Modello di utilizzo**: scegliere una delle due opzioni:
      * Per autenticazione: modello di acquisto in cui è previsto l'addebito in base al numero di autenticazioni. Usato, in genere, per scenari in cui viene usato Azure Multi-Factor Authentication in un'applicazione per il consumer.
      * Per utente abilitato: modello di acquisto in cui è previsto l'addebito in base al numero di utenti abilitati. Usato, in genere, per l'accesso dei dipendenti alle applicazioni come Office 365. Scegliere questa opzione se alcuni utenti dispongono già della licenza per Azure MFA.
   - **Sottoscrizione**: sottoscrizione di Azure a cui verrà addebitata l'attività di verifica in due passaggi tramite il provider.
   - **Directory**: tenant di Azure Active Directory a cui è associato il provider.
      * Non è necessaria una directory di Azure AD per creare un provider. Lasciare vuota la casella se si prevede di scaricare solo il server Azure Multi-Factor Authentication.
      * Il provider deve essere associato a una directory di Azure AD per sfruttare le funzionalità avanzate.
      * A una directory di Azure AD può essere associato un solo provider.

## <a name="manage-your-mfa-provider"></a>Gestire il provider MFA

Non è possibile modificare il modello di utilizzo (per utente abilitato o per autenticazione) dopo la creazione di un provider di Multi-Factor Authentication. È tuttavia possibile eliminare il provider di Multi-Factor Authentication e quindi crearne uno con un modello di utilizzo diverso.

Se il provider di Multi-Factor Authentication corrente è associato a una directory di Azure AD, è possibile eliminare il provider di Multi-Factor Authentication e crearne uno collegato allo stesso tenant di Azure AD. In alternativa, se è stato acquistato un numero sufficiente di licenze MFA, Azure AD Premium o bundle che comprendono licenze di Azure AD Premium o Azure MFA per tutti gli utenti che sono abilitati per l'autenticazione a più fattori, è possibile eliminare del tutto il provider di MFA.

Se il provider di Multi-Factor Authentication non è collegato a un tenant di Azure AD o si collega il nuovo provider di Multi-Factor Authentication a un diverso tenant di Azure AD, le impostazioni utente e le opzioni di configurazione non vengono trasferite. Inoltre, i server Azure MFA esistenti devono essere riattivati usando le credenziali di attivazione generate tramite il nuovo provider di MFA. La riattivazione dei server MFA per collegarli al nuovo provider di MFA non inciderà sull'autenticazione con chiamata telefonica e SMS, ma le notifiche dell'app mobile smettono di funzionare per tutti gli utenti fino a quando riattiveranno l'app mobile.

## <a name="next-steps"></a>Passaggi successivi

[Configurare le impostazioni di Multi-Factor Authentication](howto-mfa-mfasettings.md)

[Providers]: ./media/concept-mfa-authprovider/add-providers.png "Aggiungere provider MFA"
