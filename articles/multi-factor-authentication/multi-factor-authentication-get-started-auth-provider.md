---
title: Introduzione al provider di Azure Multi-Factor Authentication | Documentazione di Microsoft
description: Informazioni su come creare un provider di Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 71038dd1957f5322acc3d54600481e663d855151
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="getting-started-with-an-azure-multi-factor-authentication-provider"></a>Introduzione ai provider Azure Multi-Factor Authentication
La verifica in due passaggi è disponibile per impostazione predefinita per gli amministratori globali che si occupano di utenti di Azure Active Directory e Office 365. Per sfruttare le [funzionalità avanzate](multi-factor-authentication-whats-next.md), è tuttavia consigliabile acquistare la versione completa di Azure Multi-Factor Authentication (MFA).

Un provider di Azure Multi-Factor Authentication viene usato per sfruttare le funzionalità fornite dalla versione completa di Azure MFA. È destinato agli utenti che **non hanno ottenuto licenze tramite Azure MFA, Azure AD Premium o Enterprise Mobility + Security (EMS)**.  Azure MFA, Azure AD Premium e EMS includono le versione completa di Azure MFA per impostazione predefinita. Se si dispone di licenze, non è necessario un provider di Azure Multi-Factor Authentication.

È necessario un provider di Azure Multi-Factor Authentication per scaricare l'SDK.

> [!IMPORTANT]
> Per scaricare l'SDK, è necessario creare un provider di Azure Multi-Factor Authentication, anche se si dispone di licenze di Azure MFA, AAD Premium o EMS.  Se si crea un provider di Azure Multi-Factor Authentication a tale scopo e si dispone già di licenze, creare il provider con il modello **Per utente abilitato**. Collegare quindi il Provider alla directory contenente le licenze di Azure MFA, Azure AD Premium o EMS. Con questa configurazione verranno eseguiti addebiti solo se il numero di utenti singoli che eseguono la verifica in due passaggi è maggiore del numero di licenze possedute.

## <a name="what-is-an-mfa-provider"></a>Informazioni sui provider MFA

Se non si dispone di licenze per Azure Multi-Factor Authentication, è possibile creare un provider di autenticazione per richiedere la verifica in due passaggi per gli utenti. Se si sviluppa un'app personalizzata e si vuole abilitare Azure MFA, creare un provider di autenticazione e [scaricare l'SDK](multi-factor-authentication-sdk.md).

Esistono due tipi di provider di autenticazione, la cui differenza consiste nella modalità di addebito per la sottoscrizione di Azure. L'opzione in base al numero di autenticazioni calcola il numero di autenticazioni eseguite sul tenant in un mese. Questa opzione è consigliata se si dispone di un numero di utenti che eseguono l'autenticazione solo occasionalmente, come quando si richiede MFA per un'applicazione personalizzata. L'opzione in base al numero di utenti calcola il numero di persone nel tenant che eseguono la verifica in due passaggi in un mese. Questa opzione è consigliata se alcuni utenti possiedono già una licenza, ma è necessario estendere MFA ad altri utenti oltre ai termini di licenza.

## <a name="create-an-mfa-provider---public-preview"></a>Creare un provider MFA (anteprima pubblica)

Per creare un provider Azure Multi-Factor Authentication nel portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore. 
2. Selezionare **Azure Active Directory** > **Server MFA**.
3. Selezionare **Provider**.
4. Selezionare **Aggiungi**.
5. Compilare i campi seguenti e quindi selezionare **Aggiungi**.
   - **Nome**: nome del provider.
   - **Modello di utilizzo**: scegliere una delle due opzioni:
      * Per autenticazione: modello di acquisto in cui è previsto l'addebito in base al numero di autenticazioni. Usato, in genere, per scenari in cui viene usato Azure Multi-Factor Authentication in un'applicazione per il consumer.
      * Per utente abilitato: modello di acquisto in cui è previsto l'addebito in base al numero di utenti abilitati. Usato, in genere, per l'accesso dei dipendenti alle applicazioni come Office 365. Scegliere questa opzione se alcuni utenti dispongono già della licenza per Azure MFA.
   - **Sottoscrizione**: sottoscrizione di Azure a cui verrà addebitata l'attività di verifica in due passaggi tramite il provider. 
   - **Directory**: tenant di Azure Active Directory a cui è associato il provider. Tenere presente quanto segue:
      * Non è necessaria una directory di Azure AD per creare un provider. Lasciare vuota la casella se si prevede di scaricare solo il server Azure Multi-Factor Authentication o l'SDK.
      * Il provider deve essere associato a una directory di Azure AD per sfruttare le funzionalità avanzate.
      * A una directory di Azure AD può essere associato un solo provider.

## <a name="create-an-mfa-provider"></a>Creare un provider MFA
Per creare un provider Azure Multi-Factor Authentication nel portale classico, seguire questa procedura:

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com) come amministratore.
2. A sinistra, selezionare **Active Directory**.
3. Nella parte superiore della pagina Active Directory selezionare **Provider di Autenticazione a più fattori**.
   
   ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)

4. Nella parte inferiore fare clic su **Nuovo**.
   
   ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)

5. In Servizi app selezionare **Provider di Autenticazione a più fattori**
   
   ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)

6. Selezionare **Creazione rapida**.
   
   ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)

7. Compilare i campi seguenti, quindi selezionare **Crea**.
   1. **Nome**: nome del provider.
   2. **Modello di utilizzo**: scegliere una delle due opzioni:
      * Per autenticazione: modello di acquisto in cui è previsto l'addebito in base al numero di autenticazioni. Usato, in genere, per scenari in cui viene usato Azure Multi-Factor Authentication in un'applicazione per il consumer.
      * Per utente abilitato: modello di acquisto in cui è previsto l'addebito in base al numero di utenti abilitati. Usato, in genere, per l'accesso dei dipendenti alle applicazioni come Office 365. Scegliere questa opzione se alcuni utenti dispongono già della licenza per Azure MFA.
   3. **Directory**: tenant di Azure Active Directory a cui è associato il provider. Tenere presente quanto segue:
      * Non è necessaria una directory di Azure AD per creare un provider. Lasciare vuota la casella se si prevede di scaricare solo il server Azure Multi-Factor Authentication o l'SDK.
      * Il provider deve essere associato a una directory di Azure AD per sfruttare le funzionalità avanzate.
      * A una directory di Azure AD può essere associato un solo provider.  
      ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)

8. Facendo clic su Crea, viene creato il provider Multi-Factor Authentication e viene visualizzato un messaggio simile al seguente: **Creazione del provider di Multi-Factor Authentication completata**. Fare clic su **OK**.  
   
   ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)  

## <a name="manage-your-mfa-provider"></a>Gestire il provider MFA

Non è possibile modificare il modello di utilizzo (per utente abilitato o per autenticazione) dopo la creazione di un provider di Multi-Factor Authentication. È tuttavia possibile eliminare il provider di Multi-Factor Authentication e quindi crearne uno con un modello di utilizzo diverso.

Se il provider di Multi-Factor Authentication corrente è associato a una directory di Azure AD, è possibile eliminare il provider di Multi-Factor Authentication e crearne uno collegato allo stesso tenant di Azure AD. In alternativa, se è stato acquistato un numero sufficiente di licenze MFA, Azure AD Premium o Enterprise Mobility + Security (EMS) per tutti gli utenti che sono abilitati per l'autenticazione a più fattori, è possibile eliminare del tutto il provider di MFA.

Se il provider di Multi-Factor Authentication non è collegato a un tenant di Azure AD o si collega il nuovo provider di Multi-Factor Authentication a un diverso tenant di Azure AD, le impostazioni utente e le opzioni di configurazione non vengono trasferite. Inoltre, i server Azure MFA esistenti devono essere riattivati usando le credenziali di attivazione generate tramite il nuovo provider di MFA. La riattivazione dei server MFA per collegarli al nuovo provider di MFA non inciderà sull'autenticazione con chiamata telefonica e SMS, ma le notifiche dell'app mobile smetteranno di funzionare per tutti gli utenti fino a quando riattiveranno l'app mobile.

## <a name="next-steps"></a>Passaggi successivi

[Scaricare l'SDK di Multi-Factor Authentication](multi-factor-authentication-sdk.md)

[Configurare le impostazioni di Multi-Factor Authentication](multi-factor-authentication-whats-next.md)
