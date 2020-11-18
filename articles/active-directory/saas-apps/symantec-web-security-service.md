---
title: 'Esercitazione: Configurare Symantec Web Security Service (WSS) per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Symantec Web Security Service (WSS).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: d7e0db1b0bc1e7aef68ee06f3bdd5e5e0f83b73e
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94354700"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Esercitazione: Configurare Symantec Web Security Service (WSS) per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Symantec Web Security Service (WSS) e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Symantec Web Security Service (WSS).

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Symantec Web Security Service (WSS)](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Un account utente in Symantec Web Security Service (WSS) con autorizzazioni di amministratore.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Assegnazione di utenti a Symantec Web Security Service (WSS)

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Symantec Web Security Service (WSS). Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Symantec Web Security Service (WSS) seguendo le istruzioni fornite qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Suggerimenti importanti per l'assegnazione di utenti a Symantec Web Security Service (WSS)

* È consigliabile assegnare un singolo utente di Azure AD a Symantec Web Security Service (WSS) per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Symantec Web Security Service (WSS), è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Configurare Symantec Web Security Service (WSS) per il provisioning

Prima di configurare Symantec Web Security Service (WSS) per il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM in Symantec Web Security Service (WSS).

1. Accedere alla [console di amministrazione di Symantec Web Security Service](https://portal.threatpulse.com/login.jsp). Passare a **Solutions** > **Service** (Soluzioni > Servizio).

    ![Symantec Web Security Service(WSS)](media/symantec-web-security-service/service.png)

2. Passare a **Account Maintenance** > **Integrations** > **New Integration** (Manutenzione account > Integrazioni > Nuova integrazione).

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Selezionare **Third-Party Users & Groups Sync** (Sincronizzazione di utenti e gruppi di terze parti). 

    ![Screenshot dell'opzione Third-Party Users & Groups Sync.](media/symantec-web-security-service/third-party-users.png)

4.  Copiare i valori di **SCIM URL** (URL SCIM) e **Token**. Questi valori verranno immessi nei campi **URL tenant** e **Token segreto** nella scheda Provisioning dell'applicazione Symantec Web Security Service (WSS) nel portale di Azure.

    ![Screenshot della finestra di dialogo New Integration con le caselle SCIM URL e Token evidenziate.](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Aggiungere Symantec Web Security Service (WSS) dalla raccolta

Per configurare Symantec Web Security Service (WSS) per il provisioning utenti automatico con Azure AD, è necessario aggiungere Symantec Web Security Service (WSS) dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS gestite.

**Per aggiungere Symantec Web Security Service (WSS) dalla raccolta di applicazioni di Azure AD, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Symantec Web Security Service (WSS)** , selezionare **Symantec Web Security Service (WSS)** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Symantec Web Security Service (WSS) nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Configurare il provisioning utenti automatico in Symantec Web Security Service (WSS)

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Symantec Web Security Service (WSS) in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Symantec Web Security Service (WSS), seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Symantec Web Security Service (WSS)](symantec-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Symantec Web Security Service (WSS) in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Symantec Web Security Service**.

    ![Collegamento di Symantec Web Security Service (WSS) nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni di gestione con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione Credenziali amministratore immettere i valori di **SCIM URL** e **Token** recuperati in precedenza rispettivamente nei campi **URL tenant** e **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app Symantec Web Security Service. Se la connessione non riesce, verificare che l'account Symantec Web Security Service (WSS) abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Symantec Web Security Service (WSS)** (Sincronizza utenti di Azure Active Directory con Symantec Web Security Service (WSS)).

    ![Screenshot della sezione Mapping con l'opzione Synchronize Azure Active Directory Users to Symantec Web Security Service (WSS) evidenziata.](media/symantec-web-security-service/usermapping.png)

9. Esaminare gli attributi utente sincronizzati tra Azure AD e Symantec Web Security Service (WSS) nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Symantec Web Security Service (WSS) per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Screenshot della sezione Mapping di attributi che mostra 16 proprietà corrispondenti.](media/symantec-web-security-service/userattribute.png)

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Symantec Web Security Service** (Sincronizza gruppi di Azure Active Directory con Symantec Web Security Service).

    ![Screenshot della sezione Mapping con l'opzione Synchronize Azure Active Directory Groups to Symantec Web Security Service (WSS) evidenziata.](media/symantec-web-security-service/groupmapping.png)

11. Esaminare gli attributi gruppo sincronizzati tra Azure AD e Symantec Web Security Service (WSS) nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Symantec Web Security Service (WSS) per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Screenshot della sezione Mapping di attributi che mostra tre proprietà corrispondenti.](media/symantec-web-security-service/groupattribute.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Symantec Web Security Service, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in Symantec Web Security Service (WSS) scegliendo i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo rispetto a quelle successive. Per altre informazioni sul tempo necessario per effettuare il provisioning di utenti e/o gruppi, vedere [Quanto tempo sarà necessario per eseguire il provisioning degli utenti?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione **Stato corrente** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Symantec Web Security Service (WSS). Per altre informazioni, vedere [Controllare lo stato del provisioning utenti](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Per leggere i log di provisioning di Azure AD, vedere [Creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
