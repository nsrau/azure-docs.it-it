---
title: Panoramica di Multi-Factor Authentication Azure AD
description: Informazioni su come Azure AD Multi-Factor Authentication consente di salvaguardare l'accesso ai dati e alle applicazioni, garantendo al tempo stesso agli utenti una procedura di accesso semplice.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9563ed283229eb6f43d036629cfe8b84fcde25fc
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839880"
---
# <a name="how-it-works-azure-ad-multi-factor-authentication"></a>Come funziona: Azure AD Multi-Factor Authentication

L'autenticazione a più fattori è un processo per cui, durante l'accesso, all'utente viene chiesto di specificare una forma aggiuntiva di autenticazione, ad esempio l'immissione di un codice nel cellulare o la scansione dell'impronta digitale.

Se si usa solo una password per autenticare un utente, viene lasciato un vettore non sicuro per l'attacco. Se la password è vulnerabile o è stata esposta altrove, come su può avere la certezza che sia effettivamente l'utente che accede con il nome utente e la password e non un utente malintenzionato? Quando è necessaria una seconda forma di autenticazione, la sicurezza viene rafforzata, perché questo fattore aggiuntivo non è facile da ottenere o duplicare.

![Immagine concettuale delle diverse forme di autenticazione a più fattori](./media/concept-mfa-howitworks/methods.png)

Azure AD Multi-Factor Authentication funziona richiedendo due o più dei metodi di autenticazione seguenti:

* Un'informazione nota, in genere una password.
* Un oggetto che si possiede, ad esempio un dispositivo attendibile non facile da duplicare, come un telefono o una chiave hardware.
* Una caratteristica personale, ad esempio un'impronta digitale o la scansione del viso per l'identificazione biometrica.

Gli utenti possono registrarsi per la reimpostazione della password self-service e Azure AD Multi-Factor Authentication in un unico passaggio per semplificare l'esperienza di onboarding. Gli amministratori possono definire quali forme di autenticazione secondaria possono essere usate. Azure AD Multi-Factor Authentication può essere necessario anche quando gli utenti eseguono una reimpostazione della password self-service per proteggere ulteriormente il processo.

![Metodi di autenticazione in uso nella schermata di accesso](media/concept-authentication-methods/overview-login.png)

Azure AD Multi-Factor Authentication consente di salvaguardare l'accesso ai dati e alle applicazioni mantenendo la semplicità per gli utenti. Offre ulteriore sicurezza richiedendo una seconda forma di autenticazione nonché un'autenticazione avanzata tramite una gamma di [metodi di autenticazione semplici](concept-authentication-methods.md). Gli utenti possono o meno visualizzare una richiesta di autenticazione MFA in base alle decisioni di configurazione prese da un amministratore.

Le applicazioni o i servizi non devono apportare modifiche per usare Azure AD Multi-Factor Authentication. I prompt di verifica fanno parte dell'evento di accesso Azure AD, che richiede ed elabora automaticamente la richiesta di autenticazione a più fattori quando necessario.

## <a name="available-verification-methods"></a>Metodi di verifica disponibili

Quando un utente accede a un'applicazione o a un servizio e riceve una richiesta di autenticazione a più fattori, può scegliere una delle forme registrate di verifica aggiuntiva. Un amministratore potrebbe richiedere la registrazione di questi Azure AD Multi-Factor Authentication metodi di verifica oppure l'utente può accedere al proprio [profilo](https://myprofile.microsoft.com) personale per modificare o aggiungere metodi di verifica.

Con Azure AD Multi-Factor Authentication è possibile utilizzare le seguenti forme di verifica aggiuntive:

* App Microsoft Authenticator
* Token hardware OATH
* sms
* Chiamata vocale

## <a name="how-to-enable-and-use-azure-ad-multi-factor-authentication"></a>Come abilitare e usare Azure AD Multi-Factor Authentication

Utenti e gruppi possono essere abilitati per Azure AD Multi-Factor Authentication di richiedere una verifica aggiuntiva durante l'evento di accesso. Le [impostazioni predefinite di sicurezza](../fundamentals/concept-fundamentals-security-defaults.md) sono disponibili per tutti i tenant Azure ad per consentire rapidamente l'uso dell'app Microsoft Authenticator per tutti gli utenti.

Per controlli più granulari, è possibile usare i criteri di [accesso condizionale](../conditional-access/overview.md) per definire eventi o applicazioni che richiedono l'autenticazione a più fattori. Questi criteri possono consentire eventi di accesso regolari quando l'utente si trova nella rete aziendale o in un dispositivo registrato, ma richiede altri fattori di verifica quando è remoto o in un dispositivo personale.

![Diagramma della panoramica del funzionamento dell'accesso condizionale per proteggere il processo di accesso](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle licenze, vedere [funzionalità e licenze per Azure AD multi-factor authentication](concept-mfa-licensing.md).

Per vedere l'autenticazione a più fattori in azione, abilitare Azure AD Multi-Factor Authentication per un set di utenti di test nell'esercitazione seguente:

> [!div class="nextstepaction"]
> [Abilita Azure AD Multi-Factor Authentication](./tutorial-enable-azure-mfa.md)