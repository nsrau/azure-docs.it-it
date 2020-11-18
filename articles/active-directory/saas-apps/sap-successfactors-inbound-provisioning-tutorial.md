---
title: 'Esercitazione: Configurare il provisioning in ingresso di SuccessFactors in AD e Azure AD | Microsoft Docs'
description: Informazioni su come configurare il provisioning in ingresso da SuccessFactors
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.openlocfilehash: 53707261070e8efbd014614ee700df63a0925ef8
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94352728"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning"></a>Esercitazione: Configurare il provisioning di utenti da SAP SuccessFactors ad Active Directory 
Questa esercitazione illustra la procedura da eseguire per effettuare il provisioning di utenti da SuccessFactors Employee Central in Active Directory (AD) e Azure AD, con il writeback facoltativo dell'indirizzo di posta elettronica in SuccessFactors. 

>[!NOTE]
>Usare questa esercitazione se gli utenti di cui si vuole effettuare il provisioning da SuccessFactors necessitano di un account AD locale e facoltativamente di un account Azure AD. Se gli utenti di SuccessFactors hanno bisogno solo di un account Azure AD (utenti solo cloud), vedere l'esercitazione [Configurare il provisioning di utenti da SAP SuccessFactors ad Active Directory](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md). 


## <a name="overview"></a>Panoramica

Il [servizio di provisioning utenti di Azure Active Directory](../app-provisioning/user-provisioning.md) si integra con [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) per gestire il ciclo di vita delle identità degli utenti. 

I flussi di lavoro di provisioning utenti di SuccessFactors supportati dal servizio di provisioning utenti di Azure AD consentono l'automazione dei seguenti scenari di gestione delle risorse umane e del ciclo di vita delle identità:

* **Assunzione di nuovi dipendenti**: quando viene aggiunto un nuovo dipendente a SuccessFactors, viene creato automaticamente un account utente in Active Directory, in Azure Active Directory e, facoltativamente, in Microsoft 365 e in [altre applicazioni SaaS supportate da Azure AD](../app-provisioning/user-provisioning.md), con il writeback dell'indirizzo di posta elettronica a SuccessFactors.

* **Aggiornamenti di attributi e profili dei dipendenti**: se il record di un dipendente viene aggiornato in SuccessFactors (ad esempio il nome, la qualifica o il manager), il relativo account utente verrà aggiornato automaticamente in Active Directory, in Azure Active Directory e, facoltativamente, in Microsoft 365 e in [altre applicazioni SaaS supportate da Azure AD](../app-provisioning/user-provisioning.md).

* **Termine del rapporto con i dipendenti**: quando un dipendente viene eliminato da SuccessFactors, il relativo account utente viene disabilitato automaticamente in Active Directory, Azure Active Directory e, facoltativamente, in Office 365 e in [altre applicazioni SaaS supportate da Azure AD](../app-provisioning/user-provisioning.md).

* **Riassunzioni di dipendenti**: quando un dipendente viene nuovamente aggiunto in SuccessFactors, il relativo account utente precedente può essere automaticamente riattivato o sottoposto di nuovo a provisioning (a seconda delle preferenze) in Active Directory, in Azure Active Directory e, facoltativamente, in Microsoft 365 e in [altre applicazioni SaaS supportate da Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Per chi è più adatta questa soluzione di provisioning utenti?

Questa soluzione di provisioning utenti da SuccessFactors ad Active Directory è idealmente la più appropriata per:

* Organizzazioni che desiderano una soluzione predefinita basata sul cloud per il provisioning utenti di SuccessFactors

* Organizzazioni che richiedono il provisioning utenti diretto da SuccessFactors ad Active Directory

* Organizzazioni che richiedono che il provisioning utenti venga effettuato tramite i dati ottenuti da [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizzazioni che vogliono che le operazioni di aggiunta, spostamento ed eliminazione di utenti siano sincronizzate con uno o più domini, foreste o unità organizzative di Active Directory solo in base alle informazioni modificate rilevate in [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizzazioni che usano Microsoft 365 per la posta elettronica

## <a name="solution-architecture"></a>Architettura della soluzione

Questa sezione descrive l'architettura della soluzione end-to-end di provisioning degli utenti per ambienti ibridi comuni. Esistono due flussi correlati:

* **Flusso di dati HR autorevoli - da SuccessFactors a un'istanza locale di Active Directory:** in questo flusso gli eventi relativi ai lavoratori (ad esempio, nuove assunzioni, trasferimenti e risoluzioni del rapporto) si verificano prima in SuccessFactors Employee Central nel cloud e quindi i dati degli eventi vengono trasmessi nell'istanza locale di Active Directory tramite Azure AD e l'agente di provisioning. A seconda dell'evento, può determinare operazioni di creazione/aggiornamento/abilitazione o disabilitazione in Active Directory.
* **Flusso di writeback di posta elettronica - da un'istanza locale di Active Directory a SuccessFactors:** al termine della creazione in Active Directory, l'account viene sincronizzato con Azure AD tramite Azure AD Connect e l'attributo di posta elettronica può essere sottoposto a writeback in SuccessFactors.

  ![Panoramica](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Flusso di dati end-to-end dell'utente

1. Il team delle risorse umane esegue transazioni relative ai lavoratori (nuovi ingressi/spostamenti/abbandoni oppure nuove assunzioni/trasferimenti/risoluzioni del rapporto) in SuccessFactors Employee Central
2. Il servizio di provisioning di Azure AD esegue sincronizzazioni pianificate delle identità da SuccessFactors Employee Central e identifica i cambiamenti che devono essere elaborati per la sincronizzazione con l'istanza locale di Active Directory.
3. Il servizio di provisioning di Azure AD richiama l'agente di provisioning locale di Azure AD Connect con un payload di richiesta contenente operazioni di creazione/aggiornamento/abilitazione o disabilitazione dell'account AD.
4. L'agente di provisioning di Azure AD Connect usa un account del servizio per aggiungere/aggiornare i dati dell'account AD.
5. Il motore di sincronizzazione di Azure AD Connect esegue una sincronizzazione delta per eseguire il pull degli aggiornamenti in AD.
6. Gli aggiornamenti di Active Directory sono sincronizzati con Azure Active Directory.
7. Se l'[app Writeback di SuccessFactors](sap-successfactors-writeback-tutorial.md) è configurata, esegue il writeback dell'attributo di posta elettronica in SuccessFactors in base all'attributo corrispondente usato.

## <a name="planning-your-deployment"></a>Pianificazione della distribuzione

La configurazione del provisioning utenti basato su risorse umane nel cloud da SuccessFactors ad AD richiede una pianificazione considerevole che tenga conto di diversi aspetti, quali:
* Installazione dell'agente di provisioning di Azure AD Connect 
* Numero di app di provisioning utenti da SuccessFactors ad AD da distribuire
* Corrispondenza di ID, mapping degli attributi, trasformazione e filtri di ambito

Vedere il [piano di distribuzione dell'app HR basata sul cloud](../app-provisioning/plan-cloud-hr-provision.md) per indicazioni complete su questi argomenti. Vedere la [guida di riferimento all'integrazione di SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md) per informazioni sulle entità supportate, i dettagli di elaborazione e su come personalizzare l'integrazione per scenari HR diversi. 

## <a name="configuring-successfactors-for-the-integration"></a>Configurazione di SuccessFactors per l'integrazione

Un requisito comune di tutti i connettori per il provisioning di SuccessFactors è che richiedano le credenziali di un account SuccessFactors con le autorizzazioni appropriate per richiamare le API OData di SuccessFactors. Questa sezione descrive la procedura da eseguire per creare l'account del servizio in SuccessFactors e concedere le autorizzazioni appropriate. 

* [Creare/identificare l'account utente dell'API in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Creare un ruolo delle autorizzazioni API](#create-an-api-permissions-role)
* [Creare un gruppo di autorizzazioni per l'utente dell'API](#create-a-permission-group-for-the-api-user)
* [Concedere il ruolo delle autorizzazioni al gruppo di autorizzazioni](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Creare/identificare l'account utente dell'API in SuccessFactors
Collaborare con il team di amministrazione di SuccessFactors o con il partner responsabile dell'implementazione per creare o identificare un account utente in SuccessFactors che verrà usato per richiamare le API OData. Le credenziali di nome utente e password di questo account saranno necessarie per configurare le app di provisioning in Azure AD. 

### <a name="create-an-api-permissions-role"></a>Creare un ruolo delle autorizzazioni API

* Accedere a SAP SuccessFactors con un account utente che ha accesso all'Interfaccia di amministrazione.
* Cercare *Manage Permission Roles* (Gestisci ruoli autorizzazione), quindi selezionare **Manage Permission Roles** dai risultati della ricerca.
  ![Manage Permission Roles](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* In Permission Role List (Elenco ruoli autorizzazioni) fare clic su **Create New** (Crea nuovo).
  > [!div class="mx-imgBorder"]
  > ![Creazione di un nuovo ruolo di autorizzazioni](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* In **Role Name** e **Description** aggiungere rispettivamente un nome e una descrizione per il nuovo ruolo di autorizzazioni. Il nome e la descrizione devono indicare che il ruolo riguarda le autorizzazioni di utilizzo dell'API.
  > [!div class="mx-imgBorder"]
  > ![Dettagli del ruolo delle autorizzazioni](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* In Permission settings (Impostazioni autorizzazione) fare clic su **Permission** (Autorizzazione), quindi scorrere l'elenco delle autorizzazioni verso il basso e fare clic su **Manage Integration Tools** (Gestione strumenti di integrazione). Selezionare la casella **Allow Admin to Access to OData API through Basic Authentication** (Consenti all'amministratore di accedere all'API OData tramite l'autenticazione di base).
  > [!div class="mx-imgBorder"]
  > ![Gestione strumenti di integrazione](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Scorrere verso il basso nello stesso riquadro e selezionare **Employee Central API** (API Employee Central). Aggiungere le autorizzazioni di lettura e modifica dell'API ODATA, come illustrato di seguito. Selezionare l'opzione di modifica se si prevede di usare lo stesso account per lo scenario di writeback in SuccessFactors. 
  > [!div class="mx-imgBorder"]
  > ![Autorizzazioni di lettura/scrittura](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

  >[!NOTE]
  >Per l'elenco completo degli attributi recuperati da questa app di provisioning, vedere [Informazioni di riferimento sugli attributi di SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)

* Fare clic su **Done** (Fine). Fare clic su **Salva modifiche**.

### <a name="create-a-permission-group-for-the-api-user"></a>Creare un gruppo di autorizzazioni per l'utente dell'API

* Nell'interfaccia di amministrazione di SuccessFactors cercare *Manage Permission Groups* (Gestisci gruppi di autorizzazioni), quindi selezionare **Manage Permission Groups** dai risultati della ricerca.
  > [!div class="mx-imgBorder"]
  > ![Manage Permission Groups](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Nella finestra Manage Permission Groups fare clic su **Create New** (Crea nuovo).
  > [!div class="mx-imgBorder"]
  > ![Aggiungere un nuovo gruppo](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Aggiungere un nome per il nuovo gruppo. Il nome del gruppo deve indicare che il gruppo è riservato agli utenti dell'API.
  > [!div class="mx-imgBorder"]
  > ![Nome del gruppo di autorizzazioni](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Aggiungere membri al gruppo. Ad esempio, si potrebbe selezionare **Username** (Nome utente) dal menu a discesa People Pool (Pool utenti) e quindi immettere il nome utente dell'account API che verrà usato per l'integrazione. 
  > [!div class="mx-imgBorder"]
  > ![Aggiungere membri del gruppo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Fare clic su **Done** (Fine) per completare la creazione del gruppo di autorizzazioni.

### <a name="grant-permission-role-to-the-permission-group"></a>Concedere il ruolo delle autorizzazioni al gruppo di autorizzazioni

* Nell'interfaccia di amministrazione di SuccessFactors cercare *Manage Permission Roles* (Gestisci ruoli autorizzazioni), quindi selezionare **Manage Permission Roles** dai risultati della ricerca.
* In **Permission Role List** (Elenco ruoli autorizzazioni) selezionare il ruolo creato per le autorizzazioni di utilizzo dell'API.
* In **Grant this role to** (Concedi ruolo a) fare clic sul pulsante **Add** (Aggiungi).
* Selezionare **Permission Group** (Gruppo di autorizzazioni) dal menu a discesa, quindi fare clic su **Select** (Seleziona) per aprire la finestra Groups (Gruppi) per cercare e selezionare il gruppo creato in precedenza. 
  > [!div class="mx-imgBorder"]
  > ![Aggiunta del gruppo di autorizzazioni](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Verificare la concessione del ruolo delle autorizzazioni al gruppo di autorizzazioni. 
  > [!div class="mx-imgBorder"]
  > ![Dettagli sul ruolo e il gruppo di autorizzazioni](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Fare clic su **Salva modifiche**.

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Configurazione del provisioning utenti da SuccessFactors ad Active Directory

Questa sezione descrive la procedura per effettuare il provisioning degli account utente da SuccessFactors in ogni dominio di Active Directory nell'ambito dell'integrazione.

* [Aggiungere l'app di connettori di provisioning e scaricare l'agente di provisioning](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Installare e configurare gli agenti di provisioning locali](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Configurare la connettività a SuccessFactors e Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Configurare i mapping degli attributi](#part-4-configure-attribute-mappings)
* [Abilitare e avviare il provisioning utenti](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Parte 1: Aggiungere l'app di connettori di provisioning e scaricare l'agente di provisioning

**Per configurare il provisioning da SuccessFactors ad Active Directory:**

1. Passare a <https://portal.azure.com>

2. Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3. Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4. Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5. Cercare **SuccessFactors to Active Directory User Provisioning** (Provisioning utenti da SuccessFactors ad Active Directory) e aggiungere tale app dalla raccolta.

6. Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**

7. Impostare **Modalità** **di provisioning** su **Automatico**

8. Fare clic sul banner di informazioni visualizzato per scaricare l'agente di provisioning. 
   > [!div class="mx-imgBorder"]
   > ![Scaricare l'agente](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "Schermata Scarica agente")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Parte 2: installare e configurare gli agenti di provisioning locali

Per effettuare il provisioning in Active Directory locale, è necessario installare un agente di provisioning in un server con .NET Framework 4.7.1 e versioni successive e con accesso alla rete ai domini di Active Directory richiesti.

> [!TIP]
> È possibile controllare la versione di .NET Framework nel server seguendo le istruzioni disponibili [qui](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Se il server non dispone di .NET 4.7.1 o versioni successive, è possibile scaricarlo da [qui](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Trasferire il programma di installazione dell'agente scaricato nell'host del server e seguire i passaggi indicati di seguito per completare la configurazione dell'agente.

1. Accedere all'istanza di Windows Server in cui si intende installare il nuovo agente.

1. Avviare il programma di installazione dell'agente di provisioning, accettare le condizioni e fare clic sul pulsante **Installa**.

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
  
   ![Schermata Exit](./media/workday-inbound-tutorial/pa_install_screen_9.png "Schermata Exit")
   
1. Verificare l'installazione dell'agente e accertarsi che sia in esecuzione aprendo lo snap-in "Servizi" e cercare il servizio denominato "Agente di provisioning Microsoft Azure Active Directory Connect"
  
   ![Screenshot dell'agente di provisioning Microsoft Azure Active Directory Connect in esecuzione in Servizi.](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Parte 3: nell'app di provisioning configurare la connettività a SuccessFactors e Active Directory
In questo passaggio viene stabilita la connettività con SuccessFactors e Active Directory nel portale di Azure. 

1. Nel portale di Azure tornare all'app di provisioning utenti da SuccessFactors ad Active Directory creata in [Parte 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore**: immettere il nome utente dell'account utente dell'API SuccessFactors, seguito dall'ID società. Il formato è **nomeutente\@IDsocietà**

   * **Password amministratore**: immettere la password dell'account utente dell'API SuccessFactors. 

   * **URL tenant**: immettere il nome dell'endpoint di servizio dell'API OData di SuccessFactors. Immettere solo il nome host del server senza http o https. Questo valore dovrebbe essere simile a **<nome-server-api>.successfactors.com**.

   * **Foresta di Active Directory:** il "nome" del dominio di Active Directory, così come registrato con l'agente. Usare l'elenco a discesa per selezionare il dominio di destinazione per il provisioning. In genere, il valore corrisponde a una stringa simile a: *contoso.com*

   * **Contenitore di Active Directory:** immettere il nome distinto del contenitore in cui l'agente deve creare gli account utente per impostazione predefinita.
        Esempio: *OU=Users,DC=contoso,DC=com*
        > [!NOTE]
        > Questa impostazione deve essere usata per la creazione degli account utente solo se l'attributo *parentDistinguishedName* non è configurato nel mapping degli attributi. Questa impostazione non viene usata per la ricerca di utenti o per le operazioni di aggiornamento. L'intero sottoalbero del dominio rientra nell'ambito dell'operazione di ricerca.

   * **Messaggio di posta elettronica di notifica:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo per inviare una notifica di posta elettronica in caso di errore.
    > [!NOTE]
    > Il servizio di provisioning di Azure AD invia una notifica di posta elettronica se il processo di provisioning entra in uno stato di [quarantena](../app-provisioning/application-provisioning-quarantine-status.md).

   * Fare clic sul pulsante **Test connessione**. Se il test della connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. Se il test non riesce, verificare che le credenziali di SuccessFactors e Active Directory configurate nel programma di installazione dell'agente siano valide.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Dopo che le credenziali vengono salvate correttamente, la sezione **Mapping** mostrerà il mapping predefinito **Synchronize SuccessFactors Users to On Premises** (Sincronizza utenti di SuccessFactors in Active Directory locale)

### <a name="part-4-configure-attribute-mappings"></a>Parte 4: configurare i mapping degli attributi

In questa sezione verrà configurato il flusso dei dati utente da SuccessFactors ad Active Directory.

1. Nella scheda Provisioning, in **Mapping**, fare clic su **Synchronize SuccessFactors Users to On Premises Active Directory** (Sincronizza utenti di SuccessFactors in Active Directory locale).

1. Nel campo **Ambito dell'oggetto di origine** è possibile selezionare i gruppi di utenti in SuccessFactors da includere nell'ambito per il provisioning in AD, definendo un set di filtri basati su attributi. L'ambito predefinito è "tutti gli utenti in SuccessFactors". Filtri di esempio:

   * Esempio: ambito per gli utenti con personIdExternal compreso tra 1000000 e 2000000 (escluso 2000000)

      * Attributo: personIdExternal

      * Operator: REGEX Match (Corrispondenza REGEX)

      * Valore: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Esempio: solo i dipendenti, senza i lavoratori occasionali

      * Attributo: EmployeeID

      * Operator: IS NOT NULL (NON È NULL)

   > [!TIP]
   > Quando si configura l'app di provisioning per la prima volta, è necessario testare e verificare i mapping degli attributi e le espressioni per assicurarsi che restituisca il risultato desiderato. Microsoft consiglia di usare i filtri di ambito in **Ambito dell'oggetto di origine** per testare il mapping con alcuni utenti test da SuccessFactors. Dopo avere verificato che i mapping funzionino è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

   > [!CAUTION] 
   > Il comportamento predefinito del motore di provisioning è disabilitare/eliminare gli utenti che non rientrano nell'ambito. Questo comportamento potrebbe essere indesiderato nell'integrazione da SuccessFactors ad Active Directory. Per eseguire l'override di questo comportamento predefinito, fare riferimento all'articolo [Ignorare l'eliminazione di account utente che non rientrano nell'ambito](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. Nel campo **Target Object Actions** (Azioni oggetto di destinazione) è possibile applicare un filtro a livello globale per le azioni che vengono eseguite in Active Directory. **Creazione** e **Aggiornamento** sono le più comuni.

1. Nella sezione **Mapping attributi** è possibile definire il mapping dei singoli attributi di SuccessFactors agli attributi di Active Directory.

  >[!NOTE]
  >Per l'elenco completo degli attributi di SuccessFactors supportati dall'applicazione, vedere [Informazioni di riferimento sugli attributi di SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)


1. Fare clic su un mapping di attributi esistente per aggiornarlo oppure fare clic su **Aggiungi nuovo mapping** nella parte inferiore della schermata per aggiungere nuovi mapping. Il mapping di un singolo attributo supporta queste proprietà:

      * **Tipo di mapping**

         * **Diretto**: scrive il valore dell'attributo di SuccessFactors nell'attributo di AD, senza modifiche

         * **Costant** (Costante): scrive un valore stringa costante statico nell'attributo di AD

         * **Espressione**: consente di scrivere un valore personalizzato per l'attributo di Active Directory, in base a uno o più attributi di SuccessFactors. [Per altre informazioni, vedere questo articolo sulle espressioni](../app-provisioning/functions-for-customizing-application-data.md).

      * **Attributo di origine**: l'attributo utente di SuccessFactors.

      * **Valore predefinito**: facoltativo. Se l'attributo di origine ha un valore vuoto, il mapping eseguirà la scrittura di questo valore.
            Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

      * **Attributo di destinazione**: l'attributo utente in Active Directory.

      * **Abbina gli oggetti in base a questo attributo**: specifica se questo mapping deve essere usato o meno per l'identificazione univoca degli utenti tra SuccessFactors e Active Directory. In genere, è impostato sul campo dell'ID lavoratore di SuccessFactors, che solitamente è associato a uno degli attributi ID dipendente di Active Directory.

      * **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Se sono presenti più attributi, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.

      * **Applica questo mapping**

         * **Sempre**: applica il mapping sia all'azione di creazione che all'azione di aggiornamento dell'utente

         * **Only during creation** (Solo durante la creazione): applica il mapping solo alle azioni di creazione dell'utente

1. Per salvare i mapping, fare clic su **Save**, Salva, nella parte superiore della sezione Attribute-Mapping, Mapping attributi.

Dopo aver completato la configurazione di mapping di attributo, è ora possibile [abilitare e avviare il servizio di provisioning utenti](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Abilitare e avviare il provisioning utenti

Dopo aver completato le configurazioni dell'app di provisioning di SuccessFactors, è possibile attivare il servizio di provisioning nel portale di Azure.

> [!TIP]
> Per impostazione predefinita quando si attiva il servizio di provisioning, verranno avviate le operazioni di provisioning per tutti gli utenti nell'ambito. Se sono presenti errori di mapping o problemi di dati in SuccessFactors, il processo di provisioning potrebbe non riuscire e passare allo stato di quarantena. Come procedura consigliata per evitare questo problema è consigliabile configurare il filtro **Source Object Scope** (Ambito dell'oggetto di origine) e il test di mapping degli attributi con alcuni utenti test prima di avviare la sincronizzazione completa per tutti gli utenti. Dopo avere verificato che i mapping funzionino e che restituiscano i risultati desiderati è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

1. Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

2. Fare clic su **Salva**.

3. Questa operazione avvierà la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti nel tenant di SuccessFactors. È possibile controllare l'indicatore di stato per monitorare lo stato del ciclo di sincronizzazione. 

4. In qualsiasi momento è possibile controllare la scheda **Log di controllo** nel portale di Azure per vedere le azioni che sono state eseguite dal servizio di provisioning. I log di controllo elencano tutti i singoli eventi di sincronizzazione eseguiti dal servizio di provisioning, ad esempio quali utenti vengono letti da SuccessFactors e successivamente aggiunti o aggiornati in Active Directory. 

5. Al termine della sincronizzazione iniziale, verrà scritto un report di riepilogo di controllo nella scheda **Provisioning**, come illustrato di seguito.

   > [!div class="mx-imgBorder"]
   > ![Indicatore di stato del provisioning](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sugli attributi di SuccessFactors supportati per il provisioning in ingresso](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Informazioni su come configurare il writeback degli indirizzi di posta elettronica in SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
* [Informazioni su come configurare il Single Sign-On tra SuccessFactors e Azure Active Directory](successfactors-tutorial.md)
* [Informazioni sull'integrazione di altre applicazioni SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni su come esportare e importare le configurazioni del provisioning](../app-provisioning/export-import-provisioning-configuration.md)