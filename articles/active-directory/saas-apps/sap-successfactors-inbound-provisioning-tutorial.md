---
title: 'Esercitazione: Configurare il provisioning in ingresso SuccessFactors in Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare il provisioning in ingresso da SuccessFactors
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: 1ff90231-1312-463e-8949-7d976e433fc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2019
ms.author: chmutali
ms.openlocfilehash: d9317a68c8967fbe0728e8c47e59dd33367c6163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249685"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>Esercitazione: Configurare SAP SuccessFactors per il provisioning degli utenti di Active Directory (anteprima)Tutorial: Configure SAP SuccessFactors to Active Directory user provisioning (Preview)
The objective of this tutorial is to show the steps you need to perform to provision users from SuccessFactors Employee Central into Active Directory (AD) and Azure AD, with optional write-back of email address to SuccessFactors. Questa integrazione è in anteprima pubblica e supporta il recupero di più di [70 attributi utente](../app-provisioning/sap-successfactors-attribute-reference.md) da SuccessFactors Employee Central.

>[!NOTE]
>Usare questa esercitazione se gli utenti di cui si vuole eseguire il provisioning da SuccessFactors richiedono un account AD locale e, facoltativamente, un account Azure AD. Se gli utenti di SuccessFactors hanno bisogno solo di un account Azure AD (utenti solo cloud), fare riferimento all'esercitazione sulla configurazione di [SAP SuccessFactors per](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) il provisioning degli utenti di Azure AD. 


## <a name="overview"></a>Panoramica

Il servizio di [provisioning degli utenti](../app-provisioning/user-provisioning.md) di Azure Active Directory si integra con [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) per gestire il ciclo di vita delle identità degli utenti. 

I flussi di lavoro di provisioning degli utenti SuccessFactors supportati dal servizio di provisioning degli utenti di Azure AD consentono l'automazione dei seguenti scenari di gestione del ciclo di vita delle risorse umane e delle identità:

* **Assunzione di nuovi dipendenti:** quando un nuovo dipendente viene aggiunto a SuccessFactors, viene creato automaticamente un account utente in Active Directory, Azure Active Directory e, facoltativamente, Office 365 e [altre applicazioni SaaS supportate da Azure AD,](../app-provisioning/user-provisioning.md)con writeback dell'indirizzo di posta elettronica in SuccessFactors.

* **Aggiornamenti** dei profili e attributi dei dipendenti: quando un record dipendente viene aggiornato in SuccessFactors (ad esempio nome, titolo o responsabile), il relativo account utente verrà aggiornato automaticamente in Active Directory, Azure Active Directory e, facoltativamente, office 365 e [altre applicazioni SaaS supportate da Azure AD.](../app-provisioning/user-provisioning.md)

* **Chiusura dei** dipendenti: quando un dipendente viene terminato in SuccessFactors, il relativo account utente viene disabilitato automaticamente in Active Directory, Azure Active Directory e, facoltativamente, office 365 e [altre applicazioni SaaS supportate da Azure AD.](../app-provisioning/user-provisioning.md)

* **Riassunzioni dei** dipendenti: quando un dipendente viene riassuntato in SuccessFactors, il vecchio account può essere riattivato o rieseguito automaticamente (a seconda delle preferenze) in Active Directory, Azure Active Directory e, facoltativamente, office 365 e [altre applicazioni SaaS supportate da Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Per chi è più adatta questa soluzione di provisioning utenti?

Questa soluzione di provisioning degli utenti successFactors per Active Directory è ideale per:

* Organizzazioni che desiderano una soluzione predefinita basata su cloud per il provisioning degli utenti successFactors

* Organizzazioni che richiedono il provisioning diretto degli utenti da SuccessFactors ad Active Directory

* Organizzazioni che richiedono il provisioning degli utenti utilizzando i dati ottenuti da [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizzazioni che richiedono l'aggiunta, lo spostamento e l'abbandono degli utenti con una o più foreste, domini e unità organizzative di Active Directory in base solo alle informazioni sulle modifiche rilevate in [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizzazioni che usano Office 365 per la posta elettronica

## <a name="solution-architecture"></a>Architettura della soluzione

Questa sezione descrive l'architettura della soluzione end-to-end di provisioning degli utenti per ambienti ibridi comuni. Esistono due flussi correlati:

* **Flusso di dati HR autorevoli, da SuccessFactors ad Active Directory locale:** In questo flow worker eventi (ad esempio nuove assunzioni, trasferimenti, terminazioni) si verificano prima nel cloud SuccessFactors Employee Central e quindi i dati degli eventi flussi in locale Active Directory tramite Azure AD e l'agente di provisioning. A seconda dell'evento, può determinare operazioni di creazione/aggiornamento/abilitazione o disabilitazione in Active Directory.
* **Flusso di writeback della posta elettronica da Active Directory locale a SuccessFactors:** Una volta completata la creazione dell'account in Active Directory, questa viene sincronizzata con Azure AD tramite la sincronizzazione di Azure AD Connect e l'attributo di posta elettronica può essere riscritto in SuccessFactors.Once the account creation is complete in Active Directory, it is synced with Azure AD through Azure AD Connect sync and email attribute can be written back back to SuccessFactors.

  ![Panoramica](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Flusso di dati end-to-end dell'utente

1. Il team HR esegue le transazioni dei lavoratori (Joiners/Movers/Leavers o New Hires/Transfers/Terminations) in SuccessFactors Employee Central
2. Il servizio di provisioning di Azure AD esegue le sincronizzazioni pianificate delle identità da EC SuccessFactors e identifica le modifiche che devono essere elaborate per la sincronizzazione con Active Directory locale.
3. Il servizio di provisioning di Azure AD richiama l'agente di provisioning di Azure AD Connect locale con un payload della richiesta contenente le operazioni di creazione/aggiornamento/abilitazione/disabilitazione dell'account AD.
4. L'agente di provisioning di Azure AD Connect usa un account del servizio per aggiungere/aggiornare i dati dell'account AD.
5. Il motore di sincronizzazione di Azure AD Connect esegue la sincronizzazione delta per eseguire il pull degli aggiornamenti in Active Directory.The Azure AD Connect Sync engine runs delta sync to pull updates in AD.
6. Gli aggiornamenti di Active Directory sono sincronizzati con Azure Active Directory.
7. Se [l'app Writeback SuccessFactors](sap-successfactors-writeback-tutorial.md) è configurata, scrive nuovamente l'attributo email in SuccessFactors, in base all'attributo corrispondente utilizzato.

## <a name="planning-your-deployment"></a>Pianificazione della distribuzione

La configurazione del provisioning degli utenti basato su Cloud HR da SuccessFactors ad Active Directory richiede una pianificazione considerevole che copre diversi aspetti, ad esempio:Configuring Cloud HR driven user provisioning from SuccessFactors to AD requires considerable planning covering different aspects such as:
* Installazione dell'agente di provisioning di Azure AD Connect 
* Numero di SuccessFactor per le app di provisioning degli utenti di Active Directory da distribuire
* ID corrispondenza, mapping degli attributi, trasformazione e filtri di ambito

Fare riferimento al piano di [distribuzione delle risorse umane cloud](../app-provisioning/plan-cloud-hr-provision.md) per linee guida complete su questi argomenti. 

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

  >[!NOTE]
  >Per l'elenco completo degli attributi recuperati da questa app di provisioning, fare riferimento a [Riferimento all'attributo SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)

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

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Configurazione del provisioning degli utenti da SuccessFactors ad Active Directory

In questa sezione vengono illustrate le operazioni necessarie per il provisioning degli account utente da SuccessFactors a ogni dominio di Active Directory nell'ambito dell'integrazione.

* [Aggiungere l'app del connettore di provisioning e scaricare l'agente di provisioningAdd the provisioning connector app and download the Provisioning Agent](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Installare e configurare gli agenti di provisioning locali](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Configurare la connettività a SuccessFactors e Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Configurare i mapping degli attributi](#part-4-configure-attribute-mappings)
* [Abilitare e avviare il provisioning utenti](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Parte 1: Aggiungere l'app del connettore di provisioning e scaricare l'agente di provisioningPart 1: Add the provisioning connector app and download the Provisioning Agent

**Per configurare SuccessFactors per il provisioning di Active Directory:**

1. Passare a <https://portal.azure.com>.

2. Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3. Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4. Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5. Cercare **SuccessFactors in Provisioning degli**utenti di Active Directory e aggiungere l'app dalla raccolta.

6. Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**

7. Impostare **Modalità di** **provisioning** su **Automatico**

8. Fare clic sul banner informativo visualizzato per scaricare l'agente di provisioning. 
   > [!div class="mx-imgBorder"]
   > ![Scarica agente](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "Schermata Scarica agente")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Parte 2: Installare e configurare gli agenti di provisioning localiPart 2: Install and configure on-premises Provisioning Agent(s)

Per eseguire il provisioning in Active Directory in locale, l'agente di provisioning deve essere installato in un server con .NET 4.7.1 Framework e accesso di rete ai domini di Active Directory desiderati.

> [!TIP]
> È possibile controllare la versione di .NET Framework nel server seguendo le istruzioni disponibili [qui](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Se il server non dispone di .NET 4.7.1 o versioni successive, è possibile scaricarlo da [qui](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Trasferire il programma di installazione dell'agente scaricato nell'host del server e seguire i passaggi indicati di seguito per completare la configurazione dell'agente.

1. Accedere a Windows Server in cui si desidera installare il nuovo agente.

1. Avviare il programma di installazione di Provisioning Agent, accettare i termini e fare clic sul pulsante **Installa.**

   ![Schermata di installazione](./media/workday-inbound-tutorial/pa_install_screen_1.png "Schermata di installazione")
   
1. Al termine dell'installazione verrà avviata la procedura guidata e verrà visualizzata la schermata **Connect Azure AD** (Connetti Azure AD). Fare clic sul pulsante **Authenticate** (Autentica) per connettersi all'istanza di Azure AD.

   ![Connettere Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Connettere Azure AD")
   
1. Eseguire l'autenticazione all'istanza di Azure AD con credenziali di amministratore globale.

   ![Autenticazione amministratore](./media/workday-inbound-tutorial/pa_install_screen_3.png "Autenticazione amministratore")

   > [!NOTE]
   > Le credenziali di amministratore di Azure AD vengono usate solo per la connessione al tenant di Azure AD. L'agente non archivia le credenziali in locale nel server.

1. Al termine dell'autenticazione con Azure AD verrà visualizzata la schermata **Connect Active Directory** (Connetti Active Directory). In questo passaggio, inserire il nome di dominio AD e fare clic sul pulsante **Add Directory** (Aggiungi directory).

   ![Aggiungi directory](./media/workday-inbound-tutorial/pa_install_screen_4.png "Aggiungi directory")
  
1. Verrà ora richiesto di immettere le credenziali necessarie per connettersi al dominio AD. Nella stessa schermata è possibile usare **Select domain controller priority** (Seleziona priorità controller di dominio) per specificare i controller di dominio che l'agente deve usare per l'invio di richieste di provisioning.

   ![Credenziali del dominio](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Dopo aver configurato il dominio, il programma di installazione mostrerà un elenco di domini configurati. In questa schermata è possibile ripetere i passaggi 5 e 6 per aggiungere più domini o fare clic su **Next** (Avanti) per procedere alla registrazione dell'agente.

   ![Domini configurati](./media/workday-inbound-tutorial/pa_install_screen_6.png "Domini configurati")

   > [!NOTE]
   > In presenza di più domini di Active Directory (ad esempio na.contoso.com, emea.contoso.com), aggiungere singolarmente ogni dominio all'elenco.
   > Non è sufficiente aggiungere soltanto il dominio padre (ad esempio, contoso.com). È necessario registrare ogni dominio figlio con l'agente.
   
1. Esaminare i dettagli di configurazione e fare clic su **Confirm** (Conferma) per registrare l'agente.
  
   ![Schermata di conferma](./media/workday-inbound-tutorial/pa_install_screen_7.png "Schermata di conferma")
   
1. La Configurazione guidata mostra lo stato di avanzamento della registrazione dell'agente.
  
   ![Registrazione dell'agente](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registrazione dell'agente")
   
1. Al termine della registrazione dell'agente sarà possibile fare clic su **Exit** (Esci) per uscire dalla procedura guidata.
  
   ![Esci dalla schermata](./media/workday-inbound-tutorial/pa_install_screen_9.png "Esci dalla schermata")
   
1. Verificare l'installazione dell'agente e accertarsi che sia in esecuzione aprendo lo snap-in "Services" e cercare il servizio denominato "Microsoft Azure AD Connect Provisioning Agent" (Agente di provisioning Microsoft Azure Active Directory Connect)
  
   ![Servizi](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Parte 3: nell'app di provisioning, configurare la connettività a SuccessFactors e Active DirectoryPart 3: In the provisioning app, configure connectivity to SuccessFactors and Active Directory
In questo passaggio viene stabilita la connettività con SuccessFactors e Active Directory nel portale di Azure.In this step, we establish connectivity with SuccessFactors and Active Directory in the Azure portal. 

1. Nel portale di Azure tornare all'app SuccessFactors per il provisioning degli utenti di Active Directory creata nella [parte 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore:** immettere il nome utente dell'account utente dell'API SuccessFactors, con l'ID società aggiunto. Ha il formato: **\@username companyID**

   * **Password amministratore –** Immettere la password dell'account utente dell'API SuccessFactors. 

   * **URL tenant –** Immettere il nome dell'endpoint dei servizi API OData SuccessFactors. Immettere solo il nome host del server senza http o https. Questo valore dovrebbe essere simile al seguente: **<nome-server api>.successfactors.com**.

   * **Foresta di Active Directory:** il "nome" del dominio di Active Directory, così come registrato con l'agente. Usare l'elenco a discesa per selezionare il dominio di destinazione per il provisioning. In genere, il valore corrisponde a una stringa simile a: *contoso.com*

   * **Contenitore di Active Directory:** immettere il nome distinto del contenitore in cui l'agente deve creare gli account utente per impostazione predefinita.
        Esempio: *unità organizzativa, utenti, DC , contoso, DC , com*
        > [!NOTE]
        > Questa impostazione deve essere usata per la creazione degli account utente solo se l'attributo *parentDistinguishedName* non è configurato nel mapping degli attributi. Questa impostazione non viene usata per la ricerca di utenti o per le operazioni di aggiornamento. L'intero sottoalbero del dominio rientra nell'ambito dell'operazione di ricerca.

   * **E-mail di notifica –** Inserisci il tuo indirizzo email e seleziona la casella di controllo "Invia e-mail in caso di errore".
    > [!NOTE]
    > Il servizio di provisioning di Azure AD invia una notifica di posta elettronica se il processo di provisioning entra in uno stato di [quarantena](/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

   * Fare clic sul pulsante **Test connessione**. Se il test di connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. Se si verifica un errore, verificare che le credenziali SuccessFactors e le credenziali di Active Directory configurate per l'installazione dell'agente siano valide.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Una volta salvate correttamente le credenziali, nella sezione **Mapping** verrà visualizzato il mapping predefinito **Sincronizza utenti SuccessFactors in Active Directory** locale

### <a name="part-4-configure-attribute-mappings"></a>Parte 4: Configurare i mapping degli attributiPart 4: Configure attribute mappings

In questa sezione verrà configurato il modo in cui i dati utente passano da SuccessFactors ad Active Directory.

1. Nella scheda Provisioning in **Mapping**fare clic su **Sincronizza utenti SuccessFactors con Active Directory locale.**

1. Nel campo **Ambito oggetto** di origine è possibile selezionare i set di utenti in SuccessFactors che devono essere nell'ambito per il provisioning in Active Directory, definendo un set di filtri basati su attributi. L'ambito predefinito è "tutti gli utenti in SuccessFactors". Filtri di esempio:

   * Esempio: ambito per gli utenti con personIdExternal tra 1000000 e 200000 (esclusi 2000000)

      * Attributo: personIdExternal

      * Operatore: Corrispondenza REGEX

      * Valore: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Esempio: Solo i dipendenti, senza i lavoratori occasionali

      * Attributo: EmployeeID

      * Operatore: NON È NULL

   > [!TIP]
   > Quando si configura l'app di provisioning per la prima volta, è necessario testare e verificare i mapping degli attributi e le espressioni per assicurarsi che restituisca il risultato desiderato. Microsoft consiglia di utilizzare i filtri di ambito in **Source Object Scope** per testare i mapping con alcuni utenti di test di SuccessFactors. Dopo avere verificato che i mapping funzionino è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

   > [!CAUTION] 
   > Il comportamento predefinito del motore di provisioning consiste nel disabilitare/eliminare gli utenti che escono dall'ambito. Questo potrebbe non essere auspicabile nell'integrazione SuccessFactors per l'integrazione di Active Directory.This may not be desirable in your SuccessFactors to AD integration. Per eseguire l'override di questo comportamento predefinito fare riferimento all'articolo [Ignora eliminazione degli account utente che escono dall'ambitoTo](../app-provisioning/skip-out-of-scope-deletions.md) override this default behavior refer to the article Skip deletion of user accounts that go out of scope
  
1. Nel campo **Target Object Actions** (Azioni oggetto di destinazione) è possibile applicare un filtro a livello globale per le azioni che vengono eseguite in Active Directory. **Creazione** e **Aggiornamento** sono le più comuni.

1. Nella sezione **Mapping attributi** è possibile definire il mapping dei singoli attributi SuccessFactors agli attributi di Active Directory.

  >[!NOTE]
  >Per l'elenco completo dell'attributo SuccessFactors supportato dall'applicazione, fare riferimento a [Riferimento all'attributo SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)


1. Fare clic su un mapping di attributi esistente per aggiornarlo oppure fare clic su **Aggiungi nuovo mapping** nella parte inferiore della schermata per aggiungere nuovi mapping. Il mapping di un singolo attributo supporta queste proprietà:

      * **Tipo di mapping**

         * **Diretto:** scrive il valore dell'attributo SuccessFactors nell'attributo AD, senza modifiche

         * **Costante** - Scrivere un valore stringa costante statico nell'attributo AD

         * **Espressione:** consente di scrivere un valore personalizzato nell'attributo AD, in base a uno o più attributi SuccessFactors. [Per altre informazioni, vedere questo articolo sulle espressioni](../app-provisioning/functions-for-customizing-application-data.md).

      * **Attributo Source** - L'attributo user da SuccessFactors

      * **Valore predefinito**: facoltativo. Se l'attributo di origine ha un valore vuoto, il mapping eseguirà la scrittura di questo valore.
            Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

      * **Attributo target:** attributo utente in Active Directory.

      * **Corrispondenza di oggetti utilizzando questo attributo:** indica se questo mapping deve essere utilizzato per identificare in modo univoco gli utenti tra SuccessFactors e Active Directory. Questo valore viene in genere impostato nel campo ID lavoratore per SuccessFactors, che in genere viene mappato a uno degli attributi ID dipendente in Active Directory.

      * **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Se sono presenti più attributi, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.

      * **Applica questo mapping**

         * **Sempre:** applicare questo mapping alle azioni di creazione e aggiornamento degli utentiAlways – Apply this mapping on both user creation and update actions

         * **Solo durante la creazione** - Applica questo mapping solo alle azioni di creazione utente

1. Per salvare i mapping, fare clic su **Salva** nella parte superiore della sezione Mapping attributi.

Dopo aver completato la configurazione di mapping di attributo, è ora possibile [abilitare e avviare il servizio di provisioning utenti](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Abilitare e avviare il provisioning utenti

Una volta completate le configurazioni dell'app di provisioning SuccessFactors, è possibile attivare il servizio di provisioning nel portale di Azure.Once the SuccessFactors provisioning app configurations have been completed, you can turn on the provisioning service in the Azure portal.

> [!TIP]
> Per impostazione predefinita quando si attiva il servizio di provisioning, verranno avviate le operazioni di provisioning per tutti gli utenti nell'ambito. Se sono presenti errori nel mapping o problemi di dati SuccessFactors, il processo di provisioning potrebbe non riuscire e passare allo stato di quarantena. Come procedura consigliata per evitare questo problema è consigliabile configurare il filtro **Source Object Scope** (Ambito dell'oggetto di origine) e il test di mapping degli attributi con alcuni utenti test prima di avviare la sincronizzazione completa per tutti gli utenti. Dopo avere verificato che i mapping funzionino e che restituiscano i risultati desiderati è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

1. Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

2. Fare clic su **Salva**.

3. Questa operazione avvierà la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti presenti nel tenant SuccessFactors.This operation will start the initial sync, which can take a variable number of hours depending on how many users are in the SuccessFactors tenant. È possibile controllare la barra di avanzamento per tenere traccia dell'avanzamento del ciclo di sincronizzazione. 

4. In qualsiasi momento è possibile controllare la scheda **Log di controllo** nel portale di Azure per vedere le azioni che sono state eseguite dal servizio di provisioning. Nei registri di controllo sono elencati tutti i singoli eventi di sincronizzazione eseguiti dal servizio di provisioning, ad esempio quali utenti vengono letti da SuccessFactors e successivamente aggiunti o aggiornati ad Active Directory. 

5. Al termine della sincronizzazione iniziale, verrà scritto un report di riepilogo di controllo nella scheda **Provisioning**, come illustrato di seguito.

   > [!div class="mx-imgBorder"]
   > ![Barra di avanzamento del provisioning](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Passaggi successivi

* [Ulteriori informazioni sugli attributi SuccessFactors supportati per il provisioning in ingressoLearn more about supported SuccessFactors Attributes for inbound provisioning](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Informazioni su come configurare il writeback della posta elettronica in SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
* [Informazioni su come configurare l'accesso Single Sign-On tra SuccessFactors e Azure Active Directory](successfactors-tutorial.md)
* [Informazioni sull'integrazione di altre applicazioni SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni su come esportare e importare le configurazioni di provisioning](../app-provisioning/export-import-provisioning-configuration.md)
