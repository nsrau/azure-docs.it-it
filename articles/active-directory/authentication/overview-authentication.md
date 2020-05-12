---
title: Panoramica dell'autenticazione di Azure Active Directory
description: Informazioni sui diversi metodi di autenticazione e sulle funzionalità di sicurezza per gli accessi utente con Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2a6b5c22e2988ed33e6dc15a4729e7bfe938c5f
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82786918"
---
# <a name="what-is-azure-active-directory-authentication"></a>Che cos'è l'autenticazione di Azure Active Directory?

Una delle principali funzionalità di una piattaforma di gestione delle identità consiste nel verificare, o *autenticare*, le credenziali quando un utente accede a un dispositivo, a un'applicazione o a un servizio. In Azure Active Directory (Azure AD) l'autenticazione non prevede solo la semplice verifica di un nome utente e di una password. Per migliorare la sicurezza e ridurre la necessità di rivolgersi all'help desk per assistenza, l'autenticazione di Azure AD include i componenti seguenti:

* Reimpostazione della password self-service
* Azure Multi-Factor Authentication
* Integrazione ibrida per il writeback delle modifiche delle password nell'ambiente locale
* Integrazione ibrida per l'applicazione di criteri di protezione delle password per un ambiente locale
* Autenticazione senza password

## <a name="improve-the-end-user-experience"></a>Migliorare l'esperienza dell'utente finale

Azure AD consente di proteggere l'identità di un utente e di semplificare la sua esperienza di accesso. Funzionalità come la reimpostazione della password self-service consentono agli utenti di aggiornare o cambiare le loro password usando un Web browser da qualsiasi dispositivo. Questa funzionalità è particolarmente utile quando l'utente ha dimenticato la password o se il suo account è bloccato. Senza aspettare il supporto dell'help desk o di un amministratore, un utente può sbloccare il proprio account e continuare a lavorare.

Azure Multi-Factor Authentication consente agli utenti di scegliere una forma di autenticazione aggiuntiva durante l'accesso, ad esempio una telefonata o una notifica dell'app per dispositivi mobili. Questa funzionalità riduce la necessità di una singola forma fissa di autenticazione secondaria, come un token hardware. Se l'utente attualmente non ha una forma di autenticazione aggiuntiva, può scegliere un metodo diverso e continuare a lavorare.

![Metodi di autenticazione in uso nella schermata di accesso](media/concept-authentication-methods/overview-login.png)

L'autenticazione senza password rimuove completamente la necessità di creare e ricordare una password sicura. Funzionalità come Windows Hello for Business o le chiavi di sicurezza FIDO2 consentono agli utenti di accedere a un dispositivo o a un'applicazione senza password. Questa possibilità riduce la complessità associata alla gestione delle password tra ambienti diversi.

## <a name="self-service-password-reset"></a>Reimpostazione della password self-service

La reimpostazione della password self-service consente agli utenti di cambiare o reimpostare la password, senza intervento dell'amministratore o dell'help desk. Se un utente dimentica la password o ha l'account bloccato, può seguire le istruzioni per sbloccarlo autonomamente e tornare al lavoro. Questa funzionalità riduce le chiamate all'help desk e la perdita di produttività quando un utente non riesce ad accedere al dispositivo o a un'applicazione.

La reimpostazione della password self-service funziona negli scenari seguenti:

* **Modifica della password**: l'utente conosce la propria password, ma intende cambiarla.
* **Reimpostazione della password**: l'utente non riesce ad accedere, ad esempio perché ha dimenticato la password e vuole reimpostarla.
* **Sblocco dell'account**: l'utente non riesce ad accedere perché l'account è bloccato e vuole sbloccarlo.

Quando un utente aggiorna o reimposta la password tramite reimpostazione self-service, è anche possibile che ne venga eseguito il writeback in un ambiente di Active Directory locale. Il writeback delle password garantisce a un utente la possibilità di usare immediatamente le credenziali aggiornate con i dispositivi e le applicazioni locali.

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

L'autenticazione a più fattori è un processo per cui, durante l'accesso, all'utente viene chiesto di specificare una forma aggiuntiva di autenticazione, ad esempio l'immissione di un codice nel cellulare o la scansione dell'impronta digitale.

Se si usa solo una password per autenticare un utente, viene lasciato un vettore non sicuro per l'attacco. Se la password è vulnerabile o è stata esposta altrove, come su può avere la certezza che sia effettivamente l'utente che accede con il nome utente e la password e non un utente malintenzionato? Quando è necessaria una seconda forma di autenticazione, la sicurezza viene rafforzata, perché questo fattore aggiuntivo non è facile da ottenere o duplicare.

![Immagine concettuale delle diverse forme di autenticazione a più fattori](./media/concept-mfa-howitworks/methods.png)

Azure Multi-Factor Authentication funziona richiedendo due o più metodi di autenticazione seguenti:

* Un'informazione nota, in genere una password.
* Un oggetto che si possiede, ad esempio un dispositivo attendibile non facile da duplicare, come un telefono o una chiave hardware.
* Una caratteristica personale, ad esempio un'impronta digitale o la scansione del viso per l'identificazione biometrica.

Gli utenti possono registrarsi per la reimpostazione della password self-service e per Azure Multi-Factor Authentication in un unico passaggio per semplificare l'esperienza di onboarding. Gli amministratori possono definire quali forme di autenticazione secondaria possono essere usate. È anche possibile richiedere di applicare Azure Multi-Factor Authentication per proteggere ulteriormente la procedura di reimpostazione della password self-service.

## <a name="password-protection"></a>Password di protezione

Per impostazione predefinita, Azure AD blocca le password vulnerabili, ad esempio *Password1*. Viene automaticamente aggiornato e applicato un elenco globale di password vietate note per essere vulnerabili. Se un utente di Azure AD prova a impostare una di queste password vulnerabili, riceverà una notifica che lo invita a sceglierne una più sicura.

Per aumentare la sicurezza, è possibile definire criteri personalizzati per le password di protezione. Questi criteri possono usare i filtri per bloccare qualsiasi variante di una password contenente ad esempio un nome, come *Contoso*, o una località, come *Londra*.

Per la sicurezza ibrida, è possibile integrare le password di protezione di Azure AD con un ambiente Active Directory locale. Un componente installato nell'ambiente locale riceve da Azure AD l'elenco globale delle password vietate e i criteri personalizzati per le password di protezione, quindi un controller di dominio usa questi dati per elaborare gli eventi di modifica delle password. Questo approccio ibrido garantisce che venga imposto l'uso di password complesse indipendentemente dal modo in cui un utente cambia le proprie credenziali.

## <a name="passwordless-authentication"></a>Autenticazione senza password

L'obiettivo finale per molti ambienti è quello di rimuovere l'uso delle password dagli eventi di accesso. Funzionalità come la password di protezione di Azure o Azure Multi-Factor Authentication contribuiscono a migliorare la sicurezza, ma il nome utente e la password rimangono una forma vulnerabile di autenticazione suscettibile di attacchi di forza bruta.

![Sicurezza rispetto a praticità con il processo di autenticazione che punta all'assenza di password](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Quando si accede con un metodo senza password, le credenziali vengono fornite tramite l'uso di metodi come la biometrica con Windows Hello for Business o una chiave di sicurezza FIDO2. Questi metodi di autenticazione non possono essere facilmente duplicati da un utente malintenzionato.

Azure AD offre la possibilità di eseguire l'autenticazione nativa usando metodi senza password per semplificare l'esperienza di accesso degli utenti e ridurre il rischio di attacchi.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere l'[esercitazione sulla reimpostazione della password self-service][tutorial-sspr] e [Azure Multi-Factor Authentication][tutorial-azure-mfa].

Per altre informazioni sui concetti di reimpostazione della password self-service, vedere [Come funziona la reimpostazione della password self-service di Azure AD][concept-sspr].

Per altre informazioni sui concetti dell'autenticazione a più fattori, vedere [Come funziona Azure Multi-Factor Authentication][concept-mfa].

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
