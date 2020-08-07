---
title: 'Esercitazione: Configurare 8x8 per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a 8x8.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 81b4cde7-4938-4a1a-8495-003c06239b27
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2020
ms.author: Zhchia
ms.openlocfilehash: d1359ffb28cb98c0e46a6b7eff32c6faa7d00ba4
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87924774"
---
# <a name="tutorial-configure-8x8-for-automatic-user-provisioning"></a>Esercitazione: Configurare 8x8 per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire sia in Configuration Manager per 8x8 che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Una volta configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi per [8x8](https://www.8x8.com) usando il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creazione di utenti in 8x8
> * Rimozione di utenti in 8x8 quando l'accesso non è più necessario
> * Mantenimento della sincronizzazione degli attributi utente tra Azure AD e 8x8
> * [Accesso Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial) a 8x8 (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale.
* Sottoscrizione della serie 8x8 X di qualsiasi livello.
* Un account utente 8x8 con autorizzazioni dell'amministratore in [Configuration Manager](https://vo-cm.8x8.com).
* [Single Sign-on con Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial) è già stato configurato.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati [mappare tra Azure AD e 8x8](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-8x8-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare 8x8 per supportare il provisioning con Azure AD

Questa sezione illustra i passaggi per configurare 8x8 per supportare il provisioning con Azure AD.

### <a name="to-configure-a-user-provisioning-access-token-in-8x8-configuration-manager"></a>Per configurare un token di accesso per il provisioning utenti in 8x8 Configuration Manager:

1. Accedere a [Configuration Manager](https://vo-cm.8x8.com). Selezionare **Identity Management**.

   ![Identity Management](./media/8x8-provisioning-tutorial/8x8-identity-management.png)

2. Fare clic sul collegamento **Mostra informazioni sul provisioning utenti** per generare un token.

   ![Mostra provisioning utenti](./media/8x8-provisioning-tutorial/8x8-show-user-provisioning.png)

3. Copiare i valori di **URL 8x8**  e **token API 8x8**. Questi valori verranno immessi nei campi **URL tenant** e **Token segreto** rispettivamente, nella scheda Provisioning dell'applicazione 8x8 nel portale di Azure.

   ![Copiare URL e token](./media/8x8-provisioning-tutorial/8x8-copy-url-token.png)

## <a name="step-3-add-8x8-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere 8x8 dalla raccolta di applicazioni di Azure AD

Aggiungere 8x8 dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in 8x8. Se 8x8 è stato configurato in precedenza per l'accesso SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Questa è l'opzione più semplice e viene usata dalla maggior parte degli utenti.

Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a 8x8, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-8x8"></a>Passaggio 5. Configurare il provisioning automatico degli utenti per 8x8 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in 8x8 in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-8x8-in-azure-ad"></a>Per configurare il provisioning utenti automatico per 8x8 in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](./media/8x8-provisioning-tutorial/enterprise-applications.png)

    ![Pannello Tutte le applicazioni](./media/8x8-provisioning-tutorial/all-applications.png)

2. Nell'elenco delle applicazioni selezionare **8x8**.

    ![Collegamento di 8x8 nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**. Fare clic su **Inizia**.

    ![Scheda Provisioning](common/provisioning.png)

   ![Pannello di avvio](./media/8x8-provisioning-tutorial/get-started.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di amministratore** copiare l'**URL 8x8** da Configuration Manager in **URL tenant**. Copiare il **token API 8x8 Virtual** da Configuration Manager in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a 8x8. Se la connessione non riesce, verificare che l'account 8x8 abbia autorizzazioni di amministratore e riprovare.

    ![Provisioning](./media/8x8-provisioning-tutorial/provisioning.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Provision Azure Active Directory Users** (Esegui provisioning utenti di Azure Active Directory).

9. Esaminare gli attributi utente sincronizzati da Azure AD a 8x8 nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in 8x8 per le operazioni di aggiornamento. Se si sceglie di modificare l'[attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), sarà necessario assicurarsi che l'API 8x8 supporti il filtro degli utenti basato su tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Note|
   |---|---|---|
   |userName|string|Impostare sia il nome utente che l'ID della federazione|
   |externalId|string||
   |active|Boolean||
   |title|string||
   |emails[type eq "work"].value|string||
   |name.givenName|string||
   |name.familyName|string||
   |phoneNumbers[type eq "mobile"].value|string|Numero di contatto personale|
   |phoneNumbers[type eq "work"].value|string|Numero di contatto personale|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string||
   |urn:ietf:params:scim:schemas:extension:8x8:1.1:User:site|string|Non può essere aggiornato dopo la creazione dell'utente|
   |locale|string|Mapping non eseguito per impostazione predefinita|
   |timezone|string|Mapping non eseguito per impostazione predefinita|

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per 8x8, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in 8x8 selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
