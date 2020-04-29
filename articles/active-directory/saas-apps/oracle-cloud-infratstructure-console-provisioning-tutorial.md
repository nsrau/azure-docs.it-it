---
title: 'Esercitazione: configurare Oracle Cloud Infrastructure console per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD alla console di infrastruttura cloud Oracle.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378951"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Esercitazione: configurare Oracle Cloud Infrastructure console per il provisioning utenti automatico

In questa esercitazione vengono descritti i passaggi da eseguire nella console di infrastruttura cloud Oracle e in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi nella [console di infrastruttura cloud Oracle](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Oracle Cloud Infrastructure console
> * Rimuovere gli utenti nella console di Oracle Cloud Infrastructure quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e la console dell'infrastruttura cloud Oracle
> * Eseguire il provisioning di gruppi e appartenenze a gruppi in Oracle Cloud Infrastructure console
> * [Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) per Oracle Cloud Infrastructure Console (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l' [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per la configurazione del provisioning, ad esempio amministratore dell'applicazione, amministratore di applicazioni cloud, proprietario dell'applicazione o amministratore globale. 
* [Tenant](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)di controllo dell'infrastruttura cloud Oracle.
* Un account utente nel controllo dell'infrastruttura cloud Oracle con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul [funzionamento del servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare chi sarà nell' [ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e la console dell'infrastruttura cloud di Oracle](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Oracle Cloud Infrastructure console per supportare il provisioning con Azure AD

1. Accedere al portale di amministrazione di Oracle Cloud Infrastructure console. Nell'angolo superiore sinistro della schermata passare a **identità > Federazione**.

    ![Amministratore Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Fare clic sull'URL visualizzato nella pagina accanto alla console del servizio Oracle Identity cloud.

    ![URL Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Fare clic su **Aggiungi provider di identità** per creare un nuovo provider di identità. Salvare l'ID IdP da usare come parte dell'URL del tenant. Fare clic sull'icona del segno più accanto alla scheda **applicazioni** per creare un client OAuth e concedere a IDC Identity Domain Administrator AppRole.

    ![Icona del cloud Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Per configurare l'applicazione, seguire gli screenshot seguenti. Al termine della configurazione, fare clic su **Salva**.

    ![Configurazione Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Criteri di rilascio dei token Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Nella scheda configurazioni dell'applicazione espandere l'opzione **informazioni generali** per recuperare l'ID client e il segreto client.

    ![Generazione di token Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Per generare un token segreto Base64 codificare l'ID client e il segreto client nel formato **ID client: chiave privata client**. Salvare il token Secret. Questo valore verrà immesso nel campo **token segreto** nella scheda provisioning dell'applicazione console dell'infrastruttura cloud Oracle nel portale di Azure.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Oracle Cloud Infrastructure console dalla raccolta di applicazioni Azure AD

Aggiungere Oracle Cloud Infrastructure console dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in Oracle Cloud Infrastructure console. Se in precedenza è stata eseguita la configurazione di Oracle Cloud Infrastructure console per SSO, è possibile utilizzare la stessa applicazione. È tuttavia consigliabile creare un'applicazione separata quando si testa inizialmente l'integrazione. Altre informazioni sull'aggiunta di un'applicazione dalla raccolta sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi sarà nell'ambito per il provisioning 

Il servizio Azure AD provisioning consente di definire l'ambito di chi verrà eseguito il provisioning in base all'assegnazione all'applicazione e o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile usare i [passaggi](../manage-apps/assign-user-or-group-access-portal.md) seguenti per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning basato esclusivamente sugli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a Oracle Cloud Infrastructure console, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo di accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Avviare Small. Eseguire il test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Passaggio 5. Configurare il provisioning utenti automatico nella console di infrastruttura cloud Oracle 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Oracle Cloud Infrastructure console in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Oracle Cloud Infrastructure Console**.

    ![Collegamento di Oracle Cloud Infrastructure console nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **provisioning** .

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del **tenant** nel formato `https://<IdP ID>.identity.oraclecloud.com/admin/v1` . Ad esempio `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Immettere il valore del token Secret recuperato in precedenza nel **token Secret**. Fare clic su **Test connessione** per verificare che Azure ad possibile connettersi alla console di infrastruttura cloud Oracle. Se la connessione non riesce, verificare che l'account di Oracle Cloud Infrastructure Console disponga delle autorizzazioni di amministratore e riprovare.

    ![provisioning](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. Nel campo **messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che deve ricevere le notifiche degli errori di provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti alla console di infrastruttura cloud Oracle**.

9. Esaminare gli attributi utente sincronizzati da Azure AD alla console di infrastruttura cloud Oracle nella sezione **mapping attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Oracle Cloud Infrastructure console per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), è necessario assicurarsi che l'API della console dell'infrastruttura cloud Oracle supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|Stringa|
      |userName|Stringa|
      |active|Boolean|
      |title|Stringa|
      |emails[type eq "work"].value|Stringa|
      |preferredLanguage|Stringa|
      |name.givenName|Stringa|
      |name.familyName|Stringa|
      |indirizzi [tipo EQ "lavoro"]. formattato|Stringa|
      |indirizzi [digitare EQ "Work"]. località|Stringa|
      |indirizzi [digitare EQ "Work"]. Region|Stringa|
      |addresses[type eq "work"].postalCode|Stringa|
      |indirizzi [digitare EQ "Work"]. Country|Stringa|
      |addresses[type eq "work"].streetAddress|Stringa|
      |urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: User: employeeNumber|Stringa|
      |urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: User: Department|Stringa|
      |urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: User: costCenter|Stringa|
      |urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: User: Division|Stringa|
      |urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: User: Manager|Riferimento|
      |urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: utente: Organization|Stringa|
      |urn: IETF: params: SCIM: schemas: Oracle: IDC: Extension: utente: utente: bypassNotification|Boolean|
      |urn: IETF: params: SCIM: schemas: Oracle: IDC: Extension: utente: utente: isFederatedUser|Boolean|

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to Oracle Cloud Infrastructure console**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD alla console di infrastruttura cloud Oracle nella sezione **mapping attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Oracle Cloud Infrastructure console per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|Stringa|
      |externalId|Stringa|
      |Membri di|Riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per Oracle Cloud Infrastructure console, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Oracle Cloud Infrastructure console scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti nell' **ambito** nella sezione **Impostazioni** . Il ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti, a condizione che sia in esecuzione il servizio di provisioning Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato effettuato il provisioning con esito positivo o negativo
* Controllare l' [indicatore di stato per visualizzare](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) lo stato del ciclo di provisioning e il modo in cui terminarlo
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entra in quarantena. Altre informazioni sugli stati di quarantena sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
