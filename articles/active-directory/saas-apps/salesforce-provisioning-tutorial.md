---
title: 'Esercitazione: Configurare Salesforce per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d87f935f503098757e4efe402b37958283431b6e
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74120544"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Esercitazione: Configurare Salesforce per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in Salesforce e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Salesforce.

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Tenant di Azure Active Directory
* Tenant di Salesforce.com

> [!IMPORTANT]
> Se si usa un account di valutazione di Salesforce.com, non sarà possibile configurare il provisioning automatizzato degli utenti. Gli account di valutazione non hanno l'accesso API necessario, che viene abilitato solo dopo l'acquisto. Per aggirare questa limitazione, è possibile usare un [account gratuito per sviluppatori](https://developer.salesforce.com/signup) per completare l'esercitazione.

Se si usa un ambiente Salesforce Sandbox, vedere l' [Esercitazione: Integrazione di Azure Active Directory con Salesforce Sandbox](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Assegnazione di utenti a Salesforce

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti o gruppi in Azure AD devono accedere all'app Salesforce. Dopo aver deciso, è possibile assegnare questi utenti all'app Salesforce seguendo le istruzioni riportate in [Assegnare un utente o un gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Suggerimenti importanti per l'assegnazione di utenti a Salesforce

* È consigliabile assegnare un singolo utente di Azure AD a Salesforce per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Salesforce, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning

    > [!NOTE]
    > Come parte del processo di provisioning, quest'app importa da Salesforce profili che il cliente può decidere di selezionare durante l'assegnazione di utenti in Azure AD. Si noti che i profili importati da Salesforce vengono visualizzati come ruoli in Azure AD.

## <a name="enable-automated-user-provisioning"></a>Abilitare il provisioning automatizzato degli utenti

Questa sezione illustra la connessione tra il Azure AD e l' [API di provisioning dell'account utente di Salesforce](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Salesforce in base all'assegnazione di utenti e gruppi in Azure ad.

> [!Tip]
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Salesforce, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="configure-automatic-user-account-provisioning"></a>Configurare il provisioning automatico degli account utente

In questa sezione viene descritto come abilitare il provisioning utenti degli account utente di Active Directory in Salesforce.

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Se è già stato configurato Salesforce per l'accesso Single Sign-On, cercare l'istanza di Salesforce usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Salesforce** nella raccolta di applicazioni. Selezionare Salesforce nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di Salesforce e quindi la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![provisioning](./media/salesforce-provisioning-tutorial/provisioning.png)

5. Nella sezione **Credenziali di amministratore** specificare le impostazioni di configurazione seguenti:

    a. Nella casella di testo **Nome utente amministratore** digitare un nome account di Salesforce con il profilo **Amministratore di sistema** assegnato in Salesforce.com.

    b. Nella casella di testo **Password amministratore** digitare la password per questo account.

6. Per ottenere il token di sicurezza di Salesforce, aprire una nuova scheda e accedere allo stesso account di amministratore di Salesforce. Nell'angolo superiore destro della pagina fare clic sul proprio nome e quindi su **Impostazioni**.

    ![Abilita provisioning utenti automatico](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Abilita provisioning utenti automatico")

7. Nel pannello di navigazione sinistro fare clic su **My Personal Information** (Informazioni personali) per espandere la sezione corrispondente e quindi fare clic su **Reset My Security Token** (Reimposta token di sicurezza personale).
  
    ![Abilita provisioning utenti automatico](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Abilita provisioning utenti automatico")

8. Nella pagina **Reset Security Token** (Reimposta token di sicurezza) fare clic sul pulsante **Reset Security Token** (Reimposta token di sicurezza).

    ![Abilita provisioning utenti automatico](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Abilita provisioning utenti automatico")

9. Controllare la casella di posta elettronica associata a questo account di amministratore. Cercare un messaggio di posta elettronica da Salesforce.com contenente il nuovo token di sicurezza.

10. Copiare il token, passare alla finestra di Azure AD e incollarlo nel campo **Token segreto**.

11. L'**URL del tenant** deve essere immesso se l'istanza di Salesforce si trova in Salesforce Government Cloud. In caso contrario, è facoltativo. Immettere l'URL del tenant usando il formato "https://\<your-instance\>.my.salesforce.com" sostituendo \<your-instance\> con il nome dell'istanza di Salesforce.

12. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app Salesforce.

13. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo qui di seguito.

14. Fare clic su **Salva**.  

15. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Salesforce** (Sincronizza utenti di Azure Active Directory in Salesforce).

16. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Salesforce. Notar e che gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Salesforce per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

17. Per abilitare il servizio di provisioning di Azure AD per Salesforce, impostare **Stato del provisioning** su **Attivato** nella sezione Impostazioni

18. Fare clic su **Salva**.

> [!NOTE]
> Una volta eseguito il provisioning degli utenti nell'applicazione Salesforce, è necessario che l'amministratore configuri le impostazioni specifiche del linguaggio. Per ulteriori informazioni sulla configurazione della lingua, vedere [questo](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) articolo.

Viene avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a Salesforce nella sezione Utenti e gruppi. Notare che la sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai log delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app Salesforce.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Esercitazione: creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="common-issues"></a>Problemi comuni
* Se si verificano problemi nell'autorizzazione dell'accesso a Salesforce, verificare quanto segue:
    * Le credenziali utilizzate hanno accesso amministrativo a Salesforce.
    * La versione di Salesforce utilizzata supporta Accesso Web, ad esempio Developer, Enterprise, sandbox e Unlimited Edition di Salesforce.
    * L'accesso all'API Web è abilitato per l'utente.
* Il servizio di provisioning Azure AD supporta il linguaggio di provisioning, le impostazioni locali e il fuso orario per un utente. Questi attributi si trovano nei mapping degli attributi predefiniti, ma non dispongono di un attributo di origine predefinito. Assicurarsi di selezionare l'attributo di origine predefinito e che l'attributo di origine sia nel formato previsto da SalesForce. Ad esempio, localeSidKey per la lingua inglese (Stati Uniti) è en_US. Esaminare le linee guida fornite [qui](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) per determinare il formato localeSidKey appropriato. I formati languageLocaleKey sono disponibili [qui](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5). Oltre a garantire la correttezza del formato, potrebbe essere necessario assicurarsi che la lingua sia abilitata per gli utenti, come descritto [qui](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5). 
* **SalesforceLicenseLimitExceeded:** Impossibile creare l'utente nell'applicazione di destinazione perché non sono disponibili licenze per questo utente. Ottenere licenze aggiuntive per l'applicazione di destinazione o esaminare le assegnazioni degli utenti e la configurazione del mapping degli attributi per assicurarsi che gli utenti corretti vengano assegnati con gli attributi corretti.
* **SalesforceDuplicateUserName:** Non è possibile eseguire il provisioning dell'utente perché contiene un Salesforce.com ' username ' duplicato in un altro tenant di Salesforce.com.  In Salesforce.com i valori per l'attributo ' username ' devono essere univoci in tutti i tenant di Salesforce.com.  Per impostazione predefinita, il userPrincipalName di un utente in Azure Active Directory diventa il nome utente in Salesforce.com.   Sono disponibili due opzioni.  Un'opzione consiste nell'individuare e rinominare l'utente con ' username ' duplicato nell'altro tenant Salesforce.com, se si amministra anche tale tenant.  L'altra opzione consiste nel rimuovere l'accesso dall'utente Azure Active Directory al tenant Salesforce.com con cui è integrata la directory. Questa operazione verrà ritentata al successivo tentativo di sincronizzazione. 
* **SalesforceRequiredFieldMissing:** Salesforce richiede che determinati attributi siano presenti nell'utente per la creazione o l'aggiornamento dell'utente. Nell'utente manca uno degli attributi obbligatori. Assicurarsi che gli attributi, ad esempio posta elettronica e alias, siano popolati in tutti gli utenti di cui si vuole eseguire il provisioning in Salesforce. È possibile definire l'ambito degli utenti che non dispongono di questi attributi usando [filtri di ambito basati su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 
* Il mapping predefinito degli attributi per il provisioning in Salesforce include l'espressione SingleAppRoleAssignments per eseguire il mapping di appRoleAssignments in Azure AD a ProfileName in Salesforce. Assicurarsi che gli utenti non dispongano di più assegnazioni di ruolo app in Azure AD perché il mapping degli attributi supporta solo il provisioning di un ruolo. 


## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configurare l'accesso Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
