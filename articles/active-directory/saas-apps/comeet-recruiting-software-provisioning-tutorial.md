---
title: 'Esercitazione: Configurare Comeet Recruiting Software per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare il provisioning e deprovisioning degli account utente in Software Recruiting Comeet automaticamente.
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
ms.date: 05/07/2019
ms.author: zchia
ms.openlocfilehash: e8414b9737e0ee7f847827a432dd9887931a2532
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65470429"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Esercitazione: Configurare Comeet Recruiting Software per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Comeet Recruiting Software e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare il provisioning e deprovisioning di utenti e/o gruppi al Software Recruiting Comeet automaticamente.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni di Microsoft Azure generale di utilizzo per le funzionalità di anteprima, vedere [condizioni per l'utilizzo aggiuntive per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant Comeet Recruiting Software](https://www.comeet.co/)
* Un account utente in Software Recruiting Comeet con autorizzazioni di amministratore.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Aggiungere Comeet Recruiting Software dalla raccolta

Prima di configurare Comeet Recruiting Software per il provisioning utenti automatico con Azure AD, è necessario aggiungere Comeet Recruiting Software dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Comeet Recruiting Software dalla raccolta di applicazioni di Azure AD, seguire i passaggi seguenti:**

1. Nel  **[portale di Azure](https://portal.azure.com)** , nel riquadro di spostamento sinistro, selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare la **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Software Recruiting Comeet**, selezionare **Comeet Recruiting Software** nel pannello dei risultati e quindi fare clic sui **Aggiungi** pulsante per aggiungere l'applicazione.

    ![Comeet Recruiting Software nell'elenco dei risultati](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Assegnazione di utenti a Software Recruiting Comeet

Azure Active Directory Usa il concetto *assegnazioni* per determinare gli utenti che dovranno ricevere l'accesso alle App selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo di utenti e/o gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono accedere a Comeet Recruiting Software. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi al Software Recruiting Comeet seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Suggerimenti importanti per l'assegnazione di utenti a Software Recruiting Comeet

* È consigliabile che un singolo utente di Azure AD viene assegnato a Comeet Recruiting Software per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Comeet Recruiting Software, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Configurazione del provisioning utenti automatico in Comeet Recruiting Software 

Questa sezione illustra i passaggi per configurare il provisioning di Azure AD del servizio per creare, aggiornare e disabilitare utenti e/o gruppi in Comeet Recruiting Software basato su utente e/o le assegnazioni dei gruppi in Azure AD.

> [!TIP]
> Si può anche scegliere di abilitare basato su SAML single sign-on per Comeet Recruiting Software, seguendo le istruzioni disponibili nel [esercitazione Comeet Recruiting Software single sign-on](comeetrecruitingsoftware-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Per configurare il provisioning utenti automatico per il Software Recruiting Comeet in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**, quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Comeet Recruiting Software**.

    ![Nell'elenco delle applicazioni selezionare il collegamento Comeet Recruiting Software](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda provisioning](common/provisioning-automatic.png)

5. Sotto il **credenziali di amministratore** sezione di input il **URL Tenant** e **Token segreto** dell'account del Software Recruiting Comeet come descritto nel passaggio 6.

6. Nel [console di amministrazione di Software Recruiting Comeet](https://app.comeet.co/), passare alla **Comeet > Impostazioni > autenticazione > Microsoft Azure**e copiare il **Token segreto dell'azienda**valore per il **Token segreto** campo in Azure AD.

    ![Comeet Recruiting il Provisioning del Software](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Comeet Recruiting Software. Se la connessione non riesce, verificare che l'account Comeet Recruiting Software abbia autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-token.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Notifica tramite posta elettronica](common/provisioning-notification-email.png)

9. Fare clic su **Save**.

10. Sotto il **mapping** sezione, selezionare **Synchronize Azure Active Directory Users per Comeet**.

    ![Comeet Recruiting i mapping degli utenti di Software](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD con Comeet Recruiting Software nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** le proprietà utilizzate per soddisfare gli account utente in Software Recruiting Comeet per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Comeet selezione attributi di gruppo di Software](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio per il Software Recruiting Comeet il provisioning di Azure AD, impostare il **stato del Provisioning** al **sul** nel **impostazioni** sezione.

    ![Lo stato di provisioning sia attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o gruppi di cui si vuole eseguire il provisioning in Comeet Recruiting Software selezionando i valori desiderati in **ambito** nel **impostazioni** sezione.

    ![Il provisioning di ambito](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio di configurazione del Provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività, che descrivono tutte le azioni eseguite dal servizio su Comeet Recruiting Software di provisioning di Azure AD provisioning.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Comeet Recruiting Software non supporta attualmente i gruppi.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

