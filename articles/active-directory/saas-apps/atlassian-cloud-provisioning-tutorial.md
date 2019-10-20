---
title: 'Esercitazione: configurare Atlassian cloud per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Atlassian cloud.
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
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 0c3173841de25a30b84870332c7334a81773e84d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "68561597"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Esercitazione: configurare Atlassian cloud per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in Atlassian cloud e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Atlassian cloud.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).


## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant cloud Atlassian](https://www.atlassian.com/licensing/cloud)
* Un account utente in Atlassian cloud con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning Azure AD si basa sull' **API scim di Atlassian cloud**, disponibile per i team Atlassian cloud.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Aggiungere Atlassian Cloud dalla raccolta

Prima di configurare Atlassian cloud per il provisioning utenti automatico con Azure AD, è necessario aggiungere Atlassian cloud dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Atlassian cloud dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Atlassian cloud**, selezionare **Atlassian cloud** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Atlassian Cloud nell'elenco dei risultati](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Assegnazione di utenti a Atlassian cloud

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Atlassian cloud. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi a Atlassian cloud seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Suggerimenti importanti per l'assegnazione di utenti a Atlassian cloud

* È consigliabile assegnare un singolo utente Azure AD a Atlassian cloud per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Atlassian cloud, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Configurazione del provisioning utenti automatico in Atlassian cloud 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Atlassian cloud in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per Atlassian cloud, seguendo le istruzioni fornite nell' [esercitazione Single Sign-on sul cloud di Atlassian](atlassian-cloud-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Atlassian cloud in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **applicazioni aziendali**, selezionare **tutte le applicazioni**, quindi selezionare **Atlassian cloud**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Atlassian Cloud**.

    ![Collegamento di Atlassian Cloud nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning cloud Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning cloud Atlassian](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del **tenant** e il **token segreto** dell'account del cloud di Atlassian. Ecco alcuni esempi di questi valori:

   * Nel campo **URL tenant** compilare l'endpoint tenant specifico ricevuto dalla Atlassian, come descritto nel passaggio 6. Ad esempio: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * Nel campo **Token segreto** immettere il token del segreto come descritto nel passaggio 6.

6. Passare a [Atlassian Organization Manager](https://admin.atlassian.com) **> provisioning utenti** e fare clic su **Crea un token**. Copiare rispettivamente **l'URL di base della directory** e il token di **portabilità** nei campi **URL tenant** e **token segreto** .

    ![Atlassian il provisioning cloud ](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian il provisioning del cloud ](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)

    ![Provisioning cloud Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Quando si popolano i campi indicati nel passaggio 5, fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a Atlassian cloud. Se la connessione non riesce, verificare che l'account cloud Atlassian disponga delle autorizzazioni di amministratore e riprovare.

    ![Provisioning cloud Atlassian](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning cloud Atlassian](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. Fare clic su **Salva**

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory Users to Atlassian cloud**.

    ![Provisioning cloud Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a cloud Atlassian nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Atlassian cloud per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning cloud Atlassian](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to Atlassian cloud**.

    ![Provisioning cloud Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. Esaminare gli attributi di gruppo sincronizzati da Azure AD a cloud Atlassian nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Atlassian cloud per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning cloud Atlassian](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning Azure AD per Atlassian cloud, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Provisioning cloud Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Atlassian cloud scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Provisioning cloud Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning cloud Atlassian](./media/atlassian-cloud-provisioning-tutorial/save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning in Atlassian cloud.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Atlassian cloud consente il provisioning degli utenti solo da [domini verificati](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian cloud non supporta oggi i rinomi di gruppo. Ciò significa che tutte le modifiche apportate al displayName di un gruppo in Azure AD non verranno aggiornate e riflesse in Atlassian cloud.
* Il valore dell'attributo utente della **posta** in Azure ad viene popolato solo se l'utente dispone di una cassetta postale di Microsoft Exchange. Se l'utente non dispone di un attributo, è consigliabile eseguire il mapping di un attributo desiderato diverso all'attributo **emails** in Atlassian cloud.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
