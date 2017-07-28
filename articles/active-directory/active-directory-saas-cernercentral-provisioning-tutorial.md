---
title: 'Esercitazione: Configurazione di Cerner Central per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Cerner Central.
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: asmalser-msft
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 6ffab1ae12d257510b410a782a724d8d1b8b9272
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017

---

# <a name="tutorial-configuring-cerner-central-for-automatic-user-provisioning"></a>Esercitazione: Configurazione di Cerner Central per il provisioning utenti automatico


Questa esercitazione descrive le procedure da eseguire in Cerner Central e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Cerner Central. 

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory
*   Tenant di Cerner Central 
*   Account amministratore in Cerner Central 

> [!NOTE]
> Azure Active Directory si integra con Cerner Central usando il protocollo [SCIM](http://www.simplecloud.info/).

## <a name="assigning-users-to-cerner-central"></a>Assegnazione di utenti a Cerner Central

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, verranno sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere a Cerner Central. Dopo aver stabilito questo, è possibile assegnare tali utenti a Cerner Central seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente o gruppo a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Suggerimenti importanti per l'assegnazione di utenti a Cerner Central

*    È consigliabile assegnare un singolo utente di Azure AD a Cerner Central per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*    Quando si assegna un utente a Cerner Central, è necessario selezionare il ruolo **Utente** nella finestra di dialogo di assegnazione. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.


## <a name="configuring-user-provisioning-to-cerner-central"></a>Configurazione del provisioning utenti in Cerner Central

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente SCIM di Cerner Central e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Cerner Central in base all'assegnazione di utenti e gruppi in Azure AD.

> [!TIP]
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Cerner Central, seguendo le istruzioni disponibili nel [portale di Azure (https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.


### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Per configurare il provisioning automatico degli account utente in Cerner Central con Azure AD:


Per effettuare il provisioning degli account utente in Cerner Central, è necessario creare un account di sistema e generare un token di connessione OAuth che possa essere usato da Azure AD per la connessione all'endpoint SCIM di Cerner. È consigliabile anche eseguire l'integrazione in un ambiente sandbox Cerner prima della distribuzione in produzione.

1.    Il primo passaggio consiste nel garantire che le persone che gestiscono l'integrazione di Cerner e Azure AD abbiano un account CernerCare, obbligatorio per accedere alla documentazione necessaria per completare le istruzioni. Se necessario, usare gli URL riportati di seguito per creare account CernerCare in ogni ambiente applicabile.

   * Sandbox: https://sandboxcernercare.com/accounts/create

   * Produzione: https://cernercare.com/accounts/create  

2.    È quindi necessario creare un account di sistema per Azure AD. Usare le istruzioni di seguito per richiedere un account di sistema per gli ambienti sandbox e di produzione.

   * Istruzioni: https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Sandbox: https://sandboxcernercentral.com/system-accounts/

   * Produzione: https://cernercentral.com/system-accounts/

3.    Generare quindi un token di connessione OAuth per ogni account di sistema. A questo scopo, seguire queste istruzioni.

   * Istruzioni: https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Sandbox: https://sandboxcernercentral.com/system-accounts/

   * Produzione: https://cernercentral.com/system-accounts/

4. È infine necessario acquisire un ID area autenticazione del roster utenti in Cerner per completare la configurazione con Azure AD. Per informazioni su come acquisire tale ID, vedere: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM. 

5. È ora possibile configurare Azure AD per il provisioning degli account utente in Cerner. Accedere al [portale di Azure](https://portal.azure.com) e passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

6. Se si è già configurato Cerner Central per l'accesso Single Sign-On, cercare l'istanza di Cerner Central usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Cerner Central** nella raccolta di applicazioni. Selezionare Cerner Central nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

7.    Selezionare l'istanza di Cerner Central e quindi la scheda **Provisioning**.

8.    Impostare **Modalità di provisioning** su **Automatico**.

   ![Provisioning in Cerner Central](./media/active-directory-saas-cernercentral-provisioning-tutorial/Cerner.PNG)

9.  Compilare i campi seguenti in **Credenziali amministratore**:

   * Nel campo **URL tenant** immettere un URL nel formato seguente, sostituendo "ID-Area-Autenticazione-Roster-Utenti" con l'ID area autenticazione acquisito nel passaggio 4.

> https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/ID-Area-Autenticazione-Roster-Utenti/Users 

   * Nel campo **Token segreto** immettere il token di connessione OAuth generato nel passaggio 3 e fare clic su **Test connessione**.

   * Nel lato superiore destro del portale verrà visualizzata una notifica di esito positivo.

10. Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo seguente.

11. Fare clic su **Salva**. 

12. Nella sezione **Mapping degli attributi** esaminare gli attributi utente e gruppo che verranno sincronizzati da Azure AD a Cerner Central. Si noti che gli attributi selezionati come proprietà **corrispondenti** verranno usati per trovare le corrispondenze con gli account utente e i gruppi in Cerner Central per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

13. Per abilitare il servizio di provisioning di Azure AD per Cerner Central, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

14. Fare clic su **Salva**. 

Verrà avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a Cerner Central nella sezione Utenti e gruppi. Si noti che la sincronizzazione iniziale richiederà più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 20 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app Cerner Central.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Esercitazione sulla configurazione di Cerner Central per l'accesso Single Sign-On con Azure Active Directory](active-directory-saas-cernercentral-tutorial.md)
* [Gestione del provisioning degli account utente per app aziendali](active-directory-enterprise-apps-manage-provisioning.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).

