---
title: 'Esercitazione: Configurare Coda per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Coda.
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
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: Zhchia
ms.openlocfilehash: ea2f25d6b03f002448079b16bd261729807d8a03
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96179701"
---
# <a name="tutorial-configure-coda-for-automatic-user-provisioning"></a>Esercitazione: Configurare Coda per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire sia in Coda che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Una volta configurato, Azure AD effettua automaticamente il provisioning e il deprovisioning degli utenti per [Coda](https://coda.io/) usando il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creazione di utenti in Coda
> * Rimozione di utenti in Coda quando l'accesso non è più necessario
> * Mantenimento della sincronizzazione degli attributi utente tra Azure AD e Coda
> * [Accesso Single Sign-On](./coda-tutorial.md) a Coda (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md)
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale.
* Un account amministratore di [Coda Enterprise](https://help.coda.io/en/articles/3520174-getting-started-with-sso).

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare i dati di cui [eseguire il mapping tra Azure AD e Coda](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-coda-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Coda per supportare il provisioning con Azure AD

1. Aprire la console di amministrazione dell'organizzazione selezionando Organization Settings (Impostazioni organizzazione) nel menu con i tre puntini (...) sotto l'area di lavoro.

    ![Impostazioni SCIM dell'organizzazione Coda Enterprise](media/coda-provisioning-tutorial/coda-scim-enable.png)

2. Verificare che Provision with SCIM (Effettua il provisioning con SCIM) sia abilitato.
3. Prendere nota dei valori dell'URL di base SCIM e del token di connessione SCIM. Se non è presente alcun token di connessione, fare clic su Generate New Token (Genera nuovo token).

## <a name="step-3-add-coda-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Coda dalla raccolta di applicazioni di Azure AD

Aggiungere Coda dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in Coda. Se Coda è stato configurato in precedenza per l'accesso Single Sign-On, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà effettuato il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente. Se si sceglie di definire l'ambito degli utenti di cui verrà effettuato il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà effettuato il provisioning esclusivamente in base agli attributi dell'utente, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Quando si assegnano utenti a Coda, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli.

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti prima di procedere alla distribuzione generale. Quando l'ambito per il provisioning è impostato su utenti assegnati, è possibile mantenere il controllo assegnando uno o due utenti all'app. Quando l'ambito è impostato su tutti gli utenti, è possibile specificare un [filtro di definizione dell'ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-coda"></a>Passaggio 5. Configurare il provisioning utenti automatico per Coda

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti in Coda in base alle assegnazioni di utenti in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-coda-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Coda in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Coda**.

    ![Collegamento di Coda nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatica evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere l'URL tenant e il token segreto di Coda recuperati in precedenza al passaggio 2. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Coda. Se la connessione non riesce, verificare che l'account Coda abbia autorizzazioni di amministratore e riprovare.

    ![Screenshot che mostra la finestra di dialogo Credenziali amministratore, in cui è possibile immettere l'URL del tenant e il token segreto.](./media/coda-provisioning-tutorial/provisioning.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Coda** (Sincronizza utenti di Azure Active Directory in Coda).

9. Esaminare gli attributi utente sincronizzati da Azure AD a Coda nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in Coda per le operazioni di aggiornamento. Se si sceglie di modificare l'[attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API Coda supporti il filtro degli utenti basato su tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |active|Boolean|
   |name.givenName|string|
   |name.familyName|string|


10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per Coda, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti di cui si vuole effettuare il provisioning in Coda selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)