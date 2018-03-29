---
title: 'Azure Active Directory B2C: Multi-Factor Authentication | Documentazione Microsoft'
description: Come abilitare la Multi-Factor Authentication in applicazioni consumatori protette da Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: 0f560a02a51239cff0a7d4057ec7beb2f0f5c5c5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure Active Directory B2C: abilitare Multi-Factor Authentication nelle applicazioni destinate agli utenti
Azure Active Directory (Azure AD) B2C si integra direttamente con [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) per consentire l'aggiunta di un secondo livello di protezione alle esperienze di iscrizione e di accesso nelle applicazioni rivolte ai consumatori. Questo è possibile senza scrivere una singola riga di codice. Attualmente è supportata la verifica tramite chiamata telefonica e SMS. Se i criteri di iscrizione e accesso sono già stati creati, è ancora possibile abilitare Multi-Factor Authentication.

> [!NOTE]
> È anche possibile abilitare Multi-Factor Authentication durante la creazione di criteri per l'iscrizione e l'accesso, non solo modificando i criteri esistenti.
> 
> 

Questa funzionalità risulta utile per la gestione di scenari simili ai seguenti:

* Multi-Factor Authentication non viene richiesta per accedere a una sola applicazione, ma viene richiesta per accedere a un'ulteriore applicazione. Ad esempio, l’utente può accedere a una richiesta di assicurazione automatica con un account locale o di social, ma deve verificare il numero di telefono prima di accedere all'applicazione di assicurazione home registrata nella stessa directory.
* Multi-Factor Authentication non viene richiesta per accedere a un'applicazione in generale, ma viene richiesta per l'accesso alle aree sensibili. Ad esempio, l’utente può accedere a un'applicazione bancaria con un account locale o di social e controllare il saldo del conto, ma deve verificare il numero di telefono prima di effettuare un bonifico.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Modificare i criteri di iscrizione per abilitare Multi-Factor Authentication
1. [Seguire questa procedura per passare al pannello delle funzionalità B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Fare clic su **Criteri di iscrizione**.
3. Fare clic sul criterio di iscrizione (ad esempio, "B2C_1_SiUp") per aprirlo.
4. Fare clic su **Autenticazione a più fattori** e impostare **Stato** su **ON**. Fare clic su **OK**.
5. Fare clic su **Salva** nella parte superiore del pannello.

È possibile utilizzare la funzionalità "Esegui ora" nei criteri per verificare l'esperienza utente. Verificare quanto segue:

Un account utente viene creato nella directory prima che venga eseguito il passaggio di Multi-Factor Authentication. Durante il passaggio, all’utente viene richiesto di fornire il proprio numero di telefono e di verificarlo. Se la verifica ha esito positivo, il numero di telefono viene associato all'account utente per un utilizzo successivo. Anche se l'utente annulla o esce, al successivo accesso può venire richiesto all'utente di verificare nuovamente un numero di telefono (con Multi-Factor Authentication abilitata).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Modificare i criteri di accesso per abilitare Multi-Factor Authentication
1. [Seguire questa procedura per passare al pannello delle funzionalità B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Fare clic su **Criteri di accesso**.
3. Fare clic sul criterio di accesso (ad esempio, "B2C_1_SiIn") per aprirlo. Fare clic su **Modifica** nella parte superiore del pannello.
4. Fare clic su **Autenticazione a più fattori** e impostare **Stato** su **ON**. Fare clic su **OK**.
5. Fare clic su **Salva** nella parte superiore del pannello.

È possibile utilizzare la funzionalità "Esegui ora" nei criteri per verificare l'esperienza utente. Verificare quanto segue:

Quando l’utente si iscrive (utilizzando un account locale o social), se un numero di telefono verificato è collegato all'account utente, viene richiesto di verificarlo. Se non è associato alcun numero di telefono, all’utente viene richiesto di fornire un numero di telefono e di verificarlo. Se la verifica ha esito positivo, il numero di telefono viene associato all'account utente per un uso successivo.

## <a name="multi-factor-authentication-on-other-policies"></a>Multi-Factor Authentication per altri criteri
Come descritto in precedenza per i criteri di iscrizione e accesso, è possibile abilitare la Multi-Factor Authentication per i criteri di iscrizione e accesso e quelli di reimpostazione della password. Questa opzione sarà presto disponibile tra i criteri di modifica del profilo.

