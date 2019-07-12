---
title: 'Esercitazione: Configurare 4me per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e deprovisioning degli account utente in 4me.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: zchia
ms.openlocfilehash: e2e7c27d8cfa79bc7a8f8462def4d46e598cb508
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595084"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Esercitazione: Configurare 4me per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in 4me e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e deprovisioning di utenti e/o gruppi a 4me.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni di Microsoft Azure generale di utilizzo per le funzionalità di anteprima, vedere [condizioni per l'utilizzo aggiuntive per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant 4me](https://www.4me.com/trial/)
* Un account utente in 4me con autorizzazioni di amministratore.

## <a name="add-4me-from-the-gallery"></a>Aggiungere 4me dalla raccolta

Prima di configurare 4me per il provisioning utenti automatico con Azure AD, è necessario aggiungere 4me dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere 4me dalla raccolta di applicazioni di Azure AD, seguire i passaggi seguenti:**

1. Nel  **[portale di Azure](https://portal.azure.com)** , nel riquadro di spostamento sinistro, selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare la **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **4me**, selezionare **4me** nel pannello dei risultati e quindi fare clic sui **Add** pulsante per aggiungere l'applicazione.

    ![4me nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Assegnazione di utenti a 4me

Azure Active Directory Usa il concetto *assegnazioni* per determinare gli utenti che dovranno ricevere l'accesso alle App selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo di utenti e/o gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono accedere a 4me. Dopo aver stabilito questo, è possibile assegnare tali utenti e/o gruppi a 4me seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Suggerimenti importanti per l'assegnazione di utenti a 4me

* È consigliabile che un singolo utente di Azure AD viene assegnato a 4me per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a 4me, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Configurazione del provisioning utenti automatico in 4me 

Questa sezione descrive i passaggi per configurare il provisioning di Azure AD del servizio per creare, aggiornare e disabilitare utenti e/o gruppi in base alle assegnazioni utente e/o di gruppo in Azure AD 4me.

> [!TIP]
> Si può anche scegliere di abilitare basato su SAML single sign-on per 4me, seguendo le istruzioni disponibili nel [4me single sign-on di esercitazione](4me-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Per configurare il provisioning utenti automatico per 4me in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**, quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **4me**.

    ![Collegamento di 4me nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda provisioning](common/provisioning-automatic.png)

5. Per recuperare il **URL Tenant** e **Token segreto** dell'account di 4me, seguire la procedura dettagliata, come descritto nel passaggio 6.

6. Accedi alla Console di amministrazione 4me. Passare a **impostazioni**.

    ![Impostazioni 4ME](media/4me-provisioning-tutorial/4me01.png)

    Digitare **app** nella barra di ricerca.

    ![App 4ME](media/4me-provisioning-tutorial/4me02.png)

    Aprire il **SCIM** elenco a discesa per recuperare il Token segreto e l'endpoint SCIM.

    ![4ME SCIM](media/4me-provisioning-tutorial/4me03.png)

7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a 4me. Se la connessione non riesce, verificare che l'account 4me abbia autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Notifica tramite posta elettronica](common/provisioning-notification-email.png)

9. Fare clic su **Save**.

10. Sotto il **mapping** sezione, selezionare **Synchronize Azure Active Directory Users per 4me**.

    ![Mapping utente 4ME](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. Esaminare gli attributi utente sincronizzati da Azure AD a 4me nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** le proprietà utilizzate per soddisfare gli account utente in 4me per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Mapping utente 4ME](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. Sotto il **mapping** sezione, selezionare **sincronizzare Azure gruppi di Active Directory per 4me**.

    ![Mapping utente 4ME](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. Esaminare gli attributi gruppo sincronizzati da Azure AD a 4me nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** proprietà usate devono rispettare i gruppi in 4me per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![mapping dei gruppi di 4ME](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il provisioning del servizio per 4me AD Azure, impostare il **stato del Provisioning** al **sul** nel **impostazioni** sezione.

    ![Lo stato di provisioning sia attivato](common/provisioning-toggle-on.png)

16. Definire gli utenti e/o gruppi di cui si vuole eseguire il provisioning in 4me selezionando i valori desiderati in **ambito** nel **impostazioni** sezione.

    ![Il provisioning di ambito](common/provisioning-scope.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio di configurazione del Provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività, che descrivono tutte le azioni eseguite dal servizio su 4me di provisioning di Azure AD provisioning.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* 4ME ha diversi URL dell'endpoint SCIM per gli ambienti di test e produzione. Il primo termina con **.qa** mentre la seconda termina con **. com**
* 4ME generato segreto token hanno una data di scadenza di un mese dalla generazione.
* non supporta 4ME **eliminare** operations

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)