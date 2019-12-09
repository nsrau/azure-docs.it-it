---
title: 'Esercitazione: configurare il writeback SuccessFactors in Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare il writeback degli attributi in SuccessFactors da Azure AD
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
ms.openlocfilehash: c780ee973c1dabb15c37b2519eb8253d2371080a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932307"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Esercitazione: configurare il writeback degli attributi da Azure AD a SAP SuccessFactors (anteprima)
L'obiettivo di questa esercitazione è mostrare i passaggi da eseguire per gli attributi di writeback da Azure AD a SuccessFactors Employee Central. L'unico attributo attualmente supportato per il writeback è l'attributo email. 

## <a name="overview"></a>Panoramica

Dopo aver configurato l'integrazione del provisioning in ingresso usando SuccessFactors per l'app di provisioning di [ad locale](sap-successfactors-inbound-provisioning-tutorial.md) o [SuccessFactors per Azure ad](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) app di provisioning, è possibile configurare facoltativamente l'app writeback di SuccessFactors per scrivere l'indirizzo di posta elettronica in SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Per chi è più adatta questa soluzione di provisioning utenti?

Questa soluzione di provisioning utenti writeback di SuccessFactors è ideale per:

* Organizzazioni che usano Office 365 che desiderano eseguire il writeback degli attributi autorevoli gestiti dall'IT (ad esempio, l'indirizzo di posta elettronica) a SuccessFactors

## <a name="configuring-successfactors-for-the-integration"></a>Configurazione di SuccessFactors per l'integrazione

Un requisito comune di tutti i connettori di provisioning di SuccessFactors è che richiedono le credenziali di un account SuccessFactors con le autorizzazioni appropriate per richiamare le API OData SuccessFactors. In questa sezione vengono descritti i passaggi per creare l'account del servizio in SuccessFactors e concedere le autorizzazioni appropriate. 

* [Creare/identificare l'account utente dell'API in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Creazione di un ruolo autorizzazioni API](#create-an-api-permissions-role)
* [Creare un gruppo di autorizzazioni per l'utente dell'API](#create-a-permission-group-for-the-api-user)
* [Concedere il ruolo autorizzazione al gruppo di autorizzazioni](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Creare/identificare l'account utente dell'API in SuccessFactors
Collaborare con il team amministratore di SuccessFactors o con il partner di implementazione per creare o identificare un account utente in SuccessFactors che verrà usato per richiamare le API OData. Quando si configurano le app di provisioning in Azure AD, verranno richieste le credenziali di nome utente e password di questo account. 

### <a name="create-an-api-permissions-role"></a>Creazione di un ruolo autorizzazioni API

* Accedere a SAP SuccessFactors con un account utente che ha accesso al centro di amministrazione.
* Cercare *Gestisci ruoli di autorizzazione*, quindi selezionare **Gestisci ruoli di autorizzazione** dai risultati della ricerca.
  ![gestire i ruoli di autorizzazione](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Nell'elenco ruolo autorizzazione fare clic su **Crea nuovo**.
  > [!div class="mx-imgBorder"]
  > ![creare un nuovo ruolo di autorizzazione](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Aggiungere un **nome di ruolo** e una **Descrizione** per il nuovo ruolo di autorizzazione. Il nome e la descrizione devono indicare che il ruolo è per le autorizzazioni di utilizzo dell'API.
  > [!div class="mx-imgBorder"]
  > dettagli del ruolo di autorizzazione ![](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* In impostazioni autorizzazioni fare clic su **autorizzazione...** , quindi scorrere verso il basso l'elenco di autorizzazioni e fare clic su **Gestisci strumenti di integrazione**. Selezionare la casella **Consenti all'amministratore di accedere all'API OData tramite l'autenticazione di base**.
  > [!div class="mx-imgBorder"]
  > ![gestire gli strumenti di integrazione](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Scorrere verso il basso nella stessa casella e selezionare **Employee Central API**. Aggiungere le autorizzazioni come illustrato di seguito per leggere usando l'API ODATA e modificare usando l'API ODATA. Selezionare l'opzione modifica se si prevede di usare lo stesso account per lo scenario writeback per SuccessFactors. 
  > [!div class="mx-imgBorder"]
  > ![le autorizzazioni di lettura scrittura](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Fare clic su **Done** (Fine). Fare clic su **Save Changes**.

### <a name="create-a-permission-group-for-the-api-user"></a>Creare un gruppo di autorizzazioni per l'utente dell'API

* Nell'interfaccia di amministrazione di SuccessFactors cercare *Gestisci gruppi di autorizzazioni*, quindi selezionare **Gestisci gruppi di autorizzazioni** nei risultati della ricerca.
  > [!div class="mx-imgBorder"]
  > ![gestire i gruppi di autorizzazioni](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Nella finestra Gestisci gruppi di autorizzazioni, fare clic su **Crea nuovo**.
  > [!div class="mx-imgBorder"]
  > ![Aggiungi nuovo gruppo](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Aggiungere un nome di gruppo per il nuovo gruppo. Il nome del gruppo deve indicare che il gruppo è per gli utenti dell'API.
  > [!div class="mx-imgBorder"]
  > nome del gruppo di autorizzazioni ![](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Aggiungere membri al gruppo. Ad esempio, è possibile selezionare **nome utente** dal menu a discesa pool di persone e quindi immettere il nome utente dell'account API che verrà usato per l'integrazione. 
  > [!div class="mx-imgBorder"]
  > ![Aggiungere membri del gruppo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Fare **clic su fine per** completare la creazione del gruppo di autorizzazioni.

### <a name="grant-permission-role-to-the-permission-group"></a>Concedere il ruolo autorizzazione al gruppo di autorizzazioni

* Nell'interfaccia di amministrazione di SuccessFactors cercare *Gestisci ruoli di autorizzazione*, quindi selezionare **Gestisci ruoli di autorizzazione** dai risultati della ricerca.
* Dall' **elenco ruolo autorizzazione**selezionare il ruolo creato per le autorizzazioni di utilizzo dell'API.
* In **concedi questo ruolo a...** fare clic sul pulsante **Aggiungi..** ..
* Selezionare **gruppo di autorizzazioni...** dal menu a discesa, quindi fare clic su **Seleziona.** .. per aprire la finestra gruppi per cercare e selezionare il gruppo creato in precedenza. 
  > [!div class="mx-imgBorder"]
  > ![aggiungere il gruppo di autorizzazioni](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Esaminare la concessione del ruolo autorizzazione al gruppo di autorizzazioni. 
  > [!div class="mx-imgBorder"]
  > ![i dettagli del gruppo e del ruolo di autorizzazione](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Fare clic su **Save Changes**.

## <a name="configuring-successfactors-writeback"></a>Configurazione del writeback SuccessFactors

In questa sezione vengono illustrati i passaggi per 

* [Aggiungere l'app del connettore di provisioning e configurare la connettività a SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configurare i mapping degli attributi](#part-2-configure-attribute-mappings)
* [Abilitare e avviare il provisioning utenti](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: aggiungere l'app del connettore di provisioning e configurare la connettività a SuccessFactors

**Per configurare il writeback SuccessFactors:**

1. Vai a <https://portal.azure.com>

2. Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3. Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4. Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5. Cercare il **writeback SuccessFactors**e aggiungere tale app dalla raccolta.

6. Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**

7. Impostare **Modalità di** **provisioning** su **Automatico**

8. Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore** : immettere il nome utente dell'account utente dell'API SuccessFactors, con l'ID società accodato. Il formato è: **username\@companyID**

   * **Password amministratore:** Immettere la password dell'account utente dell'API SuccessFactors. 

   * **URL tenant:** Immettere il nome dell'endpoint di servizi API OData di SuccessFactors. Immettere solo il nome host del server senza http o HTTPS. Questo valore dovrebbe essere simile al seguente: **API-Server-Name.SuccessFactors.com**.

   * **Indirizzo di posta elettronica per le notifiche:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo per inviare una notifica di posta elettronica in caso di errore.
         > [!NOTE]
         > The Azure AD Provisioning Service sends email notification if the provisioning job goes into a [quarantine](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) state.

   * Fare clic sul pulsante **Test connessione**. Se il test della connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. In caso di errore, verificare che le credenziali e l'URL di SuccessFactors siano validi.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Una volta salvate correttamente le credenziali, nella sezione **mapping** verrà visualizzato il mapping predefinito **Sincronizza Azure Active Directory utenti a SuccessFactors**

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurare i mapping degli attributi

In questa sezione verrà configurato il flusso dei dati utente da SuccessFactors a Active Directory.

1. Nella scheda provisioning in **mapping**fare clic su **Sincronizza Azure Active Directory utenti a SuccessFactors**.

1. Nel campo **ambito dell'oggetto di origine** è possibile selezionare i set di utenti in Azure ad da considerare per il writeback, definendo un set di filtri basati su attributi. L'ambito predefinito è "tutti gli utenti in Azure AD". 
   > [!TIP]
   > Quando si configura l'app di provisioning per la prima volta, è necessario testare e verificare i mapping degli attributi e le espressioni per assicurarsi che restituisca il risultato desiderato. Microsoft consiglia di utilizzare i filtri di ambito nell' **ambito dell'oggetto di origine** per testare i mapping con pochi utenti di test da Azure ad. Dopo avere verificato che i mapping funzionino è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

1. Il campo **azioni oggetto di destinazione** supporta solo l'operazione di **aggiornamento** .

1. Nella sezione **mapping degli attributi** è possibile modificare solo l'ID corrispondente usato per collegare un profilo utente SuccessFactors a Azure ad utente e l'attributo in Azure ad funge da origine del messaggio di posta elettronica. 
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >Il writeback SuccessFactors supporta solo l'attributo email. Per aggiungere nuovi attributi, non usare **Aggiungi nuovo mapping** . 

1. Per salvare i mapping, fare clic su **Save**, Salva, nella parte superiore della sezione Attribute-Mapping, Mapping attributi.

Dopo aver completato la configurazione di mapping di attributo, è ora possibile [abilitare e avviare il servizio di provisioning utenti](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Abilitare e avviare il provisioning utenti

Una volta completate le configurazioni dell'app di provisioning di SuccessFactors, è possibile attivare il servizio di provisioning nel portale di Azure.

> [!TIP]
> Per impostazione predefinita quando si attiva il servizio di provisioning, verranno avviate le operazioni di provisioning per tutti gli utenti nell'ambito. Se sono presenti errori di mapping o problemi di dati in Workday, il processo di provisioning potrebbe non riuscire e passare allo stato di quarantena. Come procedura consigliata per evitare questo problema è consigliabile configurare il filtro **Source Object Scope** (Ambito dell'oggetto di origine) e il test di mapping degli attributi con alcuni utenti test prima di avviare la sincronizzazione completa per tutti gli utenti. Dopo avere verificato che i mapping funzionino e che restituiscano i risultati desiderati è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

1. Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

2. Fare clic su **Salva**

3. Questa operazione avvierà la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti presenti nel tenant di SuccessFactors. È possibile controllare l'indicatore di stato per tenere traccia dello stato di avanzamento del ciclo di sincronizzazione. 

4. In qualsiasi momento è possibile controllare la scheda **Log di controllo** nel portale di Azure per vedere le azioni che sono state eseguite dal servizio di provisioning. I log di controllo elencano tutti i singoli eventi di sincronizzazione eseguiti dal servizio di provisioning, ad esempio quali utenti vengono letti da Workday e successivamente aggiunti o aggiornati in Active Directory. 

5. Al termine della sincronizzazione iniziale, verrà scritto un report di riepilogo di controllo nella scheda **Provisioning**, come illustrato di seguito.

   > [!div class="mx-imgBorder"]
   > indicatore di stato del provisioning ![](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
* [Informazioni su come configurare Single Sign-On tra SuccessFactors e Azure Active Directory](successfactors-tutorial.md)
* [Informazioni sull'integrazione di altre applicazioni SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni su come esportare e importare le configurazioni di provisioning](../manage-apps/export-import-provisioning-configuration.md)

