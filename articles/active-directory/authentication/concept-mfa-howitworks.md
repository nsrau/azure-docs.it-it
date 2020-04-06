---
title: Panoramica di Azure Multi-Factor AuthenticationAzure Multi-Factor Authentication overview
description: Informazioni su come Azure Multi-Factor Authentication consente di proteggere l'accesso ai dati e alle applicazioni soddisfacendo al contempo la richiesta degli utenti di un semplice processo di accesso.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c50232abd12c8c0390409bd7bf72833b4f153e02
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667353"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Funzionamento di Azure Multi-Factor Authentication

L'autenticazione a più fattori è un processo per cui, durante l'accesso, all'utente viene chiesto di specificare una forma aggiuntiva di autenticazione, ad esempio l'immissione di un codice nel cellulare o la scansione dell'impronta digitale.

Se si usa solo una password per autenticare un utente, viene lasciato un vettore non sicuro per l'attacco. Se la password è vulnerabile o è stata esposta altrove, come su può avere la certezza che sia effettivamente l'utente che accede con il nome utente e la password e non un utente malintenzionato? Quando è necessaria una seconda forma di autenticazione, la sicurezza viene rafforzata, perché questo fattore aggiuntivo non è facile da ottenere o duplicare.

![Immagine concettuale delle diverse forme di autenticazione a più fattori](./media/concept-mfa-howitworks/methods.png)

Azure Multi-Factor Authentication funziona richiedendo due o più metodi di autenticazione seguenti:

* Un'informazione nota, in genere una password.
* Un oggetto che si possiede, ad esempio un dispositivo attendibile non facile da duplicare, come un telefono o una chiave hardware.
* Una caratteristica personale, ad esempio un'impronta digitale o la scansione del viso per l'identificazione biometrica.

Gli utenti possono registrarsi per la reimpostazione della password self-service e per Azure Multi-Factor Authentication in un unico passaggio per semplificare l'esperienza di onboarding. Gli amministratori possono definire quali forme di autenticazione secondaria possono essere usate. È anche possibile richiedere di applicare Azure Multi-Factor Authentication per proteggere ulteriormente la procedura di reimpostazione della password self-service.

![Metodi di autenticazione in uso nella schermata di accesso](media/concept-authentication-methods/overview-login.png)

Azure Multi-Factor Authentication consente di proteggere l'accesso ai dati e alle applicazioni, mantenendo la semplicità per gli utenti. Offre ulteriore sicurezza richiedendo una seconda forma di autenticazione nonché un'autenticazione avanzata tramite una gamma di [metodi di autenticazione semplici](concept-authentication-methods.md). Gli utenti possono o meno visualizzare una richiesta di autenticazione MFA in base alle decisioni di configurazione prese da un amministratore.

Le applicazioni o i servizi non devono apportare modifiche per usare Azure Multi-Factor Authentication.Your applications or services don't need to make any changes to use Azure Multi-Factor Authentication. Le richieste di verifica fanno parte dell'evento di accesso di Azure AD, che richiede ed elabora automaticamente la richiesta di autenticazione a più fattori quando necessario.

## <a name="available-verification-methods"></a>Metodi di verifica disponibili

Quando un utente accede a un'applicazione o un servizio e riceve un prompt di autenticazione a più fattori, può scegliere tra una delle forme registrate di verifica aggiuntiva. Un amministratore potrebbe richiedere la registrazione di questi metodi di verifica di Azure Multi-Factor Authentication oppure l'utente può accedere al proprio [profilo personale](https://myprofile.microsoft.com) per modificare o aggiungere metodi di verifica.

Con Azure Multi-Factor Authentication è possibile usare le forme aggiuntive seguenti di verifica:The following additional forms of verification can be used with Azure Multi-Factor Authentication:

* App Microsoft Authenticator
* Token hardware OATH
* sms
* Chiamata vocale

## <a name="how-to-enable-and-use-azure-multi-factor-authentication"></a>Come abilitare e usare Azure Multi-Factor AuthenticationHow to enable and use Azure Multi-Factor Authentication

Gli utenti e i gruppi possono essere abilitati per Azure Multi-Factor Authentication per richiedere ulteriore verifica durante l'evento di accesso. [Le impostazioni predefinite](../fundamentals/concept-fundamentals-security-defaults.md) di sicurezza sono disponibili per tutti i tenant di Azure AD per abilitare rapidamente l'uso dell'app Microsoft Authenticator per tutti gli utenti.

Per controlli più granulari, i criteri [di accesso condizionale](../conditional-access/overview.md) possono essere usati per definire eventi o applicazioni che richiedono l'autenticazione a più fattori. Questi criteri possono consentire eventi di accesso regolari quando l'utente si trova nella rete aziendale o in un dispositivo registrato, ma richiedono fattori di verifica aggiuntivi in remoto o su un dispositivo personale.

![Diagramma della panoramica del funzionamento dell'accesso condizionale per proteggere il processo di accesso](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle licenze, vedere [Funzionalità e licenze per Azure Multi-Factor Authentication.](concept-mfa-licensing.md)

Per vedere MFA in azione, abilitare Azure Multi-Factor Authentication per un set di utenti di test nell'esercitazione seguente:To see MFA in action, enable Azure Multi-Factor Authentication for a set of test users in the following tutorial:

> [!div class="nextstepaction"]
> [Abilitare Azure Multi-Factor Authentication](tutorial-mfa-applications.md)
