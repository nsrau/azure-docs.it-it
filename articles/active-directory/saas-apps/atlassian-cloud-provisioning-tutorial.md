---
title: 'Esercitazione: Configurazione di Atlassian Cloud per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e deprovisioning degli account utente in Atlassian Cloud.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.openlocfilehash: f4e76121f7815702270d6601413ff7a4c2c25839
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056980"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Esercitazione: Configurazione di Atlassian Cloud per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Atlassian Cloud e Azure Active Directory (Azure AD) per configurare Azure AD, eseguire automaticamente il provisioning e deprovisioning di utenti e/o gruppi ad Atlassian Cloud.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente in anteprima pubblica. Per altre informazioni sulle condizioni di Microsoft Azure generale di utilizzo per le funzionalità di anteprima, vedere [condizioni per l'utilizzo aggiuntive per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Atlassian Cloud](https://www.atlassian.com/licensing/cloud)
* Un account utente in Atlassian Cloud con le autorizzazioni di amministratore.

> [!NOTE]
> Integrazione del provisioning di Azure AD si basa sul **API SCIM di Atlassian Cloud**, che è disponibile per i team di Atlassian Cloud.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Aggiungere Atlassian Cloud dalla raccolta

Prima configurazione di Atlassian Cloud per provisioning utenti automatico con Azure AD, è necessario aggiungere Atlassian Cloud dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Atlassian Cloud dalla raccolta di applicazioni di Azure AD, seguire i passaggi seguenti:**

1. Nel  **[portale di Azure](https://portal.azure.com)**, nel riquadro di spostamento sinistro, selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare la **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Atlassian Cloud**, selezionare **Atlassian Cloud** nel pannello dei risultati e quindi fare clic sui **Add** pulsante per aggiungere l'applicazione.

    ![Atlassian Cloud nell'elenco dei risultati](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Assegnazione di utenti ad Atlassian Cloud

Azure Active Directory Usa il concetto *assegnazioni* per determinare gli utenti che dovranno ricevere l'accesso alle App selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo di utenti e/o gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono accedere ad Atlassian Cloud. Dopo aver stabilito questo, è possibile assegnare tali utenti e/o gruppi ad Atlassian Cloud seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Suggerimenti importanti per l'assegnazione di utenti ad Atlassian Cloud

* È consigliabile che un singolo utente di Azure AD è assegnato ad Atlassian Cloud per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente di Atlassian Cloud, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Configurazione del provisioning utenti automatico ad Atlassian Cloud 

Questa sezione descrive i passaggi per configurare il provisioning di Azure AD del servizio per creare, aggiornare e disabilitare utenti e/o gruppi in Atlassian Cloud basati su utente e/o le assegnazioni dei gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare basato su SAML single sign-on di Atlassian Cloud, seguendo le istruzioni disponibili nel [esercitazione Atlassian Cloud single sign-on](atlassian-cloud-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Atlassian Cloud in Azure AD:

1. Accedi per il [portale di Azure](https://portal.azure.com) e selezionare **applicazioni aziendali**, selezionare **tutte le applicazioni**, quindi selezionare **Atlassian Cloud**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Atlassian Cloud**.

    ![Collegamento di Atlassian Cloud nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Il Provisioning di Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Il Provisioning di Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Sotto il **credenziali di amministratore** sezione di input il **URL del Tenant** e **Token segreto** dell'account di Atlassian Cloud. Ecco alcuni esempi di questi valori:

   * Nel **URL Tenant** campo, specificare l'endpoint tenant specifico si riceve da Atlassian, come descritto nel passaggio 6. Ad esempio: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * Nel campo **Token segreto** immettere il token del segreto come descritto nel passaggio 6.

6. Passare a [Manager di organizzazione di Atlassian](https://admin.atlassian.com) **> il provisioning degli utenti** e fare clic su **creare un Token**. Copia il **URL di base di Directory** e **Bearer Token** per il **URL del Tenant** e **Token segreto** rispettivi campi.

    ![Il Provisioning di Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian Cloud il Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)

    ![Il Provisioning di Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi ad Atlassian Cloud. Se la connessione non riesce, verificare che l'account di Atlassian Cloud abbia le autorizzazioni di amministratore e riprovare.

    ![Il Provisioning di Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Il Provisioning di Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. Fare clic su **Save**.

10. Sotto il **mapping** sezione, selezionare **Synchronize Azure Active Directory Users ad Atlassian Cloud**.

    ![Il Provisioning di Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD ad Atlassian Cloud nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** le proprietà utilizzate per soddisfare gli account utente in Atlassian Cloud per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Il Provisioning di Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. Sotto il **mapping** sezione, selezionare **sincronizzare Azure gruppi di Active Directory ad Atlassian Cloud**.

    ![Il Provisioning di Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. Esaminare gli attributi gruppo sincronizzati da Azure AD ad Atlassian Cloud nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** proprietà usate devono rispettare i gruppi in Atlassian Cloud per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Il Provisioning di Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il provisioning del servizio di Atlassian Cloud di Azure AD, impostare il **stato del Provisioning** al **sul** nel **impostazioni** sezione.

    ![Il Provisioning di Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. Definire gli utenti e/o gruppi di cui si vuole eseguire il provisioning in Atlassian Cloud selezionando i valori desiderati in **ambito** nel **impostazioni** sezione.

    ![Il Provisioning di Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Il Provisioning di Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività, che descrivono tutte le azioni eseguite dal provisioning del servizio in Atlassian Cloud di Azure AD provisioning.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Consente di Atlassian Cloud il provisioning degli utenti solo da [domini verificati](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian Cloud non supporta attualmente le operazioni di ridenominazione di gruppo. Ciò significa che tutte le modifiche a displayName di un gruppo in Azure AD saranno non aggiornate e riflessi in Atlassian Cloud.
* Il valore della **mail** attributo utente in Azure AD viene popolato solo se l'utente ha una cassetta postale di Microsoft Exchange. Se l'utente non ha uno, è consigliabile eseguire il mapping di un attributo diverso desiderato per il **messaggi di posta elettronica** attributo in Atlassian Cloud.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione di provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
