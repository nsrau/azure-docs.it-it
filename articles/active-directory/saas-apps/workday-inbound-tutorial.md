---
title: 'Esercitazione: configurare Workday per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Workday.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/19/2019
ms.author: chmutali
ms.openlocfilehash: 913304884867751e3e4c929ce6f4cf8819c15ef2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825545"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Esercitazione: Configurare Workday per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire per importare profili di ruoli di lavoro da Workday in Active Directory e Azure Active Directory, con il writeback facoltativo dell'indirizzo di posta elettronica in Workday.

## <a name="overview"></a>Panoramica

Il [servizio di provisioning utenti di Azure Active Directory](../manage-apps/user-provisioning.md) si integra con l'[API Human Resources di Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) per il provisioning degli account utente. Azure AD usa questa connessione per consentire i flussi di lavoro di provisioning utenti seguenti:

* **Provisioning degli utenti in Active Directory** - effettuare il provisioning di set selezionati di utenti da Workday in uno o più domini di Active Directory.

* **Provisioning degli utenti solo cloud su Azure Active Directory** - Negli scenari in cui non viene utilizzato Active Directory locale, il provisioning degli utenti può essere effettuato direttamente da Workday ad Azure Active Directory utilizzando il servizio di provisioning degli utenti Azure AD.

* **Writeback degli indirizzi di posta elettronica in Workday** - il servizio di provisioning utenti di Azure AD può eseguire la scrittura in Workday degli indirizzi di posta elettronica degli utenti di Azure AD.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Quali scenari di risorse umane è in grado di gestire?

I flussi di lavoro di provisioning utenti di Workday supportati dal servizio di provisioning utenti di Azure AD consentono l'automazione dei seguenti scenari di gestione delle risorse umane e del ciclo di vita delle identità:

* **Assunzione di nuovi dipendenti**: quando viene aggiunto un nuovo dipendente a Workday, viene creato automaticamente un account utente in Active Directory, Azure Active Directory e, facoltativamente, in Office 365 e [altre applicazioni SaaS supportate da Azure AD](../manage-apps/user-provisioning.md), con il writeback dell'indirizzo di posta elettronica in Workday.

* **Aggiornamenti di attributi e profili dei dipendenti**: se il record di un dipendente viene aggiornato in Workday (ad esempio, il nome, il titolo o il manager), il relativo account utente verrà aggiornato automaticamente in Active Directory, Azure Active Directory e, facoltativamente, in Office 365 e [altre applicazioni SaaS supportate da Azure AD](../manage-apps/user-provisioning.md).

* **Eliminazione di dipendenti**: quando un dipendente viene eliminato in Workday, il relativo account utente viene disabilitato automaticamente in Active Directory, Azure Active Directory e, facoltativamente, in Office 365 e [altre applicazioni SaaS supportate da Azure AD](../manage-apps/user-provisioning.md).

* **Riassunzioni di dipendenti** - quando un dipendente viene nuovamente aggiunto in Workday, il relativo account utente precedente può essere automaticamente riattivato o sottoposto di nuovo a provisioning (a seconda delle proprie preferenze) in Active Directory, Azure Active Directory e, facoltativamente, in Office 365 e [altre applicazioni SaaS supportate da Azure AD](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Per chi è più adatta questa soluzione di provisioning utenti?

Questa soluzione di provisioning utenti Workday è attualmente disponibile in anteprima pubblica e idealmente è la più appropriata per:

* Organizzazioni che desiderano una soluzione predefinita basata sul cloud per il provisioning utenti Workday

* Organizzazioni che richiedono il provisioning utenti diretto da Workday ad Active Directory o ad Azure Active Directory

* Organizzazioni che richiedono che il provisioning utenti venga effettuato tramite i dati ottenuti dal modulo Workday HCM. Vedere [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Organizzazioni che richiedono l'aggiunta o lo spostamento degli utenti o che devono lasciare gli utenti sincronizzati con uno o più domini, foreste o unità organizzative di Active Directory solo in base alle informazioni sulle modifiche rilevate nel modulo Workday HCM. Vedere [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Organizzazioni che usano Office 365 per la posta elettronica

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

Prima di avviare l'integrazione di Workday, verificare i prerequisiti riportati di seguito e leggere le seguenti linee guida su come associare gli attuali requisiti per l'architettura Active Directory e il provisioning utenti con le soluzioni fornite da Azure Active Directory. Un [piano di distribuzione](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) completo con fogli di lavoro di pianificazione è disponibile anche per agevolare la collaborazione con i partner di integrazione Workday e le parti interessate delle risorse umane.

Questa sezione contiene gli aspetti di pianificazione seguenti:

* [Prerequisiti](#prerequisites)
* [Selezione di app di connettori di provisioning da distribuire](#selecting-provisioning-connector-apps-to-deploy)
* [Pianificazione della distribuzione dell'agente di provisioning Azure AD Connect](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integrazione con più domini di Active Directory](#integrating-with-multiple-active-directory-domains)
* [Pianificazione di mapping e trasformazioni degli attributi utente da Workday ad Active Directory](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Una sottoscrizione di Azure AD Premium P1 o di livello superiore valida con accesso di amministratore globale
* Un tenant per l'implementazione di Workday a scopo di test e integrazione
* Autorizzazioni di amministratore in Workday per creare un utente di integrazione dei sistemi e apportare modifiche ai dati dei dipendenti di prova a scopo di test
* Per il provisioning utenti in Active Directory, è necessario un server con Windows Server 2012 o versione successiva con il runtime .NET 4.7.1 o versione successiva per ospitare l'[agente di provisioning locale](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) per la sincronizzazione utenti tra Active Directory e Azure AD

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Selezione di app di connettori di provisioning da distribuire

Per semplificare i flussi di lavoro di provisioning tra Workday e Active Directory, Azure AD fornisce diverse app di connettori di provisioning che è possibile aggiungere dalla raccolta di app di Azure AD:

![Raccolta di app di AAD](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday to Active Directory Provisioning** - questa app semplifica il provisioning degli account utente da Workday in un singolo dominio di Active Directory. Se sono presenti più domini, è possibile aggiungere un'istanza di questa app dalla raccolta di app di Azure AD per ogni dominio di Active Directory in cui è necessario effettuare il provisioning.

* **Workday to Azure AD Provisioning** - mentre AAD Connect è lo strumento da usare per sincronizzare gli utenti di Active Directory in Azure Active Directory, questa app può essere usata per semplificare il provisioning utenti solo cloud da Workday a un singolo tenant di Azure Active Directory.

* **Workday Writeback** (Writeback Workday): questa app semplifica il writeback degli indirizzi di posta elettronica degli utenti da Azure Active Directory in Workday.

> [!TIP]
> La normale app "Workday" viene usata per la configurazione del Single Sign-On tra Azure Active Directory e Workday.

Usare il diagramma di flusso decisionale seguente per identificare le app di provisioning di Workday attinenti allo scenario.
    ![Diagramma di flusso decisionale](./media/workday-inbound-tutorial/wday_app_flowchart.png "Diagramma di flusso decisionale")

Usare il sommario per passare alla sezione rilevante di questa esercitazione.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Pianificazione della distribuzione dell'agente di provisioning Azure AD Connect

> [!NOTE]
> Questa sezione è rilevante solo se si prevede di distribuire Workday all'app Workday to Active Directory User Provisioning. È possibile ignorare questo passaggio se si distribuisce l'app Workday Writeback o Workday to Azure AD User Provisioning.

La soluzione di provisioning utenti da Workday ad AD richiede la distribuzione di uno o più agenti di provisioning in server che eseguono Windows 2012 R2 o versioni successive con almeno 4 GB di RAM e runtime .NET 4.7.1 e versioni successive. Valutare le considerazioni seguenti prima di installare l'agente di provisioning:

* Assicurarsi che il server host che esegue l'agente di provisioning abbia accesso in rete al dominio di Active Directory di destinazione
* La configurazione guidata dell'agente di provisioning registra l'agente con il tenant di Azure AD e il processo di registrazione richiede l'accesso a *.msappproxy.net tramite la porta 443. Assicurarsi che le regole del firewall in uscita siano in grado di consentire questa comunicazione. L'agente supporta la [configurazione proxy HTTPS in uscita](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* L'agente di provisioning usa un account del servizio per comunicare con i domini locali di Active Directory. Prima dell'installazione dell'agente, è consigliabile creare un account del servizio con autorizzazioni di amministratore di dominio sulle proprietà utente e una password senza scadenza.  
* Durante la configurazione dell'agente di provisioning è possibile selezionare i controller di dominio che devono gestire le richieste di provisioning. In presenza di vari controller di dominio distribuiti geograficamente, installare l'agente di provisioning nello stesso sito del controller di dominio preferito per migliorare l'affidabilità e le prestazioni della soluzione end-to-end
* Per la disponibilità elevata è possibile distribuire più di un agente di provisioning e registrarlo in modo che gestisca lo stesso set di domini locali di Active Directory.

> [!IMPORTANT]
> Negli ambienti di produzione Microsoft consiglia di avere almeno 3 agenti di provisioning configurati con il tenant di Azure AD per la disponibilità elevata.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integrazione con più domini di Active Directory

> [!NOTE]
> Questa sezione è rilevante solo se si prevede di distribuire Workday all'app Workday to Active Directory User Provisioning. È possibile ignorare questo passaggio se si distribuisce l'app Workday Writeback o Workday to Azure AD User Provisioning.

A seconda della topologia di Active Directory, sarà necessario decidere il numero di app connettori di provisioning utenti e il numero di agenti di provisioning da configurare. Di seguito sono riportati alcuni dei modelli di distribuzione più comuni a cui è possibile fare riferimento al momento di pianificare la distribuzione.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Scenario di distribuzione 1: Singolo tenant di Workday -> Singolo dominio di AD

In questo scenario, si ha un tenant di Workday e si vuole effettuare il provisioning utenti in un singolo dominio AD di destinazione. Ecco la configurazione di produzione consigliata per questa distribuzione.

|   |   |
| - | - |
|  No. di provisioning di agenti da distribuire in locale | 3 (per disponibilità elevata e failover) |
|  No. di app Workday to Azure AD User Provisioning da configurare nel portale di Azure | 1 |

  ![Scenario 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Scenario di distribuzione 2: Singolo tenant Workday -> Domini AD figli multipli

Questo scenario implica il provisioning utenti da Workday a più domini figlio AD di destinazione in una foresta. Ecco la configurazione di produzione consigliata per questa distribuzione.

|   |   |
| - | - |
|  No. di provisioning di agenti da distribuire in locale | 3 (per disponibilità elevata e failover) |
|  No. di app Workday to Azure AD User Provisioning da configurare nel portale di Azure | un'app per ogni dominio figlio |

  ![Scenario 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Scenario di distribuzione 3: Singolo tenant di Workday-> Foreste AD non contigue

Questo scenario implica il provisioning utenti da Workday a domini in foreste AD non contigue. Ecco la configurazione di produzione consigliata per questa distribuzione.

|   |   |
| - | - |
|  No. di provisioning di agenti da distribuire in locale | 3 per ogni foresta AD non contigua |
|  No. di app Workday to Azure AD User Provisioning da configurare nel portale di Azure | un'app per ogni dominio figlio |

  ![Scenario 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Pianificare mapping e trasformazioni degli attributi utente da Workday ad Active Directory

> [!NOTE]
> Questa sezione è rilevante solo se si prevede di distribuire Workday all'app Workday to Active Directory User Provisioning. È possibile ignorare questo passaggio se si distribuisce l'app Workday Writeback o Workday to Azure AD User Provisioning.

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

  * *Esempio: Se questa configurazione è una nuova distribuzione di Workday, è consigliabile fare in modo che Active Directory sia già popolato con i valori di Worker_ID di Workday corretti (o con il valore dell'ID univoco desiderato) per mantenere la logica di corrispondenza il più semplice possibile.*

Nelle sezioni rimanenti di questa esercitazione verrà descritto come installare e configurare queste speciali app di connettori di provisioning. La scelta delle app da configurare dipende dai sistemi in cui è necessario effettuare il provisioning e dal numero di domini di Active Directory e di tenant Azure AD nell'ambiente in uso.

## <a name="configure-integration-system-user-in-workday"></a>Configurare un utente del sistema di integrazione in Workday

Un requisito comune di tutti i connettori di provisioning Workday è la richiesta di credenziali di un utente del sistema di integrazione Workday per la connessione all'API Human Resources di Workday. Questa sezione descrive come creare un utente del sistema di integrazione in Workday e contiene le sezioni seguenti:

* [Creazione di un utente del sistema di integrazione](#creating-an-integration-system-user)
* [Creazione di un gruppo di sicurezza del sistema di integrazione](#creating-an-integration-security-group)
* [Configurazione delle autorizzazioni dei criteri di sicurezza del dominio](#configuring-domain-security-policy-permissions)
* [Configurazione delle autorizzazioni dei criteri di sicurezza dei processi aziendali](#configuring-business-process-security-policy-permissions)
* [Attivazione delle modifiche apportate ai criteri di sicurezza](#activating-security-policy-changes)

> [!NOTE]
> È possibile evitare di eseguire questa procedura usando un account di amministratore globale di Workday come account di integrazione dei sistemi. Questa soluzione è utilizzabile a scopo dimostrativo, ma non è consigliata per le distribuzioni di produzione.

### <a name="creating-an-integration-system-user"></a>Creazione di un utente del sistema di integrazione

**Per creare un utente di sistema di integrazione, seguire questa procedura:**

1. Accedere al tenant di Workday usando un account amministratore. Nell'**applicazione Workday** immettere "create user" nella casella di ricerca e quindi fare clic su **Create Integration System User** (Crea utente del sistema di integrazione).

    ![Creare un utente](./media/workday-inbound-tutorial/wd_isu_01.png "Creare un utente")
2. Completare l'attività **Create Integration System User** specificando un nome utente e una password per un nuovo utente del sistema di integrazione.  
  
* Lasciare l'opzione **Require New Password at Next Sign In (Richiedi nuova password al prossimo accesso)** non selezionata, perché l'accesso dell'utente verrà eseguito a livello di codice.
* Lasciare l'opzione **Session Timeout Minutes (Minuti di timeout della sessione)** impostata sul valore predefinito 0, in modo da evitare un timeout prematuro delle sessioni dell'utente.
* Selezionare l'opzione **Do Not Allow UI Sessions** (Non consentire sessioni di interfaccia utente) in quanto aggiunge un livello di sicurezza che impedisce a un utente con la password del sistema di integrazione di accedere a Workday.

    ![Creare un utente del sistema di integrazione](./media/workday-inbound-tutorial/wd_isu_02.png "Creare un utente del sistema di integrazione")

### <a name="creating-an-integration-security-group"></a>Creazione di un gruppo di sicurezza del sistema di integrazione

In questo passaggio si creerà un gruppo di sicurezza del sistema di integrazione non vincolato o vincolato in Workday e si assegnerà l'utente del sistema di integrazione creato nel passaggio precedente a questo gruppo.

**Per creare un gruppo di sicurezza, seguire questa procedura:**

1. Immettere "create security group" nella casella di ricerca e quindi fare clic su **Create Security Group**(Crea gruppo di sicurezza).

    ![Creare un gruppo di sicurezza](./media/workday-inbound-tutorial/wd_isu_03.png "Creare un gruppo di sicurezza")
2. Completare l'attività **Creare un gruppo di sicurezza**. 

  * Esistono due tipi di gruppi di sicurezza in Workday:
    * **Non vincolato:** tutti i membri del gruppo di sicurezza possono accedere a tutte le istanze di dati protette dal gruppo di sicurezza.
    * **Vincolato:** tutti i membri del gruppo di sicurezza hanno accesso contestuale a un subset delle istanze di dati (righe) a cui può accedere il gruppo di sicurezza.
  * Verificare il partner di integrazione di Workday per selezionare il tipo di gruppo di sicurezza appropriato per l'integrazione.
  * Una volta appurato il tipo di gruppo, selezionare **Integration System Security Group (Unconstrained)** (Gruppo di sicurezza del sistema di integrazione (Non vincolato)) o **Integration System Security Group (Unconstrained)** (Gruppo di sicurezza del sistema di integrazione (Non vincolato)) dall'elenco a discesa **Type of Tenanted Security Group** (Tipo di gruppo di sicurezza con tenant).

    ![Creare un gruppo di sicurezza](./media/workday-inbound-tutorial/wd_isu_04.png "Creare un gruppo di sicurezza")

3. Dopo aver creato il gruppo di sicurezza, verrà visualizzata una pagina in cui sarà possibile assegnare membri al gruppo. Aggiungere il nuovo utente di sistema di integrazione creato nel passaggio precedente per questo gruppo di sicurezza. Se si usa il gruppo di sicurezza *vincolato*, è necessario anche selezionare l'ambito dell'organizzazione appropriato.

    ![Modificare un gruppo di sicurezza](./media/workday-inbound-tutorial/wd_isu_05.png "Modificare un gruppo di sicurezza")

### <a name="configuring-domain-security-policy-permissions"></a>Configurazione delle autorizzazioni dei criteri di sicurezza del dominio

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

### <a name="configuring-business-process-security-policy-permissions"></a>Configurazione delle autorizzazioni dei criteri di sicurezza dei processi aziendali

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

### <a name="activating-security-policy-changes"></a>Attivazione delle modifiche apportate ai criteri di sicurezza

**Per attivare le modifiche apportate ai criteri di sicurezza, seguire questa procedura:**

1. Immettere "activate" (attiva) nella casella di ricerca e quindi fare clic sul collegamento **Activate Pending Security Policy Changes (Attiva le modifiche in sospeso ai criteri di sicurezza)**.

    ![Attivare](./media/workday-inbound-tutorial/wd_isu_16.png "Attivare")

1. Avviare l'attività Activate Pending Security Policy Changes (Attiva le modifiche in sospeso ai criteri di sicurezza) immettendo un commento a scopo di controllo e quindi fare clic su **OK**.
1. Completare l'attività nella schermata successiva selezionando la casella di controllo **Confirm (Conferma)** e quindi facendo clic su **OK**.

    ![Attivare la sicurezza in sospeso](./media/workday-inbound-tutorial/wd_isu_18.png "Attivare la sicurezza in sospeso")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Configurazione del provisioning utenti da Workday in Active Directory

Questa sezione fornisce i passaggi per configurare il provisioning degli account utente da Workday in ogni dominio di Active Directory nell'ambito dell'integrazione.

* [Installare e configurare gli agenti di provisioning locali](#part-1-install-and-configure-on-premises-provisioning-agents)
* [Aggiungere l'app del connettore di provisioning e creare la connessione a Workday](#part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Configurare i mapping degli attributi](#part-3-configure-attribute-mappings)
* [Abilitare e avviare il provisioning utenti](#enable-and-launch-user-provisioning)

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>Parte 1: installare e configurare gli agenti di provisioning locali

Per effettuare il provisioning in Active Directory locale, è necessario installare un agente in un server con .NET Framework 4.7.1 e versioni successive e con accesso alla rete ai domini di Active Directory richiesti.

> [!TIP]
> È possibile controllare la versione di .NET Framework nel server seguendo le istruzioni disponibili [qui](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Se il server non dispone di .NET 4.7.1 o versioni successive, è possibile scaricarlo da [qui](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Dopo aver distribuito .NET 4.7.1 o versioni successive, sarà possibile scaricare l'**[agente di provisioning locale qui](https://go.microsoft.com/fwlink/?linkid=847801)** e seguire i passaggi seguenti per completare la configurazione dell'agente.

1. Accedere all'istanza di Windows Server in cui si intende installare il nuovo agente.
2. Avviare il programma di installazione dell'agente di provisioning, accettare le condizioni e fare clic sul pulsante **Install** (Installa).

   ![Schermata di installazione](./media/workday-inbound-tutorial/pa_install_screen_1.png "Schermata di installazione")
3. Al termine dell'installazione verrà avviata la procedura guidata e verrà visualizzata la schermata **Connect Azure AD** (Connetti Azure AD). Fare clic sul pulsante **Authenticate** (Autentica) per connettersi all'istanza di Azure AD.

   ![Connect Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Connect Azure AD")
1. Eseguire l'autenticazione all'istanza di Azure AD con credenziali di amministratore globale.

   ![Autenticazione amministratore](./media/workday-inbound-tutorial/pa_install_screen_3.png "Autenticazione amministratore")

> [!NOTE]
> Le credenziali di amministratore di Azure AD vengono usate solo per la connessione al tenant di Azure AD. L'agente non archivia le credenziali in locale nel server.

1. Al termine dell'autenticazione con Azure AD verrà visualizzata la schermata **Connect Active Directory** (Connetti Active Directory). In questo passaggio, inserire il nome di dominio AD e fare clic sul pulsante **Add Directory** (Aggiungi directory).

   ![Add Directory](./media/workday-inbound-tutorial/pa_install_screen_4.png "Add Directory")
  
1. Verrà ora richiesto di immettere le credenziali necessarie per connettersi al dominio AD. Nella stessa schermata è possibile usare **Select domain controller priority** (Seleziona priorità controller di dominio) per specificare i controller di dominio che l'agente deve usare per l'invio di richieste di provisioning.

   ![Credenziali del dominio](./media/workday-inbound-tutorial/pa_install_screen_5.png)
1. Dopo aver configurato il dominio, il programma di installazione mostrerà un elenco di domini configurati. In questa schermata è possibile ripetere i passaggi 5 e 6 per aggiungere più domini o fare clic su **Next** (Avanti) per procedere alla registrazione dell'agente.

   ![Domini configurati](./media/workday-inbound-tutorial/pa_install_screen_6.png "Domini configurati")

   > [!NOTE]
   > In presenza di più domini di Active Directory (ad esempio na.contoso.com, emea.contoso.com), aggiungere singolarmente ogni dominio all'elenco. Non è sufficiente aggiungere soltanto il dominio padre (ad esempio, contoso.com). È necessario registrare ogni dominio figlio con l'agente.
1. Esaminare i dettagli di configurazione e fare clic su **Confirm** (Conferma) per registrare l'agente.
  
   ![Schermata di conferma](./media/workday-inbound-tutorial/pa_install_screen_7.png "Schermata di conferma")
1. La Configurazione guidata mostra lo stato di avanzamento della registrazione dell'agente.
  
   ![Registrazione dell'agente](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registrazione dell'agente")
1. Al termine della registrazione dell'agente sarà possibile fare clic su **Exit** (Esci) per uscire dalla procedura guidata.
  
   ![Schermata Exit](./media/workday-inbound-tutorial/pa_install_screen_9.png "Schermata Exit")
1. Verificare l'installazione dell'agente e accertarsi che sia in esecuzione aprendo lo snap-in "Services" e cercare il servizio denominato "Microsoft Azure AD Connect Provisioning Agent" (Agente di provisioning Microsoft Azure Active Directory Connect)
  
   ![Servizi](./media/workday-inbound-tutorial/services.png)

### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 2: aggiungere l'app del connettore di provisioning e creare la connessione a Workday

**Per configurare il provisioning da Workday in Active Directory:**

1. Passare a <https://portal.azure.com>.

2. Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3. Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4. Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5. Cercare **Workday Provisioning to Active Directory** (Provisioning Workday in Active Directory) e aggiungere tale app dalla raccolta.

6. Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**

7. Impostare **Modalità di** **provisioning** su **Automatico**

8. Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente dell'amministratore**: immettere il nome utente dell'account del sistema di integrazione Workday, aggiungendo il nome di dominio del tenant. Dovrebbe essere simile a: **username@tenant_name**

   * **Password dell'amministratore:** immettere la password dell'account del sistema di integrazione Workday

   * **URL del tenant:** immettere l'URL dell'endpoint dei servizi Web Workday per il tenant. Il valore dovrebbe essere simile a: https://wd3-impl-services1.workday.com/ccx/service/contoso4, dove  *contoso4* è sostituito dal nome del tenant corretto e *wd3-impl* è sostituito dalla stringa di ambiente corretta.

   * **Foresta di Active Directory:** il "nome" del dominio di Active Directory, così come registrato con l'agente. Usare l'elenco a discesa per selezionare il dominio di destinazione per il provisioning. In genere, il valore corrisponde a una stringa simile a: *contoso.com*

   * **Contenitore di Active Directory:** immettere il nome distinto del contenitore in cui l'agente deve creare gli account utente per impostazione predefinita.
        Esempio: *OU=Standard Users,OU=Users,DC=contoso,DC=test*
> [!NOTE]
> Questa impostazione deve essere usata per la creazione degli account utente solo se l'attributo *parentDistinguishedName* non è configurato nel mapping degli attributi. Questa impostazione non viene usata per la ricerca di utenti o per le operazioni di aggiornamento. L'intero sottoalbero del dominio rientra nell'ambito dell'operazione di ricerca.

   * **Indirizzo di posta elettronica per le notifiche:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo per inviare una notifica di posta elettronica in caso di errore.

> [!NOTE]
> Il servizio di provisioning di Azure AD invia una notifica di posta elettronica se il processo di provisioning entra in uno stato di [quarantena](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine).

   * Fare clic sul pulsante **Test connessione**. Se il test della connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. In caso contrario, verificare che le credenziali di Workday e Active Directory configurate nel programma di installazione dell'agente siano valide.

     ![Portale di Azure](./media/workday-inbound-tutorial/wd_1.png)

   * Dopo che le credenziali vengono salvate correttamente, la sezione **Mapping** mostrerà il mapping predefinito **Synchronize Workday Workers to On Premises** (Sincronizza ruoli di lavoro Workday in Active Directory locale)

### <a name="part-3-configure-attribute-mappings"></a>Parte 3: configurare i mapping degli attributi

In questa sezione verrà configurato il flusso dei dati utente da Workday in Active Directory.

1. Nella scheda Provisioning in **Mapping** fare clic su **Synchronize Workday Workers to On Premises** (Sincronizza ruoli di lavoro Workday in Active Directory locale).

2. Nel campo **Source Object Scope** (Ambito dell'oggetto di origine) è possibile selezionare i set di utenti in Workday da includere nell'ambito per il provisioning in AD, definendo un set di filtri basati su attributi. L'ambito predefinito è "tutti gli utenti in Workday". Filtri di esempio:

   * Esempio: ambito per gli utenti con ID lavoratore compreso tra 1000000 e    2000000

      * Attributo: WorkerID

      * Operator: REGEX Match (Corrispondenza REGEX)

      * Valore: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Esempio: solo i dipendenti, senza i lavoratori occasionali

      * Attributo: EmployeeID

      * Operator: IS NOT NULL (NON È NULL)

> [!TIP]
> Quando si configura l'app di provisioning per la prima volta, è necessario testare e verificare i mapping degli attributi e le espressioni per assicurarsi che restituisca il risultato desiderato. Microsoft consiglia di usare i filtri di ambito in **Source Object Scope** (Ambito dell'oggetto di origine) per testare il mapping con alcuni utenti test da Workday. Dopo avere verificato che i mapping funzionino è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

3. Nel campo **Target Object Actions** (Azioni oggetto di destinazione) è possibile applicare un filtro a livello globale per le azioni che vengono eseguite in Active Directory. **Creazione** e **Aggiornamento** sono le più comuni.

4. Nella sezione **Mapping attributi** è possibile definire il mapping dei singoli attributi di Workday agli attributi di Active Directory.

5. Fare clic su un mapping di attributi esistente per aggiornarlo oppure fare clic su **Aggiungi nuovo mapping** nella parte inferiore della schermata per aggiungere nuovi mapping. Il mapping di un singolo attributo supporta queste proprietà:

      * **Tipo di mapping**

         * **Diretto**: scrive il valore dell'attributo di Workday nell'attributo di AD, senza modifiche

         * **Costante**: scrive un valore stringa costante statico nell'attributo di AD

         * **Espressione**: consente di scrivere un valore personalizzato per l'attributo di Active Directory, in base a uno o più attributi di Workday. [Per altre informazioni, vedere questo articolo sulle espressioni](../manage-apps/functions-for-customizing-application-data.md).

      * **Attributo di origine**: l'attributo utente in Workday. Se l'attributo che si sta cercando non è presente, vedere [Personalizzazione dell'elenco di attributi utente di Workday](#customizing-the-list-of-workday-user-attributes).

      * **Valore predefinito**: facoltativo. Se l'attributo di origine ha un valore vuoto, il mapping eseguirà la scrittura di questo valore.
            Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

      * **Attributo di destinazione**: l'attributo utente in Active Directory.

      * **Abbina gli oggetti in base a questo attributo**: specifica se questo mapping deve essere usato per l'identificazione univoca degli utenti tra Workday e Active Directory. In genere, è impostato sul campo ID ruolo di lavoro per Workday, che solitamente è associato a uno degli attributi ID dipendente in Active Directory.

      * **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Se sono presenti più attributi, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.

      * **Applica questo mapping**

         * **Sempre**: applica il mapping sia all'azione di creazione che all'azione di aggiornamento dell'utente

         * **Only during creation** (Solo durante la creazione): applica il mapping solo alle azioni di creazione dell'utente

6. Per salvare i mapping, fare clic su **Save**, Salva, nella parte superiore della sezione Attribute-Mapping, Mapping attributi.

   ![Portale di Azure](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Di seguito sono riportati alcuni esempi di mapping degli attributi tra Workday e Active Directory, con alcune espressioni comuni

* L'espressione che esegue il mapping all'attributo *parentDistinguishedName* viene usata per effettuare il provisioning di un utente in diverse unità organizzative in base a uno o più attributi di origine di Workday. L'esempio qui colloca gli utenti in diverse unità organizzative in base alla città in cui si trovano.

* L'attributo *userPrincipalName* in Active Directory viene generato usando la funzione di deduplicazione [SelectUniqueValue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue) che controlla l'esistenza di un valore generato nel dominio di destinazione di AD e lo imposta solo se univoco.  

* [La documentazione sulla scrittura di espressioni è disponibile qui](../manage-apps/functions-for-customizing-application-data.md). Questa sezione include alcuni esempi di come rimuovere i caratteri speciali.

| ATTRIBUTO DI WORKDAY | ATTRIBUTO DI ACTIVE DIRECTORY |  ID CORRISPONDENTE? | CREAZIONE / AGGIORNAMENTO |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Sì** | Scritto solo al momento della creazione |
| **PreferredNameData**    |  cn    |   |   Scritto solo al momento della creazione |
| **SelectUniqueValue( Join("@", Join(".",  \[FirstName\], \[LastName\]), "contoso.com"), Join("@", Join(".",  Mid(\[FirstName\], 1, 1), \[LastName\]), "contoso.com"), Join("@", Join(".",  Mid(\[FirstName\], 1, 2), \[LastName\]), "contoso.com"))**   | userPrincipalName     |     | Scritto solo al momento della creazione 
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

Dopo aver completato la configurazione di mapping di attributo, è ora possibile [abilitare e avviare il servizio di provisioning utenti](#enable-and-launch-user-provisioning).

## <a name="configuring-user-provisioning-to-azure-ad"></a>Configurazione del provisioning utenti in Azure AD

Le sezioni seguenti descrivono i passaggi per la configurazione del provisioning utenti da Workday ad Azure AD per le distribuzioni solo cloud.

* [Aggiungere l'app del connettore di provisioning in Azure AD e creare la connessione a Workday](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Configurare i mapping degli attributi di Workday e Azure AD](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Abilitare e avviare il provisioning utenti](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Eseguire la procedura seguente solo se sono presenti utenti solo cloud, di cui è necessario eseguire il provisioning in Azure AD e non in Active Directory locale.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: aggiungere l'app del connettore di provisioning in Azure AD e creare la connessione a Workday

**Per configurare il provisioning da Workday ad Azure Active Directory per gli utenti solo cloud:**

1. Passare a <https://portal.azure.com>.

2. Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3. Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4. Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5. Cercare **Workday to Azure AD provisioning** (Provisioning Workday in Azure AD) e aggiungere tale app dalla raccolta.

6. Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**

7. Impostare **Modalità di** **provisioning** su **Automatico**

8. Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore**: immettere il nome utente dell'account del sistema di integrazione Workday, aggiungendo il nome di dominio del tenant. Dovrebbe essere simile a: username@contoso4

   * **Password dell'amministratore:** immettere la password dell'account del sistema di integrazione Workday

   * **URL del tenant:** immettere l'URL dell'endpoint dei servizi Web Workday per il tenant. Questo valore dovrebbe essere simile a: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, dove  *contoso4* è sostituito dal nome del tenant corretto e *wd3-impl* è sostituito dalla stringa di ambiente corretta. Se l'URL non è noto, determinare l'URL corretto da usare insieme al proprio partner di integrazione Workday o al supporto tecnico.

   * **Indirizzo di posta elettronica per le notifiche:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo "Invia una notifica di posta elettronica in caso di errore".

   * Fare clic sul pulsante **Test connessione**.

   * Se il test della connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. In caso contrario, verificare che l'URL e le credenziali per Workday siano validi in Workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Parte 2: Configurare i mapping degli attributi di Workday e Azure AD

In questa sezione verrà configurato il flusso dei dati utente da Workday in Azure Active Directory per gli utenti solo cloud.

1. Nella scheda Provisioning, in **Mapping**, fare clic su **Synchronize Workers to Azure AD** (Sincronizza lavoratori in Azure AD).

2. Nel campo **Source Object Scope** (Ambito dell'oggetto di origine) è possibile selezionare i set di utenti in Workday da includere nell'ambito per il provisioning in Azure AD, definendo un set di filtri basati su attributi. L'ambito predefinito è "tutti gli utenti in Workday". Filtri di esempio:

   * Esempio: ambito per gli utenti con ID lavoratore compreso tra 1000000 e    2000000

      * Attributo: WorkerID

      * Operator: REGEX Match (Corrispondenza REGEX)

      * Valore: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Esempio: solo i lavoratori occasionali, senza i dipendenti

      * Attributo: ContingentID

      * Operator: IS NOT NULL (NON È NULL)

3. Nel campo **Target Object Actions** (Azioni oggetto di destinazione) è possibile applicare un filtro a livello globale per le azioni che vengono eseguite in Azure AD. **Create** (Crea) e **Update** (Aggiorna) sono le più comuni.

4. Nella sezione **Mapping attributi** è possibile definire il mapping dei singoli attributi di Workday agli attributi di Active Directory.

5. Fare clic su un mapping di attributi esistente per aggiornarlo oppure fare clic su **Aggiungi nuovo mapping** nella parte inferiore della schermata per aggiungere nuovi mapping. Il mapping di un singolo attributo supporta queste proprietà:

   * **Tipo di mapping**

      * **Direct** (Diretto): scrive il valore dell'attributo di Workday nell'attributo di AD, senza modifiche

      * **Costant** (Costante): scrive un valore stringa costante statico nell'attributo di AD

      * **Espressione**: consente di scrivere un valore personalizzato per l'attributo di Active Directory, in base a uno o più attributi di Workday. [Per altre informazioni, vedere questo articolo sulle espressioni](../manage-apps/functions-for-customizing-application-data.md).

   * **Attributo di origine**: l'attributo utente in Workday. Se l'attributo che si sta cercando non è presente, vedere [Personalizzazione dell'elenco di attributi utente di Workday](#customizing-the-list-of-workday-user-attributes).

   * **Valore predefinito**: facoltativo. Se l'attributo di origine ha un valore vuoto, il mapping eseguirà la scrittura di questo valore.
            Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

   * **Attributo di destinazione**: l'attributo utente in Azure AD.

   * **Match objects using this attribute** (Abbina gli oggetti in base a questo attributo): specifica se questo mapping deve essere usato per l'identificazione univoca degli utenti tra Workday e Azure AD. Questo valore è impostato in genere sul campo ID ruolo di lavoro per Workday, che solitamente è associato all'attributo ID dipendente (nuovo) o a un attributo di estensione in Azure AD.

   * **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Se sono presenti più attributi, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.

   * **Applica questo mapping**

     * **Sempre**: applica il mapping sia all'azione di creazione che all'azione di aggiornamento dell'utente

     * **Only during creation** (Solo durante la creazione): applica il mapping solo alle azioni di creazione dell'utente

6. Per salvare i mapping, fare clic su **Salva** nella parte superiore della sezione Mapping attributi.

Dopo aver completato la configurazione di mapping di attributo, è ora possibile [abilitare e avviare il servizio di provisioning utenti](#enable-and-launch-user-provisioning).

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Configurazione del writeback degli indirizzi di posta elettronica in Workday

Seguire queste istruzioni per configurare il writeback degli indirizzi di posta elettronica degli utenti da Azure Active Directory in Workday.

* [Aggiungere l'app del connettore Writeback e creare la connessione a Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Configurare i mapping degli attributi di writeback](#part-2-configure-writeback-attribute-mappings)
* [Abilitare e avviare il provisioning utenti](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Aggiungere l'app del connettore Writeback e creare la connessione a Workday

**Per configurare il connettore di Workday Writeback:**

1. Passare a <https://portal.azure.com>.

2. Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3. Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4. Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5. Cercare **Workday Writeback** (Writeback Workday) e aggiungere tale applicazione dalla raccolta.

6. Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**

7. Impostare **Modalità di** **provisioning** su **Automatico**

8. Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore**: immettere il nome utente dell'account del sistema di integrazione Workday, aggiungendo il nome di dominio del tenant. Dovrebbe essere simile a: *username@contoso4*

   * **Password dell'amministratore:** immettere la password dell'account del sistema di integrazione Workday

   * **URL tenant:** immettere l'URL dell'endpoint dei servizi Web Workday per il tenant. Dovrebbe essere simile a: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, dove *contoso4* è sostituito dal nome del tenant corretto e *wd3-impl* è sostituito dalla stringa di ambiente corretta (se necessario).

   * **Indirizzo di posta elettronica per le notifiche:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo "Invia una notifica di posta elettronica in caso di errore".

   * Fare clic sul pulsante **Test connessione**. Se il test della connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. In caso contrario, verificare che l'URL e le credenziali per Workday siano validi in Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Parte 2: Configurare i mapping degli attributi di writeback

In questa sezione si configurerà il flusso degli attributi writeback da Azure AD a Workday.

1. Nella scheda Provisioning, in **Mapping**, fare clic su **Synchronize Azure AD Users to Workday** (Sincronizza utenti Azure AD in Workday).

2. Nel campo **Source Object Scope** (Ambito dell'oggetto di origine) è possibile scegliere di filtrare i set di utenti di Azure Active Directory di cui eseguire il writeback degli indirizzi di posta elettronica in Workday. L'ambito predefinito è "tutti gli utenti in Azure AD".

3. Nella sezione **Mapping degli attributi**, aggiornare l'ID corrispondente per indicare l'attributo in Azure Active Directory in cui è memorizzato l'ID del ruolo di lavoro Workday o l'ID del dipendente. Un metodo comune per garantire la corrispondenza è sincronizzare l'ID lavoratore o l'ID dipendente di Workday in extensionAttribute1-15 in Azure AD e quindi usare questo attributo in Azure AD per associare gli utenti in Workday.

4. Per salvare i mapping, fare clic su **Salva** nella parte superiore della sezione Mapping attributi.

Dopo aver completato la configurazione di mapping di attributo, è ora possibile [abilitare e avviare il servizio di provisioning utenti](#enable-and-launch-user-provisioning). 

## <a name="enable-and-launch-user-provisioning"></a>Abilitare e avviare il provisioning utenti

Dopo aver completato le configurazioni dell'app di provisioning Workday, è possibile attivare il servizio di provisioning nel portale di Azure.

> [!TIP]
> Per impostazione predefinita quando si attiva il servizio di provisioning, verranno avviate le operazioni di provisioning per tutti gli utenti nell'ambito. Se sono presenti errori di mapping o problemi di dati in Workday, il processo di provisioning potrebbe non riuscire e passare allo stato di quarantena. Come procedura consigliata per evitare questo problema è consigliabile configurare il filtro **Source Object Scope** (Ambito dell'oggetto di origine) e il test di mapping degli attributi con alcuni utenti test prima di avviare la sincronizzazione completa per tutti gli utenti. Dopo avere verificato che i mapping funzionino e che restituiscano i risultati desiderati è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

1. Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

2. Fare clic su **Save**.

3. Questa operazione avvierà la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti nel tenant di Workday. 

4. In qualsiasi momento è possibile controllare la scheda **Log di controllo** nel portale di Azure per vedere le azioni che sono state eseguite dal servizio di provisioning. I log di controllo elencano tutti i singoli eventi di sincronizzazione eseguiti dal servizio di provisioning, ad esempio quali utenti vengono letti da Workday e successivamente aggiunti o aggiornati in Active Directory. Vedere la sezione [Troubleshooting](#troubleshooting-provisioning-issues) (Risoluzione dei problemi) per istruzioni su come esaminare i log di controllo e correggere gli errori di provisioning.

5. Al termine della sincronizzazione iniziale, verrà scritto un report di riepilogo di controllo nella scheda **Provisioning**, come illustrato di seguito.

   ![Portale di Azure](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Domande frequenti

* **Domande relative alla funzionalità di soluzione**
  * [Durante l'elaborazione di una nuova assunzione da Workday, come viene impostata la password per il nuovo account utente in Active Directory?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [La soluzione supporta l'invio di notifiche di posta elettronica dopo il completamento di operazioni di provisioning?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Come gestire la distribuzione delle password per le nuove assunzioni e fornire in modo sicuro un meccanismo per reimpostare le password?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [La soluzione memorizza nella cache i profili utente di Workday nel cloud di Azure AD o a livello di agente di provisioning?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [La soluzione supporta l'assegnazione in locale di gruppi di Active Directory all'utente?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Quale API di Workday viene usata dalla soluzione per eseguire query e aggiornare i profili dei ruoli di lavoro in Workday?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [È possibile configurare il tenant di Workday HCM con due tenant di Azure AD?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Come mai l'app di provisioning utenti "Workday to Azure AD" non è supportata se è stata distribuita da Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Come suggerire miglioramenti o richiedere nuove funzionalità correlate all'integrazione di Workday e Azure AD?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Domande relative all'agente di provisioning**
  * [Qual è la versione disponibile a livello generale dell'agente di provisioning?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Come capire la versione dell'agente di provisioning?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Microsoft esegue automaticamente il push degli aggiornamenti dell'agente di provisioning?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [È possibile installare l'agente di provisioning nello stesso server in cui è in esecuzione AAD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect)
  * [Come si configura l'agente di provisioning in modo da usare un server proxy per la comunicazione HTTP in uscita?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Come garantire che l'agente di provisioning sia in grado di comunicare con il tenant di Azure AD e che nessun firewall blocchi le porte richieste dall'agente?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Come annullare la registrazione del dominio associato all'agente di provisioning?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Come si disinstalla l'agente di provisioning?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Domande relative al mapping attributi e alla configurazione Workday to Active Directory**
  * [Come eseguire il backup o esportare una copia di lavoro del mapping e dello schema dell'attributo di provisioning di Workday?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [L'utente dispone di attributi personalizzati in Workday e Active Directory. Come si configura il corretto funzionamento con gli attributi personalizzati della soluzione?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [È possibile effettuare il provisioning delle foto degli utenti da Workday ad Active Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Come è possibile sincronizzare i numeri di telefono da Workday per uso pubblico, in base il consenso dell'utente?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Come si formattano i nomi visualizzati in AD in base agli attributi di reparto, paese o città e come si gestiscono le variazioni di area?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Come è possibile usare SelectUniqueValue per generare valori univoci per l'attributo samAccountName?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Come rimuovere i caratteri con segno diacritico e convertirli in caratteri alfabetici italiani?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Domande relative alla funzionalità di soluzione

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Durante l'elaborazione di una nuova assunzione da Workday, come viene impostata la password per il nuovo account utente in Active Directory?

Quando l'agente di provisioning locale ottiene una richiesta per creare un nuovo account di AD, viene generata automaticamente una password casuale complessa progettata per soddisfare i requisiti di complessità delle password definiti dal server AD e la imposta per l'oggetto utente. Questa password non viene registrata in un punto qualsiasi.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>La soluzione supporta l'invio di notifiche di posta elettronica dopo il completamento di operazioni di provisioning?

No, l'invio di notifiche tramite posta elettronica dopo il completamento delle operazioni di provisioning non è supportato nella versione corrente.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Come gestire la distribuzione delle password per le nuove assunzioni e fornire in modo sicuro un meccanismo per reimpostare le password?

Uno dei passaggi finali coinvolti nel provisioning di un nuovo account AD è il recapito della password temporanea assegnata all'account AD dell'utente. Molte aziende usano ancora l'approccio tradizionale: fornire la password temporanea al manager dell'utente, che la trasmette alla nuova assunzione o al lavoratore occasionale. Questo processo ha un difetto intrinseco nel sistema di sicurezza; è presente un'opzione per implementare un migliore approccio usando le funzionalità di Azure AD.

Come parte del processo di assunzione, i team delle risorse umane in genere eseguono una verifica delle attività precedenti ed esaminano attentamente il numero di telefono cellulare delle nuove assunzioni. Inoltre, con l'integrazione del provisioning utenti da Workday ad Active Directory, è possibile compilare e implementare una funzionalità di reimpostazione della password self-service per l'utente durante il suo primo giorno di lavoro. Questa operazione si ottiene propagando l'attributo "Mobile Number" (Numero di telefono cellulare) della nuova assunzione da Workday ad AD e quindi da AD ad Azure AD mediante AAD Connect. Una volta che "Numero di telefono cellulare" è presente in Azure AD è possibile abilitare la [reimpostazione della password self-service (SSPR)](../authentication/howto-sspr-authenticationdata.md) per conto dell'utente, così che durante il suo primo giorno, la nuova assunzione possa usare il numero di dispositivo mobili registrato e verificato per l'autenticazione.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>La soluzione memorizza nella cache i profili utente di Workday sul cloud di Azure AD o a livello di agente di provisioning?

No, la soluzione non gestisce una cache di profili utente. Il servizio di provisioning di Azure AD funge semplicemente da elaboratore dati, leggendo i dati di Workday e scrivendoli nelle destinazioni Active Directory o Azure AD. Vedere la sezione [Managing personal data](#managing-personal-data) (Gestione dei dati personali) per dettagli relativi alla privacy e alla conservazione dei dati dell'utente.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>La soluzione supporta l'assegnazione in locale di gruppi di AD all'utente?

Questa funzionalità non è attualmente supportata. La soluzione consigliata è l'implementazione di uno script di PowerShell che interroga l'endpoint dell'API Azure AD Graph per i dati del log di controllo e li usa per attivare scenari come l'assegnazione dei gruppi. Questo script di PowerShell può essere collegato a un'utilità di pianificazione e distribuito nella stessa finestra in cui è in esecuzione l'agente di provisioning.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Quali API di Workday vengono usate dalla soluzione per eseguire query e aggiornare i profili dei ruoli di lavoro in Workday?

La soluzione attualmente usa le API di Workday seguenti:

* Get_Workers (v21.1) per il recupero di informazioni relative al lavoratore
* Maintain_Contact_Information (v26.1) per la funzionalità di Writeback dell'indirizzo di posta elettronica di lavoro

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>È possibile configurare il tenant di Workday HCM con due tenant di Azure AD?

Sì, questa configurazione è supportata. Ecco i passaggi di livello elevato per configurare questo scenario:

* Distribuire l'agente di provisioning n. 1 e registrarlo con il tenant n. 1 di Azure AD.
* Distribuire l'agente di provisioning n. 2 e registrarlo con il tenant n. 2 di Azure AD.
* Configurare ogni agente con i domini, sulla base dei "Sottodomini" che verranno gestiti da ogni agente di provisioning. Un agente può gestire più domini.
* Nel portale di Azure, impostare l'app Workday to Azure AD User Provisioning in ogni tenant e configurarla con i rispettivi domini.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Come mai l'app di provisioning utenti "Workday to Azure AD" non è supportata se è stata distribuita Azure AD Connect?

Quando Azure AD viene usato in una modalità ibrida (che contiene una combinazione di utenti cloud e locali), è importante disporre di una definizione chiara di "origine dell'autorità". In genere gli scenari ibridi richiedono la distribuzione di Azure AD Connect. Dopo aver distribuito Azure AD Connect, AD locale diventa l'origine dell'autorità. L'introduzione alla combinazione del connettore di Workday to Azure AD può causare una situazione in cui i valori attributo di Workday possono potenzialmente sovrascrivere i valori impostati da Azure AD Connect. Di conseguenza l'app di provisioning utenti "Workday to Azure AD" non è supportata quando viene abilitato Azure AD Connect. In tali situazioni, è consigliabile usare l'app di provisioning utenti "Workday to AD" per recuperare gli utenti nell'AD locale e sincronizzarli in Azure AD con Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Come suggerire miglioramenti o richiedere nuove funzionalità correlate all'integrazione di Workday e Azure AD?

I commenti sono molto apprezzati, perché aiutano a stabilire la direzione per miglioramenti e versioni future. Tutti i commenti e suggerimenti sono i benvenuti. Microsoft invita a sottoporre idee o suggerimenti di miglioramento nel [forum dei commenti e suggerimenti di Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory). Per feedback specifici relativi all'integrazione di Workday, selezionare la categoria *SaaS Applications* (Applicazioni SaaS) ed effettuare la ricerca usando le parole chiave *Workday* per commenti e suggerimenti relativi a Workday.

![App SaaS di UserVoice](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![Workday UserVoice](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Quando si suggerisce una nuova idea, verificare se altri utenti hanno già suggerito una funzionalità simile. In tal caso, è possibile votare a favore della richiesta di funzionalità o miglioramento. È anche possibile lasciare un commento riguardante il caso d'uso specifico per mostrare il supporto all'idea e dimostrare in che modo la funzionalità potrebbe essere utile per il proprio contesto.

### <a name="provisioning-agent-questions"></a>Domande relative all'agente di provisioning

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Qual è la versione disponibile a livello generale dell'agente di provisioning?

* Le versioni disponibili a livello generale dell'agente di provisioning sono la 1.1.30 e successive.
* Se la versione dell'agente è precedente alla 1.1.30, si esegue la versione di anteprima pubblica che verrà automaticamente aggiornata alla versione disponibile a livello generale se il server che ospita l'agente dispone di runtime .NET 4.7.1.
  * È possibile [controllare la versione di .NET](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) installata sul server. Se .NET 4.7.1 non è in esecuzione sul server, [scaricarla e installarla](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). L'agente di provisioning sarà aggiornato automaticamente alla versione disponibile a livello generale dopo l'installazione di .NET 4.7.1.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Come capire la versione dell'agente di provisioning?

* Accedere al server di Windows in cui è installato l'agente di provisioning.
* Passare al menu **Control Panel** -> (Pannello di controllo)**Uninstall or Change a Program** (Disinstalla o modifica programma)
* Cercare la versione corrispondente alla voce **Microsoft Azure AD Connect Provisioning Agent** (Agente di provisioning Microsoft Azure AD Connect)

  ![Portale di Azure](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Microsoft esegue automaticamente il push degli aggiornamenti dell'agente di provisioning?

Sì, Microsoft esegue automaticamente gli aggiornamenti dell'agente di provisioning. È possibile disabilitare gli aggiornamenti automatici arrestando il servizio di Windows **Microsoft Azure AD Connect Agent Updater** (Updater agente Microsoft Azure AD Connect)

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect"></a>È possibile installare l'agente di provisioning nello stesso server in cui è in esecuzione AAD Connect?

Sì, è possibile installare l'agente di provisioning nello stesso server in cui è in esecuzione AAD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>Al momento della configurazione, l'agente di provisioning richiede le credenziali di amministratore di Azure AD. L'agente archivia le credenziali in locale nel server?

Al momento della configurazione, l'agente di provisioning richiede le credenziali di amministratore di Azure AD solo per la connessione al tenant di Azure AD. L'agente non archivia le credenziali in locale nel server. Tuttavia mantiene le credenziali usate per connettersi al *dominio locale di Active Directory*  in un insieme di credenziali delle password di Windows locale.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Come si configura l'agente di provisioning in modo da usare un server proxy per la comunicazione HTTP in uscita?

L'agente di provisioning supporta l'utilizzo di proxy in uscita. È possibile configurarlo modificando il file di configurazione dell'agente **c:\Programmi\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**. Aggiungere le righe seguenti, verso la fine del file appena prima del tag di chiusura `</configuration>`.
Sostituire le variabili [server proxy] e [proxy-port] con il nome del server proxy e i valori della porta.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Come garantire che l'agente di provisioning sia in grado di comunicare con il tenant di Azure AD e che nessun firewall blocchi le porte richieste dall'agente?

È inoltre possibile controllare se tutte le porte di cui si dispone sono aperte mediante lo [strumento di test delle porte del connettore](https://aadap-portcheck.connectorporttest.msappproxy.net/) dalla rete locale. La presenza di più segni di spunta verdi indica una maggiore resilienza.

Per assicurarsi che lo strumento fornisca i risultati corretti, accertarsi di:

* Aprire lo strumento in un browser dal server in cui è installato l'agente di provisioning.
* Assicurarsi che qualsiasi proxy o firewall applicabile all'agente di provisioning venga applicato anche a questa pagina. Questa operazione può essere eseguita in Internet Explorer selezionando **Impostazioni -> Opzioni Internet -> Connessioni -> Impostazioni Lan**. In questa pagina viene visualizzato il campo "Usa un server di proxy per la rete LAN". Selezionare questa casella e inserire l'indirizzo del proxy nel campo "Indirizzo".

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Un agente di provisioning può essere configurato per effettuare il provisioning di più domini di Active Directory?

Sì, un agente di provisioning può essere configurato per gestire più domini di AD purché l'agente comunichi con i controller di dominio corrispondenti. Microsoft consiglia di configurare un gruppo di 3 agenti di provisioning che gestiscono lo stesso set di domini di Active Directory per garantire la disponibilità elevata e fornire supporto in caso di failover.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Come annullare la registrazione del dominio associato all'agente di provisioning?

* Recuperare l'*ID tenant*  del tenant di Azure AD dal portale di Azure.
* Accedere al server di Windows in cui è installato l'agente di provisioning.
* Aprire PowerShell come amministratore Windows.
* Passare alla directory contenente gli script di registrazione ed eseguire i comandi seguenti sostituendo il parametro \[ID tenant \] con il valore dell'ID tenant.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Nell'elenco di agenti che vengono visualizzati: copiare il valore del campo "id" dalla risorsa la cui proprietà *resourceName* è uguale al nome di dominio Active Directory.
* Incollare l'ID in questo comando ed eseguirlo in Powershell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Avviare la configurazione guidata dell'agente.
* Tutti gli altri agenti assegnati precedentemente a questo dominio dovranno essere riconfigurati.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Come si disinstalla l'agente di provisioning?

* Accedere al server di Windows in cui è installato l'agente di provisioning.
* Passare al menu **Pannello di controllo** -> **Disinstalla o modifica programma**
* Disinstallare i programmi seguenti:
  * Microsoft Azure AD Connect Provisioning Agent
  * Microsoft Azure AD Connect Agent Updater
  * Microsoft Azure AD Connect Provisioning Agent Package

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Domande relative al mapping attributi e alla configurazione da Workday ad Active Directory

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Come eseguire il backup o esportare una copia di lavoro del mapping attributi di provisioning e schema di Workday?

È possibile usare l'API Microsoft Graph per esportare la configurazione del provisioning utenti di Workday. Vedere i passaggi descritti nella sezione [Exporting and Importing your Workday User Provisioning Attribute Mapping configuration](#exporting-and-importing-your-configuration) (Esportare e importare la configurazione del mapping attributi di provisioning utenti di Workday) per informazioni dettagliate.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Avere attributi personalizzati in Workday e Azure Active Directory. Come si configura il corretto funzionamento con gli attributi personalizzati della soluzione?

La soluzione supporta gli attributi personalizzati di Workday e Active Directory. Per aggiungere attributi personalizzati allo schema di mapping, aprire il pannello **Attribute Mapping** (Mapping degli attributi) e scorrere verso il basso per espandere la sezione **Show advanced options** (Mostra opzioni avanzate). 

![Modifica elenco attributi](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Per aggiungere gli attributi di Workday personalizzati, selezionare l'opzione *Edit attribute list for Workday* (Modifica elenco attributi per Workday); per aggiungere attributi AD personalizzati, selezionare l'opzione *Edit attribute list for On Premises Active Directory* (Modifica elenco attributi per Active Directory locale).

Vedere anche la pagina relativa alla

* [Personalizzazione dell'elenco di attributi utente di Workday](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Come si configura la soluzione per aggiornare solo gli attributi in Active Directory in base ai cambiamenti di Workday per non creare nuovi account Active Directory?

Questa configurazione può essere ottenuta impostando **Azioni oggetto di destinazione** nel pannello **Mapping degli attributi** come illustrato di seguito:

![Aggiorna azione](./media/workday-inbound-tutorial/wd_target_update_only.png)

Selezionare la casella di controllo "Update" (Aggiorna) solo per le operazioni di aggiornamento da Workday ad Active Directory. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>È possibile effettuare il provisioning delle foto dell'utente da Workday ad Active Directory?

La soluzione attualmente non supporta l'impostazione di attributi binari come *thumbnailPhoto* e *jpegPhoto* in Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Come è possibile sincronizzare i numeri di telefono da Workday per uso pubblico, in base il consenso dell'utente?

* Passare al pannello "Provisioning" dell'app di provisioning Workday.
* Fare clic sui mapping degli attributi 
* In **Mapping** selezionare **Synchronize Workday Workers to On Premises Active Directory** (Sincronizza ruoli di lavoro Workday in Active Directory locale) o **Synchronize Workers to Azure AD** (Sincronizza ruoli di lavoro Workday in Azure AD).
* Nella pagina dei mapping degli attributi, scorrere verso il basso e selezionare la casella "Show Advanced Options" (Mostra opzioni avanzate).  Selezionare **Edit attribute list for Workday** (Modifica elenco attributi per Workday)
* Nel pannello visualizzato, individuare l'attributo "Mobile" e fare clic sulla riga in modo che sia possibile modificare l'**espressione API** ![GDPR per dispositivi mobili](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Sostituire l'**espressione API** con la nuova espressione seguente, che recupera il numero del cellulare di lavoro solo se "Public Usage Flag" (Flag di utilizzo pubblico) è impostato su "True" in Workday.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Salvare l'elenco attributi.
* Salvare il mapping attributi.
* Cancellare lo stato corrente e riavviare la sincronizzazione completa.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Come si formattano i nomi visualizzati in AD in base agli attributi di reparto, paese o città e come si gestiscono le variazioni di area?

È un requisito comune configurare l'attributo *displayName* in AD in modo che fornisca informazioni sul reparto e il paese dell'utente. Ad esempio, se John Smith lavora nel reparto Marketing negli Stati Uniti, è consigliabile che il suo *displayName* compaia come *Smith, John (Marketing-US)*.

Ecco come è possibile gestire tali requisiti per la creazione di *CN* o *displayName* in modo da includere gli attributi, ad esempio società, unità aziendale, città o paese.

* Ogni attributo di Workday viene recuperato usando un'espressione XPATH API sottostante che può essere configurata in **Attribute Mapping -> Advanced Section -> Edit attribute list for Workday** (Mapping degli attributi -> sezione Avanzate -> Modifica elenco attributi per Workday). Ecco l'espressione XPATH API predefinita per Workday *PreferredFirstName*, *PreferredLastName*, *Company* (Azienda) e attributi *SupervisoryOrganization*.

     [!div class="mx-tdCol2BreakAll"]
     | Attributo di Workday | Espressione API XPATH |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Azienda | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Supervisory']/wd:Organization_Name/text() |
  
   Confermare assieme al proprio team Workday che l'espressione API precedente sia valida per la configurazione del tenant di Workday. Se necessario, è possibile modificarle come descritto nella sezione [Customizing the list of Workday user attributes](#customizing-the-list-of-workday-user-attributes) (Personalizzazione dell'elenco di attributi utente di Workday).

* Analogamente, le informazioni relative al paese presenti in Workday vengono recuperate usando l'XPATH seguente: *wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference*

     Sono disponibili 5 attributi relativi al paese che sono disponibili nella sezione dell'elenco attributi di Workday.

     | Attributo di Workday | Espressione API XPATH |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Verificare con il proprio team Workday che l'espressione API precedente sia valida per la configurazione del tenant di Workday. Se necessario, è possibile modificarle come descritto nella sezione [Customizing the list of Workday user attributes](#customizing-the-list-of-workday-user-attributes) (Personalizzazione dell'elenco di attributi utente di Workday).

* Per compilare la corretta espressione di mapping degli attributi, identificare quale attributo di Workday "autorevole" rappresenta il nome proprio, il cognome, il paese e il reparto dell'utente. Si supponga che gli attributi siano rispettivamente *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* e *SupervisoryOrganization*. È possibile usare questo attributo per compilare un'espressione per l'attributo di Active Directory *displayName* come indicato di seguito per ottenere un nome visualizzato, come *Smith, John (Marketing-US)*.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Dopo aver creato l'espressione corretta, modificare la tabella di mapping degli attributi e modificare il mapping degli attributi *displayName*, come illustrato di seguito:   ![Mapping di DisplayName](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Ampliando l'esempio precedente, si consideri l'ipotesi di convertire i nomi di città provenienti da Workday in valori a sintassi abbreviata e usarli per creare nomi visualizzati, come *Smith, John (CHI)* oppure *Doe, Jane (NYC)*, allora questo risultato può essere ottenuto usando un'espressione Switch con l'attributo Workday *Municipality* come variabile determinante.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    Vedere anche la pagina relativa alla
  * [Sintassi della funzione Switch](../manage-apps/functions-for-customizing-application-data.md#switch)
  * [Sintassi della funzione Join](../manage-apps/functions-for-customizing-application-data.md#join)
  * [Sintassi della funzione Append](../manage-apps/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Come è possibile usare SelectUniqueValue per generare valori univoci per l'attributo samAccountName?

Si consideri l'ipotesi di generare valori univoci per l'attributo *samAccountName* mediante una combinazione degli attributi *FirstName* e *LastName* da Workday. Di seguito un'espressione che è possibile iniziare con:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
)
```

Come funziona l'espressione sopra indicata: Se l'utente John Smith tenta di generare JSmith ma JSmith esiste già, l'espressione genera JoSmith, e se presente anche questa, viene generato JohSmith. L'espressione assicura anche che il valore generato soddisfi il limite di lunghezza e le limitazioni di caratteri speciali associati a *samAccountName*.

Vedere anche la pagina relativa alla

* [Sintassi della funzione Mid](../manage-apps/functions-for-customizing-application-data.md#mid)
* [Sintassi della funzione Replace](../manage-apps/functions-for-customizing-application-data.md#replace)
* [Sintassi della funzione SelectUniqueValue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Come rimuovere i caratteri con segno diacritico e convertirli in caratteri alfabetici italiani?

Usare la funzione [NormalizeDiacritics](../manage-apps/functions-for-customizing-application-data.md#normalizediacritics) per rimuovere i caratteri speciali nel nome e cognome dell'utente durante la creazione di un indirizzo di posta elettronica o valore CN per l'utente.

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

Questa sezione fornisce linee guida specifiche su come risolvere i problemi relativi al provisioning con l'integrazione di Workday usando i log di controllo di Azure AD e i log del Visualizzatore eventi di Windows Server. Si basa sui passaggi di risoluzione dei problemi generici e concetti acquisiti nell'[Esercitazione: Creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md)

Questa sezione contiene gli aspetti della risoluzione problemi seguenti:

* [Configurazione di Visualizzatore eventi di Windows per la risoluzione dei problemi dell'agente](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Configurazione dei log di controllo del portale di Azure per la risoluzione dei problemi di servizio](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Riconoscimento di log per operazioni di creazione per l'account utente di AD](#understanding-logs-for-ad-user-account-create-operations)
* [Riconoscimento di log per operazioni di aggiornamento manager](#understanding-logs-for-manager-update-operations)
* [Risoluzione degli errori più comuni](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Configurazione di Visualizzatore eventi di Windows per la risoluzione dei problemi dell'agente

* Accedere al computer server di Windows in cui è installato l'agente di provisioning
* Aprire l'app desktop **Visualizzatore eventi di Windows Server**.
* Selezionare **Registri di Windows > Applicazione**.
* Usare l'opzione **Filter Current Log…** (Filtra log corrente...) per visualizzare tutti gli eventi registrati in **AAD. Connect.ProvisioningAgent** ed escludere gli eventi con ID evento "5", specificando il filtro "-5", come illustrato di seguito.

  ![Visualizzatore eventi di Windows](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Fare clic su **OK** e ordinare la visualizzazione dei risultati dalla colonna di **data e ora**.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Configurazione dei log di controllo del portale di Azure per la risoluzione dei problemi di servizio

* Avviare il [portale di Azure](https://portal.azure.com) e passare alla sezione **Audit logs** (Log di controllo) dell'applicazione di provisioning Workday, come descritto in precedenza in questa esercitazione.
* Usare il pulsante **Columns** (Colonne)nella pagina dei log di controllo per mostrare nella visualizzazione solo le colonne seguenti: data, attività, stato, motivo dello stato. Questa configurazione permette di concentrare l'attenzione solo sui dati rilevanti per la risoluzione dei problemi.

  ![Colonne log di controllo](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Usare i parametri di query **Destinazione** e **Intervallo di date** per filtrare la visualizzazione. 
  * Impostare il parametro di query **Destinazione** all' "ID ruolo di lavoro" o "ID dipendente" dell'oggetto di lavoro di Workday.
  * Impostare **Date Range** (Intervallo di date) per un periodo di tempo appropriato ad analizzare errori o problemi con il provisioning.

  ![Filtri dei log di controllo](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Riconoscimento di log per operazioni di creazione per l'account utente di AD

Quando viene rilevata una nuova assunzione in Workday (si supponga con ID dipendente *21023*), il servizio di provisioning di Azure AD tenta di creare un nuovo account utente di AD per il ruolo di lavoro e crea nel processo 4 record di log di controllo come descritto di seguito:

  [ ![Log di controllo creano operazioni](media/workday-inbound-tutorial/wd_audit_logs_02.png) ](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Quando si fa clic su uno dei record relativi a log di controllo, verrà visualizzata la pagina **Activity Details** (Dettagli attività). Ecco ciò che mostra la pagina **Activity Details** (Dettagli attività) per ogni tipo di record di log.

* Record **di importazione Workday**: in questo record di log vengono visualizzate le informazioni sul ruolo di lavoro recuperate da Workday. Usare le informazioni nella sezione del record del log *Additional Details* (Altri dettagli) per risolvere i problemi con il recupero dei dati da Workday. Di seguito viene illustrato un record di esempio è illustrato insieme a riferimenti sull'interpretazione di ogni campo.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* Record di **importazione AD**: questo record di log consente di visualizzare le informazioni dell'account recuperate da AD. Poiché durante la creazione iniziale dell'utente non esiste un account AD, *Activity Status Reason* (Motivo dello stato attività) indicherà che non è stato trovato alcun account con il valore dell'attributo ID corrispondente in Active Directory. Usare le informazioni nella sezione del record del log *Additional Details* (Altri dettagli) per risolvere i problemi con il recupero dei dati da Workday. Di seguito viene illustrato un record di esempio insieme a riferimenti sull'interpretazione di ogni campo.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Per trovare i record di log dell'agente di provisioning corrispondente a questa operazione di importazione di Active Directory, aprire il log del Visualizzatore eventi di Windows e usare il menu opzioni **Find…** (Trova...) per trovare le voci di log che contengono il valore dell'attributo ID corrispondente o il valore dell'attributo Joining Property (Proprietà di abbinamento) (in questo caso *21023*).

  ![Find](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Cercare la voce con *Event ID = 9*, che fornirà il filtro di ricerca LDAP usato dall'agente per recuperare l'account AD. È possibile verificare se questo è il filtro di ricerca corretto per recuperare le voci univoche dell'utente.

  ![Ricerca LDAP](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  Il record che lo segue immediatamente con *Event ID = 2* acquisisce il risultato dell'operazione di ricerca e comunica se ha prodotto risultati.

  ![Risultati LDAP](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* Record dell'**azione della regola di sincronizzazione**: questo record di log mostra i risultati delle regole di mapping di attributo e configura i filtri di ambito insieme all'azione di provisioning che verrà eseguita per elaborare l'evento in ingresso di Workday. Usare le informazioni nella sezione del record del log *Additional Details* (Altri dettagli) per risolvere i problemi con l'azione di sincronizzazione. Di seguito viene illustrato un record di esempio insieme a riferimenti sull'interpretazione di ogni campo.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Se si verificano problemi con le espressioni di mapping di attributi o i dati in ingresso di Workday presentano problemi (ad esempio: valori vuoti oppure null per gli attributi obbligatori), sarà possibile osservare un errore in questa fase con il codice di errore che ne fornisce i dettagli.

* Record di **esportazione AD**: questo record di log consente di visualizzare il risultato dell'operazione di creazione dell'account AD insieme ai valori di attributo che sono stati impostati nel processo. Usare le informazioni nella sezione del record del log *Additional Details* (Altri dettagli) per risolvere i problemi con l'operazione di creazione account. Di seguito viene illustrato un record di esempio insieme a riferimenti sull'interpretazione di ogni campo. Nella sezione "Additional Details" (Dettagli aggiuntivi), "EventName" è impostato su "EntryExportAdd", "JoiningProperty" è impostata sul valore dell'attributo ID di abbinamento, "SourceAnchor" è impostato su WorkdayID (WID) associato al record e "TargetAnchor" è impostato sul valore dell'attributo AD "ObjectGuid" dell'utente appena creato. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Per trovare i record di log dell'agente di provisioning corrispondente a questa operazione di importazione di AD, aprire il log del Visualizzatore eventi di Windows e usare il menu opzioni **Find…** (Trova...) per trovare le voci di log che contengono il valore dell'attributo ID corrispondente o il valore dell'attributo Joining Property (Proprietà di abbinamento) (in questo caso *21023*).  

  Cercare un record HTTP POST corrispondente al timestamp dell'operazione di esportazione con *Event ID = 2*. Questo record contiene i valori di attributo inviati dal servizio di provisioning all'agente di provisioning.

  [![Aggiungere SCIM](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Immediatamente dopo l'evento precedente, deve essere presente un altro evento che acquisisce la risposta dell'operazione di creazione account AD. Questo evento restituisce il nuovo objectGuid creato in AD e lo imposta come attributo TargetAnchor nel servizio di provisioning.

  [![Aggiungere SCIM](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Riconoscimento di log per operazioni di aggiornamento manager

L'attributo manager è un attributo di riferimento in AD. Il servizio di provisioning non imposta l'attributo manager durante l'operazione di creazione dell'utente. Piuttosto l'attributo manager viene impostato come parte di un'operazione di *aggiornamento* dopo la creazione di account di AD per l'utente. Estendendo l'esempio precedente, si supponga che una nuova assunzione con ID dipendente "21451" venga attivata in Workday e che il manager del nuovo assunto (*21023*) abbia già un account AD. In questo scenario, la ricerca del log di controllo per l'utente 21451 mostra 5 voci.

  [ ![Aggiornamento manager](media/workday-inbound-tutorial/wd_audit_logs_03.png) ](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

I primi 4 record sono come quelli descritti come parte della creazione dell'utente. Il 5° record è l'esportazione associata aggiornamento dell'attributo manager. Il record del log mostra il risultato dell'operazione di aggiornamento manager dell'account AD, che viene eseguita usando l'attributo manager *objectGuid*.

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>Risoluzione degli errori più comuni

Questa sezione illustra gli errori più comuni riscontrati con il provisioning utenti Workday e la relativa risoluzione. Le macchine virtuali sono raggruppate come indicato di seguito:

* [Errori agente di provisioning](#provisioning-agent-errors)
* [Errori di connettività](#connectivity-errors)
* [Errori di creazione account utente AD](#ad-user-account-creation-errors)
* [Errori di aggiornamento account utente AD](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Errori agente di provisioning

|#|Scenario di errore |Possibili cause|Risoluzione consigliata|
|--|---|---|---|
|1.| Errore durante l'installazione dell'agente di provisioning con messaggio di errore:  *Service 'Microsoft Azure AD Connect Provisioning Agent' (AADConnectProvisioningAgent) failed to start. Verify that you have sufficient privileges to start the system.* Impossibile avviare il servizio "Microsoft Azure AD Connect Provisioning Agent" (AADConnectProvisioningAgent). Verificare di disporre di privilegi sufficienti per l'arresto dei servizi di sistema. | Questo errore viene in genere visualizzato se si sta provando a installare l'agente di provisioning in un controller di dominio e il criterio di gruppo impedisce l'avvio del servizio.  Viene visualizzato anche se si dispone di una versione precedente dell'agente in esecuzione e non è stato disinstallato prima di avviare una nuova installazione.| Installare l'agente di provisioning in un server non DC. Assicurarsi che le versioni precedenti dell'agente vengano disinstallate prima di installare il nuovo agente.|
|2.| Il servizio Windows "Microsoft Azure AD Connect Provisioning Agent" è in stato di *avvio* e non passa allo stato di *esecuzione*. | Come parte dell'installazione, la procedura guidata dell'agente crea un account locale (**NT Service\\AADConnectProvisioningAgent**) nel server che corrisponde all'account di **accesso** usato per l'avvio del servizio. Questo errore si verifica se un criterio di sicurezza nel server Windows impedisce agli account locali di eseguire i servizi. | Aprire la *console dei servizi*. Fare clic con il pulsante destro del mouse su "Microsoft Azure AD Connect Provisioning Agent" (Agente di provisioning di Microsoft Azure Active Directory Connect) e specificare l'account dell'amministratore di dominio nella scheda di accesso per eseguire il servizio. Riavviare il servizio. |
|3.| Quando si configura l'agente di provisioning con il dominio di AD nel passaggio *Connect Active Directory* (Connetti Active Directory), la procedura guidata richiede troppo tempo a caricare lo schema di AD infine raggiunge il timeout. | Questo errore in genere viene visualizzato se la procedura guidata non riesce a contattare il server di controller di dominio AD a causa di problemi relativi al firewall. | Nella schermata di procedura guidata *Connect Active Directory* (Connetti Active Directory), pur fornendo le credenziali per il dominio AD, è presente un'opzione chiamata *Select domain controller priority* (Seleziona la priorità del controller di dominio). Usare questa opzione per selezionare un controller di dominio che sia presente nello stesso sito dell'agente del server e assicurarsi che non siano presenti regole firewall che bloccano la comunicazione. |

#### <a name="connectivity-errors"></a>Errori di connettività

Se il servizio di provisioning non è in grado di connettersi a Workday o Active Directory, ciò potrebbe causare lo stato di quarantena del provisioning stesso. Usare la tabella seguente per risolvere i problemi di connettività.

|#|Scenario di errore |Possibili cause|Risoluzione consigliata|
|--|---|---|---|
|1.| Quando si fa clic su **Test Connection** (Connessione di test), viene visualizzato il messaggio di errore: *There was an error connecting to Active Directory. Please ensure that the on-premises Provisioning Agent is running and it is configured with the correct Active Directory domain.* (Errore di connessione ad Active Directory. Accertarsi che l'agente di provisioning locale sia in esecuzione e che sia configurato con il dominio Active Directory corretto.) | Questo errore in genere viene visualizzato se l'agente di provisioning non è in esecuzione o se è presente un firewall che blocca la comunicazione tra Azure AD e l'agente di provisioning. È inoltre possibile visualizzare questo errore, se il dominio non è configurato nella procedura guidata dell'agente. | Aprire la console dei *servizi* nel server di Windows per verificare che l'agente sia in esecuzione. Aprire la configurazione guidata dell'agente di provisioning e verificare che il dominio corretto sia stato registrato con l'agente.  |
|2.| Il processo di provisioning entra in stato di quarantena nei fine settimana (ven-sab) e si ottiene una notifica di posta elettronica che segnala la presenza di un errore con la sincronizzazione. | Una delle cause più comuni di questo errore è il tempo di inattività pianificato di Workday. Se si usa un tenant di implementazione di Workday, tenere presente che Workday ha pianificato i tempi di inattività per i suoi tenant di implementazione durante i fine settimana (in genere da venerdì sera a sabato mattina) e, in tale periodo, l'app di provisioning Workday potrebbe passare alla stato di quarantena perché non in grado di connettersi a Workday. Ritorna allo stato normale quando il tenant di implementazione di Workday torna online. In rari casi, è inoltre possibile visualizzare questo errore se la password utente del sistema di integrazione viene modificata a causa di un aggiornamento del tenant o se l'account è bloccato o scaduto. | Verificare con il proprio amministratore o partner di integrazione Workday per capire quando Workday pianifica i tempi di inattività per ignorare i messaggi di avviso durante il periodo di inattività e confermare la disponibilità una volta che l'istanza Workday è di nuovo online.  |


#### <a name="ad-user-account-creation-errors"></a>Errori di creazione account utente AD

|#|Scenario di errore |Possibili cause|Risoluzione consigliata|
|--|---|---|---|
|1.| Errori nelle operazioni di esportazione nel log di controllo con il messaggio *Errore: OperationsError-SvcErr: Errore dell'operazione. Non è stato configurato alcun riferimento superiore per il servizio directory. Il servizio directory non è quindi in grado di generare riferimenti a oggetti che si trovano all'esterno di questa foresta.* | Questo errore viene in genere visualizzato se l'unità organizzativa *Contenitore di Active Directory* non è impostata correttamente o se si verificano problemi con l'espressione di mapping usata per *parentDistinguishedName*. | Controllare il parametro unità organizzativa *Contenitore Active Directory* per errori di digitazione. Se si usa *parentDistinguishedName* nel mapping attributi, assicurarsi che esegue sempre la valutazione in un contenitore noto all'interno del dominio di AD. Verificare gli eventi di *esportazione* nei log di controllo per visualizzare il valore generato. |
|2.| Errori nelle operazioni di esportazione nel log di controllo con il codice di errore: *SystemForCrossDomainIdentityManagementBadResponse* e il messaggio di *Errore: ConstraintViolation-AtrErr: Un valore nella richiesta non è valido. Un valore per l'attributo è al di fuori dell'intervallo di valori consentito. \nError Details: CONSTRAINT_ATT_TYPE - company*. | Mentre questo errore è specifico per l'attributo *company* (azienda) questo errore può verificarsi per gli altri attributi, ad esempio *CN* anche. Questo errore viene visualizzato a causa di un vincolo dello schema AD applicato. Per impostazione predefinita, come gli attributi *società* e *CN* in AD presentano un limite massimo di 64 caratteri. Se il valore proveniente da Workday conta più di 64 caratteri, verrà visualizzato il messaggio di errore. | Verificare l'evento di *esportazione* nei log di controllo per visualizzare il valore per l'attributo riportato nel messaggio di errore. Prendere in considerazione il troncamento di valore proveniente da Workday tramite la funzione[Mid](../manage-apps/functions-for-customizing-application-data.md#mid) o modificare i mapping di un attributo di AD che non ha vincoli di lunghezza simili.  |

#### <a name="ad-user-account-update-errors"></a>Errori di aggiornamento account utente Active Directory

Durante il processo di aggiornamento dell'account utente di AD, il servizio di provisioning legge le informazioni da Workday e AD, esegue le regole di mapping degli attributi e determina se debba essere applicata una modifica. Di conseguenza si attiva un evento di aggiornamento. Se in uno di questi passaggi viene rilevato un errore, viene registrato nei log di controllo. Usare la tabella seguente per risolvere i problemi di aggiornamento.

|#|Scenario di errore |Possibili cause|Risoluzione consigliata|
|--|---|---|---|
|1.| Errori di azione delle regole di sincronizzazione nel log di controllo con il messaggio*EventName = EntrySynchronizationError ed ErrorCode = EndpointUnavailable*. | Questo errore viene visualizzato se il servizio di provisioning non riesce a recuperare i dati del profilo utente da Active Directory a causa di un errore di elaborazione rilevato dagli agenti di provisioning locali. | Verificare nei log del Visualizzatore eventi dell'agente di provisioning per gli eventi di errore che indicano problemi con l'operazione di lettura (Filtra per ID evento n. 2). |
|2.| L'attributo manager in AD non viene aggiornato per determinati utenti in AD. | La causa più probabile di questo errore è l'uso di regole di ambito e se il responsabile dell'utente non fa parte dell'ambito. È anche possibile riscontrare questo problema se l'attributo ID corrispondente del manager (ad esempio EmployeeID) non viene trovato nella destinazione di dominio AD o non è impostato sul valore corretto. | Esaminare il filtro di ambito e aggiungere l'utente manager nell'ambito. Controllare il profilo manager in AD per assicurarsi che vi sia un valore per l'attributo ID corrispondente. |

## <a name="managing-your-configuration"></a>Gestire la configurazione

Questa sezione descrive come è possibile estendere, personalizzare e gestire ulteriormente la configurazione del provisioning utenti basato su Workday. Include gli argomenti seguenti:

* [Personalizzazione dell'elenco di attributi utente di Workday](#customizing-the-list-of-workday-user-attributes)  
* [Esportazione e importazione della configurazione](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Personalizzazione dell'elenco di attributi utente di Workday

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

    ```xml
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

13. Sulla barra dei comandi di Workday Studio selezionare **File > Open File** (File > Apri file) e aprire il file XML salvato. Questa azione permetterà l'apertura del file nell'editor XML di Workday Studio.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. Nell'albero di file, spostarsi attraverso **/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker** per ritrovare i dati dell'utente.

15. In **wd: Worker** trovare l'attributo da aggiungere e selezionarlo.

16. Copiare l'espressione XPath per l'attributo selezionato dal campo **Document Path** (Percorso documento).

17. Rimuovere il prefisso **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** dall'espressione copiata.

18. Se l'ultimo elemento dell'espressione copiata è un nodo (esempio: "/ wd: Birth_Date"), accodare **/text ()** alla fine dell'espressione. Ciò non è necessario se l'ultimo elemento è un attributo (ad esempio: "/@wd: type").

19. Il risultato dovrebbe essere simile a `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Questo è il valore che verrà copiato nel portale di Azure.

**Per aggiungere l'attributo utente Workday personalizzato alla configurazione del provisioning:**

1. Avviare il [portale di Azure](https://portal.azure.com) e passare alla sezione Provisioning dell'applicazione di provisioning di Workday, come descritto in precedenza in questa esercitazione.

2. Impostare **Stato del provisioning** su **No** e selezionare **Salva**. In questo modo, le modifiche verranno applicate solo quando si è pronti.

3. In **Mapping** selezionare **Synchronize Workday Workers to On Premises Active Directory** (Sincronizza ruoli di lavoro Workday in Active Directory locale) o **Synchronize Workers to Azure AD** (Sincronizza ruoli di lavoro Workday in Azure AD).

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

12. Tornando alla scheda **Provisioning** selezionare di nuovo **Synchronize Workers to On Premises Active Directory** (Sincronizza ruoli di lavoro Workday in Active Directory locale) o di nuovo **Synchronize Workers to Azure AD** (Sincronizza ruoli di lavoro Workday in Azure AD).

13. Selezionare **Aggiungi nuovo mapping**.

14. Il nuovo attributo dovrebbe ora essere visualizzato nell'elenco **Attributo di origine**.

15. Aggiungere un mapping per il nuovo attributo in base alle esigenze.

16. Al termine, ricordarsi di impostare **Stato del provisioning** di nuovo su **Sì** e salvare.

### <a name="exporting-and-importing-your-configuration"></a>Esportare e importare la configurazione

Questa sezione descrive come usare l'API Microsoft Graph e Graph Explorer per esportare i mapping e gli schemi degli attributi di provisioning di Workday in un file JSON e come importarli nuovamente in Azure AD.

#### <a name="step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id"></a>Passaggio 1: Recuperare l'ID di entità Servizio app di provisioning Workday (ID oggetto)

1. Avviare il [portale di Azure](https://portal.azure.com) e passare alla sezione delle proprietà dell'applicazione di provisioning Workday.
1. Nella sezione delle proprietà dell'app di provisioning, copiare il valore GUID associato al campo *Object ID* (ID oggetto). Questo valore è denominato anche **ServicePrincipalId** dell'app e verrà usato nelle operazioni di Graph Explorer.

   ![ID entità Servizio app Workday](./media/workday-inbound-tutorial/wd_export_01.png)

#### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passaggio 2: Accedere a Microsoft Graph Explorer

1. Avviare [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Fare clic sul pulsante "Sign-In with Microsoft" (Accedi con Microsoft) e accedere con le credenziali di amministratore globale di AD o di amministratore dell'app.

    ![Accedere a Graph](./media/workday-inbound-tutorial/wd_export_02.png)

1. Quando l'accesso è stato completato, vengono visualizzati i dettagli dell'account utente nel riquadro sinistro.

#### <a name="step-3-retrieve-the-provisioning-job-id-of-the-workday-provisioning-app"></a>Passaggio 3: Recuperare l'ID del processo di provisioning dell'app di provisioning Workday

In Microsoft Graph Explorer, eseguire la query GET seguente, sostituendo [servicePrincipalId] con il **ServicePrincipalId** estratto dal [Passaggio 1](#step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Si otterrà una risposta come illustrato di seguito. Copiare l'attributo"id" presente nella risposta. Questo valore è il **ProvisioningJobId** e verrà usato per recuperare i metadati dello schema sottostante.

   [![Id processo di provisioning](./media/workday-inbound-tutorial/wd_export_03.png)](./media/workday-inbound-tutorial/wd_export_03.png#lightbox)

#### <a name="step-4-download-the-provisioning-schema"></a>Passaggio 4: Scaricare lo schema di provisioning

In Microsoft Graph Explorer, eseguire la query GET seguente, sostituendo [servicePrincipalId] e [ProvisioningJobId] con il ServicePrincipalId e il ProvisioningJobId recuperati nei passaggi precedenti.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Copiare l'oggetto JSON dalla risposta e salvarlo in un file per creare un backup dello schema.

#### <a name="step-5-import-the-provisioning-schema"></a>Passaggio 5: Importare lo schema di provisioning

> [!CAUTION]
> Eseguire questo passaggio solo se è necessario modificare lo schema per la configurazione che non può essere modificato usando il portale di Azure o se è necessario ripristinare la configurazione da un file di cui è stato eseguito il backup in precedenza con uno schema di lavoro valido.

In Microsoft Graph Explorer, eseguire la query PUT seguente, sostituendo [servicePrincipalId] e [ProvisioningJobId] con il ServicePrincipalId e il ProvisioningJobId recuperati nei passaggi precedenti.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Nella scheda "Corpo della richiesta" copiare il contenuto del file dello schema JSON.

   [![Corpo della richiesta](./media/workday-inbound-tutorial/wd_export_04.png)](./media/workday-inbound-tutorial/wd_export_04.png#lightbox)

Nella scheda "Richiesta delle intestazioni" aggiungere l'attributo di intestazione Content-Type con valore "application/json"

   [![Intestazioni della richiesta](./media/workday-inbound-tutorial/wd_export_05.png)](./media/workday-inbound-tutorial/wd_export_05.png#lightbox)

Fare clic sul pulsante "Run Query" (Esegui Query) per importare il nuovo schema.

## <a name="managing-personal-data"></a>Gestione dei dati personali

La soluzione di provisioning di Workday per Active Directory richiede l'installazione di un agente di provisioning in un server locale Windows. Questo agente crea i log nel registro eventi di Windows che può contenere informazioni personali a seconda dei mapping degli attributi da Workday in agli attributi mapping di AD. Per conformità agli obblighi di privacy degli utenti, è possibile garantire che nessun dato venga conservato nei registri eventi oltre le 48 ore impostando un'attività pianificata di Windows per eliminare il log eventi.

Il servizio di provisioning di Azure AD rientra nella categoria di **elaboratore dati** della classificazione del GDPR. In quanto pipeline di elaborazione dati, Azure AD Connect Health fornisce servizi di elaborazione dati ai principali partner e utenti finali. Il servizio di provisioning di Azure AD non genera dati utente e non ha alcun controllo indipendente su quali dati personali vengono raccolti e su come vengono usati. Il recupero, l'aggregazione, l'analisi e la creazione di report dei dati nel servizio di provisioning di Azure AD si basano sui dati aziendali esistenti.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Per quanto riguarda la conservazione dei dati, il servizio di provisioning di Azure AD non genera report, non esegue analisi o fornisce informazioni dettagliate oltre i 30 giorni. Di conseguenza, il servizio di provisioning di Azure AD non archivia, elabora o conserva i dati oltre i 30 giorni. Ciò è conforme al GDPR, all'Informativa sulla privacy Microsoft e ai criteri di conservazione dei dati di Azure AD.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
* [Informazioni su come configurare l'accesso Single Sign-On tra Workday e Azure Active Directory](workday-tutorial.md)
* [Informazioni sull'integrazione di altre applicazioni SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni su come usare le API Microsoft Graph per gestire le configurazioni di provisioning](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
