---
title: 'Esercitazione: configurare Workday per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Workday.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/18/2018
ms.author: chmutali
ms.openlocfilehash: 754c3278cb01e010718fa4d3cb257acf6ffe99c9
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849854"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning-preview"></a>Esercitazione: configurare Workday per il provisioning utenti automatico (anteprima)

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire per importare profili di ruoli di lavoro da Workday in Active Directory e Azure Active Directory, con il writeback facoltativo dell'indirizzo di posta elettronica in Workday.

## <a name="overview"></a>Panoramica

Il [servizio di provisioning utenti di Azure Active Directory](../manage-apps/user-provisioning.md) si integra con l'[API Human Resources di Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) per il provisioning degli account utente. Azure AD usa questa connessione per consentire i flussi di lavoro di provisioning utenti seguenti:

* **Provisioning degli utenti in Active Directory**: sincronizzare set selezionati di utenti da Workday in uno o più domini di Active Directory.

* **Provisioning degli utenti solo cloud su Azure Active Directory** - Negli scenari in cui non viene utilizzato Active Directory locale, il provisioning degli utenti può essere effettuato direttamente da Workday ad Azure Active Directory utilizzando il servizio di provisioning degli utenti Azure AD. 

* **Writeback degli indirizzi di posta elettronica in Workday**: il servizio di provisioning utenti di Azure AD può eseguire la scrittura in Workday dell'indirizzo di posta elettronica degli utenti di Azure AD. 

### <a name="what-human-resources-scenarios-does-it-cover"></a>Quali scenari di risorse umane è in grado di gestire?

I flussi di lavoro di provisioning utenti di Workday supportati dal servizio di provisioning utenti di Azure AD consentono l'automazione dei seguenti scenari di gestione delle risorse umane e del ciclo di vita delle identità:

* **Assunzione di nuovi dipendenti**: quando viene aggiunto un nuovo dipendente a Workday, viene creato automaticamente un account utente in Active Directory, Azure Active Directory e, facoltativamente, in Office 365 e [altre applicazioni SaaS supportate da Azure AD](../manage-apps/user-provisioning.md), con il writeback dell'indirizzo di posta elettronica in Workday.

* **Aggiornamenti di attributi e profili dei dipendenti**: se il record di un dipendente viene aggiornato in Workday (ad esempio, il nome, il titolo o il manager), il relativo account utente verrà aggiornato automaticamente in Active Directory, Azure Active Directory e, facoltativamente, in Office 365 e [altre applicazioni SaaS supportate da Azure AD](../manage-apps/user-provisioning.md).

* **Eliminazione di dipendenti**: quando un dipendente viene eliminato in Workday, il relativo account utente viene disabilitato automaticamente in Active Directory, Azure Active Directory e, facoltativamente, in Office 365 e [altre applicazioni SaaS supportate da Azure AD](../manage-apps/user-provisioning.md).

* **Riassunzioni di dipendenti**: quando un dipendente viene nuovamente aggiunto in Workday, il relativo account utente precedente può essere automaticamente riattivato o sottoposto di nuovo a provisioning (a seconda delle proprie preferenze) in Active Directory, Azure Active Directory e, facoltativamente, in Office 365 e [altre applicazioni SaaS supportate da Azure AD](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Per chi è più adatta questa soluzione di provisioning utenti?

Questa soluzione di provisioning utenti Workday è attualmente disponibile in anteprima pubblica e idealmente è la più appropriata per:

* Organizzazioni che desiderano una soluzione predefinita basata sul cloud per il provisioning utenti Workday

* Organizzazioni che richiedono il provisioning utenti diretto da Workday ad Active Directory o ad Azure Active Directory

* Organizzazioni che richiedono che il provisioning utenti venga effettuato tramite i dati ottenuti dal modulo Workday HCM. Vedere [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Organizzazioni che richiedono l'aggiunta o lo spostamento degli utenti o che devono lasciare gli utenti sincronizzati con uno o più domini, foreste o unità organizzative di Active Directory solo in base alle informazioni sulle modifiche rilevate nel modulo Workday HCM. Vedere [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Organizzazioni che usano Office 365 per la posta elettronica

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="solution-architecture"></a>Architettura della soluzione

Questa sezione descrive l'architettura della soluzione end-to-end di provisioning degli utenti per ambienti ibridi comuni. Esistono due flussi correlati:

* **Flusso di dati rilevante delle risorse umane, da Workday ad Active Directory locale:** in questo flusso gli eventi del ruolo di lavoro (ad esempio, nuove assunzioni, trasferimenti e risoluzioni) si verificano prima nel tenant di risorse umane Workday nel cloud e quindi i dati degli eventi vengono trasmessi nell'istanza locale di Active Directory tramite Azure AD e l'agente di provisioning. A seconda dell'evento, può determinare operazioni di creazione/aggiornamento/abilitazione o disabilitazione in Active Directory.
* **Flusso di writeback di posta elettronica, da un'istanza locale di Active Directory a Workday:** al termine della creazione in Active Directory, l'account viene sincronizzato con Azure AD tramite Azure AD Connect e l'attributo di posta elettronica originato da Active Directory, può essere sottoposto a writeback in Workday.

![Panoramica](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Flusso di dati end-to-end dell'utente

1. Il team delle risorse umane esegue transazioni di ruoli di lavoro (nuovi ingressi/spostamenti/abbandoni oppure nuove assunzioni/trasferimenti/risoluzioni) in Workday HCM
2. Il servizio di provisioning di Azure AD esegue sincronizzazioni pianificate delle identità da Workday HR e identifica le modifiche che devono essere elaborate per la sincronizzazione con Active Directory in locale.
3. Il servizio di provisioning di Azure AD richiama l'agente di provisioning locale di AAD Connect con un payload di richiesta contenente operazioni di creazione/aggiornamento/abilitazione o disabilitazione dell'account AD.
4. L'agente di provisioning di Azure AD Connect usa un account del servizio per aggiungere/aggiornare i dati dell'account AD.
5. Il motore Azure AD Connect/AD Sync esegue una sincronizzazione delta per eseguire il pull degli aggiornamenti in AD.
6. Gli aggiornamenti di Active Directory sono sincronizzati con Azure Active Directory.
7. Se è configurato il connettore di Workday Writeback, esegue il writeback dell'attributo di posta elettronica in Workday in base all'attributo di corrispondenza usato.


## <a name="planning-your-deployment"></a>Pianificazione della distribuzione

Prima di avviare l'integrazione di Workday, verificare i prerequisiti riportati di seguito e leggere le seguenti linee guida su come associare gli attuali requisiti per l'architettura Active Directory e il provisioning utenti con le soluzioni fornite da Azure Active Directory.

### <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Una sottoscrizione di Azure AD Premium P1 valida con accesso di amministratore globale
* Un tenant per l'implementazione di Workday a scopo di test e integrazione
* Autorizzazioni di amministratore in Workday per creare un utente di integrazione dei sistemi e apportare modifiche ai dati dei dipendenti di prova a scopo di test
* Per il provisioning degli utenti in Active Directory, è necessario un server con Windows Server 2012 o versione successiva con il runtime .NET 4.7 o versione successiva per ospitare l'[agente di provisioning locale](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) per la sincronizzazione tra Active Directory e Azure AD

### <a name="planning-considerations"></a>Considerazioni sulla pianificazione

Azure AD offre un ricco set di connettori di provisioning per semplificare l'esecuzione del provisioning e la gestione del ciclo di vita delle identità da Workday ad Active Directory, Azure AD, app SaaS e altro ancora. Le funzionalità da usare e la configurazione della soluzione variano a seconda dell'ambiente e dei requisiti dell'organizzazione. Per iniziare, prendere nota del numero degli elementi seguenti presenti e distribuiti nell'organizzazione:

* Quante foreste di Active Directory sono in uso?
* Quanti domini di Active Directory sono in uso?
* Quante unità organizzative di Active Directory sono in uso?
* Quanti tenant di Azure Active Directory sono in uso?
* Sono presenti utenti di cui deve essere effettuato il provisioning sia in Active Directory che in Azure Active Directory (ad esempio, utenti "ibridi")?
* Sono presenti utenti di cui deve essere effettuato il provisioning in Azure Active Directory, ma non in Active Directory (ad esempio, utenti "solo cloud")?
* Deve essere eseguito il writeback degli indirizzi di posta elettronica degli utenti in Workday?

Dopo aver risposto a queste domande, è possibile pianificare la distribuzione del provisioning di Workday seguendo le linee guida riportate di seguito.

#### <a name="planning-deployment-of-aad-connect-provisioning-agent"></a>Pianificazione della distribuzione dell'agente di provisioning AAD Connect

La soluzione di provisioning degli utenti da Workday ad AD richiede la distribuzione di uno o più agenti di provisioning in server che eseguono Windows 2012 R2 o versione successiva con almeno 4 GB di RAM e runtime .NET 4.7 e versioni successive. Valutare le considerazioni seguenti prima di installare l'agente di provisioning:

* Assicurarsi che il server host che esegue l'agente di provisioning abbia accesso in rete al dominio di Active Directory di destinazione
* La Configurazione guidata dell'agente di provisioning registra l'agente con il tenant di Azure AD e il processo di registrazione richiede l'accesso a *.msappproxy.net sulla porta 8082. Assicurarsi che le regole del firewall in uscita siano in grado di consentire questa comunicazione.
* L'agente di provisioning usa un account del servizio per comunicare con i domini locali di Active Directory. Prima dell'installazione dell'agente, è consigliabile creare un account del servizio con autorizzazioni di lettura/scrittura sulle proprietà utente e una password senza scadenza.  
* Durante la configurazione dell'agente di provisioning è possibile selezionare i controller di dominio che devono gestire le richieste di provisioning. In presenza di vari controller di dominio distribuiti geograficamente, installare l'agente di provisioning nello stesso sito del controller di dominio preferito per migliorare l'affidabilità e le prestazioni della soluzione end-to-end
* Per la disponibilità elevata è possibile distribuire più di un agente di provisioning e registrarlo in modo che gestisca lo stesso set di domini locali di Active Directory.

> [!IMPORTANT]
> Negli ambienti di produzione Microsoft consiglia di avere almeno 3 agenti di provisioning configurati con il tenant di Azure AD per la disponibilità elevata.

#### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Selezione di app di connettori di provisioning da distribuire

Quando si integrano Workday e Active Directory, sono presenti più sistemi di origine e di destinazione che è necessario tenere presente:

| Sistema di origine | Sistema di destinazione | Note |
| ---------- | ---------- | ---------- |
| Workday | Dominio di Active Directory | Ogni dominio viene considerato come un sistema di destinazione distinto |
| Workday | Tenant di Azure AD | Come richiesto per gli utenti solo cloud |
| Foresta di Active Directory | Tenant di Azure AD | Questo flusso è attualmente gestito da AAD Connect |
| Tenant di Azure AD | Workday | Per il writeback degli indirizzi di posta elettronica |

Per semplificare i flussi di lavoro di provisioning tra Workday e Active Directory, Azure AD fornisce diverse app di connettori di provisioning che è possibile aggiungere dalla raccolta di app di Azure AD:

![Raccolta di app di AAD](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday to Active Directory Provisioning** (Provisioning Workday in Active Directory): questa app semplifica il provisioning degli account utente da Workday in un singolo dominio di Active Directory. Se sono presenti più domini, è possibile aggiungere un'istanza di questa app dalla raccolta di app di Azure AD per ogni dominio di Active Directory in cui è necessario effettuare il provisioning.

* **Workday to Azure AD Provisioning** (Provisioning Workday in Azure AD): mentre AAD Connect è lo strumento da usare per sincronizzare gli utenti di Active Directory in Azure Active Directory, questa app può essere usata per semplificare il provisioning degli utenti solo cloud da Workday a un singolo tenant di Azure Active Directory.

* **Workday Writeback** (Writeback Workday): questa app semplifica il writeback degli indirizzi di posta elettronica degli utenti da Azure Active Directory in Workday.

> [!TIP]
> La normale app "Workday" viene usata per la configurazione del Single Sign-On tra Azure Active Directory e Workday. 

#### <a name="determine-workday-to-ad-user-attribute-mapping-and-transformations"></a>Determinare mapping e trasformazioni degli attributi utente da Workday ad Azure

Prima di configurare il provisioning degli utenti in un dominio di Active Directory, cercare di rispondere alle domande seguenti. Le risposte a queste domande permettono di determinare come devono essere impostati i filtri di ambito e i mapping degli attributi.

* **Di quali utenti in Workday è necessario effettuare il provisioning in questa foresta di Active Directory?**

   * *Esempio: utenti il cui attributo "Company" di Workday contiene il valore "Contoso", e l'attributo "Worker_Type" contiene "Regular"*

* **Come vengono instradati gli utenti alle diverse unità organizzative?**

   * *Esempio: gli utenti vengono instradati alle unità organizzative che corrispondono alla posizione di un ufficio, in base a quanto definito negli attributi "Municipality" e "Country_Region_Reference" di Workday*

* **Come devono essere immessi gli attributi seguenti in Active Directory?**

   * Nome comune (cn)
      * *Esempio: usare il valore di User_ID di Workday, impostato dalle risorse umane*
      
   * ID dipendente (employeeId)
      * *Esempio: usare il valore di Worker_ID di Workday*
      
   * Nome dell'account SAM (sAMAccountName)
      * *Esempio: usare il valore di User_ID di Workday, filtrato tramite un'espressione di provisioning di Azure AD per rimuovere i caratteri non validi*
      
   * Nome dell'entità utente (userPrincipalName)
      * *Esempio: usare il valore di User_ID di Workday, con un'espressione di provisioning di Azure AD per aggiungere un nome di dominio*

* **Come devono essere impostate le corrispondenze degli utenti tra Workday e Active Directory?**

  * *Esempio: gli utenti con un valore di "Worker_ID" di Workday specifico vengono associati agli utenti di Active Directory il cui attributo "employeeID" ha lo stesso valore. Se il valore di Worker_ID non viene trovato in Active Directory, creare un nuovo utente.*
  
* **La foresta di Active Directory contiene già gli ID utente necessari per il funzionamento della logica di corrispondenza?**

  * *Esempio: se questa è una nuova distribuzione di Workday, è assolutamente consigliabile fare in modo che Active Directory sia già popolato con i valori di Worker_ID di Workday corretti (o con il valore dell'ID univoco desiderato) per mantenere la logica di corrispondenza il più semplice possibile.*



Nelle sezioni rimanenti di questa esercitazione verrà descritto come installare e configurare queste speciali app di connettori di provisioning. La scelta delle app da configurare dipende dai sistemi in cui è necessario effettuare il provisioning e dal numero di domini di Active Directory e di tenant Azure AD nell'ambiente in uso.



## <a name="configure-integration-system-user-in-workday"></a>Configurare un utente del sistema di integrazione in Workday

Un requisito comune di tutti i connettori di provisioning Workday è che richiedono le credenziali di un account di integrazione dei sistemi Workday per la connessione all'API Human Resources di Workday. Questa sezione descrive come creare un utente del sistema di integrazione in Workday.

> [!NOTE]
> È possibile evitare di eseguire questa procedura usando un account di amministratore globale di Workday come account di integrazione dei sistemi. Questa soluzione è utilizzabile a scopo dimostrativo, ma non è consigliata per le distribuzioni di produzione.

### <a name="create-an-integration-system-user"></a>Creare un utente del sistema di integrazione

**Per creare un utente di sistema di integrazione, seguire questa procedura:**

1. Accedere al tenant di Workday usando un account amministratore. Nell'**applicazione Workday** immettere "create user" nella casella di ricerca e quindi fare clic su **Create Integration System User** (Crea utente del sistema di integrazione).

    ![Creare un utente](./media/workday-inbound-tutorial/wd_isu_01.png "Creare un utente")
2. Completare l'attività **Create Integration System User** specificando un nome utente e una password per un nuovo utente del sistema di integrazione.  
 * Lasciare l'opzione **Require New Password at Next Sign In (Richiedi nuova password al prossimo accesso)** non selezionata, perché l'accesso dell'utente verrà eseguito a livello di codice.
 * Lasciare l'opzione **Session Timeout Minutes (Minuti di timeout della sessione)** impostata sul valore predefinito 0, in modo da evitare un timeout prematuro delle sessioni dell'utente.
 * Selezionare l'opzione **Do Not Allow UI Sessions** (Non consentire sessioni di interfaccia utente) in quanto aggiunge un livello di sicurezza che impedisce a un utente con la password del sistema di integrazione di accedere a Workday. 

    ![Creare un utente del sistema di integrazione](./media/workday-inbound-tutorial/wd_isu_02.png "Creare un utente del sistema di integrazione")

### <a name="create-a-security-group"></a>Creare un gruppo di sicurezza
In questo passaggio si creerà un gruppo di sicurezza del sistema di integrazione non vincolato in Workday e si assegnerà l'utente creato nel passaggio precedente a questo gruppo.

**Per creare un gruppo di sicurezza, seguire questa procedura:**

1. Immettere "create security group" nella casella di ricerca e quindi fare clic su **Create Security Group**(Crea gruppo di sicurezza).

    ![Creare un gruppo di sicurezza](./media/workday-inbound-tutorial/wd_isu_03.png "Creare un gruppo di sicurezza")
2. Completare l'attività **Creare un gruppo di sicurezza**.  
   * Selezionare **Integration System Security Group (Unconstrained)** (Gruppo di sicurezza del sistema di integrazione - Non vincolato) nell'elenco a discesa **Type of Tenanted Security Group** (Tipo di gruppo di sicurezza con tenant).

    ![Creare un gruppo di sicurezza](./media/workday-inbound-tutorial/wd_isu_04.png "Creare un gruppo di sicurezza")

3. Dopo aver creato il gruppo di sicurezza, verrà visualizzata una pagina in cui sarà possibile assegnare membri al gruppo. Aggiungere il nuovo utente del sistema di integrazione a questo gruppo di sicurezza e selezionare l'ambito dell'organizzazione appropriato.
![Modificare un gruppo di sicurezza](./media/workday-inbound-tutorial/wd_isu_05.png "Modificare un gruppo di sicurezza")
 
### <a name="configure-domain-security-policy-permissions"></a>Configurare le autorizzazioni dei criteri di sicurezza del dominio
In questo passaggio si concedono al gruppo di sicurezza le autorizzazioni dei criteri di sicurezza del dominio per i dati del ruolo di lavoro.

**Per configurare le autorizzazioni dei criteri di sicurezza del dominio:**

1. Immettere **Domain Security Configuration** (Configurazione della sicurezza del dominio) nella casella di ricerca e quindi fare clic sul collegamento **Domain Security Configuration Policies** (Criteri di configurazione della sicurezza del dominio).  

    ![Criteri di sicurezza di dominio](./media/workday-inbound-tutorial/wd_isu_06.png "Criteri di sicurezza di dominio")  
2. Nella casella di testo **Domain** (Dominio) cercare i domini seguenti e aggiungerli al filtro uno alla volta.  
   * *External Account Provisioning* (Provisioning account esterno)
   * *Worker Data: Public Worker Reports* (Dati ruolo di lavoro: report ruoli di lavoro pubblici)
   * *Person Data: Work Contact Information* (Dati ruolo di lavoro: Informazioni contatto di lavoro)
   * *Worker Data: All Positions* (Dati ruolo di lavoro: tutte le posizioni)
   * *Worker Data: Current Staffing Information* (Dati ruolo di lavoro: informazioni correnti del personale)
   * *Worker Data: Business Title on Worker Profile* (Dati ruolo di lavoro: qualifica riportata sul profilo)
 
    ![Criteri di sicurezza di dominio](./media/workday-inbound-tutorial/wd_isu_07.png "Criteri di sicurezza di dominio")  

    ![Criteri di sicurezza di dominio](./media/workday-inbound-tutorial/wd_isu_08.png "Criteri di sicurezza di dominio") 

    Fare clic su **OK**.

3. Nel report che viene visualizzato selezionare i puntini di sospensione (...) visualizzati accanto a **External Account Provisioning** (Provisioning account esterno) e fare clic sulla voce di menu **Domain -> Edit Security Policy Permissions** (Dominio -> Modifica autorizzazioni criteri di sicurezza)

    ![Criteri di sicurezza di dominio](./media/workday-inbound-tutorial/wd_isu_09.png "Criteri di sicurezza di dominio")  

4. Nella pagina **Edit Domain Security Policy Permissions (Modifica autorizzazioni criteri di sicurezza di dominio)** scorrere fino alla sezione **Integration Permissions** (Autorizzazioni di integrazione). Fare clic sul segno "+" per aggiungere il gruppo del sistema di integrazione all'elenco dei gruppi di sicurezza con autorizzazioni di integrazione **Get** e **Put**.

    ![Modificare un'autorizzazione](./media/workday-inbound-tutorial/wd_isu_10.png "Modificare un'autorizzazione")  

5. Fare clic sul segno "+" per aggiungere il gruppo del sistema di integrazione all'elenco dei gruppi di sicurezza con autorizzazioni di integrazione **Get** e **Put**.

    ![Modificare un'autorizzazione](./media/workday-inbound-tutorial/wd_isu_11.png "Modificare un'autorizzazione")  

6. Ripetere i passaggi precedenti da 3 a 5 per ognuno di questi criteri di sicurezza rimanenti:

   | Operazione | Criteri di sicurezza del dominio |
   | ---------- | ---------- | 
   | Get e put | Worker Data: Public Worker Reports (Dati ruolo di lavoro: report ruoli di lavoro pubblici) |
   | Get e put | Person Data: Work Contact Information (Dati ruolo personali: Informazioni contatto di lavoro) |
   | Get | Worker Data: All Positions (Dati ruolo di lavoro: tutte le posizioni) |
   | Get | Worker Data: Current Staffing Information (Dati ruolo di lavoro: informazioni correnti sul personale) |
   | Get | Worker Data: Business Title on Worker Profile (Dati ruolo di lavoro: qualifica riportata sul profilo) |

### <a name="configure-business-process-security-policy-permissions"></a>Configurare le autorizzazioni dei criteri di sicurezza dei processi aziendali
In questo passaggio si concedono al gruppo di sicurezza le autorizzazioni dei criteri di sicurezza dei processi aziendali per i dati del ruolo di lavoro. Questo passaggio è obbligatorio per la configurazione del connettore dell'app Workday Writeback. 

**Per configurare le autorizzazioni dei criteri di sicurezza dei processi aziendali:**

1. Immettere **Business Process Policy** (Criteri di processi aziendali) nella casella di ricerca e quindi fare clic sul collegamento **Edit Business Process Security Policy** (Modifica criteri di sicurezza dei processi aziendali).  

    ![Criteri di sicurezza dei processi aziendali](./media/workday-inbound-tutorial/wd_isu_12.png "Criteri di sicurezza dei processi aziendali")  

2. Nella casella di testo **Business Process Type** (Tipo di processo aziendale) cercare *Contact* (Contatto) e selezionare il processo aziendale **Contact Change** (Modifica contatto), quindi fare clic su **OK**.

    ![Criteri di sicurezza dei processi aziendali](./media/workday-inbound-tutorial/wd_isu_13.png "Criteri di sicurezza dei processi aziendali")  

3. Nella pagina **Edit Business Process Security Policy** (Modifica criteri di sicurezza dei processi aziendali) scorrere fino alla sezione **Maintain Contact Information (Web Service)** (Gestisci informazioni di contatto (servizio Web)).

    ![Criteri di sicurezza dei processi aziendali](./media/workday-inbound-tutorial/wd_isu_14.png "Criteri di sicurezza dei processi aziendali")  

4. Selezionare e aggiungere il nuovo gruppo di sicurezza del sistema di integrazione all'elenco dei gruppi di sicurezza che possono avviare la richiesta di servizi Web. Fare clic su **Done** (Fine). 

    ![Criteri di sicurezza dei processi aziendali](./media/workday-inbound-tutorial/wd_isu_15.png "Criteri di sicurezza dei processi aziendali")  

 
### <a name="activate-security-policy-changes"></a>Attivare le modifiche apportate ai criteri di sicurezza

**Per attivare le modifiche apportate ai criteri di sicurezza, seguire questa procedura:**

1. Immettere "activate" (attiva) nella casella di ricerca e quindi fare clic sul collegamento **Activate Pending Security Policy Changes (Attiva le modifiche in sospeso ai criteri di sicurezza)**.

    ![Attivare](./media/workday-inbound-tutorial/wd_isu_16.png "Attivare") 
2. Avviare l'attività Activate Pending Security Policy Changes (Attiva le modifiche in sospeso ai criteri di sicurezza) immettendo un commento a scopo di controllo e quindi fare clic su **OK**. 

    ![Attivare la sicurezza in sospeso](./media/workday-inbound-tutorial/wd_isu_17.png "Attivare la sicurezza in sospeso")  
1. Completare l'attività nella schermata successiva selezionando la casella di controllo **Confirm (Conferma)** e quindi facendo clic su **OK**.

    ![Attivare la sicurezza in sospeso](./media/workday-inbound-tutorial/wd_isu_18.png "Attivare la sicurezza in sospeso")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Configurazione del provisioning utenti da Workday in Active Directory

Seguire queste istruzioni per configurare il provisioning degli account utente da Workday in ogni dominio di Active Directory nell'ambito dell'integrazione.

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>Parte 1: installare e configurare gli agenti di provisioning locali

Per effettuare il provisioning in Active Directory locale, è necessario installare un agente in un server con .NET Framework 4.7 e versioni successive e accesso in rete ai domini di Active Directory richiesti.

> [!TIP]
> È possibile controllare la versione di .NET Framework nel server seguendo le istruzioni disponibili [qui](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Se il server non dispone di .NET 4.7 o versione successiva, è possibile scaricarlo da [qui](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).  

Dopo aver distribuito .NET 4.7 o versioni successive, sarà possibile scaricare l' **[agente di provisioning locale qui](https://go.microsoft.com/fwlink/?linkid=847801)** e seguire i passaggi indicati di seguito per completare la configurazione dell'agente.

1. Accedere all'istanza di Windows Server in cui si intende installare il nuovo agente.
2. Avviare il programma di installazione dell'agente di provisioning, accettare le condizioni e fare clic sul pulsante **Install** (Installa).
![Schermata di installazione](./media/workday-inbound-tutorial/pa_install_screen_1.png "Schermata di installazione")

3. Al termine dell'installazione verrà avviata la procedura guidata e verrà visualizzata la schermata **Connect Azure AD** (Connetti Azure AD). Fare clic sul pulsante **Authenticate** (Autentica) per connettersi all'istanza di Azure AD.
![Connect Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Connect Azure AD")

4. Eseguire l'autenticazione all'istanza di Azure AD con credenziali di amministratore globale. 
![Autenticazione amministratore](./media/workday-inbound-tutorial/pa_install_screen_3.png "Autenticazione amministratore")

5. Al termine dell'autenticazione con Azure AD verrà visualizzata la schermata **Connect Active Directory** (Connetti Active Directory). In questo passaggio immettere il nome di dominio Active Directory e fare clic sul pulsante **Add Directory** (Aggiungi directory).
![Add Directory](./media/workday-inbound-tutorial/pa_install_screen_4.png "Add Directory")

6. Verrà ora richiesto di immettere le credenziali necessarie per connettersi al dominio AD. Nella stessa schermata è possibile usare **Select domain controller priority** (Seleziona priorità controller di dominio) per specificare i controller di dominio che l'agente deve usare per l'invio di richieste di provisioning.
![Credenziali del dominio](./media/workday-inbound-tutorial/pa_install_screen_5.png "Credenziali del dominio")

7. Dopo aver configurato il dominio, il programma di installazione mostrerà un elenco di domini configurati. In questa schermata è possibile ripetere i passaggi 5 e 6 per aggiungere più domini o fare clic su **Next** (Avanti) per procedere alla registrazione dell'agente. 
![Domini configurati](./media/workday-inbound-tutorial/pa_install_screen_6.png "Domini configurati")

   > [!NOTE]
   > In presenza di più domini di Active Directory (ad esempio na.contoso.com, emea.contoso.com), aggiungere singolarmente ogni dominio all'elenco. L'aggiunta del solo dominio padre (ad esempio, contoso.com) non è sufficiente. È consigliabile registrare ogni dominio figlio con l'agente. 

8. Esaminare i dettagli di configurazione e fare clic su **Confirm** (Conferma) per registrare l'agente. 
![Schermata di conferma](./media/workday-inbound-tutorial/pa_install_screen_7.png "Schermata di conferma")

9. La Configurazione guidata mostra lo stato di avanzamento della registrazione dell'agente.
![Registrazione dell'agente](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registrazione dell'agente")

10. Al termine della registrazione dell'agente sarà possibile fare clic su **Exit** (Esci) per uscire dalla procedura guidata. 
![Schermata Exit](./media/workday-inbound-tutorial/pa_install_screen_9.png "Schermata Exit")

11. Verificare l'installazione dell'agente e accertarsi che sia in esecuzione aprendo lo snap-in "Services". Cercare il servizio denominato "Microsoft Azure AD Connect Provisioning Agent" ![Services](./media/workday-inbound-tutorial/services.png)  


**Risoluzione dei problemi dell'agente**

Il [registro eventi di Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) nel computer Windows Server che ospita l'agente contiene gli eventi per tutte le operazioni eseguite dall'agente. Per visualizzare questi eventi:
    
1. Aprire **Eventvwr.msc**.
2. Selezionare **Registri di Windows > Applicazione**.
3. Visualizzare tutti gli eventi registrati nell'origine **AAD.Connect.ProvisioningAgent**. 
4. Controllare errori e avvisi.

    
### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 2: aggiungere l'app del connettore di provisioning e creare la connessione a Workday

**Per configurare il provisioning da Workday in Active Directory:**

1.  Passare a <https://portal.azure.com>.

2.  Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3.  Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4.  Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5.  Cercare **Workday Provisioning to Active Directory** (Provisioning Workday in Active Directory) e aggiungere tale app dalla raccolta.

6.  Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**

7.  Impostare **Modalità di** **provisioning** su **Automatico**

8.  Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore**: immettere il nome utente dell'account del sistema di integrazione Workday, aggiungendo il nome di dominio del tenant. **Dovrebbe essere simile a: username@contoso4**

   * **Password di amministratore:** immettere la password dell'account del sistema di integrazione Workday

   * **URL tenant:** immettere l'URL dell'endpoint dei servizi Web Workday per il tenant. Dovrebbe essere simile a: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, dove contoso4 è sostituito dal nome del tenant corretto e wd3-impl è sostituito dalla stringa di ambiente corretta.

   * **Foresta di Active Directory:** il "nome" del dominio di Active Directory, come registrato con l'agente. In genere si tratta di una stringa simile a: *contoso.com*

   * **Contenitore di Active Directory:** immettere il nome distinto del contenitore in cui l'agente deve creare gli account utente per impostazione predefinita. 
        Esempio: *OU=Standard Users,OU=Users,DC=contoso,DC=test*
> [!NOTE]
> Questa impostazione deve essere usata per la creazione degli account utente solo se l'attributo *parentDistinguishedName* non è configurato nel mapping degli attributi. Questa impostazione non viene usata per la ricerca di utenti o per le operazioni di aggiornamento. L'intero sottoalbero del dominio rientra nell'ambito dell'operazione di ricerca.
   * **Indirizzo di posta elettronica per le notifiche:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo "Invia una notifica di posta elettronica in caso di errore".
> [!NOTE]
> Il servizio di provisioning di Azure AD invia una notifica di posta elettronica se il processo di provisioning entra in uno stato di [quarantena](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine).

   * Fare clic sul pulsante **Test connessione**. Se il test della connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. In caso contrario, verificare che le credenziali di Workday e Active Directory configurate nel programma di installazione dell'agente siano valide.

![Portale di Azure](./media/workday-inbound-tutorial/wd_1.png)

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: configurare i mapping degli attributi 

In questa sezione verrà configurato il flusso dei dati utente da Workday in Active Directory.

1.  Nella scheda Provisioning, in **Mapping**, fare clic su **Synchronize Workday Workers to OnPremises** (Sincronizza lavoratori Workday in locale).

2.  Nel campo **Ambito dell'oggetto di origine** è possibile selezionare i set di utenti in Workday inclusi nell'ambito per il provisioning in Active Directory, definendo un set di filtri basati su attributi. L'ambito predefinito è "tutti gli utenti in Workday". Filtri di esempio:

   * Esempio: ambito per gli utenti con ID lavoratore compreso tra 1000000 e    2000000

      * Attributo: WorkerID

      * Operator: REGEX Match (Corrispondenza REGEX)

      * Valore: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Esempio: solo i dipendenti, senza i lavoratori occasionali 

      * Attributo: EmployeeID

      * Operator: IS NOT NULL (NON È NULL)

3.  Nel campo **Azioni oggetto di destinazione** è possibile applicare un filtro a livello globale per le azioni di cui è consentita l'esecuzione in Active Directory. **Creazione** e **Aggiornamento** sono le più comuni.

4.  Nella sezione **Mapping attributi** è possibile definire il mapping dei singoli attributi di Workday agli attributi di Active Directory.

5. Fare clic su un mapping di attributi esistente per aggiornarlo oppure fare clic su **Aggiungi nuovo mapping** nella parte inferiore della schermata per aggiungere nuovi mapping. Il mapping di un singolo attributo supporta queste proprietà:

      * **Tipo di mapping**

         * **Diretto**: scrive il valore dell'attributo di Workday nell'attributo di Active Directory, senza modifiche

         * **Costante**: scrive un valore stringa costante statico nell'attributo di Active Directory

         * **Espressione**: consente di scrivere un valore personalizzato per l'attributo di Active Directory, in base a uno o più attributi di Workday. [Per altre informazioni, vedere questo articolo sulle espressioni](../manage-apps/functions-for-customizing-application-data.md).

      * **Attributo di origine**: l'attributo utente in Workday. Se l'attributo che si sta cercando non è presente, vedere [Personalizzazione dell'elenco di attributi utente di Workday](#customizing-the-list-of-workday-user-attributes).

      * **Valore predefinito**: facoltativo. Se l'attributo di origine ha un valore vuoto, il mapping eseguirà la scrittura di questo valore.
            Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

      * **Attributo di destinazione**: l'attributo utente in Active Directory.

      * **Abbina gli oggetti in base a questo attributo**: specifica se questo mapping deve essere usato per l'identificazione univoca degli utenti tra Workday e Active Directory. In genere, è impostato sul campo ID lavoratore per Workday, che solitamente è associato a uno degli attributi ID dipendente in Active Directory.

      * **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Se sono presenti più attributi, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.

      * **Applica questo mapping**
       
         * **Sempre**: applica il mapping sia all'azione di creazione che all'azione di aggiornamento dell'utente

         * **Only during creation** (Solo durante la creazione): applica il mapping solo alle azioni di creazione dell'utente

6. Per salvare i mapping, fare clic su **Salva** nella parte superiore della sezione Mapping attributi.

![Portale di Azure](./media/workday-inbound-tutorial/WD_2.PNG)

**Di seguito sono riportati alcuni esempi di mapping degli attributi tra Workday e Active Directory, con alcune espressioni comuni**

-   L'espressione che esegue il mapping all'attributo parentDistinguishedName viene usata per effettuare il provisioning di un utente in diverse unità organizzative in base a uno o più attributi di origine di Workday. L'esempio qui colloca gli utenti in diverse unità organizzative in base alla città in cui si trovano.

-   L'attributo userPrincipalName in Active Directory viene generato tramite la concatenazione dell'ID utente di Workday con un suffisso di dominio

-   [La documentazione sulla scrittura di espressioni è disponibile qui](../manage-apps/functions-for-customizing-application-data.md). Sono inclusi alcuni esempi su come rimuovere i caratteri speciali.

  
| ATTRIBUTO DI WORKDAY | ATTRIBUTO DI ACTIVE DIRECTORY |  ID CORRISPONDENTE? | CREAZIONE / AGGIORNAMENTO |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Sì** | Scritto solo al momento della creazione |
| **UserID**    |  cn    |   |   Scritto solo al momento della creazione |
| **Join("@", [UserID], "contoso.com")**   | userPrincipalName     |     | Scritto solo al momento della creazione 
| **Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Scritto solo al momento della creazione |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | Creazione e aggiornamento |
| **FirstName**   | givenName       |     |    Creazione e aggiornamento |
| **LastName**   |   sn   |     |  Creazione e aggiornamento |
| **PreferredNameData**  |  displayName |     |   Creazione e aggiornamento |
| **Company**         | company   |     |  Creazione e aggiornamento |
| **SupervisoryOrganization**  | department  |     |  Creazione e aggiornamento |
| **ManagerReference**   | manager  |     |  Creazione e aggiornamento |
| **BusinessTitle**   |  title     |     |  Creazione e aggiornamento | 
| **AddressLineData**    |  streetAddress  |     |   Creazione e aggiornamento |
| **Municipality**   |   l   |     | Creazione e aggiornamento |
| **CountryReferenceTwoLetter**      |   co |     |   Creazione e aggiornamento |
| **CountryReferenceTwoLetter**    |  c  |     |         Creazione e aggiornamento |
| **CountryRegionReference** |  st     |     | Creazione e aggiornamento |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Creazione e aggiornamento |
| **PostalCode**  |   postalCode  |     | Creazione e aggiornamento |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Creazione e aggiornamento |
| **Fax**      | facsimileTelephoneNumber     |     |    Creazione e aggiornamento |
| **Mobile**  |    mobile       |     |       Creazione e aggiornamento |
| **LocalReference** |  preferredLanguage  |     |  Creazione e aggiornamento |                                               
| **Switch(\[Municipality\], "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", “London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  Creazione e aggiornamento |
  


### <a name="part-4-start-the-service"></a>Parte 4: avviare il servizio
Dopo aver completato le parti 1-3, è possibile avviare il servizio di provisioning nel portale di Azure.

1.  Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

2. Fare clic su **Save**.

3. Verrà avviata la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti in Workday.

4. In qualsiasi momento è possibile controllare la scheda **Log di controllo** nel portale di Azure per vedere le azioni che sono state eseguite dal servizio di provisioning. I log di controllo elencano tutti i singoli eventi di sincronizzazione eseguiti dal servizio di provisioning, ad esempio quali utenti vengono letti da Workday e successivamente aggiunti o aggiornati in Active Directory. **[Vedere la guida alla creazione di report per il provisioning per istruzioni dettagliate su come leggere i log di controllo](../manage-apps/check-status-user-account-provisioning.md)**

1.  Controllare il [registro eventi di Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) nel computer Windows Server che ospita l'agente per eventuali nuovi errori o avvisi. Questi eventi possono essere visualizzati avviando **Eventvwr.msc** sul server e selezionando **Registri di Windows > Applicazione**. Tutti i messaggi correlati al provisioning vengono registrati sotto **AADSyncAgent**.

6. Al termine verrà scritto un report di riepilogo di controllo nella scheda **Provisioning**, come illustrato di seguito.

![Portale di Azure](./media/workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Configurazione del provisioning utenti in Azure Active Directory
La configurazione del provisioning in Azure Active Directory dipende dei requisiti di provisioning, come descritto nella tabella seguente.

| Scenario | Soluzione |
| -------- | -------- |
| **È necessario eseguire il provisioning degli utenti in Active Directory e Azure AD** | Usare  **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** |
| **È necessario eseguire il provisioning degli utenti solo in Active Directory** | Usare  **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** |
| **È necessario eseguire il provisioning degli utenti solo in Azure AD (solo cloud)** | Usare l'app **Workday to Azure Active Directory provisioning** (Provisioning Workday in Azure Active Directory) nella raccolta di app |

Per istruzioni sulla configurazione di Azure AD Connect, vedere la [documentazione di Azure AD Connect](../hybrid/whatis-hybrid-identity.md).

Nelle sezioni seguenti viene descritta la configurazione di una connessione tra Workday e Azure AD per il provisioning degli utenti solo cloud.

> [!IMPORTANT]
> Eseguire la procedura seguente solo se sono presenti utenti solo cloud, di cui è necessario eseguire il provisioning in Azure AD e non in Active Directory locale.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: aggiungere l'app del connettore di provisioning in Azure AD e creare la connessione a Workday

**Per configurare il provisioning da Workday ad Azure Active Directory per gli utenti solo cloud:**

1.  Passare a <https://portal.azure.com>.

2.  Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3.  Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4.  Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5.  Cercare **Workday to Azure AD provisioning** (Provisioning Workday in Azure AD) e aggiungere tale app dalla raccolta.

6.  Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**

7.  Impostare **Modalità di** **provisioning** su **Automatico**

8.  Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore**: immettere il nome utente dell'account del sistema di integrazione Workday, aggiungendo il nome di dominio del tenant. Dovrebbe essere simile a: username@contoso4

   * **Password di amministratore:** immettere la password dell'account del sistema di integrazione Workday

   * **URL tenant:** immettere l'URL dell'endpoint dei servizi Web Workday per il tenant. Dovrebbe essere simile a: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, dove contoso4 è sostituito dal nome del tenant corretto e wd3-impl è sostituito dalla stringa di ambiente corretta. Se l'URL non è noto, determinare l'URL corretto da usare insieme al proprio partner di integrazione Workday o al supporto tecnico.

   * **Indirizzo di posta elettronica per le notifiche:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo "Invia una notifica di posta elettronica in caso di errore".

   * Fare clic sul pulsante **Test connessione**.

   * Se il test della connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. In caso contrario, verificare che l'URL e le credenziali per Workday siano validi in Workday.

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: configurare i mapping degli attributi 

In questa sezione verrà configurato il flusso dei dati utente da Workday in Azure Active Directory per gli utenti solo cloud.

1. Nella scheda Provisioning, in **Mapping**, fare clic su **Synchronize Workers to Azure AD** (Sincronizza lavoratori in Azure AD).

2. Nel campo **Ambito dell'oggetto di origine** è possibile selezionare i set di utenti in Workday inclusi nell'ambito per il provisioning in Azure AD, definendo un set di filtri basati su attributi. L'ambito predefinito è "tutti gli utenti in Workday". Filtri di esempio:

   * Esempio: ambito per gli utenti con ID lavoratore compreso tra 1000000 e    2000000

      * Attributo: WorkerID

      * Operator: REGEX Match (Corrispondenza REGEX)

      * Valore: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Esempio: solo i lavoratori occasionali, senza i dipendenti

      * Attributo: ContingentID

      * Operator: IS NOT NULL (NON È NULL)

3. Nel campo **Azioni oggetto di destinazione** è possibile applicare un filtro a livello globale per le azioni di cui è consentita l'esecuzione in Azure AD. **Creazione** e **Aggiornamento** sono le più comuni.

4. Nella sezione **Mapping attributi** è possibile definire il mapping dei singoli attributi di Workday agli attributi di Active Directory.

5. Fare clic su un mapping di attributi esistente per aggiornarlo oppure fare clic su **Aggiungi nuovo mapping** nella parte inferiore della schermata per aggiungere nuovi mapping. Il mapping di un singolo attributo supporta queste proprietà:

   * **Tipo di mapping**

      * **Diretto**: scrive il valore dell'attributo di Workday nell'attributo di Active Directory, senza modifiche

      * **Costante**: scrive un valore stringa costante statico nell'attributo di Active Directory

      * **Espressione**: consente di scrivere un valore personalizzato per l'attributo di Active Directory, in base a uno o più attributi di Workday. [Per altre informazioni, vedere questo articolo sulle espressioni](../manage-apps/functions-for-customizing-application-data.md).

   * **Attributo di origine**: l'attributo utente in Workday. Se l'attributo che si sta cercando non è presente, vedere [Personalizzazione dell'elenco di attributi utente di Workday](#customizing-the-list-of-workday-user-attributes).

   * **Valore predefinito**: facoltativo. Se l'attributo di origine ha un valore vuoto, il mapping eseguirà la scrittura di questo valore.
            Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

   * **Attributo di destinazione**: l'attributo utente in Azure AD.

   * **Abbina gli oggetti in base a questo attributo**: specifica se questo mapping deve essere usato per l'identificazione univoca degli utenti tra Workday e Azure AD. In genere, è impostato sul campo ID lavoratore per Workday, che solitamente è associato all'attributo ID dipendente (nuovo) o a un attributo di estensione in Azure AD.

   * **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Se sono presenti più attributi, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.

   * **Applica questo mapping**

     * **Sempre**: applica il mapping sia all'azione di creazione che all'azione di aggiornamento dell'utente

     * **Only during creation** (Solo durante la creazione): applica il mapping solo alle azioni di creazione dell'utente

6. Per salvare i mapping, fare clic su **Salva** nella parte superiore della sezione Mapping attributi.

### <a name="part-3-start-the-service"></a>Parte 3: avviare il servizio
Dopo aver completato le parti 1-2, è possibile avviare il servizio di provisioning.

1. Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

2. Fare clic su **Save**.

3. Verrà avviata la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti in Workday.

4. I singoli eventi di sincronizzazione possono essere visualizzati nella scheda **Log di controllo**. **[Vedere la guida alla creazione di report per il provisioning per istruzioni dettagliate su come leggere i log di controllo](../manage-apps/check-status-user-account-provisioning.md)**

5. Al termine verrà scritto un report di riepilogo di controllo nella scheda **Provisioning**, come illustrato di seguito.

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Configurazione del writeback degli indirizzi di posta elettronica in Workday
Seguire queste istruzioni per configurare il writeback degli indirizzi di posta elettronica degli utenti da Azure Active Directory in Workday.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: aggiungere l'app del connettore di provisioning e creare la connessione a Workday

**Per configurare il connettore di Workday Writeback:**

1. Passare a <https://portal.azure.com>.

2. Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3. Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4. Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5. Cercare **Workday Writeback** (Writeback Workday) e aggiungere tale applicazione dalla raccolta.

6. Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**

7. Impostare **Modalità di** **provisioning** su **Automatico**

8. Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore**: immettere il nome utente dell'account del sistema di integrazione Workday, aggiungendo il nome di dominio del tenant. Dovrebbe essere simile a: username@contoso4

   * **Password di amministratore:** immettere la password dell'account del sistema di integrazione Workday

   * **URL tenant:** immettere l'URL dell'endpoint dei servizi Web Workday per il tenant. Dovrebbe essere simile a: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, dove contoso4 è sostituito dal nome del tenant corretto e wd3-impl è sostituito dalla stringa di ambiente corretta (se necessario).

   * **Indirizzo di posta elettronica per le notifiche:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo "Invia una notifica di posta elettronica in caso di errore".

   * Fare clic sul pulsante **Test connessione**. Se il test della connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. In caso contrario, verificare che l'URL e le credenziali per Workday siano validi in Workday.

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: configurare i mapping degli attributi 

In questa sezione verrà configurato il flusso dei dati utente da Workday in Active Directory.

1. Nella scheda Provisioning, in **Mapping**, fare clic su **Synchronize Azure AD Users to Workday** (Sincronizza utenti Azure AD in Workday).

2. Nel campo **Ambito dell'oggetto di origine** è possibile scegliere di filtrare i set di utenti di Azure Active Directory per cui eseguire il writeback degli indirizzi di posta elettronica in Workday. L'ambito predefinito è "tutti gli utenti in Azure AD". 

3. Nella sezione **Mapping degli attributi**, aggiornare l'ID corrispondente per indicare l'attributo in Azure Active Directory in cui è memorizzato l'ID del ruolo di lavoro Workday o l'ID del dipendente. Un metodo comune per garantire la corrispondenza è sincronizzare l'ID lavoratore o l'ID dipendente di Workday in extensionAttribute1-15 in Azure AD e quindi usare questo attributo in Azure AD per associare gli utenti in Workday. 

4. Per salvare i mapping, fare clic su **Salva** nella parte superiore della sezione Mapping attributi.

### <a name="part-3-start-the-service"></a>Parte 3: avviare il servizio
Dopo aver completato le parti 1-2, è possibile avviare il servizio di provisioning.

1. Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

2. Fare clic su **Save**.

3. Verrà avviata la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti in Workday.

4. I singoli eventi di sincronizzazione possono essere visualizzati nella scheda **Log di controllo**. **[Vedere la guida alla creazione di report per il provisioning per istruzioni dettagliate su come leggere i log di controllo](../manage-apps/check-status-user-account-provisioning.md)**

5. Al termine verrà scritto un report di riepilogo di controllo nella scheda **Provisioning**, come illustrato di seguito.

## <a name="customizing-the-list-of-workday-user-attributes"></a>Personalizzazione dell'elenco di attributi utente di Workday
Le app di provisioning di Workday per Active Directory e Azure AD includono entrambe un elenco predefinito di attributi utente di Workday tra cui scegliere. Questi elenchi tuttavia non sono esaustivi. Workday supporta molte centinaia di attributi utente possibili, che possono essere standard o univoci per il tenant di Workday. 

Il servizio di provisioning di Azure AD consente di personalizzare l'elenco o un attributo di Workday per includere tutti gli attributi esposti nell'operazione [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) dell'API Human Resources.

A tale scopo, è necessario usare [Workday Studio](https://community.workday.com/studio-download) per estrarre le espressioni XPath che rappresentano gli attributi che si vuole usare e quindi aggiungerli alla configurazione del provisioning usando l'editor di attributi avanzato nel portale di Azure.

**Per recuperare un'espressione XPath per un attributo utente di Workday:**

1. Scaricare e installare [Workday Studio](https://community.workday.com/studio-download). Per accedere al programma di installazione, è necessario un account della community di Workday.

2. Scaricare il file WDSL Human_Resources Workday dall'URL seguente: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Avviare Workday Studio.

4. Sulla barra dei comandi selezionare l'opzione **Workday > Test Web Service in Tester** (Workday > Testa servizio Web nel tester).

5. Selezionare **External** (Esterno) e quindi selezionare il file Human_Resources WSDL scaricato al passaggio 2.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Impostare il campo **Location** (Percorso) su `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, ma sostituendo "IMPL-CC" con il tipo di istanza effettivo e "TENANT" con il vero nome del tenant.

7. Impostare **Operation** (Operazione) su **Get_Workers**

8.  Fare clic sul piccolo collegamento **configure** (configura) sotto i riquadri relativi a richiesta e risposta per impostare le credenziali di Workday. Selezionare **Authentication** (Autenticazione) e quindi immettere nome utente e password per l'account di sistema di integrazione di Workday. Assicurarsi di formattare il nome utente come name@tenant, lasciando l'opzione **WS-Security UsernameToken** (Oggetto UsernameToken WS-Security) selezionata.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Selezionare **OK**.

10. Nel riquadro **Request** (Richiesta) incollare il codice XML sottostante e impostare **Employee_ID** sull'ID del dipendente di un utente reale nel tenant di Workday. Selezionare un utente per il quale l'attributo da estrarre sia popolato.

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
            <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. Fare clic su **Send Request** (Invia richiesta) (freccia verde) per eseguire il comando. Se il comando ha esito positivo, la risposta verrà visualizzata nel riquadro **Response** (Risposta). Controllare la risposta per assicurarsi che contenga i dati dell'ID utente immesso e non un errore.

12. In caso di esito positivo, copiare il codice XML dal riquadro **Response** (Risposta) e salvarlo come file XML.

13. Sulla barra dei comandi di Workday Studio selezionare **File > Open File** (File > Apri file) e aprire il file XML salvato. Il file verrà aperto nell'editor XML di Workday Studio.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. Nell'albero di file, spostarsi attraverso **/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker** per ritrovare i dati dell'utente. 

15. In **wd: Worker** trovare l'attributo da aggiungere e selezionarlo.

16. Copiare l'espressione XPath per l'attributo selezionato dal campo **Document Path** (Percorso documento).

1. Rimuovere il prefisso **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** dall'espressione copiata.

18. Se l'ultimo elemento dell'espressione copiata è un nodo (esempio: "/ wd: Birth_Date"), accodare **/text ()** alla fine dell'espressione. Ciò non è necessario se l'ultimo elemento è un attributo (ad esempio: "/@wd: type").

19. Il risultato dovrebbe essere simile a `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Questo è ciò che verrà copiato nel portale di Azure.


**Per aggiungere l'attributo utente Workday personalizzato alla configurazione del provisioning:**

1. Avviare il [portale di Azure](https://portal.azure.com) e passare alla sezione Provisioning dell'applicazione di provisioning di Workday, come descritto in precedenza in questa esercitazione.

2. Impostare **Stato del provisioning** su **No** e selezionare **Salva**. In questo modo, le modifiche verranno applicate solo quando si è pronti.

3. In **Mapping** selezionare **Synchronize Workers to OnPremises** (Sincronizza ruoli di lavoro con ambiente locale) o **Synchronize Workers to Azure AD** (Sincronizza ruoli di lavoro con Azure AD).

4. Scorrere fino alla parte inferiore della schermata successiva e selezionare **Mostra opzioni avanzate**.

5. Selezionare **Modifica elenco attributi per Workday**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Scorrere fino alla fine dell'elenco di attributi, dove si trovano i campi di input.

7. Per **Nome** immettere un nome visualizzato per l'attributo.

8. Per **Tipo** selezionare il tipo appropriato per l'attributo (il più comune è **String**).

9. Per **Espressione API** immettere l'espressione XPath copiata da Workday Studio. Esempio: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Selezionare **Aggiungi attributo**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Selezionare **Salva** sopra e quindi **Sì** nella finestra di dialogo. Chiudere la schermata Mapping attributi, se è ancora aperta.

12. Tornando nella scheda **Provisioning** selezionare di nuovo **Synchronize Workers to OnPremises** (Sincronizza ruoli di lavoro con ambiente locale) o **Synchronize Workers to Azure AD** (Sincronizza ruoli di lavoro con Azure AD).

13. Selezionare **Aggiungi nuovo mapping**.

14. Il nuovo attributo dovrebbe ora essere visualizzato nell'elenco **Attributo di origine**.

15. Aggiungere un mapping per il nuovo attributo in base alle esigenze.

16. Al termine, ricordarsi di impostare **Stato del provisioning** di nuovo su **Sì** e salvare.

## <a name="known-issues"></a>Problemi noti

* La scrittura di dati nell’attributo utente thumbnailPhoto in Active Directory locale non è attualmente supportato.

* Il connettore "Da Workday ad Azure AD" non è attualmente supportato sui tenant Azure AD in cui AAD Connect è abilitato.  

## <a name="managing-personal-data"></a>Gestione dei dati personali

La soluzione di provisioning di Workday per Active Directory richiede l'installazione di un agente di sincronizzazione in un server aggiunto a un dominio. Questo agente crea i log nel registro eventi di Windows che può contenere informazioni personali.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
* [Informazioni su come configurare l'accesso Single Sign-On tra Workday e Azure Active Directory](workday-tutorial.md)
* [Informazioni sull'integrazione di altre applicazioni SaaS con Azure Active Directory](tutorial-list.md)

