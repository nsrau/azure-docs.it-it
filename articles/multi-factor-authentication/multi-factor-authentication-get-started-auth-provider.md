---
title: Introduzione al provider di Azure Multi-Factor Authentication | Documentazione di Microsoft
description: Informazioni su come creare un provider di Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4572913c2bc732c31f38021f9d3ccc34417de875
ms.openlocfilehash: be4361af8f3f3024ab7f12114760f6f58e77deaf


---
# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Introduzione all'uso di un provider di Azure Multi-Factor Authentication
La verifica in due passaggi è disponibile per impostazione predefinita per gli amministratori globali che si occupano di utenti di Azure Active Directory e Office 365. Per sfruttare le [funzionalità avanzate](multi-factor-authentication-whats-next.md), è tuttavia consigliabile acquistare la versione completa di Azure Multi-Factor Authentication (MFA).

> [!NOTE]
> Un provider di Azure Multi-Factor Authentication viene usato per sfruttare le funzionalità fornite dalla versione completa di Azure MFA. È destinato agli utenti che **non hanno ottenuto licenze tramite Azure MFA, Azure AD Premium o EMS**.  Azure MFA, Azure AD Premium e EMS includono le versione completa di Azure MFA per impostazione predefinita.  Se si dispone di licenze, non è necessario un provider di Azure Multi-Factor Authentication.

È necessario un provider di Azure Multi-Factor Authentication per scaricare l'SDK.

> [!IMPORTANT]
> Per scaricare l'SDK, creare un provider di Azure Multi-Factor Authentication, anche se si dispone di licenze di Azure MFA, AAD Premium o EMS.  Se si crea un provider di Azure Multi-Factor Authentication a tale scopo e si dispone già di licenze, creare il provider con il modello **Per utente abilitato**. Collegare quindi il Provider alla directory contenente le licenze di Azure MFA, Azure AD Premium o EMS.  In questo modo verranno eseguiti addebiti, solo se il numero di utenti singoli che usano l'SDK è maggiore del numero di licenze possedute.

## <a name="create-a-multi-factor-auth-provider"></a>Creare un provider di Multi-Factor Authentication
Usare la procedura seguente per creare un provider di Azure Multi-Factor Authentication.

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com) come amministratore.
2. A sinistra, selezionare **Active Directory**.
3. Nella parte superiore della pagina Active Directory selezionare **Provider di Autenticazione a più fattori**.
   ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. Nella parte inferiore fare clic su **Nuovo**.
   ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. In Servizi app selezionare **Provider di Multi-Factor Authentication**
   ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Selezionare **Creazione rapida**.
   ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
7. Compilare i campi seguenti, quindi selezionare **Crea**.
   1. **Nome** : nome del provider Multi-Factor Authentication.
   2. **Modello di utilizzo**: per abilitare utenti singoli o per pagare per ogni verifica. Scegliere una delle due opzioni:
      * Per autenticazione: modello di acquisto in cui è previsto l'addebito in base al numero di autenticazioni. Usato, in genere, per scenari in cui viene usato Azure Multi-Factor Authentication in un'applicazione per il consumer.
      * Per utente abilitato: modello di acquisto in cui è previsto l'addebito in base al numero di utenti abilitati. Usato, in genere, per l'accesso dei dipendenti alle applicazioni come Office 365. Scegliere questa opzione se alcuni utenti dispongono già della licenza per Azure MFA.
   3. **Directory** : tenant di Azure Active Directory a cui è associato il provider Multi-Factor Authentication. Tenere presente quanto segue:
      * Non è necessaria una directory di Azure AD per creare un Provider Multi-Factor Authentication. Lasciare vuota la casella se si prevede di usare solo il server Azure Multi-Factor Authentication o l'SDK.
      * Il provider di Azure Multi-Factor Authentication deve essere associato a una directory di Azure AD per sfruttare le funzionalità avanzate.
      * Azure AD Connect, AAD Sync e DirSync sono necessari unicamente se si esegue la sincronizzazione dell'ambiente Active Directory locale con una directory di Azure AD.  Se si usa solo una directory di Azure AD non sincronizzata, non è necessaria.
        ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
8. Facendo clic su Crea, viene creato il provider Multi-Factor Authentication e viene visualizzato un messaggio simile al seguente: **Creazione del provider di Multi-Factor Authentication completata**. Fare clic su **OK**.
   ![Creazione di un provider di MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)




<!--HONumber=Feb17_HO3-->


