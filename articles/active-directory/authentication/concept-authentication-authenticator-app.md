---
title: Metodo di autenticazione dell'app Microsoft Authenticator-Azure Active Directory
description: Informazioni sull'uso dell'app Microsoft Authenticator in Azure Active Directory per contribuire a migliorare e proteggere gli eventi di accesso
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: eab1a3f81666cd3bddea9954f210e5825a538356
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840118"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Metodi di autenticazione nell'app Azure Active Directory Microsoft Authenticator

L'app Microsoft Authenticator offre un livello di sicurezza aggiuntivo per l'account aziendale o dell'Istituto di istruzione Azure AD o per il account Microsoft ed è disponibile per [Android](https://go.microsoft.com/fwlink/?linkid=866594) e [iOS](https://go.microsoft.com/fwlink/?linkid=866594). Con l'app Microsoft Authenticator, gli utenti possono eseguire l'autenticazione senza password durante l'accesso o come opzione di verifica aggiuntiva durante la reimpostazione della password self-service (SSPR) o gli eventi di Multi-Factor Authentication Azure AD.

Gli utenti possono ricevere una notifica tramite l'app per dispositivi mobili da approvare o rifiutare, oppure possono usare l'app Authenticator per generare un codice di verifica OATH che può essere inserito in un'interfaccia di accesso. Se si abilitano sia la notifica che il codice di verifica, gli utenti che registrano l'app Authenticator possono usare uno dei due metodi per verificare la propria identità.

Per usare l'app Authenticator in un prompt di accesso invece di una combinazione di nome utente e password, vedere [Abilitare l'accesso senza password con l'app Microsoft Authenticator (anteprima)](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Gli utenti non possono registrare l'app per dispositivi mobili quando abilitano la reimpostazione della password self-service. Possono invece registrarla all'indirizzo [https://aka.ms/mfasetup](https://aka.ms/mfasetup) o come parte della registrazione delle informazioni di sicurezza combinate alla pagina [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

## <a name="passwordless-sign-in"></a>Accesso senza password

Anziché visualizzare la richiesta di una password dopo l'immissione di un nome utente, un utente che ha abilitato l'accesso tramite telefono dall'app Microsoft Authenticator Visualizza un messaggio per toccare un numero nell'app. Quando viene selezionato il numero corretto, il processo di accesso è completo.

![Esempio di accesso del browser che richiede all'utente di approvare l'accesso](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Questo metodo di autenticazione offre un elevato livello di sicurezza e rimuove la necessità per l'utente di fornire una password al momento dell'accesso. L'accesso senza password con l'app Microsoft Authenticator è attualmente in anteprima.

Per iniziare a usare l'accesso senza password, vedere [abilitare l'accesso senza password con l'app Microsoft Authenticator](howto-authentication-passwordless-phone.md).

## <a name="notification-through-mobile-app"></a>Notifica tramite app per dispositivi mobili

L'app Authenticator consente di impedire l'accesso non autorizzato agli account e di arrestare le transazioni illecite eseguendo il push di una notifica allo smartphone o al tablet dell'utente. Gli utenti visualizzano la notifica e, se legittima, selezionano **Verifica**. Altrimenti possono selezionare **Nega**.

![Screenshot di un esempio di prompt del Web browser per la notifica dell'app Authenticator per completare il processo di accesso](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Se l'organizzazione dispone di personale che lavora o viaggia in Cina, la *notifica tramite* il metodo di app per dispositivi mobili nei dispositivi Android non funziona in quel paese/area geografica perché i servizi Google Play (incluse le notifiche push) sono bloccati nell'area. Tuttavia, le notifiche iOS funzionano. Per i dispositivi Android, è necessario rendere disponibili metodi di autenticazione alternativi per tali utenti.

## <a name="verification-code-from-mobile-app"></a>Codice di verifica dall'app per dispositivi mobili

L'app Authenticator può essere usata come token software per generare un codice di verifica OATH. Dopo aver inserito il nome utente e la password, si immette il codice inviato dall'app Authenticator nell'interfaccia di accesso. Il codice di verifica offre una seconda forma di autenticazione.

Gli utenti possono avere una combinazione composta da fino a cinque token hardware OATH o applicazioni di autenticazione, quali l'app Microsoft Authenticator, configurate per l'uso in qualsiasi momento.

> [!WARNING]
> Per garantire il livello di sicurezza più elevato per la reimpostazione della password self-service quando viene richiesto un solo metodo di ripristino, il codice di verifica rappresenta l'unica opzione disponibile per gli utenti.
>
> Quando vengono richiesti due metodi, gli utenti possono eseguire la reimpostazione usando la notifica o il codice di verifica, oltre a un altro metodo abilitato.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare l'accesso senza password, vedere [abilitare l'accesso senza password con l'app Microsoft Authenticator](howto-authentication-passwordless-phone.md).

Altre informazioni sulla configurazione dei metodi di autenticazione con l' [API REST di Microsoft Graph beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).
