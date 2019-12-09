---
title: 'Esercitazione: configurare il provisioning in ingresso di SuccessFactors in Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare il provisioning in ingresso da SuccessFactors a Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: fac4f61e-d942-4429-a298-9ba74db95077
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: d032bf1241f355af110ee8f4da38ff4685bd2e3f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932294"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning-preview"></a>Esercitazione: configurare SAP SuccessFactors per il provisioning utenti di Azure AD (anteprima)
Questa esercitazione descrive la procedura da eseguire per effettuare il provisioning dei dati di lavoro da SuccessFactors Employee Central a Azure Active Directory, con un write-back facoltativo dell'indirizzo di posta elettronica a SuccessFactors. Questa integrazione è in anteprima pubblica e supporta il recupero di più di [70 attributi utente](../manage-apps/sap-successfactors-attribute-reference.md) da SuccessFactors Employee Central. 

>[!NOTE]
>Usare questa esercitazione se gli utenti di cui si vuole eseguire il provisioning da SuccessFactors sono utenti solo cloud che non necessitano di un account AD locale. Se gli utenti richiedono solo un account AD locale o un account AD e Azure AD, fare riferimento all'esercitazione su [come configurare SAP SuccessFactors per Active Directory](sap-successfactors-inbound-provisioning-tutorial.md#overview) il provisioning degli utenti. 

## <a name="overview"></a>Panoramica

Il [servizio di provisioning utenti Azure Active Directory](../manage-apps/user-provisioning.md) si integra con il [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) per gestire il ciclo di vita dell'identità degli utenti. 

I flussi di lavoro di provisioning utenti di SuccessFactors supportati dal servizio di provisioning utenti Azure AD consentono l'automazione dei seguenti scenari di gestione delle risorse umane e del ciclo di vita delle identità:

* **Assunzione di nuovi dipendenti** : quando viene aggiunto un nuovo dipendente a SuccessFactors, viene creato automaticamente un account utente in Azure Active Directory e, facoltativamente, Office 365 e [altre applicazioni SaaS supportate da Azure ad](../manage-apps/user-provisioning.md), con Write-back dell'indirizzo di posta elettronica a SuccessFactors.

* **Aggiornamenti del profilo e dell'attributo del dipendente** : quando un record del dipendente viene aggiornato in SuccessFactors (ad esempio, il nome, il titolo o il responsabile), il relativo account utente verrà aggiornato automaticamente Azure Active Directory e, facoltativamente, Office 365 e [altre applicazioni SaaS supportate da Azure ad](../manage-apps/user-provisioning.md).

* **Interruzioni dei dipendenti** : quando un dipendente viene terminato in SuccessFactors, il relativo account utente viene disabilitato automaticamente in Azure Active Directory e, facoltativamente, Office 365 e [altre applicazioni SaaS supportate da Azure ad](../manage-apps/user-provisioning.md).

* **Riassunzioni dei dipendenti** : quando un dipendente viene riassunto in SuccessFactors, il relativo account precedente può essere riattivato automaticamente o nuovamente sottoposto a provisioning (a seconda delle preferenze) per Azure Active Directory e, facoltativamente, Office 365 e [altre applicazioni SaaS supportate da Azure ad](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Per chi è più adatta questa soluzione di provisioning utenti?

Questo SuccessFactors per Azure Active Directory soluzione di provisioning degli utenti è particolarmente adatto per:

* Organizzazioni che desiderano una soluzione predefinita basata sul cloud per il provisioning utenti di SuccessFactors

* Organizzazioni che richiedono il provisioning utenti diretto da SuccessFactors a Azure Active Directory

* Organizzazioni che richiedono il provisioning degli utenti usando i dati ottenuti da [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizzazioni che usano Office 365 per la posta elettronica

## <a name="solution-architecture"></a>Architettura della soluzione

Questa sezione descrive l'architettura della soluzione di provisioning degli utenti end-to-end per gli utenti solo cloud. Esistono due flussi correlati:

* **Flusso di dati HR autorevole-da SuccessFactors a Azure Active Directory:** In questo flusso, i flussi di lavoro, ad esempio i nuovi assunti, i trasferimenti, le terminazioni, si verificano prima nel cloud SuccessFactors Employee Central, quindi i dati degli eventi passano in Azure Active Directory. A seconda dell'evento, può causare operazioni di creazione, aggiornamento, abilitazione o disabilitazione in Azure AD.
* **Flusso di writeback della posta elettronica: da Active Directory locali a SuccessFactors:** Una volta completata la creazione dell'account in Azure Active Directory, è possibile eseguire il writeback del valore dell'attributo email o dell'UPN generato in Azure AD in SuccessFactors.

  ![Panoramica](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Flusso di dati end-to-end dell'utente

1. Il team HR esegue le transazioni di lavoro (join/spostamenti/abbandonati o nuove assunzioni/trasferimenti/interruzioni) in SuccessFactors Employee Central
2. Il servizio di provisioning Azure AD esegue sincronizzazioni pianificate di identità da SuccessFactors EC e identifica le modifiche che devono essere elaborate per la sincronizzazione con Active Directory locale.
3. Il servizio di provisioning Azure AD determina la modifica e richiama l'operazione di creazione/aggiornamento/abilitazione/disabilitazione per l'utente in Azure AD.
4. Se l' [app writeback SuccessFactors](sap-successfactors-writeback-tutorial.md) è configurata, l'indirizzo di posta elettronica dell'utente viene recuperato dal Azure ad. 
5. Azure AD servizio di provisioning scrive l'attributo email in SuccessFactors, in base all'attributo corrispondente usato.

## <a name="planning-your-deployment"></a>Pianificazione della distribuzione

La configurazione del provisioning utenti basato su cloud HR da SuccessFactors a Azure AD richiede una pianificazione considerevole che copre aspetti diversi, ad esempio:

* Determinazione dell'ID corrispondente 
* Mapping degli attributi
* Trasformazione attributo 
* Filtri per la definizione dell'ambito

Per indicazioni complete su questi argomenti, vedere il [piano di distribuzione HR cloud](../manage-apps/plan-cloud-hr-provision.md) . 

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

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Configurazione del provisioning utenti da SuccessFactors a Azure AD

Questa sezione illustra la procedura per il provisioning degli account utente da SuccessFactors a Azure AD.

* [Aggiungere l'app del connettore di provisioning e configurare la connettività a SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configurare i mapping degli attributi](#part-2-configure-attribute-mappings)
* [Abilitare e avviare il provisioning utenti](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: aggiungere l'app del connettore di provisioning e configurare la connettività a SuccessFactors

**Per configurare SuccessFactors per Azure AD il provisioning:**

1. Vai a <https://portal.azure.com>

2. Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3. Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4. Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5. Cercare **SuccessFactors per Azure Active Directory il provisioning utenti**e aggiungere tale app dalla raccolta.

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
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Una volta salvate correttamente le credenziali, nella sezione **mapping** verrà visualizzato il mapping predefinito **sincronizzare gli utenti di SuccessFactors con Azure Active Directory**

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurare i mapping degli attributi

In questa sezione verrà configurato il flusso dei dati utente da SuccessFactors a Active Directory.

1. Nella scheda provisioning in **mapping**fare clic su **Sincronizza utenti SuccessFactors per Azure Active Directory**.

1. Nel campo **ambito dell'oggetto di origine** è possibile selezionare i set di utenti in SuccessFactors che devono essere nell'ambito per il provisioning in Azure ad, definendo un set di filtri basati su attributi. L'ambito predefinito è "tutti gli utenti in SuccessFactors". Filtri di esempio:

   * Esempio: ambito per gli utenti con personIdExternal compreso tra 1 milione e 2 milioni (escluso 2 milioni)

      * Attributo: personIdExternal

      * Operatore: Corrispondenza REGEX

      * Valore: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Esempio: Solo i dipendenti, senza i lavoratori occasionali

      * Attributo: EmployeeID

      * Operatore: NON È NULL

   > [!TIP]
   > Quando si configura l'app di provisioning per la prima volta, è necessario testare e verificare i mapping degli attributi e le espressioni per assicurarsi che restituisca il risultato desiderato. Microsoft consiglia di usare i filtri di ambito nell' **ambito dell'oggetto di origine** per testare i mapping con alcuni utenti di test di SuccessFactors. Dopo avere verificato che i mapping funzionino è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

   > [!CAUTION] 
   > Il comportamento predefinito del motore di provisioning è disabilitare/eliminare gli utenti che non rientrano nell'ambito. Questo potrebbe non essere auspicabile in SuccessFactors per Azure AD l'integrazione. Per eseguire l'override di questo comportamento predefinito, vedere l'articolo [ignorare l'eliminazione di account utente che non rientrano nell'ambito](../manage-apps/skip-out-of-scope-deletions.md)
  
1. Nel campo **Target Object Actions** (Azioni oggetto di destinazione) è possibile applicare un filtro a livello globale per le azioni che vengono eseguite in Active Directory. **Creazione** e **Aggiornamento** sono le più comuni.

1. Nella sezione **mapping** degli attributi è possibile definire la modalità di mapping dei singoli attributi SuccessFactors a Active Directory attributi.

  >[!NOTE]
  >Per l'elenco completo dell'attributo SuccessFactors supportato dall'applicazione, vedere [riferimento all'attributo SuccessFactors](../manage-apps/sap-successfactors-attribute-reference.md)


1. Fare clic su un mapping di attributi esistente per aggiornarlo oppure fare clic su **Aggiungi nuovo mapping** nella parte inferiore della schermata per aggiungere nuovi mapping. Il mapping di un singolo attributo supporta queste proprietà:

      * **Tipo di mapping**

         * **Direct** : scrive il valore dell'attributo SuccessFactors nell'attributo di Active Directory, senza modifiche

         * **Costant** (Costante): scrive un valore stringa costante statico nell'attributo di AD

         * **Espressione** : consente di scrivere un valore personalizzato nell'attributo di Active Directory, in base a uno o più attributi SuccessFactors. [Per altre informazioni, vedere questo articolo sulle espressioni](../manage-apps/functions-for-customizing-application-data.md).

      * **Attributo di origine** : attributo utente da SuccessFactors

      * **Valore predefinito**: facoltativo. Se l'attributo di origine ha un valore vuoto, il mapping eseguirà la scrittura di questo valore.
            Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

      * **Attributo di destinazione**: l'attributo utente in Active Directory.

      * **Corrisponde a oggetti che usano questo attributo** , indipendentemente dal fatto che questo mapping debba essere usato per identificare in modo univoco gli utenti tra SuccessFactors e Active Directory. Questo valore viene in genere impostato sul campo ID di lavoro per SuccessFactors, che in genere viene mappato a uno degli attributi ID dipendente in Active Directory.

      * **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Se sono presenti più attributi, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.

      * **Applica questo mapping**

         * **Sempre**: applica il mapping sia all'azione di creazione che all'azione di aggiornamento dell'utente

         * **Only during creation** (Solo durante la creazione): applica il mapping solo alle azioni di creazione dell'utente

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

* [Altre informazioni sugli attributi SuccessFactors supportati per il provisioning in ingresso](../manage-apps/sap-successfactors-attribute-reference.md)
* [Informazioni su come configurare il writeback della posta elettronica in SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
* [Informazioni su come configurare Single Sign-On tra SuccessFactors e Azure Active Directory](successfactors-tutorial.md)
* [Informazioni sull'integrazione di altre applicazioni SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni su come esportare e importare le configurazioni di provisioning](../manage-apps/export-import-provisioning-configuration.md)


