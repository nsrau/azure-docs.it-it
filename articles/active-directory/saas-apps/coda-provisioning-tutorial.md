---
title: 'Esercitazione: configurare coda per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a coda.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: Zhchia
ms.openlocfilehash: d5748b9d374a33e0aa3c52d9623f4e73d3bace1c
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89302922"
---
# <a name="tutorial-configure-coda-for-automatic-user-provisioning"></a>Esercitazione: configurare la coda per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire sia in coda sia in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning degli utenti a [coda](https://coda.io/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in coda
> * Rimuovere gli utenti in coda quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e coda
> * [Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial) a coda (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale.
* Un account di amministratore dell' [organizzazione coda](https://help.coda.io/en/articles/3520174-getting-started-with-sso) .

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e coda](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-coda-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare coda per supportare il provisioning con Azure AD

1. Aprire la console di amministrazione dell'organizzazione selezionando Impostazioni organizzazione in... menu sotto l'area di lavoro.

    ![Impostazioni SCIM dell'organizzazione della coda Enterprise](media/coda-provisioning-tutorial/coda-scim-enable.png)

2. Verificare che il provisioning con SCIM sia abilitato.
3. Prendere nota dell'URL di base SCIM e del token di porta SCIM. Se non è presente alcun token di porta, fare clic su Genera nuovo token.

## <a name="step-3-add-coda-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere coda dalla raccolta di applicazioni di Azure AD

Aggiungere coda dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in coda. Se in precedenza è stato configurato coda per SSO, è possibile utilizzare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning

Il servizio Azure AD provisioning consente di definire l'ambito di chi verrà eseguito il provisioning in base all'assegnazione all'applicazione e o in base agli attributi dell'utente. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile usare i [passaggi](../manage-apps/assign-user-or-group-access-portal.md) seguenti per assegnare gli utenti all'applicazione. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning basato esclusivamente sugli attributi dell'utente, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

* Quando si assegnano utenti a coda, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli.

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti assegnati, è possibile controllarlo assegnando uno o due utenti all'app. Quando l'ambito è impostato su tutti gli utenti, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).


## <a name="step-5-configure-automatic-user-provisioning-to-coda"></a>Passaggio 5. Configurare il provisioning utenti automatico in coda

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare gli utenti in TestApp in base alle assegnazioni utente in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-coda-in-azure-ad"></a>Per configurare il provisioning utenti automatico per coda in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Coda**.

    ![Collegamento coda nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del tenant di coda e il token del segreto recuperati in precedenza nel passaggio 2. Fare clic su **Test connessione** per verificare che Azure ad possibile connettersi a coda. Se la connessione non riesce, verificare che l'account di coda abbia le autorizzazioni di amministratore e riprovare.

    ![provisioning](./media/coda-provisioning-tutorial/provisioning.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a coda**.

9. Esaminare gli attributi utente sincronizzati da Azure AD a coda nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in coda per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), sarà necessario assicurarsi che l'API coda supporti l'applicazione di filtri agli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |active|Boolean|
   |name.givenName|string|
   |name.familyName|string|


10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per coda, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti di cui si vuole eseguire il provisioning in coda scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti definiti nell' **ambito** nella sezione **Impostazioni** . Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
