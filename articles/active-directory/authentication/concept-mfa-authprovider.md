---
title: Provider di autenticazione a più fattori di Azure-Azure Active Directory
description: Quando è necessario usare un provider di autenticazione con Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57ac23bbb0465be2f15e0a35bb4fd7c331e83988
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848715"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Quando usare un provider di Azure Multi-Factor Authentication

La verifica in due passaggi è disponibile per impostazione predefinita per gli amministratori globali che si occupano di utenti di Azure Active Directory e Office 365. Per sfruttare le [funzionalità avanzate](howto-mfa-mfasettings.md), è tuttavia consigliabile acquistare la versione completa di Azure Multi-Factor Authentication (MFA).

Un provider di Azure Multi-Factor Authentication consente di sfruttare le funzionalità offerte da Azure Multi-Factor Authentication per gli utenti che **non dispongono di licenze**.

> [!NOTE]
> A partire dal 1 ° settembre 2018 non sarà più possibile creare nuovi provider di autenticazione. I provider di autenticazione esistenti possono continuare a essere usati e aggiornati, ma la migrazione non è più possibile. L'autenticazione a più fattori continuerà a essere disponibile come funzionalità nelle licenze di Azure AD Premium.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Avvertenze relative ad Azure MFA SDK

Si noti che l'SDK è stato deprecato e continuerà a funzionare solo fino al 14 novembre 2018. Dopo tale periodo, le chiamate all'SDK avranno esito negativo.

## <a name="what-is-an-mfa-provider"></a>Informazioni sui provider MFA

Esistono due tipi di provider di autenticazione, la cui differenza consiste nella modalità di addebito per la sottoscrizione di Azure. L'opzione in base al numero di autenticazioni calcola il numero di autenticazioni eseguite sul tenant in un mese. Questa opzione è consigliata se si ha un numero di utenti che eseguono l'autenticazione solo occasionalmente. L'opzione in base al numero di utenti calcola il numero di persone nel tenant che eseguono la verifica in due passaggi in un mese. Questa opzione è consigliata se alcuni utenti possiedono già una licenza, ma è necessario estendere MFA ad altri utenti oltre ai termini di licenza.

## <a name="manage-your-mfa-provider"></a>Gestire il provider MFA

Non è possibile modificare il modello di utilizzo (per utente abilitato o per autenticazione) dopo la creazione di un provider di Multi-Factor Authentication.

Se è stato acquistato un numero sufficiente di licenze per tutti gli utenti che sono abilitati per l'autenticazione a più fattori, è possibile eliminare completamente il provider di MFA.

Se il provider di Multi-Factor Authentication non è collegato a un tenant di Azure AD o si collega il nuovo provider di Multi-Factor Authentication a un diverso tenant di Azure AD, le impostazioni utente e le opzioni di configurazione non vengono trasferite. Inoltre, i server Azure MFA esistenti devono essere riattivati usando le credenziali di attivazione generate tramite il provider di MFA. La riattivazione dei server MFA per il collegamento al provider di MFA non inciderà sull'autenticazione con chiamata telefonica e SMS, ma le notifiche dell'app mobile non funzioneranno per tutti gli utenti fino a quando non verrà riattivata l'app mobile.

### <a name="removing-an-authentication-provider"></a>Rimozione di un provider di autenticazione

> [!CAUTION]
> Non viene confermata l'eliminazione di un provider di autenticazione. La selezione di **Delete** è un processo permanente.

I provider di autenticazione sono disponibili nella **portale di Azure** > **Azure Active Directory** i **provider** **di > a** più fattori > . Fare clic su provider elencati per visualizzare i dettagli e le configurazioni associate a tale provider.

Prima di rimuovere un provider di autenticazione, prendere nota delle impostazioni personalizzate configurate nel provider. Decidere quali impostazioni devono essere migrate alle impostazioni di autenticazione a più fattori generali dal provider e completare la migrazione di tali impostazioni. 

I server di autenticazione a più fattori di Azure collegati ai provider dovranno essere riattivati usando le credenziali generate in **portale di Azure** >  > **Azure Active Directory** **le impostazioni**dell'autenticazione a più **fattori > ** server. Prima di riattivare, i file seguenti devono essere eliminati dalla directory `\Program Files\Multi-Factor Authentication Server\Data\` nei server di autenticazione a più fattori di Azure nell'ambiente in uso:

- caCert
- cert
- groupCACert
- groupKey
- groupName
- licenseKey
- pkey

![Eliminare un provider di autenticazione dal portale di Azure](./media/concept-mfa-authprovider/authentication-provider-removal.png)

Dopo aver verificato che sia stata eseguita la migrazione di tutte le impostazioni, è possibile passare al **portale di Azure** > **Azure Active Directory** >  > **provider** di autenticazione a più fattori e selezionare i puntini di sospensione e selezionare **Elimina**.

> [!WARNING]
> L'eliminazione di un provider di autenticazione eliminerà eventuali informazioni di report associate a tale provider. È possibile salvare i report attività prima di eliminare il provider.

> [!NOTE]
> Gli utenti con versioni precedenti dell'app Microsoft Authenticator e del server di autenticazione a più fattori di Azure potrebbero dover registrare nuovamente l'app.

## <a name="next-steps"></a>Passaggi successivi

[Configurare le impostazioni di Multi-Factor Authentication](howto-mfa-mfasettings.md)
