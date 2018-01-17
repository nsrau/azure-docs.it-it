---
title: Guida introduttiva alla reimpostazione della password self-service - Azure Active Directory
description: Distribuire rapidamente la reimpostazione self-service della password di Azure AD
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 7b1a8611392b9f7f9648de53a924498631e171f1
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Distribuzione rapida della reimpostazione della password self-service di Azure AD

> [!IMPORTANT]
> **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso** vedere [Password di Azure AD dimenticata](active-directory-passwords-update-your-own-password.md).

La reimpostazione self-service della password (SSPR) offre agli amministratori IT una modalità semplice per consentire agli utenti di reimpostare o sbloccare le password o gli account. Il sistema include report dettagliati per tenere traccia del momento in cui gli utenti accedono al sistema, oltre a notifiche per segnalare usi impropri.

Questa guida presuppone che sia già disponibile un tenant di valutazione o con licenza di Azure Active Directory (Azure AD). Per informazioni sulla configurazione di Azure AD, vedere l'[introduzione ad Azure AD](get-started-azure-ad.md).

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Abilitare la reimpostazione della password self-service per il tenant di Azure AD

1. Nel tenant di Azure AD esistente, selezionare **Reimpostazione password**.

2. Nella pagina **Proprietà** scegliere una delle opzioni seguenti in **Reimpostazione password self-service abilitata**:
   * **Nessuno**: nessuno può usare la reimpostazione password self-service.
   * **Selezionato**: solo i membri di un gruppo specifico di Azure AD scelti dall'amministratore possono usare la reimpostazione password self-service. È consigliabile definire un gruppo di utenti e usare questa impostazione nella distribuzione di questa funzionalità per un modello di verifica.
   * **Tutti**: tutti gli utenti con account nel tenant di Azure AD possono usare la reimpostazione password self-service. È consigliabile usare questa impostazione quando si è pronti a distribuire la funzionalità nell'intero tenant dopo avere completato un modello di verifica.

   > [!IMPORTANT]
   > Gli account amministratore di Azure potranno sempre reimpostare le password indipendentemente dal valore su cui questa opzione è impostata. 

3. Nella pagina **Metodi di autenticazione** scegliere:
   * **Numero di metodi da reimpostare**: è supportato un minimo di uno o un massimo di due.
   * **Metodi disponibili per gli utenti**: ne è necessario almeno uno, ma è consigliabile avere a disposizione un'opzione aggiuntiva.
      * **Posta elettronica** invia un messaggio con un codice all'indirizzo di posta elettronica di autenticazione configurato dell'utente.
      * **Cellulare**: consente all'utente di ricevere una chiamata o un SMS con un codice al numero di telefono cellulare configurato.
      * **Telefono ufficio**: chiama l'utente fornendo un codice al numero di telefono dell'ufficio configurato.
      * Per **Domande di sicurezza** è necessario scegliere:
         * **Numero di domande necessarie per la registrazione**: è il requisito minimo per la registrazione. Un utente può decidere di rispondere a più domande per creare un pool di domande da cui scegliere. Possono essere impostate da tre a cinque domande. Il valore deve essere maggiore o uguale al numero di domande necessarie per la reimpostazione della password. L'utente può aggiungere domande personalizzate facendo clic sul pulsante **Personalizzata** quando seleziona le domande di sicurezza.
         * **Numero di domande necessarie per la reimpostazione**: può essere impostato su un valore compreso da tre a cinque. Si tratta delle domande cui rispondere prima di consentire la reimpostazione o lo sblocco della password di un utente.
            
    ![Autenticazione][Authentication]

4. Consigliato: in **Personalizzazione** è possibile modificare il collegamento **Contattare l'amministratore** in modo che punti a una pagina o a un indirizzo di posta elettronica definito. È consigliabile usare questo collegamento ad esempio per un indirizzo di posta elettronica o un sito Web che gli utenti sono soliti usare per inviare domande al supporto tecnico.

5. Facoltativo: la pagina **Registrazione** offre agli amministratori le opzioni seguenti:
   * Richiedere agli utenti di registrarsi all'accesso.
   * Impostare il numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione.

6. Facoltativo: la pagina **Notifiche** offre agli amministratori le opzioni seguenti:
   * Inviare notifiche agli utenti al momento della reimpostazione della password.
   * Inviare una notifica a tutti gli amministratori quando altri amministratori reimpostano le proprie password.

A questo punto è stata configurata la reimpostazione password self-service per il tenant di Azure AD. Gli utenti possono ora usare le istruzioni disponibili negli articoli [Eseguire la registrazione per la reimpostazione password self-service](active-directory-passwords-reset-register.md) e [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md) per aggiornare la password senza l'intervento dell'amministratore. È possibile interrompere la procedura se si usa solo il cloud oppure passare alla sezione successiva per configurare la sincronizzazione delle password con un dominio Active Directory locale.

> [!TIP]
> Testare la reimpostazione password self-service con un utente e non con un amministratore, perché Microsoft applica requisiti di autenticazione avanzata per gli account di tipo amministratore di Azure. Per altre informazioni sui criteri delle password amministratore, vedere l'[articolo sui criteri delle password](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-an-existing-identity-source"></a>Configurare la sincronizzazione con un'origine delle identità esistente

Per abilitare la sincronizzazione delle identità locali con Azure AD è necessario installare e configurare [Azure AD Connect](./connect/active-directory-aadconnect.md) in un server dell'organizzazione. Questa applicazione gestisce la sincronizzazione di utenti e gruppi dall'origine delle identità esistente al tenant di Azure AD. Per altre informazioni, vedere:

* [Eseguire l'aggiornamento da DirSync o Azure AD Sync ad Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Introduzione ad Azure AD Connect con le impostazioni rapide](./connect/active-directory-aadconnect-get-started-express.md)
* [Configurare il writeback delle password](active-directory-passwords-writeback.md#configure-password-writeback) per riscrivere le password da Azure AD alla directory locale

### <a name="on-premises-policy-change"></a>Modifica dei criteri locali

Se si sincronizzano gli utenti da un dominio di Active Directory locale e si vuole consentire agli utenti di reimpostare le password immediatamente, apportare la modifica seguente ai criteri password locali:

1. Passare a **Configurazione computer** > **Criteri** > **Impostazioni di Windows** > **Impostazioni di sicurezza** > **Criteri account** > **Criteri password**.

2. Impostare il valore di **Validità minima password** su **0 giorni**.

Questa impostazione di sicurezza determina il periodo di tempo, in giorni, in cui una password deve essere usata prima che l'utente possa cambiarla. Se si imposta il valore di uso minimo su **0 giorni**, gli utenti possono usare la reimpostazione password self-service se le password vengono cambiate dai team di supporto.

![Criteri][Policy]

## <a name="disable-self-service-password-reset"></a>Disabilitare la reimpostazione password self-service

La disabilitazione della reimpostazione password self-service è un'operazione semplice. Aprire il tenant di Azure AD e passare a **Reimpostazione password** > **Proprietà** e scegliere **Nessuno** in **Reimpostazione password self-service abilitata**.

### <a name="learn-more"></a>Altre informazioni
Gli articoli seguenti forniscono altre informazioni sull'uso della reimpostazione della password con Azure AD:

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](active-directory-passwords-best-practices.md)
* [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md)
* [Registrarsi per la reimpostazione della password self-service](active-directory-passwords-reset-register.md)
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

In questa guida introduttiva è stato illustrato come configurare la reimpostazione self-service della password per gli utenti. Per completare questa procedura, passare al portale di Azure:

> [!div class="nextstepaction"]
> [Abilitare la reimpostazione self-service delle password](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/active-directory-passwords-getting-started/sspr-authentication-methods.png "Metodi di autenticazione di Azure AD disponibili e quantità necessaria"
[Policy]: ./media/active-directory-passwords-getting-started/password-policy.png "Criteri di gruppo delle password locali impostati su 0 giorni"

