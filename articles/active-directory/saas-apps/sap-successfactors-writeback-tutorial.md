---
title: 'Esercitazione: Configurare il writeback di SuccessFactors in Azure Active Directory Documenti Microsoft'
description: Informazioni su come configurare il writeback degli attributi in SuccessFactors da Azure ADLearn how to configure attribute writeback to SuccessFactors from Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 2de0cdd32428884170f549afacdbd52c3a10c93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060049"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Esercitazione: Configurare il writeback degli attributi da Azure AD a SAP SuccessFactors (anteprima)Tutorial: Configure attribute writeback from Azure AD to SAP SuccessFactors (Preview)
L'obiettivo di questa esercitazione è mostrare i passaggi da eseguire per scrivere gli attributi da Azure AD a SuccessFactors Employee Central.The objective of this tutorial is to show the steps you need to writeback attributes from Azure AD to SuccessFactors Employee Central. L'unico attributo attualmente supportato per il writeback è l'attributo email. 

## <a name="overview"></a>Panoramica

Dopo aver configurato l'integrazione del provisioning in ingresso usando SuccessFactors nell'app di provisioning di Active Directory locale o SuccessFactors nell'app di provisioning di Azure AD, puoi configurare facoltativamente l'app WriteBack SuccessFactors per scrivere l'indirizzo di posta elettronica in SuccessFactors.After you setup inbound provisioning integration using either [SuccessFactors to on-premises AD](sap-successfactors-inbound-provisioning-tutorial.md) provisioning app or [SuccessFactors to Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) provisioning app, you can optionally configure the SuccessFactors Writeback app to write email address back to SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Per chi è più adatta questa soluzione di provisioning utenti?

Questa soluzione di provisioning degli utenti writeback SuccessFactors è ideale per:This SuccessFactors Writeback user provisioning solution is ideally suited for:

* Organizzazioni che utilizzano Office 365 che desiderano scrivere attributi autorevoli gestiti dall'IT (ad esempio l'indirizzo di posta elettronica) a SuccessFactors

## <a name="configuring-successfactors-for-the-integration"></a>Configurazione di SuccessFactors per l'integrazione

Un requisito comune di tutti i connettori di provisioning SuccessFactors è che richiedono le credenziali di un account SuccessFactors con le autorizzazioni appropriate per richiamare le API OData SuccessFactors. In questa sezione vengono descritti i passaggi per creare l'account del servizio in SuccessFactors e concedere le autorizzazioni appropriate. 

* [Creare/identificare l'account utente dell'API in SuccessFactorsCreate/identify API user account in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Creare un ruolo di autorizzazioni APICreate an API permissions role](#create-an-api-permissions-role)
* [Creare un gruppo di autorizzazioni per l'utente dell'APICreate a Permission Group for the API user](#create-a-permission-group-for-the-api-user)
* [Concedere il ruolo di autorizzazione al gruppo di autorizzazioniGrant Permission Role to the Permission Group](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Creare/identificare l'account utente dell'API in SuccessFactorsCreate/identify API user account in SuccessFactors
Collaborare con il team di amministrazione o il partner di implementazione SuccessFactors per creare o identificare un account utente in SuccessFactors che verrà utilizzato per richiamare le API OData. Le credenziali di nome utente e password di questo account saranno necessarie durante la configurazione delle app di provisioning in Azure AD. 

### <a name="create-an-api-permissions-role"></a>Creare un ruolo di autorizzazioni APICreate an API permissions role

* Accedere a SAP SuccessFactors con un account utente che ha accesso all'interfaccia di amministrazione.
* Cercare *Gestisci ruoli di autorizzazione*, quindi selezionare **Gestisci ruoli di autorizzazione** dai risultati della ricerca.
  ![Gestire i ruoli di autorizzazione](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Nell'Elenco ruoli autorizzazione fare clic su **Crea nuovo**.
  > [!div class="mx-imgBorder"]
  > ![Crea nuovo ruolo di autorizzazione](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Aggiungere un **Nome ruolo** e **una Descrizione** per il nuovo ruolo di autorizzazione. Il nome e la descrizione devono indicare che il ruolo è per le autorizzazioni di utilizzo dell'API.
  > [!div class="mx-imgBorder"]
  > ![Dettaglio ruolo autorizzazione](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* In Impostazioni autorizzazione fare clic su **Autorizzazione...**, quindi scorrere l'elenco delle autorizzazioni e fare clic su Gestisci strumenti di **integrazione**. Selezionare la casella **Consenti all'amministratore di accedere all'API OData tramite l'autenticazione di base**.
  > [!div class="mx-imgBorder"]
  > ![Gestire gli strumenti di integrazione](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Scorrere verso il basso nella stessa casella e selezionare **API Di Employee Central**. Aggiungere le autorizzazioni come illustrato di seguito per leggere utilizzando l'API ODATA e modificare utilizzando l'API ODATA. Selezionare l'opzione di modifica se si prevede di utilizzare lo stesso account per lo scenario Writeback to SuccessFactors. 
  > [!div class="mx-imgBorder"]
  > ![Autorizzazioni di scrittura in letturaRead write permissions](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Fare clic su **Done** (Fine). Fare clic su **Save Changes**.

### <a name="create-a-permission-group-for-the-api-user"></a>Creare un gruppo di autorizzazioni per l'utente dell'APICreate a Permission Group for the API user

* Nell'interfaccia di amministrazione di SuccessFactors cercare *Gestisci gruppi*di autorizzazioni , quindi selezionare Gestisci gruppi di **autorizzazioni** nei risultati della ricerca.
  > [!div class="mx-imgBorder"]
  > ![Gestire i gruppi di autorizzazioni](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Nella finestra Gestisci gruppi di autorizzazioni fare clic su **Crea nuovo**.
  > [!div class="mx-imgBorder"]
  > ![Aggiungere un nuovo gruppo](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Aggiungere un Nome gruppo per il nuovo gruppo. Il nome del gruppo deve indicare che il gruppo è per gli utenti API.
  > [!div class="mx-imgBorder"]
  > ![Nome del gruppo di autorizzazioni](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Aggiungere membri al gruppo. Ad esempio, è possibile selezionare **Nome utente** dal menu a discesa Pool di utenti e quindi immettere il nome utente dell'account API che verrà utilizzato per l'integrazione. 
  > [!div class="mx-imgBorder"]
  > ![Aggiungere membri del gruppo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Fare clic su **Fine** per completare la creazione del gruppo di autorizzazioni.

### <a name="grant-permission-role-to-the-permission-group"></a>Concedere il ruolo di autorizzazione al gruppo di autorizzazioniGrant Permission Role to the Permission Group

* Nell'interfaccia di amministrazione di SuccessFactors cercare *Gestisci ruoli di autorizzazione*, quindi selezionare Gestisci ruoli di **autorizzazione** dai risultati della ricerca.
* **Nell'elenco dei ruoli di autorizzazione**selezionare il ruolo creato per le autorizzazioni di utilizzo delle API.
* In **Concedi questo ruolo a...**, fare clic sul pulsante **Aggiungi....**
* Selezionare **Gruppo di autorizzazioni...** dal menu a discesa, quindi fare clic su **Seleziona...** per aprire la finestra Gruppi in cui cercare e selezionare il gruppo creato in precedenza. 
  > [!div class="mx-imgBorder"]
  > ![Aggiungere un gruppo di autorizzazioni](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Esaminare la concessione Ruolo autorizzazione al gruppo di autorizzazioni. 
  > [!div class="mx-imgBorder"]
  > ![Dettaglio ruolo autorizzazione e gruppo](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Fare clic su **Save Changes**.

## <a name="configuring-successfactors-writeback"></a>Configurazione del writeback di SuccessFactorsConfiguring SuccessFactors Writeback

In questa sezione vengono illustrate le procedure per 

* [Aggiungere l'app connettore di provisioning e configurare la connettività a SuccessFactorsAdd the provisioning connector app and configure connectivity to SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configurare i mapping degli attributi](#part-2-configure-attribute-mappings)
* [Abilitare e avviare il provisioning utenti](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Aggiungere l'app connettore di provisioning e configurare la connettività a SuccessFactorsPart 1: Add the provisioning connector app and configure connectivity to SuccessFactors

**Per configurare il writeback SuccessFactors:**

1. Passare a <https://portal.azure.com>.

2. Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3. Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4. Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5. Cercare **SuccessFactors Writeback**e aggiungere l'app dalla raccolta.

6. Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**

7. Impostare **Modalità di** **provisioning** su **Automatico**

8. Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore:** immettere il nome utente dell'account utente dell'API SuccessFactors, con l'ID società aggiunto. Ha il formato: **\@username companyID**

   * **Password amministratore –** Immettere la password dell'account utente dell'API SuccessFactors. 

   * **URL tenant –** Immettere il nome dell'endpoint dei servizi API OData SuccessFactors. Immettere solo il nome host del server senza http o https. Questo valore dovrebbe essere simile al seguente: **api-server-name.successfactors.com**.

   * **E-mail di notifica –** Inserisci il tuo indirizzo email e seleziona la casella di controllo "Invia e-mail in caso di errore".
    > [!NOTE]
    > Il servizio di provisioning di Azure AD invia una notifica di posta elettronica se il processo di provisioning entra in uno stato di [quarantena](/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

   * Fare clic sul pulsante **Test connessione**. Se il test di connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. Se non riesce, verificare che le credenziali e l'URL successFactors siano validi.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Una volta salvate correttamente le credenziali, nella sezione **Mapping** verrà visualizzato il mapping predefinito Sincronizza utenti di **Azure Active Directory in SuccessFactors**

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurare i mapping degli attributi

In questa sezione verrà configurato il modo in cui i dati utente passano da SuccessFactors ad Active Directory.

1. Nella scheda Provisioning in **Mapping**fare clic su Sincronizza utenti di **Azure Active Directory con SuccessFactors**.

1. Nel campo **Ambito oggetto** di origine è possibile selezionare i set di utenti in Azure AD da considerare per il writeback, definendo un set di filtri basati su attributi. L'ambito predefinito è "tutti gli utenti in Azure AD". 
   > [!TIP]
   > Quando si configura l'app di provisioning per la prima volta, è necessario testare e verificare i mapping degli attributi e le espressioni per assicurarsi che restituisca il risultato desiderato. Microsoft consiglia di usare i filtri di ambito in **Ambito oggetto** di origine per testare i mapping con alcuni utenti di test di Azure AD. Dopo avere verificato che i mapping funzionino è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

1. Il campo **Target Object Actions** supporta solo l'operazione di **aggiornamento.**

1. Nella sezione **Mapping di** attributi è possibile modificare solo l'ID corrispondente usato per collegare un profilo utente SuccessFactors con un utente di Azure AD e quale attributo funge da origine della posta elettronica. 
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >Il writeback SuccessFactors supporta solo l'attributo email. Non utilizzare **Aggiungi nuova mappatura** per aggiungere nuovi attributi. 

1. Per salvare i mapping, fare clic su **Salva** nella parte superiore della sezione Mapping attributi.

Dopo aver completato la configurazione di mapping di attributo, è ora possibile [abilitare e avviare il servizio di provisioning utenti](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Abilitare e avviare il provisioning utenti

Una volta completate le configurazioni dell'app di provisioning SuccessFactors, è possibile attivare il servizio di provisioning nel portale di Azure.Once the SuccessFactors provisioning app configurations have been completed, you can turn on the provisioning service in the Azure portal.

> [!TIP]
> Per impostazione predefinita quando si attiva il servizio di provisioning, verranno avviate le operazioni di provisioning per tutti gli utenti nell'ambito. Se sono presenti errori di mapping o problemi di dati in Workday, il processo di provisioning potrebbe non riuscire e passare allo stato di quarantena. Come procedura consigliata per evitare questo problema è consigliabile configurare il filtro **Source Object Scope** (Ambito dell'oggetto di origine) e il test di mapping degli attributi con alcuni utenti test prima di avviare la sincronizzazione completa per tutti gli utenti. Dopo avere verificato che i mapping funzionino e che restituiscano i risultati desiderati è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

1. Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

2. Fare clic su **Salva**.

3. Questa operazione avvierà la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti presenti nel tenant SuccessFactors.This operation will start the initial sync, which can take a variable number of hours depending on how many users are in the SuccessFactors tenant. È possibile controllare la barra di avanzamento per tenere traccia dell'avanzamento del ciclo di sincronizzazione. 

4. In qualsiasi momento è possibile controllare la scheda **Log di controllo** nel portale di Azure per vedere le azioni che sono state eseguite dal servizio di provisioning. I log di controllo elencano tutti i singoli eventi di sincronizzazione eseguiti dal servizio di provisioning, ad esempio quali utenti vengono letti da Workday e successivamente aggiunti o aggiornati in Active Directory. 

5. Al termine della sincronizzazione iniziale, verrà scritto un report di riepilogo di controllo nella scheda **Provisioning**, come illustrato di seguito.

   > [!div class="mx-imgBorder"]
   > ![Barra di avanzamento del provisioning](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
* [Informazioni su come configurare l'accesso Single Sign-On tra SuccessFactors e Azure Active Directory](successfactors-tutorial.md)
* [Informazioni sull'integrazione di altre applicazioni SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni su come esportare e importare le configurazioni di provisioning](../app-provisioning/export-import-provisioning-configuration.md)

