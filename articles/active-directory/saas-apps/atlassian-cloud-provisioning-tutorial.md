---
title: 'Esercitazione: Configurare Atlassian Cloud per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Atlassian Cloud.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Atlassian Cloud.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 7ddccef00cf1b5ad524c0e1eaa7aed52c0e55197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059335"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Esercitazione: Configurare Atlassian Cloud per il provisioning automatico degli utentiTutorial: Configure Atlassian Cloud for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Atlassian Cloud e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Atlassian Cloud.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un inquilino Atlassian Cloud](https://www.atlassian.com/licensing/cloud)
* Un account utente in Atlassian Cloud con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione di provisioning di Azure AD si basa **sull'API SCIM di Atlassian Cloud**, disponibile per i team di Atlassian Cloud.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Aggiungere Atlassian Cloud dalla raccolta

Prima di configurare Atlassian Cloud per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Atlassian Cloud dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Atlassian Cloud dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Atlassian Cloud from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Atlassian Cloud**, selezionare **Atlassian Cloud** nel riquadro dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Atlassian Cloud nell'elenco dei risultati](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Assegnazione di utenti a Atlassian Cloud

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Atlassian Cloud. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a Atlassian Cloud seguendo le istruzioni qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Suggerimenti importanti per l'assegnazione di utenti a Atlassian Cloud

* È consigliabile assegnare un singolo utente di Azure AD a Atlassian Cloud per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente ad Atlassian Cloud, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Configurazione del provisioning automatico degli utenti in Atlassian Cloud 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Atlassian Cloud in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Atlassian Cloud seguendo le istruzioni fornite [nell'esercitazione Single Sign-On di Atlassian Cloud](atlassian-cloud-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Atlassian Cloud in Azure AD:To configure automatic user provisioning for Atlassian Cloud in Azure AD:

1. Accedere al [portale](https://portal.azure.com) di Azure e selezionare **Applicazioni aziendali**, selezionare **Tutte le applicazioni**, quindi **selezionare Atlassian Cloud**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Atlassian Cloud**.

    ![Collegamento di Atlassian Cloud nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Provisioning cloud atlassiana](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Provisioning cloud atlassiana](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Passare ad [Atlassian Organization Manager](https://admin.atlassian.com) **> selezionare la directory > dell'organizzazione**.

    ![Provisioning cloud atlassiana](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. Fare clic su **Provisioning utente** e quindi su Crea **una directory**. Copiare rispettivamente **l'URL di base della directory** e il token **Bearer** nei campi **URL tenant** e **Token segreto.**

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Nella sezione **Credenziali amministratore** immettere l'URL **tenant** e il **token segreto** dell'account di Atlassian Cloud. Ecco alcuni esempi di questi valori:

   * Nel campo **URL tenant** compilare l'endpoint tenant specifico ricevuto dall'Atlassiano, come descritto nel passaggio 6. Ad esempio: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * Nel campo **Token segreto** popolare il token segreto come descritto nel passaggio 6.

8. Dopo aver popolato i campi visualizzati nel passaggio 7, fare clic su **Test connessione** per verificare che Azure AD possa connettersi ad Atlassian Cloud. Se la connessione non riesce, verificare che l'account Atlassian Cloud disponga delle autorizzazioni di amministratore e riprovare.

    ![Provisioning cloud atlassiana](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning cloud atlassiana](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. Fare clic su **Salva**.

11. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con Atlassian Cloud**.

    ![Provisioning cloud atlassiana](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. Esaminare gli attributi utente sincronizzati da Azure AD ad Atlassian Cloud nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Atlassian Cloud per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning cloud atlassiana](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. Nella sezione **Mapping** selezionare Sincronizza gruppi di Azure Active Directory con il cloud **atlassiano**.

    ![Provisioning cloud atlassiana](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. Esaminare gli attributi di gruppo sincronizzati da Azure AD ad Atlassian Cloud nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in Atlassian Cloud per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning cloud atlassiana](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

16. Per abilitare il servizio di provisioning di Azure AD per Atlassian Cloud, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Atlassian Cloud, change the **Provisioning Status** to On in the **Settings** section.

    ![Provisioning cloud atlassiana](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Atlassian Cloud scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Provisioning cloud atlassiana](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning cloud atlassiana](./media/atlassian-cloud-provisioning-tutorial/save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Atlassian Cloud.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Atlassian Cloud.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Atlassian Cloud consente il provisioning degli utenti solo da [domini verificati.](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)
* Atlassian Cloud non supporta le ridenominazioni di gruppo oggi. Ciò significa che le modifiche apportate al displayName di un gruppo in Azure AD non verranno aggiornate e riflesse in Atlassian Cloud.
* Il valore dell'attributo utente di **posta elettronica** in Azure AD viene popolato solo se l'utente dispone di una cassetta postale di Microsoft Exchange. Se l'utente non ne ha uno, si consiglia di mappare un attributo desiderato diverso all'attributo **emails** in Atlassian Cloud.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png