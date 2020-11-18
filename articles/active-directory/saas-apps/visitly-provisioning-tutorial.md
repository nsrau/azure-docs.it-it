---
title: 'Esercitazione: Configurare Visitly per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Visitly.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: ff3f3ab65df2d801b7c962de7cce645e9fc00b30
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358610"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Esercitazione: Configurare Visitly per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Visitly e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Visitly.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni importanti su questo servizio e su come funziona, insieme alle domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning degli utenti in applicazioni SaaS (software-as-a-service) con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo generali di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Visitly](https://www.visitly.io/pricing/)
* Un account utente in Visitly con autorizzazioni di amministratore

## <a name="assign-users-to-visitly"></a>Assegnare utenti a Visitly 

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti o gruppi in Azure AD devono poter accedere a Visitly. Quindi assegnare questi utenti o gruppi a Visitly seguendo le istruzioni riportate di seguito:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Suggerimenti importanti per l'assegnazione di utenti a Visitly 

* È consigliabile assegnare un singolo utente di Azure AD a Visitly per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Visitly, selezionare qualsiasi ruolo valido specifico dell'applicazione, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning.

## <a name="set-up-visitly-for-provisioning"></a>Configurare Visitly per il provisioning

Prima di configurare il dispositivo per il provisioning per il provisioning utenti automatico tramite Azure AD, è necessario abilitare il provisioning SCIM (System for Cross-domain Identity Management) in Visitly.

1. Accedere a [Visitly](https://app.visitly.io/login). Selezionare **Integrations** > **Host Synchronization** (Integrazioni > Sincronizzazione host).

    ![Host Synchronization](media/Visitly-provisioning-tutorial/login.png)

2. Selezionare la sezione **Azure AD**.

    ![Sezione Azure AD](media/Visitly-provisioning-tutorial/integration.png)

3. Copiare il valore di **API Key** (Chiave API). Questo valore verrà immesso nella casella **Token segreto** nella scheda **Provisioning** dell'applicazione Visitly nel portale di Azure.

    ![Chiave API](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Aggiungere Visitly dalla raccolta

Per configurare Visitly per il provisioning utenti automatico con Azure AD, è necessario aggiungere Visitly dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS gestite.

Per aggiungere Visitly dalla raccolta di Azure AD, seguire questa procedura.

1. Nel riquadro di spostamento sinistro del [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Visitly**, selezionare **Visitly** nel riquadro dei risultati e quindi selezionare **Aggiungi** per aggiungere l'applicazione.

    ![Visitly nell'elenco risultati](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Configurare il provisioning utenti automatico per Visitly 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Visitly in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> Per abilitare l'accesso Single Sign-On basato su SAML per Visitly, seguire le istruzioni contenute nell'[esercitazione sull'accesso Single Sign-On per Visitly](Visitly-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Configurare il provisioning utenti automatico per Visitly in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Tutte le applicazioni](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Visitly**.

    ![Collegamento di Visitly nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning impostata su Automatico](common/provisioning-automatic.png)

5. Nella sezione Credenziali amministratore immettere i valori di `https://api.visitly.io/v1/usersync/SCIM` e **API Key** recuperati in precedenza rispettivamente nei campi **URL tenant** e **Token segreto**. Selezionare **Test connessione** per verificare che Azure AD possa connettersi a Visitly. Se la connessione non riesce, verificare che l'account Visitly abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nella casella **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo di posta elettronica di una persona o di un gruppo che riceverà le notifiche degli errori di provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![E-mail di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Visitly** (Sincronizza utenti di Azure Active Directory con Visitly).

    ![Mapping utente in Visitly](media/visitly-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati tra Azure AD e Visitly nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Visitly per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente in Visitly](media/visitly-provisioning-tutorial/userattribute.png)

10. Per configurare i filtri di ambito, seguire le istruzioni riportate nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per Visitly, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti o i gruppi di cui effettuare il provisioning in Visitly selezionando i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti o i gruppi definiti nell'**Ambito** della sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo rispetto a quelle successive. Per altre informazioni sul tempo necessario per effettuare il provisioning di utenti o gruppi, vedere [Quanto tempo sarà necessario per eseguire il provisioning degli utenti?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione **Stato corrente** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Visitly. Per altre informazioni, vedere [Controllare lo stato del provisioning utenti](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Per leggere i log di provisioning di Azure AD, vedere [Creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

Visitly non supporta le eliminazioni definitive. È supportata solo l'eliminazione temporanea.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
