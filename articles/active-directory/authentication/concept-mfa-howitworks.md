---
title: Panoramica di Azure Multi-Factor Authentication
description: Informazioni sul modo in cui Azure Multi-Factor Authentication aiuta a proteggere l'accesso ai dati e alle applicazioni, soddisfacendo al tempo stesso la richiesta di un processo di accesso semplice.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5f8d08f0814ec69719c002ea9efd39bb38b7d16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88718017"
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

Azure Multi-Factor Authentication aiuta a proteggere l'accesso ai dati e alle applicazioni mantenendo la semplicità per gli utenti. Offre ulteriore sicurezza richiedendo una seconda forma di autenticazione nonché un'autenticazione avanzata tramite una gamma di [metodi di autenticazione semplici](concept-authentication-methods.md). Gli utenti possono o meno visualizzare una richiesta di autenticazione MFA in base alle decisioni di configurazione prese da un amministratore.

Le applicazioni o i servizi non devono apportare modifiche per l'uso di Azure Multi-Factor Authentication. I prompt di verifica fanno parte dell'evento di accesso Azure AD, che richiede ed elabora automaticamente la richiesta di autenticazione a più fattori quando necessario.

## <a name="available-verification-methods"></a>Metodi di verifica disponibili

Quando un utente accede a un'applicazione o a un servizio e riceve una richiesta di autenticazione a più fattori, può scegliere una delle forme registrate di verifica aggiuntiva. Un amministratore potrebbe richiedere la registrazione di questi metodi di verifica Multi-Factor Authentication di Azure oppure l'utente può accedere al proprio [profilo](https://myprofile.microsoft.com) personale per modificare o aggiungere metodi di verifica.

Con Multi-Factor Authentication di Azure è possibile usare le seguenti forme di verifica aggiuntive:

* App Microsoft Authenticator
* Token hardware OATH
* SMS
* Chiamata vocale

## <a name="how-to-enable-and-use-azure-multi-factor-authentication"></a>Come abilitare e usare Azure Multi-Factor Authentication

Utenti e gruppi possono essere abilitati per Multi-Factor Authentication di Azure per richiedere una verifica aggiuntiva durante l'evento di accesso. Le [impostazioni predefinite di sicurezza](../fundamentals/concept-fundamentals-security-defaults.md) sono disponibili per tutti i tenant Azure ad per consentire rapidamente l'uso dell'app Microsoft Authenticator per tutti gli utenti.

Per controlli più granulari, è possibile usare i criteri di [accesso condizionale](../conditional-access/overview.md) per definire eventi o applicazioni che richiedono l'autenticazione a più fattori. Questi criteri possono consentire eventi di accesso regolari quando l'utente si trova nella rete aziendale o in un dispositivo registrato, ma richiede altri fattori di verifica quando è remoto o in un dispositivo personale.

![Diagramma della panoramica del funzionamento dell'accesso condizionale per proteggere il processo di accesso](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle licenze, vedere [funzionalità e licenze per Azure multi-factor authentication](concept-mfa-licensing.md).

Per vedere l'autenticazione a più fattori in azione, abilitare Multi-Factor Authentication di Azure per un set di utenti di test nell'esercitazione seguente:

> [!div class="nextstepaction"]
> [Abilitare Azure Multi-Factor Authentication](./tutorial-enable-azure-mfa.md)