---
title: 'Esercitazione: configurare il writeback di SAP SuccessFactors in Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare il writeback degli attributi in SAP SuccessFactors da Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.openlocfilehash: 4b048053a553176f73b5bd199bcb6e28bc74cc6c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88533997"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>Esercitazione: configurare il writeback degli attributi da Azure AD a SAP SuccessFactors
L'obiettivo di questa esercitazione è mostrare i passaggi per la scrittura di attributi da Azure AD a SAP SuccessFactors Employee Central. 

## <a name="overview"></a>Panoramica

È possibile configurare l'app writeback di SAP SuccessFactors per scrivere attributi specifici da Azure Active Directory a SAP SuccessFactors Employee Central. L'app di provisioning writeback SuccessFactors supporta l'assegnazione di valori ai seguenti attributi centrali dei dipendenti:

* Indirizzo di posta elettronica aziendale
* Username
* Numero di telefono dell'ufficio (incluso il codice paese, il codice dell'area, il numero e l'estensione)
* Flag primario numero di telefono aziendale
* Numero di telefono cellulare (incluso il codice del paese, il codice di area, il numero)
* Flag primario telefono cellulare 
* Attributi utente custom01-custom15
* attributo loginMethod

> [!NOTE]
> Questa app non presenta alcuna dipendenza dalle app per l'integrazione del provisioning utenti in ingresso SuccessFactors. È possibile configurarlo indipendentemente da [SuccessFactors nell'](sap-successfactors-inbound-provisioning-tutorial.md) app di provisioning di ad locale o [SuccessFactors per Azure ad app di](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) provisioning.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Per chi è più adatta questa soluzione di provisioning utenti?

Questa soluzione di provisioning utenti writeback di SuccessFactors è ideale per:

* Le organizzazioni che usano Office 365 che desiderano eseguire il writeback degli attributi autorevoli gestiti dall'IT (ad esempio indirizzo di posta elettronica, telefono, nome utente) a SuccessFactors Employee Central.

## <a name="configuring-successfactors-for-the-integration"></a>Configurazione di SuccessFactors per l'integrazione

Tutti i connettori di provisioning di SuccessFactors richiedono le credenziali di un account SuccessFactors con le autorizzazioni appropriate per richiamare le API OData centrali del dipendente. In questa sezione vengono descritti i passaggi per creare l'account del servizio in SuccessFactors e concedere le autorizzazioni appropriate. 

* [Creare/identificare l'account utente dell'API in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Creazione di un ruolo autorizzazioni API](#create-an-api-permissions-role)
* [Creare un gruppo di autorizzazioni per l'utente dell'API](#create-a-permission-group-for-the-api-user)
* [Concedere il ruolo autorizzazione al gruppo di autorizzazioni](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Creare/identificare l'account utente dell'API in SuccessFactors
Collaborare con il team amministratore di SuccessFactors o con il partner di implementazione per creare o identificare un account utente in SuccessFactors che verrà usato per richiamare le API OData. Quando si configurano le app di provisioning in Azure AD, verranno richieste le credenziali di nome utente e password di questo account. 

### <a name="create-an-api-permissions-role"></a>Creazione di un ruolo autorizzazioni API

1. Accedere a SAP SuccessFactors con un account utente che ha accesso al centro di amministrazione.
1. Cercare *Gestisci ruoli di autorizzazione*, quindi selezionare **Gestisci ruoli di autorizzazione** dai risultati della ricerca.

   ![Gestisci ruoli di autorizzazione](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. Nell'elenco ruolo autorizzazione fare clic su **Crea nuovo**.

   > [!div class="mx-imgBorder"]
   > ![Crea nuovo ruolo autorizzazione](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. Aggiungere un **nome di ruolo** e una **Descrizione** per il nuovo ruolo di autorizzazione. Il nome e la descrizione devono indicare che il ruolo è per le autorizzazioni di utilizzo dell'API.

   > [!div class="mx-imgBorder"]
   > ![Dettagli ruolo autorizzazione](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. In impostazioni autorizzazioni fare clic su **autorizzazione...**, quindi scorrere verso il basso l'elenco di autorizzazioni e fare clic su **Gestisci strumenti di integrazione**. Selezionare la casella **Consenti all'amministratore di accedere all'API OData tramite l'autenticazione di base**.

   > [!div class="mx-imgBorder"]
   > ![Gestione degli strumenti di integrazione](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. Scorrere verso il basso nella stessa casella e selezionare **Employee Central API**. Aggiungere le autorizzazioni come illustrato di seguito per leggere usando l'API ODATA e modificare usando l'API ODATA. Selezionare l'opzione Edit (modifica) se si prevede di usare lo stesso account per lo scenario write-back to SuccessFactors. 

   > [!div class="mx-imgBorder"]
   > ![Autorizzazioni di lettura scrittura](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Fare clic su **Done** (Fine). Fare clic su **Salva modifiche**.

### <a name="create-a-permission-group-for-the-api-user"></a>Creare un gruppo di autorizzazioni per l'utente dell'API

1. Nell'interfaccia di amministrazione di SuccessFactors cercare *Gestisci gruppi di autorizzazioni*, quindi selezionare **Gestisci gruppi di autorizzazioni** nei risultati della ricerca.

   > [!div class="mx-imgBorder"]
   > ![Gestisci gruppi di autorizzazioni](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. Nella finestra Gestisci gruppi di autorizzazioni, fare clic su **Crea nuovo**.

   > [!div class="mx-imgBorder"]
   > ![Aggiungere un nuovo gruppo](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. Aggiungere un nome di gruppo per il nuovo gruppo. Il nome del gruppo deve indicare che il gruppo è per gli utenti dell'API.

   > [!div class="mx-imgBorder"]
   > ![Nome gruppo di autorizzazioni](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. Aggiungere membri al gruppo. Ad esempio, è possibile selezionare **nome utente** dal menu a discesa pool di persone e quindi immettere il nome utente dell'account API che verrà usato per l'integrazione. 

   > [!div class="mx-imgBorder"]
   > ![Aggiungere membri del gruppo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)

1. Fare **clic su fine per** completare la creazione del gruppo di autorizzazioni.

### <a name="grant-permission-role-to-the-permission-group"></a>Concedere il ruolo autorizzazione al gruppo di autorizzazioni

1. Nell'interfaccia di amministrazione di SuccessFactors cercare *Gestisci ruoli di autorizzazione*, quindi selezionare **Gestisci ruoli di autorizzazione** dai risultati della ricerca.
1. Dall' **elenco ruolo autorizzazione**selezionare il ruolo creato per le autorizzazioni di utilizzo dell'API.
1. In **concedi questo ruolo a...** fare clic sul pulsante **Aggiungi..** ..
1. Selezionare **gruppo di autorizzazioni...** dal menu a discesa, quindi fare clic su **Seleziona.** .. per aprire la finestra gruppi per cercare e selezionare il gruppo creato in precedenza. 

   > [!div class="mx-imgBorder"]
   > ![Aggiungi gruppo di autorizzazioni](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. Esaminare la concessione del ruolo autorizzazione al gruppo di autorizzazioni. 
   > [!div class="mx-imgBorder"]
   > ![Dettagli gruppo e ruolo autorizzazione](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. Fare clic su **Salva modifiche**.

## <a name="preparing-for-successfactors-writeback"></a>Preparazione per il writeback SuccessFactors

L'app di provisioning del writeback SuccessFactors USA determinati valori di *codice* per l'impostazione di messaggi di posta elettronica e numeri di telefono in Employee Central. Questi valori di *codice* vengono impostati come valori costanti nella tabella di mapping degli attributi e sono diversi per ogni istanza di SuccessFactors. Questa sezione usa il [post](https://www.postman.com/downloads/) per recuperare i valori del codice. Per inviare richieste HTTP, è possibile usare [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) o qualsiasi altro strumento simile. 

### <a name="download-and-configure-postman-with-your-successfactors-tenant"></a>Scaricare e configurare il post con il tenant di SuccessFactors

1. Scarica il [post](https://www.postman.com/downloads/)
1. Creare una "nuova raccolta" nell'app di pubblicazione. Chiamarlo "SuccessFactors". 

   > [!div class="mx-imgBorder"]
   > ![Nuova raccolta di post](./media/sap-successfactors-inbound-provisioning/new-postman-collection.png)

1. Nella scheda "Authorization" immettere le credenziali dell'utente dell'API configurato nella sezione precedente. Configurare il tipo come "autenticazione di base". 

   > [!div class="mx-imgBorder"]
   > ![Autorizzazione per i post](./media/sap-successfactors-inbound-provisioning/postman-authorization.png)

1. Salvare la configurazione. 

### <a name="retrieve-constant-value-for-emailtype"></a>Recupera il valore costante per emailType

1. In postazione fare clic sui puntini di sospensione (...) associati alla raccolta SuccessFactors e aggiungere una nuova richiesta denominata "Get email types", come illustrato di seguito. 

   > [!div class="mx-imgBorder"]
   > ![Richiesta di posta elettronica del post ](./media/sap-successfactors-inbound-provisioning/postman-email-request.png)

1. Aprire il pannello della richiesta "Get email type". 
1. Nell'URL GET aggiungere l'URL seguente, sostituendo `successFactorsAPITenantName` con il tenant API per l'istanza di SuccessFactors. 
   `https://<successfactorsAPITenantName>/odata/v2/Picklist('ecEmailType')?$expand=picklistOptions&$select=picklistOptions/id,picklistOptions/externalCode&$format=json`

   > [!div class="mx-imgBorder"]
   > ![Tipo di messaggio di posta elettronica per l'invio](./media/sap-successfactors-inbound-provisioning/postman-get-email-type.png)

1. La scheda "autorizzazione" erediterà l'autenticazione configurata per la raccolta. 
1. Fare clic su "Send" (Invia) per richiamare la chiamata API. 
1. Nel corpo della risposta, visualizzare il set di risultati JSON e cercare l'ID corrispondente a `externalCode = B` . 

   > [!div class="mx-imgBorder"]
   > ![Risposta al tipo di messaggio di posta elettronica](./media/sap-successfactors-inbound-provisioning/postman-email-type-response.png)

1. Prendere nota di questo valore come costante da usare con *emailType* nella tabella di mapping degli attributi.

### <a name="retrieve-constant-value-for-phonetype"></a>Recupera il valore costante per phoneType

1. In postazione fare clic sui puntini di sospensione (...) associati alla raccolta SuccessFactors e aggiungere una nuova richiesta denominata "Get phone types", come illustrato di seguito. 

   > [!div class="mx-imgBorder"]
   > ![Richiesta telefono del post](./media/sap-successfactors-inbound-provisioning/postman-phone-request.png)

1. Aprire il pannello di richiesta "Ottieni tipo di telefono". 
1. Nell'URL GET aggiungere l'URL seguente, sostituendo `successFactorsAPITenantName` con il tenant API per l'istanza di SuccessFactors. 
   `https://<successfactorsAPITenantName>/odata/v2/Picklist('ecPhoneType')?$expand=picklistOptions&$select=picklistOptions/id,picklistOptions/externalCode&$format=json`

   > [!div class="mx-imgBorder"]
   > ![Tipo di telefono Get di post](./media/sap-successfactors-inbound-provisioning/postman-get-phone-type.png)

1. La scheda "autorizzazione" erediterà l'autenticazione configurata per la raccolta. 
1. Fare clic su "Send" (Invia) per richiamare la chiamata API. 
1. Nel corpo della risposta, visualizzare il set di risultati JSON e cercare l' *ID* corrispondente a `externalCode = B` e `externalCode = C` . 

   > [!div class="mx-imgBorder"]
   > ![Postazione-telefono](./media/sap-successfactors-inbound-provisioning/postman-phone-type-response.png)

1. Prendere nota di questi valori come costanti da usare con *businessPhoneType* e *cellPhoneType* nella tabella di mapping degli attributi.

## <a name="configuring-successfactors-writeback-app"></a>Configurazione dell'app writeback SuccessFactors

In questa sezione vengono illustrati i passaggi per 

* [Aggiungere l'app del connettore di provisioning e configurare la connettività a SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configurare i mapping degli attributi](#part-2-configure-attribute-mappings)
* [Abilitare e avviare il provisioning utenti](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: aggiungere l'app del connettore di provisioning e configurare la connettività a SuccessFactors

**Per configurare il writeback SuccessFactors:**

1. Passare a <https://portal.azure.com>.

2. Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3. Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4. Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5. Cercare il **writeback SuccessFactors**e aggiungere tale app dalla raccolta.

6. Dopo aver aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **provisioning**

7. Impostare la **modalità** di **provisioning** su **automatico**

8. Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore** : immettere il nome utente dell'account utente dell'API SuccessFactors, con l'ID società accodato. Il formato è: **username \@ companyID**

   * **Password amministratore:** Immettere la password dell'account utente dell'API SuccessFactors. 

   * **URL tenant:** Immettere il nome dell'endpoint di servizi API OData di SuccessFactors. Immettere solo il nome host del server senza http o HTTPS. Questo valore dovrebbe essere simile a: `api4.successfactors.com` .

   * **Messaggio di posta elettronica di notifica:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo per inviare una notifica di posta elettronica in caso di errore.
    > [!NOTE]
    > Il servizio di provisioning di Azure AD invia una notifica di posta elettronica se il processo di provisioning entra in uno stato di [quarantena](/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

   * Fare clic sul pulsante **Test connessione**. Se il test della connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. In caso di errore, verificare che le credenziali e l'URL di SuccessFactors siano validi.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Una volta salvate correttamente le credenziali, nella sezione **mapping** verrà visualizzato il mapping predefinito. Aggiornare la pagina, se i mapping degli attributi non sono visibili.  

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurare i mapping degli attributi

In questa sezione verrà configurato il flusso dei dati utente da SuccessFactors a Active Directory.

1. Nella scheda provisioning in **mapping**fare clic su **provision Azure Active Directory Users**.

1. Nel campo **ambito dell'oggetto di origine** è possibile selezionare i set di utenti in Azure ad da considerare per il writeback, definendo un set di filtri basati su attributi. L'ambito predefinito è "tutti gli utenti in Azure AD". 
   > [!TIP]
   > Quando si configura l'app di provisioning per la prima volta, è necessario testare e verificare i mapping degli attributi e le espressioni per assicurarsi che restituisca il risultato desiderato. Microsoft consiglia di utilizzare i filtri di ambito nell' **ambito dell'oggetto di origine** per testare i mapping con pochi utenti di test da Azure ad. Dopo avere verificato che i mapping funzionino è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

1. Il campo **azioni oggetto di destinazione** supporta solo l'operazione di **aggiornamento** .

1. Nella sezione **mapping** degli attributi della tabella di mapping è possibile eseguire il mapping degli attributi Azure Active Directory seguenti a SuccessFactors. La tabella seguente fornisce indicazioni su come eseguire il mapping degli attributi write-back. 

   | \# | Attributo di Azure AD | Attributo SuccessFactors | Commenti |
   |--|--|--|--|
   | 1 | employeeId | personIdExternal | Per impostazione predefinita, questo attributo è l'identificatore corrispondente. Anziché employeeId, è possibile usare qualsiasi altro Azure AD attributo che può archiviare il valore uguale a personIdExternal in SuccessFactors.    |
   | 2 | mail | email | Origine dell'attributo di posta elettronica mappa. A scopo di test, è possibile eseguire il mapping di userPrincipalName alla posta elettronica. |
   | 3 | 8448 | emailType | Questo valore costante è il valore ID SuccessFactors associato alla posta elettronica aziendale. Aggiornare questo valore in modo che corrisponda all'ambiente SuccessFactors. Per la procedura per impostare questo valore, vedere la sezione [recuperare il valore costante per emailType](#retrieve-constant-value-for-emailtype) . |
   | 4 | true | emailIsPrimary | Usare questo attributo per impostare la posta elettronica aziendale come primaria in SuccessFactors. Se la posta elettronica aziendale non è primaria, impostare questo flag su false. |
   | 5 | userPrincipalName | [custom01 – custom15] | Utilizzando **Aggiungi nuovo mapping**, è possibile scrivere facoltativamente userPrincipalName o qualsiasi attributo Azure ad in un attributo personalizzato disponibile nell'oggetto utente SuccessFactors.  |
   | 6 | samAccountName locale | nomeutente | Utilizzando **Aggiungi nuovo mapping**, è possibile eseguire facoltativamente il mapping di sAMAccountName locale all'attributo nome utente SuccessFactors. |
   | 7 | SSO | loginMethod | Se il tenant di SuccessFactors è configurato per l'accesso [SSO parziale](https://apps.support.sap.com/sap/support/knowledge/en/2320766), quindi si usa Aggiungi nuovo mapping, è possibile impostare facoltativamente loginMethod su un valore costante di "SSO" o "pwd". |
   | 8 | telephoneNumber | businessPhoneNumber | Usare questo mapping per eseguire il flusso di *telephoneNumber* da Azure ad a SuccessFactors numero di telefono aziendale/lavoro. |
   | 9 | 10605 | businessPhoneType | Questo valore costante è il valore ID SuccessFactors associato al telefono aziendale. Aggiornare questo valore in modo che corrisponda all'ambiente SuccessFactors. Per la procedura per impostare questo valore, vedere la sezione [recuperare il valore costante per PhoneType](#retrieve-constant-value-for-phonetype) . |
   | 10 | true | businessPhoneIsPrimary | Utilizzare questo attributo per impostare il flag primario per il numero di telefono dell'ufficio. I valori validi sono true o false. |
   | 11 | mobile | cellPhoneNumber | Usare questo mapping per eseguire il flusso di *telephoneNumber* da Azure ad a SuccessFactors numero di telefono aziendale/lavoro. |
   | 12 | 10606 | cellPhoneType | Questo valore costante è il valore ID SuccessFactors associato al telefono cellulare. Aggiornare questo valore in modo che corrisponda all'ambiente SuccessFactors. Per la procedura per impostare questo valore, vedere la sezione [recuperare il valore costante per PhoneType](#retrieve-constant-value-for-phonetype) . |
   | 13 | false | cellPhoneIsPrimary | Utilizzare questo attributo per impostare il flag primario per il numero di telefono cellulare. I valori validi sono true o false. |
 
1. Convalidare ed esaminare i mapping degli attributi. 
 
    >[!div class="mx-imgBorder"]
    >![Mapping degli attributi writeback](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. Fare clic su **Salva** per salvare i mapping. A questo punto, si aggiorneranno le espressioni API del percorso JSON per usare i codici phoneType nell'istanza di SuccessFactors. 
1. Selezionare **Mostra le opzioni avanzate**. 

    >[!div class="mx-imgBorder"]
    >![Mostra opzioni avanzate](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. Fare clic su **modifica elenco attributi per SuccessFactors**. 

   > [!NOTE] 
   > Se l'opzione **modifica elenco attributi per SuccessFactors** non viene visualizzata nella portale di Azure, usare l'URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* per accedere alla pagina. 

1. La colonna **espressione API** in questa vista consente di visualizzare le espressioni di percorso JSON usate dal connettore. 
1. Aggiornare le espressioni di percorso JSON per il telefono aziendale e il telefono cellulare per usare il valore ID (*businessPhoneType* e *cellPhoneType*) corrispondente all'ambiente. 

    >[!div class="mx-imgBorder"]
    >![Modifica percorso JSON telefono](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. Fare clic su **Salva** per salvare i mapping.

## <a name="enable-and-launch-user-provisioning"></a>Abilitare e avviare il provisioning utenti

Una volta completate le configurazioni dell'app di provisioning di SuccessFactors, è possibile attivare il servizio di provisioning nel portale di Azure.

> [!TIP]
> Per impostazione predefinita quando si attiva il servizio di provisioning, verranno avviate le operazioni di provisioning per tutti gli utenti nell'ambito. Se si verificano errori nel mapping o nei problemi di dati, il processo di provisioning potrebbe non riuscire e passare allo stato di quarantena. Come procedura consigliata per evitare questo problema è consigliabile configurare il filtro **Source Object Scope** (Ambito dell'oggetto di origine) e il test di mapping degli attributi con alcuni utenti test prima di avviare la sincronizzazione completa per tutti gli utenti. Dopo avere verificato che i mapping funzionino e che restituiscano i risultati desiderati è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

1. Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

2. Fare clic su **Salva**.

3. Questa operazione avvierà la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti presenti nel tenant di SuccessFactors. È possibile controllare l'indicatore di stato per tenere traccia dello stato di avanzamento del ciclo di sincronizzazione. 

4. In qualsiasi momento è possibile controllare la scheda **Log di controllo** nel portale di Azure per vedere le azioni che sono state eseguite dal servizio di provisioning. I log di controllo elencano tutti gli eventi di sincronizzazione singoli eseguiti dal servizio di provisioning, ad esempio quali utenti vengono letti da Employee Central e successivamente aggiunti o aggiornati a Active Directory. 

5. Al termine della sincronizzazione iniziale, verrà scritto un report di riepilogo di controllo nella scheda **Provisioning**, come illustrato di seguito.

   > [!div class="mx-imgBorder"]
   > ![Indicatore di stato del provisioning](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>Scenari supportati, problemi noti e limitazioni

Vedere la [sezione scenari di writeback](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) della Guida di riferimento per l'integrazione di SAP SuccessFactors. 

## <a name="next-steps"></a>Passaggi successivi

* [Approfondimenti sul riferimento all'integrazione Azure AD e SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md)
* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
* [Informazioni su come configurare Single Sign-On tra SuccessFactors e Azure Active Directory](successfactors-tutorial.md)
* [Informazioni sull'integrazione di altre applicazioni SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni su come esportare e importare le configurazioni di provisioning](../app-provisioning/export-import-provisioning-configuration.md)

