---
title: 'Esercitazione: Configurare Salesforce per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 812bc3c2e13c4513161eebb30b93fee21cc091b7
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Esercitazione: Configurare Salesforce per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in Salesforce e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Salesforce.

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory.
*   È necessario disporre di un tenant valido per Salesforce for Work o Salesforce for Education. È possibile usare un account della versione di valutazione gratuita per entrambi i servizi.
*   Account utente in Salesforce con autorizzazioni di amministratore di team.

## <a name="assigning-users-to-salesforce"></a>Assegnazione di utenti a Salesforce

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti o gruppi in Azure AD devono accedere all'app Salesforce. Dopo aver deciso, è possibile assegnare questi utenti all'app Salesforce seguendo le istruzioni riportate in [Assegnare un utente o un gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Suggerimenti importanti per l'assegnazione di utenti a Salesforce

*   È consigliabile assegnare un singolo utente di Azure AD a Salesforce per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*  Quando si assegna un utente a Salesforce, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning

    > [!NOTE]
    > Questa app importa ruoli personalizzati da Salesforce come parte del processo di provisioning che il cliente può decidere di selezionare durante l'assegnazione di utenti

## <a name="enable-automated-user-provisioning"></a>Abilitare il provisioning utenti automatico

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di Salesforce e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Salesforce in base all'assegnazione di utenti e gruppi in Azure AD.

>[!Tip]
>Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Salesforce, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="configure-automatic-user-account-provisioning"></a>Configurare il provisioning automatico degli account utente

In questa sezione viene descritto come abilitare il provisioning utenti degli account utente di Active Directory in Salesforce.

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Se è già stato configurato Salesforce per l'accesso Single Sign-On, cercare l'istanza di Salesforce usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Salesforce** nella raccolta di applicazioni. Selezionare Salesforce nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di Salesforce e quindi la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![provisioning](./media/active-directory-saas-salesforce-provisioning-tutorial/provisioning.png)

5. Nella sezione **Credenziali di amministratore** specificare le impostazioni di configurazione seguenti:
   
    a. Nella casella di testo **Nome utente amministratore** digitare un nome account di Salesforce con il profilo **Amministratore di sistema** assegnato in Salesforce.com.
   
    b. Nella casella di testo **Password amministratore** digitare la password per questo account.

6. Per ottenere il token di sicurezza di Salesforce, aprire una nuova scheda e accedere allo stesso account di amministratore di Salesforce. Nell'angolo superiore destro della pagina fare clic sul proprio nome e quindi su **Impostazioni**.

     ![Enable automatic user provisioning](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-my-settings.png "Enable automatic user provisioning")

7. Nel pannello di navigazione sinistro fare clic su **My Personal Information** (Informazioni personali) per espandere la sezione corrispondente e quindi fare clic su **Reset My Security Token** (Reimposta token di sicurezza personale).
  
    ![Enable automatic user provisioning](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-personal-reset.png "Enable automatic user provisioning")

8. Nella pagina **Reset Security Token** (Reimposta token di sicurezza) fare clic sul pulsante **Reset Security Token** (Reimposta token di sicurezza).

    ![Enable automatic user provisioning](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-reset-token.png "Enable automatic user provisioning")

9. Controllare la casella di posta elettronica associata a questo account di amministratore. Cercare un messaggio di posta elettronica da Salesforce.com contenente il nuovo token di sicurezza.

10. Copiare il token, passare alla finestra di Azure AD e incollarlo nel campo **Token segreto**.

11. L'**URL del tenant** deve essere immesso se l'istanza di Salesforce si trova in Salesforce Government Cloud. In caso contrario, è facoltativo. Immettere l'URL del tenant usando il formato https://your-instance.my.salesforce.com, sostituendo your-instance con il nome dell'istanza di Salesforce.

12. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app Salesforce.

13. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo qui di seguito.

14. Fare clic su **Salva**.  
    
15.  Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Salesforce** (Sincronizza utenti di Azure Active Directory in Salesforce).

16. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Salesforce. Notar e che gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Salesforce per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

17. Per abilitare il servizio di provisioning di Azure AD per Salesforce, impostare **Stato del provisioning** su **Attivato** nella sezione Impostazioni

18. Fare clic su **Salva**.

Viene avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a Salesforce nella sezione Utenti e gruppi. Notare che la sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 20 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app Salesforce.

È ora possibile creare un account di test. Attendere 20 minuti per verificare che l'account sia stato sincronizzato con Salesforce.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configurare l'accesso Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
