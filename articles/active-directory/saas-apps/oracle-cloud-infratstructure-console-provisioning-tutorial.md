---
title: 'Esercitazione: Configurare Oracle Cloud Infrastructure Console per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Oracle Cloud Infrastructure Console.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e22c8746-7638-4a0f-ae08-7db0c477cf52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 5aa33529a1957b6e7728b3a87bacf6bb91d987ae
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378951"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Esercitazione: Configurare Oracle Cloud Infrastructure Console per il provisioning automatico degli utentiTutorial: Configure Oracle Cloud Infrastructure Console for automatic user provisioning

Questa esercitazione descrive i passaggi da eseguire sia in Oracle Cloud Infrastructure Console che in Azure Active Directory (Azure AD) per configurare il provisioning automatico degli utenti. Quando si configura la configurazione, Azure AD esegue automaticamente il provisioning e il provisioning di utenti e gruppi nella [console dell'infrastruttura cloud Oracle](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) usando il servizio provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Oracle Cloud Infrastructure Console
> * Rimuovere gli utenti in Oracle Cloud Infrastructure Console quando non richiedono più l'accesso
> * Mantenere sincronizzati gli attributi utente tra Azure AD e Oracle Cloud Infrastructure Console
> * Effettuare il provisioning di gruppi e appartenenze ai gruppi nella console dell'infrastruttura Oracle Cloud
> * [Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) per Oracle Cloud Infrastructure Console (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning (ad esempio Amministratore applicazioni, Amministratore applicazione cloud, Proprietario applicazione o Amministratore globale). 
* Un [tenant](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)di Oracle Cloud Infrastructure Control .
* Un account utente in Oracle Cloud Infrastructure Control con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul funzionamento del servizio di [provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinare chi riverrà [nell'ambito del provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determinare i dati di [cui eseguire il mapping tra Azure AD e Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare Oracle Cloud Infrastructure Console per supportare il provisioning con Azure ADConfigure Oracle Cloud Infrastructure Console to support provisioning with Azure AD

1. Accedi al portale di amministrazione di Oracle Cloud Infrastructure Console. Nell'angolo superiore sinistro dello schermo passare a **Federazione > identità**.

    ![Amministratore Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Fare clic sull'URL visualizzato nella pagina accanto a Oracle Identity Cloud Service Console.

    ![Oracle URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Fare clic su Aggiungi provider di **identità** per creare un nuovo provider di identità. Salvare l'ID IdP da utilizzare come parte dell'URL del tenant. Fare clic sull'icona più accanto alla scheda **Applicazioni** per creare un client OAuth e concedere l'app AppRole di amministratore di dominio identità IDCS.

    ![Icone Oracle Cloud](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Seguire le schermate seguenti per configurare l'applicazione. Una volta che la configurazione è fatto clic su **Salva**.

    ![Configurazione Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Criteri di emissione di token Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Nella scheda Configurazioni dell'applicazione espandere l'opzione **Informazioni generali** per recuperare l'ID client e il segreto client.

    ![Generazione di token Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Per generare un token segreto Base64 codificalare l'ID client e il segreto client nel formato **ID client:Segreto client**. Salvare il token segreto. Questo valore verrà immesso nel campo **Token segreto** nella scheda di provisioning dell'applicazione Oracle Cloud Infrastructure Console nel portale di Azure.This value will be entered in the Secret Token field in the provisioning tab of your Oracle Cloud Infrastructure Console application in the Azure portal.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Oracle Cloud Infrastructure Console dalla raccolta di applicazioni di Azure ADAdd Oracle Cloud Infrastructure Console from the Azure AD application gallery

Aggiungere Oracle Cloud Infrastructure Console dalla raccolta di applicazioni di Azure AD per avviare la gestione del provisioning in Oracle Cloud Infrastructure Console.Add Oracle Cloud Infrastructure Console from the Azure AD application gallery to start managing provisioning to Oracle Cloud Infrastructure Console. Se in precedenza è stata configurata Oracle Cloud Infrastructure Console per SSO, è possibile utilizzare la stessa applicazione. Tuttavia, è consigliabile creare un'app separata durante il test iniziale dell'integrazione. Ulteriori informazioni sull'aggiunta di un'applicazione dalla raccolta [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi riverrà nell'ambito del provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito degli utenti in base all'assegnazione all'applicazione e o in base agli attributi dell'utente/gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning nell'app in base all'assegnazione, è possibile usare la [procedura](../manage-apps/assign-user-or-group-access-portal.md) seguente per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning in base esclusivamente agli attributi dell'utente o del gruppo, è possibile utilizzare un filtro di ambito come descritto [di seguito.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* Quando si assegnano utenti e gruppi a Oracle Cloud Infrastructure Console, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non effettivamente autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Inizia in piccolo. Esegui test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Passaggio 5. Configurare il provisioning automatico degli utenti in Oracle Cloud Infrastructure Console 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Oracle Cloud Infrastructure Console in Azure AD:To configure automatic user provisioning for Oracle Cloud Infrastructure Console in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Oracle Cloud Infrastructure Console**.

    ![Il collegamento Oracle Cloud Infrastructure Console nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere l'URL `https://<IdP ID>.identity.oraclecloud.com/admin/v1` **tenant** nel formato . Ad esempio `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Immettere il valore del token segreto recuperato in precedenza in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a Oracle Cloud Infrastructure Console.Click **Test Connection** to ensure Azure AD can connect to Oracle Cloud Infrastructure Console. Se la connessione non riesce, verificare che l'account Oracle Cloud Infrastructure Console disponga delle autorizzazioni di amministratore e riprovare.

    ![provisioning](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. Nel campo **E-mail** di notifica immettere l'indirizzo di posta elettronica di una persona o di un gruppo che deve ricevere le notifiche di errore di provisioning e selezionare la casella di controllo Invia una **notifica tramite posta elettronica quando si verifica un errore.**

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con Oracle Cloud Infrastructure Console.**

9. Esaminare gli attributi utente sincronizzati da Azure AD a Oracle Cloud Infrastructure Console nella sezione **Mapping degli attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Oracle Cloud Infrastructure Console per le operazioni di aggiornamento. Se si sceglie di modificare l'attributo di [destinazione corrispondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)è necessario assicurarsi che l'API di Oracle Cloud Infrastructure Console supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |userName|string|
      |active|Boolean|
      |title|string|
      |emails[type eq "work"].value|string|
      |preferredLanguage|string|
      |name.givenName|string|
      |name.familyName|string|
      |addresses[tipo eq "lavoro"].formato|string|
      |addresses[tipo eq "lavoro"].località|string|
      |addresses[digitare eq "lavoro"].regione|string|
      |addresses[type eq "work"].postalCode|string|
      |addresses[tipo eq "lavoro"].country|string|
      |addresses[type eq "work"].streetAddress|string|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|string|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|string|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|string|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informazioni di riferimento|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|string|
      |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:bypassNotification|Boolean|
      |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:isFederatedUser|Boolean|

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con Oracle Cloud Infrastructure Console.**

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Oracle Cloud Infrastructure Console nella sezione **Mapping degli attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in Oracle Cloud Infrastructure Console per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |externalId|string|
      |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Oracle Cloud Infrastructure Console, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Oracle Cloud Infrastructure Console, change **the Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Oracle Cloud Infrastructure Console scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni.** L'esecuzione del ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti, purché sia in esecuzione il servizio di provisioning di Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:Once you've configured provisioning, use the following resources to monitor your deployment:

* Utilizzare i registri di [provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare quali utenti sono stati sottoposti a provisioning con successo o meno
* Controllare la barra di [avanzamento](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e la sua vicinanza al completamento
* Se la configurazione di provisioning sembra essere in uno stato non integro, l'applicazione andrà in quarantena. Ulteriori informazioni sugli stati di quarantena [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
