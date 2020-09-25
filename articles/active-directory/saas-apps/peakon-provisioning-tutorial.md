---
title: 'Esercitazione: configurare il provisioning utenti automatico di Peakon con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Peakon.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 06fca39b1170d36f22040ccf48eb02f948e47e41
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91304673"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Esercitazione: configurare Peakon per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in Peakon e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Peakon.

> [!NOTE]
>  L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone che siano già presenti i prerequisiti seguenti

* Un tenant di Azure AD.
* [Tenant di Peakon](https://peakon.com/us/pricing/).
* Un account utente in Peakon con autorizzazioni di amministratore.

## <a name="assigning-users-to-peakon"></a>Assegnazione di utenti a Peakon

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Peakon. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a Peakon seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Suggerimenti importanti per l'assegnazione di utenti a Peakon 

* È consigliabile assegnare un singolo Azure AD utente a Peakon per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Peakon, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-peakon-for-provisioning"></a>Configurare Peakon per il provisioning

1.  Accedere a [Peakon Admin Console](https://app.Peakon.com/login). Fare clic su **Configurazione**. 

    ![Console di amministrazione di Peakon](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Selezionare **Integrations (integrazioni**).
    
    ![Screenshot delle opzioni di configurazione con l'opzione Integrations denominata.](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Abilitare il **provisioning dei dipendenti**.

    ![Screenshot della sezione del provisioning dei dipendenti con l'opzione Abilita denominata.](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Copiare i valori per l' **URL SCIM 2,0** e il **token di porta OAuth**. Questi valori verranno immessi nel campo **URL tenant** e **token segreto** nella scheda provisioning dell'applicazione Peakon nel portale di Azure.

    ![Peakon creare token](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Aggiungere Peakon dalla raccolta

Per configurare Peakon per il provisioning utenti automatico con Azure AD, è necessario aggiungere Peakon dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Peakon**, selezionare **Peakon** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Peakon nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Configurazione del provisioning utenti automatico in Peakon 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Peakon in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per Peakon, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di Peakon](peakon-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Per configurare il provisioning utenti automatico per Peakon in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Peakon**.

    ![Collegamento di Peakon nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni Gestisci con l'opzione di provisioning denominata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa modalità di provisioning con l'opzione automatica chiamata.](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l' **URL scim 2,0** e i valori dei **token di porta OAuth** recuperati in precedenza rispettivamente in **URL tenant** e **token segreto** . Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a Peakon. Se la connessione non riesce, verificare che l'account Peakon disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

8. Fare clic su **Salva**.

9. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Peakon**.

    ![Mapping utente Peakon](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Esaminare gli attributi utente sincronizzati da Azure AD a Peakon nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Peakon per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Peakon](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni seguenti fornite nell'esercitazione relativa al       [filtro di ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su Peakon.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Tutti gli attributi utente personalizzati in Peakon devono essere estesi dall'estensione utente SCIM personalizzata di Peakon di `urn:ietf:params:scim:schemas:extension:peakon:2.0:User` .

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)