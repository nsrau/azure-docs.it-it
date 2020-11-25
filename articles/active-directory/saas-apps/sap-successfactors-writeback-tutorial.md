---
title: 'Esercitazione: Configurare il writeback di SAP SuccessFactors in Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare il writeback degli attributi da Azure AD a SAP SuccessFactors
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: d39e00a80ab167936a749c73867b4343e6ed9d76
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006439"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>Esercitazione: Configurare il writeback degli attributi da Azure AD a SAP SuccessFactors
L'obiettivo di questa esercitazione è illustrare la procedura di writeback di attributi da Azure AD a SAP SuccessFactors Employee Central. 

## <a name="overview"></a>Panoramica

È possibile configurare l'app Writeback di SAP SuccessFactors per scrivere attributi specifici da Azure Active Directory a SAP SuccessFactors Employee Central. L'app di provisioning Writeback di SuccessFactors supporta l'assegnazione di valori agli attributi di Employee Central seguenti:

* Indirizzo di posta elettronica aziendale
* Username
* Numero di telefono aziendale (comprensivo di codice paese, prefisso, numero e interno)
* Flag principale numero di telefono aziendale
* Numero di telefono cellulare (comprensivo di codice paese, prefisso, numero)
* Flag principale telefono cellulare 
* Attributi utente custom01-custom15
* Attributo loginMethod

> [!NOTE]
> Questa app non ha alcuna dipendenza dalle app di integrazione del provisioning utenti in ingresso di SuccessFactors. È possibile configurarla in modo indipendente dall'app di provisioning da [SuccessFactors ad AD locale](sap-successfactors-inbound-provisioning-tutorial.md) o dall'app di provisioning da [SuccessFactors ad Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Per chi è più adatta questa soluzione di provisioning utenti?

Questa soluzione di provisioning utenti Writeback di SuccessFactors è idealmente la più appropriata per:

* Le organizzazioni che usano Microsoft 365 e vogliono eseguire il writeback degli attributi autorevoli gestiti dall'IT (ad esempio indirizzo di posta elettronica, telefono, nome utente) in SuccessFactors Employee Central.

## <a name="configuring-successfactors-for-the-integration"></a>Configurazione di SuccessFactors per l'integrazione

Tutti i connettori per il provisioning di SuccessFactors richiedono le credenziali di un account SuccessFactors con le autorizzazioni appropriate per richiamare le API OData di Employee Central. Questa sezione descrive la procedura da eseguire per creare l'account del servizio in SuccessFactors e concedere le autorizzazioni appropriate. 

* [Creare/identificare l'account utente dell'API in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Creare un ruolo delle autorizzazioni API](#create-an-api-permissions-role)
* [Creare un gruppo di autorizzazioni per l'utente dell'API](#create-a-permission-group-for-the-api-user)
* [Concedere il ruolo delle autorizzazioni al gruppo di autorizzazioni](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Creare/identificare l'account utente dell'API in SuccessFactors
Collaborare con il team di amministrazione di SuccessFactors o con il partner responsabile dell'implementazione per creare o identificare un account utente in SuccessFactors che verrà usato per richiamare le API OData. Le credenziali di nome utente e password di questo account saranno necessarie per configurare le app di provisioning in Azure AD. 

### <a name="create-an-api-permissions-role"></a>Creare un ruolo delle autorizzazioni API

1. Accedere a SAP SuccessFactors con un account utente che ha accesso all'Interfaccia di amministrazione.
1. Cercare *Manage Permission Roles* (Gestisci ruoli autorizzazione), quindi selezionare **Manage Permission Roles** dai risultati della ricerca.

   ![Manage Permission Roles](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. In Permission Role List (Elenco ruoli autorizzazioni) fare clic su **Create New** (Crea nuovo).

   > [!div class="mx-imgBorder"]
   > ![Creazione di un nuovo ruolo di autorizzazioni](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. In **Role Name** e **Description** aggiungere rispettivamente un nome e una descrizione per il nuovo ruolo di autorizzazioni. Il nome e la descrizione devono indicare che il ruolo riguarda le autorizzazioni di utilizzo dell'API.

   > [!div class="mx-imgBorder"]
   > ![Dettagli del ruolo delle autorizzazioni](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. In Permission settings (Impostazioni autorizzazione) fare clic su **Permission** (Autorizzazione), quindi scorrere l'elenco delle autorizzazioni verso il basso e fare clic su **Manage Integration Tools** (Gestione strumenti di integrazione). Selezionare la casella **Allow Admin to Access to OData API through Basic Authentication** (Consenti all'amministratore di accedere all'API OData tramite l'autenticazione di base).

   > [!div class="mx-imgBorder"]
   > ![Gestione strumenti di integrazione](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. Scorrere verso il basso nello stesso riquadro e selezionare **Employee Central API** (API Employee Central). Aggiungere le autorizzazioni di lettura e modifica dell'API ODATA, come illustrato di seguito. Selezionare l'opzione di modifica se si prevede di usare lo stesso account per lo scenario di writeback in SuccessFactors. 

   > [!div class="mx-imgBorder"]
   > ![Autorizzazioni di lettura/scrittura](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Fare clic su **Done** (Fine). Fare clic su **Salva modifiche**.

### <a name="create-a-permission-group-for-the-api-user"></a>Creare un gruppo di autorizzazioni per l'utente dell'API

1. Nell'interfaccia di amministrazione di SuccessFactors cercare *Manage Permission Groups* (Gestisci gruppi di autorizzazioni), quindi selezionare **Manage Permission Groups** dai risultati della ricerca.

   > [!div class="mx-imgBorder"]
   > ![Manage Permission Groups](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. Nella finestra Manage Permission Groups fare clic su **Create New** (Crea nuovo).

   > [!div class="mx-imgBorder"]
   > ![Aggiungere un nuovo gruppo](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. Aggiungere un nome per il nuovo gruppo. Il nome del gruppo deve indicare che il gruppo è riservato agli utenti dell'API.

   > [!div class="mx-imgBorder"]
   > ![Nome del gruppo di autorizzazioni](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. Aggiungere membri al gruppo. Ad esempio, si potrebbe selezionare **Username** (Nome utente) dal menu a discesa People Pool (Pool utenti) e quindi immettere il nome utente dell'account API che verrà usato per l'integrazione. 

   > [!div class="mx-imgBorder"]
   > ![Aggiungere membri del gruppo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)

1. Fare clic su **Done** (Fine) per completare la creazione del gruppo di autorizzazioni.

### <a name="grant-permission-role-to-the-permission-group"></a>Concedere il ruolo delle autorizzazioni al gruppo di autorizzazioni

1. Nell'interfaccia di amministrazione di SuccessFactors cercare *Manage Permission Roles* (Gestisci ruoli autorizzazioni), quindi selezionare **Manage Permission Roles** dai risultati della ricerca.
1. In **Permission Role List** (Elenco ruoli autorizzazioni) selezionare il ruolo creato per le autorizzazioni di utilizzo dell'API.
1. In **Grant this role to** (Concedi ruolo a) fare clic sul pulsante **Add** (Aggiungi).
1. Selezionare **Permission Group** (Gruppo di autorizzazioni) dal menu a discesa, quindi fare clic su **Select** (Seleziona) per aprire la finestra Groups (Gruppi) per cercare e selezionare il gruppo creato in precedenza. 

   > [!div class="mx-imgBorder"]
   > ![Aggiunta del gruppo di autorizzazioni](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. Verificare la concessione del ruolo delle autorizzazioni al gruppo di autorizzazioni. 
   > [!div class="mx-imgBorder"]
   > ![Dettagli sul ruolo e il gruppo di autorizzazioni](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. Fare clic su **Salva modifiche**.

## <a name="preparing-for-successfactors-writeback"></a>Preparazione dell'app Writeback di SuccessFactors

L'app di provisioning Writeback di SuccessFactors usa determinati valori di *codice* per impostare gli indirizzi di posta elettronica e i numeri di telefono in Employee Central. Questi valori di *codice* sono impostati come valori costanti nella tabella di mapping degli attributi e sono diversi per ogni istanza di SuccessFactors. Questa sezione illustra la procedura per acquisire questi valori di *codice*.

   > [!NOTE]
   > Coinvolgere l'amministratore di SuccessFactors per completare la procedura. 

### <a name="identify-email-and-phone-number-picklist-names"></a>Identificare i nomi degli elenchi a discesa di indirizzi di posta elettronica e numeri di telefono 

In SAP SuccessFactors un *elenco a discesa* è un set di opzioni configurabile da cui un utente può effettuare una selezione. I diversi tipi di indirizzo di posta elettronica e numero di telefono (ad esempio aziendale, personale, altro) sono rappresentati mediante un elenco a discesa. In questo passaggio si identificheranno gli elenchi a discesa configurati nel tenant di SuccessFactors per archiviare i valori di indirizzi di posta elettronica e numeri di telefono. 
 
1. Nell'interfaccia di amministrazione di SuccessFactors cercare *Manage Business Configuration* (Gestione configurazione aziendale). 

   > [!div class="mx-imgBorder"]
   > ![Manage Business Configuration](./media/sap-successfactors-inbound-provisioning/manage-business-config.png)

1. In **HRIS Elements** (Elementi HRIS) selezionare **emailInfo** e fare clic su *Details* (Dettagli) per il campo **email-type**.

   > [!div class="mx-imgBorder"]
   > ![Ottenere le informazioni sugli indirizzi di posta elettronica](./media/sap-successfactors-inbound-provisioning/get-email-info.png)

1. Nella pagina dei dettagli di **email-type** prendere nota del nome dell'elenco a discesa associato a questo campo. Per impostazione predefinita, il nome è **ecEmailType**. Nel proprio tenant potrebbe tuttavia essere diverso. 

   > [!div class="mx-imgBorder"]
   > ![Identificare l'elenco a discesa degli indirizzi di posta elettronica](./media/sap-successfactors-inbound-provisioning/identify-email-picklist.png)

1. In **HRIS Elements** (Elementi HRIS) selezionare **phoneInfo** e fare clic su *Details* (Dettagli) per il campo **phone-type**.

   > [!div class="mx-imgBorder"]
   > ![Ottenere le informazioni sui numeri di telefono](./media/sap-successfactors-inbound-provisioning/get-phone-info.png)

1. Nella pagina dei dettagli di **phone-type** prendere nota del nome dell'elenco a discesa associato a questo campo. Per impostazione predefinita, il nome è **ecPhoneType**. Nel proprio tenant potrebbe tuttavia essere diverso. 

   > [!div class="mx-imgBorder"]
   > ![Identificare l'elenco a discesa dei numeri di telefono](./media/sap-successfactors-inbound-provisioning/identify-phone-picklist.png)

### <a name="retrieve-constant-value-for-emailtype"></a>Recuperare il valore costante di emailType

1. Nell'interfaccia di amministrazione di SuccessFactors cercare e aprire *Picklist Center* (Centro elenchi a discesa). 
1. Per trovare l'elenco a discesa degli indirizzi di posta elettronica, usare il nome acquisito nella sezione precedente, ovvero ecEmailType. 

   > [!div class="mx-imgBorder"]
   > ![Trovare l'elenco a discesa degli indirizzi di posta elettronica](./media/sap-successfactors-inbound-provisioning/find-email-type-picklist.png)

1. Aprire l'elenco a discesa del tipo di indirizzo di posta elettronica Active (Attivo). 

   > [!div class="mx-imgBorder"]
   > ![Aprire l'elenco a discesa del tipo di indirizzo di posta elettronica Active](./media/sap-successfactors-inbound-provisioning/open-active-email-type-picklist.png)

1. Nella pagina dell'elenco a discesa ecEmailType selezionare il tipo di indirizzo di posta elettronica *Business* (aziendale).

   > [!div class="mx-imgBorder"]
   > ![Selezionare il tipo di indirizzo di posta elettronica Business](./media/sap-successfactors-inbound-provisioning/select-business-email-type.png)

1. Prendere nota del valore di **Option ID** (ID opzione) associato all'indirizzo di posta elettronica *Business*. È il codice che verrà usato con *emailType* nella tabella di mapping degli attributi.

   > [!div class="mx-imgBorder"]
   > ![Ottenere il codice del tipo di indirizzo di posta elettronica](./media/sap-successfactors-inbound-provisioning/get-email-type-code.png)

   > [!NOTE]
   > Quando si copia il valore, eliminare la virgola. Ad esempio, se il valore di **Option ID** è *8,448*, impostare *emailType* in Azure AD sul numero costante *8448* (senza virgola). 

### <a name="retrieve-constant-value-for-phonetype"></a>Recuperare il valore costante di phoneType

1. Nell'interfaccia di amministrazione di SuccessFactors cercare e aprire *Picklist Center* (Centro elenchi a discesa). 
1. Per trovare l'elenco a discesa dei numeri di telefono, usare il nome acquisito nella sezione precedente. 

   > [!div class="mx-imgBorder"]
   > ![Trovare l'elenco a discesa dei numeri di telefono](./media/sap-successfactors-inbound-provisioning/find-phone-type-picklist.png)

1. Aprire l'elenco a discesa del tipo di numero di telefono Active (Attivo). 

   > [!div class="mx-imgBorder"]
   > ![Aprire l'elenco a discesa del tipo di numero di telefono Active](./media/sap-successfactors-inbound-provisioning/open-active-phone-type-picklist.png)

1. Nella pagina dell'elenco a discesa ecPhoneType esaminare i diversi tipi di numeri di telefono elencati in **Picklist Values** (Valori elenco a discesa).

   > [!div class="mx-imgBorder"]
   > ![Esaminare i tipi di numeri di telefono](./media/sap-successfactors-inbound-provisioning/review-phone-types.png)

1. Prendere nota del valore di **Option ID** (ID opzione) associato al numero di telefono *Business*. È il codice che verrà usato con *businessPhoneType* nella tabella di mapping degli attributi.

   > [!div class="mx-imgBorder"]
   > ![Ottenere il codice dei numeri di telefono Business](./media/sap-successfactors-inbound-provisioning/get-business-phone-code.png)

1. Prendere nota del valore di **Option ID** (ID opzione) associato al numero di telefono *Cell* (Cellulare). È il codice che verrà usato con *cellPhoneType* nella tabella di mapping degli attributi.

   > [!div class="mx-imgBorder"]
   > ![Ottenere il codice dei numeri di telefono Cell](./media/sap-successfactors-inbound-provisioning/get-cell-phone-code.png)

   > [!NOTE]
   > Quando si copia il valore, eliminare la virgola. Ad esempio, se il valore di **Option ID** è *10,606*, impostare *cellPhoneType* in Azure AD sul numero costante *10606* (senza virgola). 


## <a name="configuring-successfactors-writeback-app"></a>Configurazione dell'app Writeback di SuccessFactors

Questa sezione illustra la procedura per 

* [Aggiungere l'app connettore di provisioning e configurare la connettività a SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configurare i mapping degli attributi](#part-2-configure-attribute-mappings)
* [Abilitare e avviare il provisioning utenti](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Aggiungere l'app connettore di provisioning e configurare la connettività a SuccessFactors

**Per configurare l'app Writeback di SuccessFactors:**

1. Passare a <https://portal.azure.com>

2. Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3. Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4. Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5. Cercare **SuccessFactors Writeback** (Writeback di SuccessFactors) e aggiungere tale applicazione dalla raccolta.

6. Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**

7. Impostare **Modalità** **di provisioning** su **Automatico**

8. Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore**: immettere il nome utente dell'account utente dell'API SuccessFactors, seguito dall'ID società. Il formato è **nomeutente\@IDsocietà**

   * **Password amministratore**: immettere la password dell'account utente dell'API SuccessFactors. 

   * **URL tenant**: immettere il nome dell'endpoint di servizio dell'API OData di SuccessFactors. Immettere solo il nome host del server senza http o https. Il valore dovrebbe essere simile a `api4.successfactors.com`.

   * **Messaggio di posta elettronica di notifica:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo per inviare una notifica di posta elettronica in caso di errore.
    > [!NOTE]
    > Il servizio di provisioning di Azure AD invia una notifica di posta elettronica se il processo di provisioning entra in uno stato di [quarantena](../app-provisioning/application-provisioning-quarantine-status.md).

   * Fare clic sul pulsante **Test connessione**. Se il test della connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. Se il test non riesce, verificare che le credenziali e l'URL di SuccessFactors siano validi.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Dopo il salvataggio delle credenziali, la sezione **Mapping** mostrerà il mapping predefinito. Se i mapping degli attributi non sono visibili, aggiornare la pagina.  

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurare i mapping degli attributi

In questa sezione verrà configurato il flusso dei dati utente da SuccessFactors ad Active Directory.

1. Nella scheda Provisioning, in **Mapping**, fare clic su **Provision Azure Active Directory Users** (Effettua il provisioning degli utenti di Azure Active Directory).

1. Nel campo **Ambito dell'oggetto di origine** è possibile selezionare i gruppi di utenti in Azure AD da considerare per il writeback, definendo un set di filtri basati su attributi. L'ambito predefinito è "tutti gli utenti in Azure AD". 
   > [!TIP]
   > Quando si configura l'app di provisioning per la prima volta, è necessario testare e verificare i mapping degli attributi e le espressioni per assicurarsi che restituisca il risultato desiderato. Microsoft consiglia di usare i filtri di ambito in **Ambito dell'oggetto di origine** per testare il mapping con alcuni utenti test da Azure AD. Dopo avere verificato che i mapping funzionino è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

1. Il campo **Azioni oggetto di destinazione** supporta solo l'operazione di **aggiornamento**.

1. Nella tabella di mapping inclusa nella sezione **Mapping attributi** è possibile eseguire il mapping degli attributi di Azure Active Directory seguenti a SuccessFactors. La tabella seguente fornisce indicazioni su come eseguire il mapping degli attributi di writeback. 

   | \# | Attributo di Azure AD | Attributo di SuccessFactors | Commenti |
   |--|--|--|--|
   | 1 | employeeId | personIdExternal | Per impostazione predefinita, questo attributo è l'identificatore corrispondente. Al posto di employeeId si può usare qualsiasi altro attributo di Azure AD in grado di archiviare il valore uguale a personIdExternal in SuccessFactors.    |
   | 2 | mail | email | Eseguire il mapping dell'origine dell'attributo email. A scopo di test, è possibile eseguire il mapping di userPrincipalName a email. |
   | 3 | 8448 | emailType | Questo valore costante è il valore dell'ID SuccessFactors associato al tipo di indirizzo di posta elettronica aziendale. Aggiornare il valore in modo che corrisponda all'ambiente di SuccessFactors. Per la procedura da eseguire per impostare questo valore, vedere la sezione [Recuperare il valore costante di emailType](#retrieve-constant-value-for-emailtype). |
   | 4 | true | emailIsPrimary | Usare questo attributo per impostare l'indirizzo di posta elettronica aziendale come primario in SuccessFactors. Se l'indirizzo di posta elettronica aziendale non è quello primario, impostare questo flag su false. |
   | 5 | userPrincipalName | [custom01 – custom15] | Facoltativamente, usando **Aggiungi nuovo mapping** è possibile scrivere l'attributo userPrincipalName o qualsiasi attributo di Azure AD in un attributo personalizzato disponibile nell'oggetto utente di SuccessFactors.  |
   | 6 | on-prem-samAccountName | username | Facoltativamente, usando **Aggiungi nuovo mapping** è possibile eseguire il mapping dell'attributo samAccountName locale all'attributo username di SuccessFactors. |
   | 7 | SSO | loginMethod | Se il tenant di SuccessFactors è configurato per il [Single Sign-On parziale](https://apps.support.sap.com/sap/support/knowledge/en/2320766), usando Aggiungi nuovo mapping è possibile impostare loginMethod su un valore costante "SSO" o "PWD". |
   | 8 | telephoneNumber | businessPhoneNumber | Usare questo mapping per trasmettere *telephoneNumber* da Azure AD al numero di telefono aziendale/di lavoro di SuccessFactors. |
   | 9 | 10605 | businessPhoneType | Questo valore costante è il valore dell'ID SuccessFactors associato al tipo di numero di telefono aziendale. Aggiornare il valore in modo che corrisponda all'ambiente di SuccessFactors. Per la procedura da eseguire per impostare questo valore, vedere la sezione [Recuperare il valore costante di phoneType](#retrieve-constant-value-for-phonetype). |
   | 10 | true | businessPhoneIsPrimary | Usare questo attributo per impostare il flag primario per il numero di telefono aziendale. I valori validi sono true e false. |
   | 11 | mobile | cellPhoneNumber | Usare questo mapping per trasmettere *telephoneNumber* da Azure AD al numero di telefono aziendale/di lavoro di SuccessFactors. |
   | 12 | 10606 | cellPhoneType | Questo valore costante è il valore dell'ID SuccessFactors associato al tipo di numero di telefono cellulare. Aggiornare il valore in modo che corrisponda all'ambiente di SuccessFactors. Per la procedura da eseguire per impostare questo valore, vedere la sezione [Recuperare il valore costante di phoneType](#retrieve-constant-value-for-phonetype). |
   | 13 | false | cellPhoneIsPrimary | Usare questo attributo per impostare il flag primario per il numero di telefono cellulare. I valori validi sono true e false. |
 
1. Convalidare e rivedere i mapping degli attributi. 
 
    >[!div class="mx-imgBorder"]
    >![Mapping degli attributi di writeback](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. Fare clic su **Salva** per salvare i mapping. A questo punto occorre aggiornare le espressioni API JSON Path in modo che usino i codici phoneType nell'istanza di SuccessFactors. 
1. Selezionare **Mostra le opzioni avanzate**. 

    >[!div class="mx-imgBorder"]
    >![Mostra opzioni avanzate](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. Selezionare **Edit attribute list for SuccessFactors** (Modifica elenco attributi per SuccessFactors). 

   > [!NOTE] 
   > Se l'opzione **Edit attribute list for SuccessFactors** non è visualizzata nel portale di Azure, usare l'URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* per accedere alla pagina. 

1. La colonna **Espressione API** contiene le espressioni JSON Path usate dal connettore. 
1. Aggiornare le espressioni JSON Path per il numero di telefono aziendale e il numero di telefono cellulare in modo che usino il valore ID (*businessPhoneType* e *cellPhoneType*) corrispondente all'ambiente. 

    >[!div class="mx-imgBorder"]
    >![Modifica dell'espressione JSON Path per il numero di telefono](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. Fare clic su **Salva** per salvare i mapping.

## <a name="enable-and-launch-user-provisioning"></a>Abilitare e avviare il provisioning utenti

Dopo aver completato le configurazioni dell'app di provisioning di SuccessFactors, è possibile attivare il servizio di provisioning nel portale di Azure.

> [!TIP]
> Per impostazione predefinita quando si attiva il servizio di provisioning, verranno avviate le operazioni di provisioning per tutti gli utenti nell'ambito. Se sono presenti errori di mapping o problemi di dati, il processo di provisioning potrebbe non riuscire e passare allo stato di quarantena. Come procedura consigliata per evitare questo problema è consigliabile configurare il filtro **Source Object Scope** (Ambito dell'oggetto di origine) e il test di mapping degli attributi con alcuni utenti test prima di avviare la sincronizzazione completa per tutti gli utenti. Dopo avere verificato che i mapping funzionino e che restituiscano i risultati desiderati è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

1. Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

1. Selezionare **Ambito**. È possibile selezionare una delle opzioni seguenti: 
   * **Sincronizza tutti gli utenti e i gruppi**: selezionare questa opzione se si prevede di eseguire il writeback degli attributi mappati di tutti gli utenti da Azure AD a SuccessFactors, in base alle regole di ambito definite in **Mapping** -> **Ambito dell'oggetto di origine**. 
   * **Sincronizza solo utenti e gruppi assegnati**: selezionare questa opzione se si prevede di eseguire il writeback degli attributi mappati solo degli utenti assegnati a questa applicazione nell'opzione di menu **Applicazione** -> **Gestisci** -> **Utenti e gruppi**. Questi utenti sono inoltre soggetti alle regole di ambito definite in **Mapping** -> **Ambito dell'oggetto di origine**.

   > [!div class="mx-imgBorder"]
   > ![Selezione dell'ambito del writeback](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > L'app di provisioning Writeback di SuccessFactors non supporta l'assegnazione di gruppi. È supportata solo l'assegnazione di utenti. 

1. Fare clic su **Salva**.

1. Questa operazione avvierà la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti nel tenant di Azure AD e dell'ambito definito per l'operazione. È possibile controllare l'indicatore di stato per monitorare lo stato del ciclo di sincronizzazione. 

1. In qualsiasi momento è possibile controllare la scheda **Log di provisioning** nel portale di Azure per vedere le azioni che sono state eseguite dal servizio di provisioning. In questa scheda sono elencati tutti i singoli eventi di sincronizzazione eseguiti dal servizio di provisioning. 

1. Al termine della sincronizzazione iniziale, verrà scritto un report di riepilogo di controllo nella scheda **Provisioning**, come illustrato di seguito.

   > [!div class="mx-imgBorder"]
   > ![Indicatore di stato del provisioning](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>Scenari supportati, problemi noti e limitazioni

Vedere la sezione [Scenari di writeback](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) della guida di riferimento all'integrazione di SAP SuccessFactors. 

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni di riferimento approfondite sull'integrazione fra Azure AD e SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md)
* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
* [Informazioni su come configurare il Single Sign-On tra SuccessFactors e Azure Active Directory](successfactors-tutorial.md)
* [Informazioni sull'integrazione di altre applicazioni SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni su come esportare e importare le configurazioni del provisioning](../app-provisioning/export-import-provisioning-configuration.md)