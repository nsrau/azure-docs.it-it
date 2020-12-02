---
title: 'Esercitazione: Configurare 15Five per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in 15Five.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 00f713e8d319d7ad8dcea014429c57d7fba40541
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181221"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Esercitazione: Configurare 15Five per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in 15Five e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in [15Five](https://www.15five.com/pricing/). Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory.

> [!NOTE]
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creazione di utenti in 15Five
> * Rimozione di utenti in 15Five quando l'accesso non è più necessario
> * Mantenimento della sincronizzazione degli attributi utente tra Azure AD e 15Five
> * Provisioning di gruppi e appartenenza a gruppi in 15Five
> * [Accesso Single Sign-On](./15five-tutorial.md) a 15Five (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md).
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale.
* [Un tenant di 15Five](https://www.15five.com/pricing/).
* Un account utente in 15Five con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare i dati di cui [eseguire il mapping tra Azure AD e 15Five](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-15five-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare 15Five per supportare il provisioning con Azure AD

Prima di configurare 15Five per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning SCIM in 15Five.

1. Accedere alla [console di amministrazione di 15Five](https://my.15five.com/). Passare a **Features > Integrations** (Funzionalità > Integrazioni).

    :::image type="content" source="media/15five-provisioning-tutorial/integration.png" alt-text="Screenshot della console di amministrazione di 15Five. La voce Integrations è visualizzata sotto Features in un menu ed entrambe le voci sono evidenziate." border="false":::

2.  Fare clic su **SCIM 2.0**.

    :::image type="content" source="media/15five-provisioning-tutorial/image00.png" alt-text="Screenshot della pagina Integrations nella console di amministrazione di 15Five. In Tool è evidenziata la voce SCIM 2.0." border="false":::

3.  Passare a **SCIM integration > Generate OAuth token** (Integrazione SCIM > Genera token OAuth).

    :::image type="content" source="media/15five-provisioning-tutorial/image02.png" alt-text="Screenshot della pagina SCIM Integration nella console di amministrazione di 15Five. La voce Generate OAuth token è evidenziata." border="false":::

4.  Copiare i valori di **SCIM 2.0 base URL** (URL di base SCIM 2.0) e **Access Token** (Token di accesso). Questi valori dovranno essere immessi nei campi **URL tenant** e **Token segreto** nella scheda Provisioning dell'applicazione 15Five nel portale di Azure.
    
    :::image type="content" source="media/15five-provisioning-tutorial/image03.png" alt-text="Screenshot della pagina SCIM Integration. Nella tabella Token i valori accanto a SCIM 2.0 base URL e Access token sono evidenziati." border="false":::

## <a name="step-3-add-15five-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere 15Five dalla raccolta di applicazioni di Azure AD

Aggiungere 15Five dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in 15Five. Se 15Five è stato configurato in precedenza per l'accesso Single Sign-On, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a 15Five, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-15five"></a>Passaggio 5. Configurare il provisioning utenti automatico per 15Five 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in 15Five in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Per configurare il provisioning utenti automatico per 15Five in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **15Five**.

    ![Collegamento di 15Five nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatica evidenziata.](common/provisioning-automatic.png)

5.  Nella sezione Credenziali amministratore immettere i valori dell'**URL di base SCIM 2.0 e del token di accesso** recuperati in precedenza rispettivamente nei campi **URL tenant** e **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a 15Five. Se la connessione non riesce, verificare che l'account 15Five abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to 15Five** (Sincronizza utenti di Azure Active Directory in 15Five).

9. Esaminare gli attributi utente sincronizzati da Azure AD a 15Five nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in 15Five per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.


   |Attributo|Type|
   |---|---|
   |active|Boolean|
   |title|string|
   |emails[type eq "work"].value|string|
   |userName|string|
   |name.givenName|string|
   |name.familyName|string|
   |externalId|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informazioni di riferimento|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|string|
   |urn:ietf:params:scim:schemas:extension:15Five:2.0:User:location|string|
   |urn:ietf:params:scim:schemas:extension:15Five:2.0:User:startDate|string|

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to 15Five** (Sincronizza gruppi di Azure Active Directory in 15Five).

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a 15Five nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con i gruppi in 15Five per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |externalId|string|
      |displayName|string|
      |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per 15Five, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in 15Five selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

    L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).  
    
## <a name="connector-limitations"></a>Limitazioni dei connettori

* 15Five non supporta le eliminazioni temporanee di utenti.

## <a name="change-log"></a>Registro delle modifiche

* 16/06/2020: è stato aggiunto il supporto per l'attributo dell'estensione enterprise "Manager" e degli attributi personalizzati "Location" e "Start Date" per gli utenti.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md).