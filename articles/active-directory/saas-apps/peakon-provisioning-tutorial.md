---
title: 'Esercitazione: Configurare Peakon provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e deprovisioning degli account utente in Peakon.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 2547f34432ca1b4b52f34c343bb4aad2f2407f53
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673145"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Esercitazione: Configurare Peakon per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Peakon e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e deprovisioning di utenti e/o gruppi a Peakon.

> [!NOTE]
>  L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente in anteprima. Per altre informazioni sulle condizioni di Microsoft Azure generale di utilizzo per le funzionalità di anteprima, vedere [condizioni per l'utilizzo aggiuntive per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione si presuppone che si disponga già i prerequisiti seguenti

* Un tenant di Azure AD.
* [Un tenant Peakon](https://peakon.com/us/pricing/).
* Un account utente in Peakon con autorizzazioni di amministratore.

## <a name="assigning-users-to-peakon"></a>Assegnazione di utenti a Peakon

Azure Active Directory Usa il concetto *assegnazioni* per determinare gli utenti che dovranno ricevere l'accesso alle App selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo di utenti e/o gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono accedere a Peakon. Dopo aver stabilito questo, è possibile assegnare tali utenti e/o gruppi a Peakon seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Suggerimenti importanti per l'assegnazione di utenti a Peakon 

* È consigliabile che un singolo utente di Azure AD viene assegnato a Peakon per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Peakon, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-peakon-for-provisioning"></a>Configurare Peakon per il provisioning

1.  Accedi per i [Console di amministrazione Peakon](https://app.Peakon.com/login). Fare clic su **configurazione**. 

    ![Console di amministrazione Peakon](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Selezionare **integrazioni**.
    
    ![Effettuare il provisioning Peakon dipendente](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Abilitare **dipendente Provisioning**.

    ![Effettuare il provisioning Peakon dipendente](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Copiare i valori per **SCIM 2.0 URL** e **OAuth Bearer Token**. Questi valori verranno immessi nel **URL Tenant** e **Token segreto** campo nella scheda Provisioning dell'applicazione Peakon nel portale di Azure.

    ![Creare Token Peakon](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Aggiungere Peakon dalla raccolta

Per configurare Peakon per provisioning utenti automatico con Azure AD, è necessario aggiungere Peakon dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

1. Nel  **[portale di Azure](https://portal.azure.com)** , nel riquadro di spostamento sinistro, selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare la **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Peakon**, selezionare **Peakon** nel pannello dei risultati e quindi fare clic sui **Add** pulsante per aggiungere l'applicazione.

    ![Peakon nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Configurazione del provisioning utenti automatico in Peakon 

Questa sezione descrive i passaggi per configurare il provisioning di Azure AD del servizio per creare, aggiornare e disabilitare utenti e/o gruppi in Peakon base assegnazioni utente e/o di gruppo in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare basato su SAML single sign-on per Peakon, seguendo le istruzioni disponibili nel [esercitazione Peakon Single sign-on](peakon-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Per configurare il provisioning utenti automatico per Peakon in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**, quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Peakon**.

    ![Il collegamento Peakon nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda provisioning](common/provisioning-automatic.png)

5. Sotto il **credenziali di amministratore** sezione di input il **SCIM 2.0 URL** e **OAuth Bearer Token** valori recuperati in precedenza nel **URL del Tenant** e **Token segreto** rispettivamente. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Peakon. Se la connessione non riesce, verificare che l'account Peakon abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e Token](common/provisioning-testconnection-tenanturltoken.png)

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Notifica tramite posta elettronica](common/provisioning-notification-email.png)

8. Fare clic su **Save**.

9. Sotto il **mapping** sezione, selezionare **Synchronize Azure Active Directory Users per Peakon**.

    ![Mapping utente Peakon](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Esaminare gli attributi utente sincronizzati da Azure AD a Peakon nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** le proprietà utilizzate per soddisfare gli account utente in Peakon per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente Peakon](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Per configurare i filtri di ambito, vedere le istruzioni fornite nel [esercitazione filtro Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Il provisioning di ambito](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio di configurazione del Provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività, che descrivono tutte le azioni eseguite dal servizio su Peakon di provisioning di Azure AD provisioning.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Tutti gli attributi utente personalizzati in Peakon dovranno essere esteso da SCIM utente estensione personalizzata del Peakon di `urn:ietf:params:scim:schemas:extension:peakon:2.0:User`.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)