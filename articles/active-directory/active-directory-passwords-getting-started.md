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
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 07c7f3ad066c735054cb339f6e09aa4d7d23f23a
ms.contentlocale: it-it
ms.lasthandoff: 08/11/2017

---
# <a name="quickstart-azure-ad-self-service-password-reset"></a>Guida introduttiva: Reimpostazione self-service della password di Azure AD

> [!IMPORTANT]
> **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md).

## <a name="rapidly-deploy-self-service-password-reset"></a>Distribuire rapidamente la reimpostazione self-service della password

La reimpostazione self-service della password (SSPR) offre agli amministratori IT una modalità semplice per consentire agli utenti di reimpostare o sbloccare le password o gli account. Il sistema include report dettagliati per tenere traccia del momento in cui gli utenti usano il sistema, oltre a notifiche per segnalare usi impropri.

Questa guida presuppone che sia già disponibile un tenant di valutazione o con licenza di Azure AD. Per informazioni sulla configurazione di Azure AD, vedere l'[introduzione ad Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/).

1. Nel tenant di Azure AD esistente, selezionare **"Reimpostazione password"**

2. Nella schermata **"Proprietà"** scegliere una delle opzioni seguenti in "Reimpostazione password self-service abilitata"
    * Nessuno: nessuno può usare la reimpostazione password self-service
    * Un gruppo: solo i membri di un gruppo specifico di Azure AD scelti dall'amministratore possono usare la reimpostazione password self-service
    * Tutti: tutti gli utenti con account nel tenant di Azure AD possono usare la reimpostazione password self-service

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

4. CONSIGLIATO: **"Personalizzazione"** consente di modificare il collegamento "Contattare l'amministratore" in modo che punti a una pagina o a un indirizzo di posta elettronica definito

5. FACOLTATIVO: La schermata **"Registrazione"** offre agli amministratori le opzioni seguenti:
    * Richiedere agli utenti di registrarsi all'accesso
    * Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione

6. FACOLTATIVO: La schermata **"Notifica"** offre agli amministratori le opzioni seguenti:
    * Inviare notifiche agli utenti al momento della reimpostazione della password
    * Inviare una notifica a tutti gli amministratori quando altri amministratori reimpostano le proprie password

**A questo punto è stata configurata la reimpostazione password self-service per il tenant di Azure AD**. È possibile interrompere la procedura o proseguire con la configurazione della sincronizzazione delle password con un dominio AD locale.

> [!NOTE]
> Testare la reimpostazione password self-service con un utente e non con un amministratore, perché Microsoft applica requisiti di autenticazione avanzata per gli account di tipo amministratore di Azure. Per altre informazioni sui criteri delle password amministratore, vedere l'[articolo sui criteri delle password](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-existing-identity-source"></a>Configurare la sincronizzazione con un'origine delle identità esistente

Per abilitare la sincronizzazione delle identità locali con Azure AD è necessario installare e configurare [Azure AD Connect](./connect/active-directory-aadconnect.md) in un server dell'organizzazione. Questa applicazione gestisce la sincronizzazione di utenti e gruppi dall'origine delle identità esistente al tenant di Azure AD.

* [Eseguire l'aggiornamento da DirSync o Azure AD Sync ad Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Introduzione alle impostazioni rapide per Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md)
* [Configurare il writeback delle password](active-directory-passwords-writeback.md#configuring-password-writeback) per riscrivere le password da Azure AD alla directory locale.

## <a name="disabling-self-service-password-reset"></a>Disabilitazione della reimpostazione self-service della password

Per disabilitare la reimpostazione self-service della password è sufficiente aprire il tenant di Azure AD e passare a **Reimpostazione password > Proprietà** e scegliere **Nessuno** in **Reimpostazione password self-service abilitata**

### <a name="learn-more"></a>Altre informazioni
I collegamenti seguenti forniscono altre informazioni sull'uso della reimpostazione della password con Azure AD

* [**Licenze**](active-directory-passwords-licensing.md): configurare le licenze di Azure AD
* [**Dati** ](active-directory-passwords-data.md): informazioni sui dati necessari e su come vengono usati per la gestione delle password
* [**Implementazione**](active-directory-passwords-best-practices.md): pianificare e distribuire agli utenti la reimpostazione password self-service usando le istruzioni disponibili in questo articolo
* [**Personalizzazione**](active-directory-passwords-customize.md) - personalizzare l'aspetto dell'esperienza della reimpostazione password self-service per l'azienda.
* [**Criteri**](active-directory-passwords-policy.md) - comprendere e impostare i criteri password di Azure AD
* [**Creazione di report**](active-directory-passwords-reporting.md) - verificare se, quando e dove gli utenti accedono alla reimpostazione password self-service
* [**Approfondimento tecnico**](active-directory-passwords-how-it-works.md): approfondimento sul funzionamento
* [**Domande frequenti**](active-directory-passwords-faq.md) - Come Perché? Cosa? Dove? Chi? Quando? - Risposte alle domande di maggiore interesse
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere i problemi comuni con la reimpostazione password self-service

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato illustrato come configurare la reimpostazione self-service della password per gli utenti. Per passare al portale di Azure per completare questa procedura, seguire il collegamento seguente per il portale.

> [!div class="nextstepaction"]
> [Abilitare la reimpostazione self-service delle password](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)


