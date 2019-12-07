---
title: 'Esercitazione: configurare il provisioning in ingresso di SuccessFactors in Azure Active Directory | Microsoft Docs'
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
ms.openlocfilehash: 85f3c8b9bc4167350b8a56f118128b89df142611
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896922"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>Esercitazione: configurare SAP SuccessFactors per il provisioning utenti di Active Directory (anteprima)
Questa esercitazione descrive la procedura da eseguire per importare i dati del ruolo di lavoro da SuccessFactors Employee Central in Active Directory e Azure Active Directory, con Write-back facoltativo dell'indirizzo di posta elettronica in SuccessFactors.

## <a name="overview"></a>Panoramica

Il [servizio di provisioning utenti Azure Active Directory](../manage-apps/user-provisioning.md) si integra con il [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) per gestire il ciclo di vita dell'identità degli utenti. 

I flussi di lavoro di provisioning utenti di SuccessFactors supportati dal servizio di provisioning utenti Azure AD consentono l'automazione dei seguenti scenari di gestione delle risorse umane e del ciclo di vita delle identità:

* **Assunzione di nuovi dipendenti** : quando viene aggiunto un nuovo dipendente a SuccessFactors, un account utente viene creato automaticamente in Active Directory, Azure Active Directory e, facoltativamente, Office 365 e [altre applicazioni SaaS supportate da Azure ad](../manage-apps/user-provisioning.md), con Write-back dell'indirizzo di posta elettronica a SuccessFactors.

* **Aggiornamenti del profilo e dell'attributo del dipendente** : quando un record del dipendente viene aggiornato in SuccessFactors (ad esempio il nome, il titolo o il responsabile), il relativo account utente verrà aggiornato automaticamente in Active Directory, Azure Active Directory e, facoltativamente, Office 365 e [altre applicazioni SaaS supportate da Azure ad](../manage-apps/user-provisioning.md).

* **Interruzioni dei dipendenti** : quando un dipendente viene terminato in SuccessFactors, il relativo account utente viene disabilitato automaticamente in Active Directory, Azure Active Directory e, facoltativamente, Office 365 e [altre applicazioni SaaS supportate da Azure ad](../manage-apps/user-provisioning.md).

* **Riassunzioni dei dipendenti** : quando un dipendente viene riassunto in SuccessFactors, il relativo account precedente può essere riattivato automaticamente o nuovamente sottoposto a provisioning (a seconda delle proprie preferenze) per Active Directory, Azure Active Directory e, facoltativamente, Office 365 e [altre applicazioni SaaS supportate da Azure ad](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Per chi è più adatta questa soluzione di provisioning utenti?

Questo SuccessFactors per Active Directory soluzione di provisioning degli utenti è particolarmente adatto per:

* Organizzazioni che desiderano una soluzione predefinita basata sul cloud per il provisioning utenti di SuccessFactors

* Organizzazioni che richiedono il provisioning utenti diretto da SuccessFactors a Active Directory

* Organizzazioni che richiedono il provisioning degli utenti usando i dati ottenuti da [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizzazioni che richiedono l'aggiunta, lo stato di trasferimento e l'uscita degli utenti da sincronizzare a una o più Active Directory foreste, domini e unità organizzative basate solo su informazioni sulle modifiche rilevate in [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizzazioni che usano Office 365 per la posta elettronica

## <a name="solution-architecture"></a>Architettura della soluzione

Questa sezione descrive l'architettura della soluzione end-to-end di provisioning degli utenti per ambienti ibridi comuni. Esistono due flussi correlati:

* **Flusso di dati HR autorevole-da SuccessFactors a Active Directory locali:** In questo flusso, i flussi di lavoro, ad esempio i nuovi assunti, i trasferimenti, le terminazioni, si verificano prima nel cloud SuccessFactors Employee Central, quindi i dati degli eventi passano in locale Active Directory tramite Azure AD e l'agente di provisioning. A seconda dell'evento, può determinare operazioni di creazione/aggiornamento/abilitazione o disabilitazione in Active Directory.
* **Flusso di writeback della posta elettronica: da Active Directory locali a SuccessFactors:** Una volta che la creazione dell'account è stata completata in Active Directory, viene sincronizzata con Azure AD tramite Azure AD Connect la sincronizzazione e l'attributo di posta elettronica possono essere riscritti in SuccessFactors.

  ![Panoramica](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Flusso di dati end-to-end dell'utente

1. Il team HR esegue le transazioni di lavoro (join/spostamenti/abbandonati o nuove assunzioni/trasferimenti/interruzioni) in SuccessFactors Employee Central
2. Il servizio di provisioning Azure AD esegue sincronizzazioni pianificate di identità da SuccessFactors EC e identifica le modifiche che devono essere elaborate per la sincronizzazione con Active Directory locale.
3. Il servizio di provisioning di Azure AD richiama l'agente di provisioning Azure AD Connect locale con un payload di richiesta che contiene le operazioni di creazione/aggiornamento/abilitazione/disabilitazione dell'account Active Directory.
4. L'agente di provisioning di Azure AD Connect usa un account del servizio per aggiungere/aggiornare i dati dell'account AD.
5. Il motore di sincronizzazione Azure AD Connect esegue la sincronizzazione Delta per eseguire il pull degli aggiornamenti in Active Directory.
6. Gli aggiornamenti di Active Directory sono sincronizzati con Azure Active Directory.
7. Se il connettore writeback SuccessFactors è configurato, scrive l'attributo di posta elettronica e il nome utente in SuccessFactors, in base all'attributo corrispondente usato.

## <a name="planning-your-deployment"></a>Pianificazione della distribuzione

La configurazione del provisioning utenti basato su cloud HR da SuccessFactors ad ad richiede una pianificazione considerevole che copre aspetti diversi, ad esempio:
* Impostazione dell'agente di provisioning Azure AD Connect 
* Numero di SuccessFactors per le app di provisioning utenti ad da distribuire
* ID corrispondente, mapping di attributi, trasformazione e filtri di ambito

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

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Configurazione del provisioning utenti da SuccessFactors a Active Directory

Questa sezione illustra i passaggi per il provisioning degli account utente da SuccessFactors a ogni dominio Active Directory nell'ambito dell'integrazione.

* [Aggiungere l'app del connettore di provisioning e scaricare l'agente di provisioning](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Installare e configurare gli agenti di provisioning locali](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Configurare la connettività a SuccessFactors e Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Configurare i mapping degli attributi](#part-4-configure-attribute-mappings)
* [Abilitare e avviare il provisioning utenti](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Parte 1: aggiungere l'app del connettore di provisioning e scaricare l'agente di provisioning

**Per configurare SuccessFactors per Active Directory il provisioning:**

1. Vai a <https://portal.azure.com>

2. Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3. Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4. Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5. Cercare **SuccessFactors per Active Directory il provisioning utenti**e aggiungere tale app dalla raccolta.

6. Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**

7. Impostare **Modalità di** **provisioning** su **Automatico**

8. Fare clic sul banner informativo visualizzato per scaricare l'agente di provisioning. 
   > [!div class="mx-imgBorder"]
   > ![Scarica agente](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "Schermata di download dell'agente")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Parte 2: installare e configurare agenti di provisioning locali

Per eseguire il provisioning in Active Directory in locale, l'agente di provisioning deve essere installato in un server con .NET 4.7.1 + Framework e accesso di rete ai domini Active Directory desiderati.

> [!TIP]
> È possibile controllare la versione di .NET Framework nel server seguendo le istruzioni disponibili [qui](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Se il server non dispone di .NET 4.7.1 o versioni successive, è possibile scaricarlo da [qui](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Trasferire il programma di installazione dell'agente scaricato nell'host del server e seguire i passaggi indicati di seguito per completare la configurazione dell'agente.

1. Accedere al server Windows in cui si vuole installare il nuovo agente.

1. Avviare il programma di installazione dell'agente di provisioning, accettare le condizioni e fare clic sul pulsante **Installa** .

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
  
   ![Schermata di uscita](./media/workday-inbound-tutorial/pa_install_screen_9.png "Schermata di uscita")
   
1. Verificare l'installazione dell'agente e accertarsi che sia in esecuzione aprendo lo snap-in "Services" e cercare il servizio denominato "Microsoft Azure AD Connect Provisioning Agent" (Agente di provisioning Microsoft Azure Active Directory Connect)
  
   ![Services](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Parte 3: nell'app di provisioning configurare la connettività a SuccessFactors e Active Directory
In questo passaggio viene stabilita la connettività con SuccessFactors e Active Directory nel portale di Azure. 

1. Nella portale di Azure tornare a SuccessFactors per Active Directory app di provisioning utente creata nella [parte 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore** : immettere il nome utente dell'account utente dell'API SuccessFactors, con l'ID società accodato. Il formato è: **username\@companyID**

   * **Password amministratore:** Immettere la password dell'account utente dell'API SuccessFactors. 

   * **URL tenant:** Immettere il nome dell'endpoint di servizi API OData di SuccessFactors. Immettere solo il nome host del server senza http o HTTPS. Questo valore dovrebbe essere simile al seguente: **< API-server-name >. SuccessFactors. com**.

   * **Foresta di Active Directory:** il "nome" del dominio di Active Directory, così come registrato con l'agente. Usare l'elenco a discesa per selezionare il dominio di destinazione per il provisioning. In genere, il valore corrisponde a una stringa simile a: *contoso.com*

   * **Contenitore di Active Directory:** immettere il nome distinto del contenitore in cui l'agente deve creare gli account utente per impostazione predefinita.
        Esempio: *ou = Users, DC = contoso, DC = com*
        > [!NOTE]
        > Questa impostazione deve essere usata per la creazione degli account utente solo se l'attributo *parentDistinguishedName* non è configurato nel mapping degli attributi. Questa impostazione non viene usata per la ricerca di utenti o per le operazioni di aggiornamento. L'intero sottoalbero del dominio rientra nell'ambito dell'operazione di ricerca.

   * **Indirizzo di posta elettronica per le notifiche:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo per inviare una notifica di posta elettronica in caso di errore.
         > [!NOTE]
         > The Azure AD Provisioning Service sends email notification if the provisioning job goes into a [quarantine](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) state.

   * Fare clic sul pulsante **Test connessione**. Se il test della connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. In caso di errore, verificare che le credenziali di SuccessFactors e le credenziali di Active Directory configurate per l'installazione dell'agente siano valide.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Una volta salvate correttamente le credenziali, la sezione **mapping** visualizzerà il mapping predefinito **Sincronizza gli utenti di SuccessFactors in locale Active Directory**

### <a name="part-4-configure-attribute-mappings"></a>Parte 4: configurare i mapping degli attributi

In questa sezione verrà configurato il flusso dei dati utente da SuccessFactors a Active Directory.

1. Nella scheda provisioning in **mapping**fare clic su **Sincronizza utenti SuccessFactors in locale Active Directory**.

1. Nel campo **ambito dell'oggetto di origine** è possibile selezionare i set di utenti in SuccessFactors che devono essere nell'ambito per il provisioning in Active Directory, definendo un set di filtri basati su attributi. L'ambito predefinito è "tutti gli utenti in SuccessFactors". Filtri di esempio:

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
   > Il comportamento predefinito del motore di provisioning è disabilitare/eliminare gli utenti che non rientrano nell'ambito. Questa operazione potrebbe non essere auspicabile nell'integrazione di SuccessFactors con Active Directory. Per eseguire l'override di questo comportamento predefinito, vedere l'articolo [ignorare l'eliminazione di account utente che non rientrano nell'ambito](../manage-apps/skip-out-of-scope-deletions.md)
  
1. Nel campo **Target Object Actions** (Azioni oggetto di destinazione) è possibile applicare un filtro a livello globale per le azioni che vengono eseguite in Active Directory. **Creazione** e **Aggiornamento** sono le più comuni.

1. Nella sezione **mapping** degli attributi è possibile definire la modalità di mapping dei singoli attributi SuccessFactors a Active Directory attributi.

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

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
* [Informazioni su come configurare Single Sign-On tra SuccessFactors e Azure Active Directory](successfactors-tutorial.md)
* [Informazioni sull'integrazione di altre applicazioni SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni su come esportare e importare le configurazioni di provisioning](../manage-apps/export-import-provisioning-configuration.md)













