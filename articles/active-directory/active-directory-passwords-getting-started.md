---
title: 'Guida introduttiva: Reimpostazione password self-service di Azure AD | Microsoft Docs'
description: Distribuire rapidamente la reimpostazione self-service della password di Azure AD
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 7d97fad04a0aa549d0e3a182282f898302e8e41a
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Distribuzione rapida della reimpostazione della password self-service di Azure AD

> [!IMPORTANT]
> **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md).

La reimpostazione self-service della password (SSPR) offre agli amministratori IT una modalità semplice per consentire agli utenti di reimpostare o sbloccare le password o gli account. Il sistema include report dettagliati per tenere traccia del momento in cui gli utenti usano il sistema, oltre a notifiche per segnalare usi impropri.

Questa guida presuppone che sia già disponibile un tenant di valutazione o con licenza di Azure AD. Per informazioni sulla configurazione di Azure AD, vedere l'[introduzione ad Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/).

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Abilitare la reimpostazione della password self-service per il tenant di Azure AD

1. Nel tenant di Azure AD esistente, selezionare **"Reimpostazione password"**

2. Nella schermata **"Proprietà"** scegliere una delle opzioni seguenti in "Reimpostazione password self-service abilitata":
    * Nessuno: nessuno può usare la reimpostazione password self-service.
    * Selezionato: solo i membri di un gruppo specifico di Azure AD scelti dall'amministratore possono usare la reimpostazione password self-service. È consigliabile definire un gruppo di utenti e usare questa impostazione nella distribuzione per un modello di verifica.
    * Tutti: tutti gli utenti con account nel tenant di Azure AD possono usare la reimpostazione password self-service. È consigliabile impostare questa opzione quando si è pronti a distribuire la funzionalità nell'intero tenant dopo avere completato un modello di verifica.

3. Nella schermata **"Metodi di autenticazione"** scegliere
    * Numero di metodi da reimpostare: è supportato un minimo di uno o un massimo di due
    * Metodi disponibili per gli utenti: ne è necessario almeno uno, ma è consigliabile avere a disposizione un'opzione aggiuntiva
        * **Posta elettronica** invia un messaggio con un codice all'indirizzo di posta elettronica di autenticazione configurato dell'utente
        * **Cellulare** consente all'utente di ricevere una chiamata o un SMS con un codice al numero di telefono cellulare configurato
        * **Telefono ufficio** chiama l'utente fornendo un codice al numero di telefono dell'ufficio configurato
        * Per **Domande di sicurezza** è necessario scegliere
            * Numero di domande necessarie per la registrazione: è il requisito minimo per la registrazione. Un utente può decidere di rispondere a più domande per creare un pool di domande da cui scegliere. Possono essere impostate da 3 a 5 domande. Il valore deve essere maggiore o uguale al numero di domande necessarie per la reimpostazione.
                * È possibile aggiungere domande personalizzate facendo clic sul pulsante "Personalizzata" quando si seleziona Domande di sicurezza
            * Numero di domande necessarie per la reimpostazione: può essere impostato su un valore compreso da 3 a 5. Si tratta delle domande cui rispondere prima di consentire la reimpostazione o lo sblocco della password di un utente.
            
    ![Autenticazione][Authentication]

4. CONSIGLIATO: **"Personalizzazione"** consente di modificare il collegamento "Contattare l'amministratore" in modo che punti a una pagina o a un indirizzo di posta elettronica definito. È consigliabile usare questo collegamento ad esempio per un indirizzo di posta elettronica o un sito Web che gli utenti sono soliti usare per il supporto.

5. FACOLTATIVO: La schermata **"Registrazione"** offre agli amministratori le opzioni seguenti:
    * Richiedere agli utenti di registrarsi all'accesso
    * Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione

6. FACOLTATIVO: La schermata **"Notifica"** offre agli amministratori le opzioni seguenti:
    * Inviare notifiche agli utenti al momento della reimpostazione della password
    * Inviare una notifica a tutti gli amministratori quando altri amministratori reimpostano le proprie password

**A questo punto è stata configurata la reimpostazione password self-service per il tenant di Azure AD**. Gli utenti possono ora usare le istruzioni disponibili negli articoli [Eseguire la registrazione per la reimpostazione password self-service](active-directory-passwords-reset-register.md) e [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md) per aggiornare la password senza l'intervento dell'amministratore. È possibile interrompere la procedura se si usa solo il cloud o proseguire con la configurazione della sincronizzazione delle password con un dominio AD locale.

> [!IMPORTANT]
> Testare la reimpostazione password self-service con un utente e non con un amministratore, perché Microsoft applica requisiti di autenticazione avanzata per gli account di tipo amministratore di Azure. Per altre informazioni sui criteri delle password amministratore, vedere l'[articolo sui criteri delle password](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-existing-identity-source"></a>Configurare la sincronizzazione con un'origine delle identità esistente

Per abilitare la sincronizzazione delle identità locali con Azure AD è necessario installare e configurare [Azure AD Connect](./connect/active-directory-aadconnect.md) in un server dell'organizzazione. Questa applicazione gestisce la sincronizzazione di utenti e gruppi dall'origine delle identità esistente al tenant di Azure AD.

* [Eseguire l'aggiornamento da DirSync o Azure AD Sync ad Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Introduzione alle impostazioni rapide per Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md)
* [Configurare il writeback delle password](active-directory-passwords-writeback.md#configuring-password-writeback) per riscrivere le password da Azure AD alla directory locale.

### <a name="on-premises-policy-change"></a>Modifica dei criteri locali

Se si sincronizzano gli utenti da un dominio di Active Directory locale e si vuole consentire agli utenti di reimpostare le password immediatamente, apportare la modifica seguente ai criteri password locali:

**Configurazione computer** > **Criteri** > **Impostazioni di Windows** > **Impostazioni di sicurezza** > **Criteri account** > **Criteri password**

**Validità minima password**: 0 giorni

Questa impostazione di sicurezza determina il periodo di tempo (in giorni) in cui una password deve essere usata prima che l'utente possa cambiarla. Impostandola su **0 giorni**, gli utenti possono usare la reimpostazione della password self-service se le password vengono cambiate dai team di supporto.

![Criteri][Policy]

## <a name="disabling-self-service-password-reset"></a>Disabilitazione della reimpostazione self-service della password

Per disabilitare la reimpostazione self-service della password è sufficiente aprire il tenant di Azure AD e passare a **Reimpostazione password > Proprietà** e scegliere **Nessuno** in **Reimpostazione password self-service abilitata**

### <a name="learn-more"></a>Altre informazioni
I collegamenti seguenti forniscono altre informazioni sull'uso della reimpostazione della password con Azure AD

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](active-directory-passwords-best-practices.md)
* [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md).
* [Registrarsi per la reimpostazione della password self-service](active-directory-passwords-reset-register.md).
* [Domande sulle licenze](active-directory-passwords-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](active-directory-passwords-data.md)
* [Metodi di autenticazione disponibili per gli utenti](active-directory-passwords-how-it-works.md#authentication-methods)
* [Opzioni dei criteri per la reimpostazione della password self-service](active-directory-passwords-policy.md)
* [Panoramica del writeback delle password](active-directory-passwords-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](active-directory-passwords-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](active-directory-passwords-how-it-works.md)
* [Come risolvere i problemi di reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato illustrato come configurare la reimpostazione self-service della password per gli utenti. Per passare al portale di Azure per completare questa procedura, seguire il collegamento seguente per il portale.

> [!div class="nextstepaction"]
> [Abilitare la reimpostazione self-service delle password](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/active-directory-passwords-getting-started/sspr-authentication-methods.png "Metodi di autenticazione di Azure AD disponibili e quantità necessaria"
[Policy]: ./media/active-directory-passwords-getting-started/password-policy.png "Criteri di gruppo delle password locali impostati su 0 giorni"
