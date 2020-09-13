---
title: 'Esercitazione: configurare il provisioning in ingresso per la giornata lavorativa in Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare il provisioning in ingresso da giorni lavorativi a Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 7d47c21da1279271b12933a2e4642abcce622600
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90015484"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>Esercitazione: configurare la giornata lavorativa per il provisioning degli utenti Azure AD
Questa esercitazione descrive la procedura da eseguire per effettuare il provisioning dei dati di lavoro da giorni lavorativi a Azure Active Directory. 

>[!NOTE]
>Usare questa esercitazione se gli utenti di cui si vuole eseguire il provisioning dalla giornata lavorativa sono utenti solo cloud che non necessitano di un account AD locale. Se gli utenti necessitano solo di un account AD locale o di un account AD e Azure AD, fare riferimento all'esercitazione sulla [configurazione della giornata lavorativa per Active Directory](workday-inbound-tutorial.md) il provisioning degli utenti. 

## <a name="overview"></a>Panoramica

Il [servizio di provisioning utenti di Azure Active Directory](../app-provisioning/user-provisioning.md) si integra con l'[API Human Resources di Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) per il provisioning degli account utente. I flussi di lavoro di provisioning utenti di Workday supportati dal servizio di provisioning utenti di Azure AD consentono l'automazione dei seguenti scenari di gestione delle risorse umane e del ciclo di vita delle identità:

* **Assunzione di nuovi dipendenti** : quando un nuovo dipendente viene aggiunto alla giornata lavorativa, viene creato automaticamente un account utente in Azure Active Directory e, facoltativamente, Microsoft 365 e [altre applicazioni SaaS supportate da Azure ad](../app-provisioning/user-provisioning.md), con Write-back dell'indirizzo di posta elettronica per la giornata lavorativa.

* **Aggiornamenti del profilo e dell'attributo del dipendente** : quando un record del dipendente viene aggiornato nella giornata lavorativa (ad esempio, il nome, il titolo o il responsabile), il relativo account utente verrà aggiornato automaticamente Azure Active Directory e, facoltativamente, Microsoft 365 e [altre applicazioni SaaS supportate da Azure ad](../app-provisioning/user-provisioning.md).

* **Interruzioni dei dipendenti** : quando un dipendente viene terminato nella giornata lavorativa, il relativo account utente viene disabilitato automaticamente in Azure Active Directory e, facoltativamente, Microsoft 365 e [altre applicazioni SaaS supportate da Azure ad](../app-provisioning/user-provisioning.md).

* **Riassunzioni dei dipendenti** : quando un dipendente viene riassunto nella giornata lavorativa, il relativo account precedente può essere riattivato automaticamente o nuovamente sottoposto a provisioning (a seconda delle preferenze) per Azure Active Directory e, facoltativamente, Microsoft 365 e [altre applicazioni SaaS supportate da Azure ad](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Per chi è più adatta questa soluzione di provisioning utenti?

Questa giornata lavorativa per Azure Active Directory soluzione di provisioning degli utenti è particolarmente adatta per:

* Organizzazioni che desiderano una soluzione predefinita basata sul cloud per il provisioning utenti Workday

* Organizzazioni che richiedono il provisioning utenti diretto dalla giornata lavorativa a Azure Active Directory

* Organizzazioni che richiedono il provisioning degli utenti usando i dati ottenuti dalla giornata lavorativa

* Organizzazioni che usano Microsoft 365 per la posta elettronica

## <a name="solution-architecture"></a>Architettura della soluzione

Questa sezione descrive l'architettura della soluzione di provisioning degli utenti end-to-end per gli utenti solo cloud. Esistono due flussi correlati:

* **Flusso di dati HR autorevole: da giorni lavorativi a Azure Active Directory:** In questo flusso, i flussi di lavoro, ad esempio i nuovi assunti, i trasferimenti, le terminazioni, si verificano per la prima volta nella giornata lavorativa, quindi i dati degli eventi passano a Azure Active Directory A seconda dell'evento, può causare operazioni di creazione, aggiornamento, abilitazione o disabilitazione in Azure AD.
* **Flusso di writeback: dalla Active Directory locale alla giornata lavorativa:** Quando la creazione dell'account viene completata in Active Directory, viene sincronizzata con Azure AD tramite Azure AD Connect e le informazioni, ad esempio la posta elettronica, il nome utente e il numero di telefono possono essere riscritte nella giornata lavorativa.

  ![Panoramica](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>Flusso di dati end-to-end dell'utente

1. Il team HR esegue transazioni di lavoro (join/spostamenti/abbandonati o nuove assunzioni/trasferimenti/terminazioni) nel giorno lavorativo centrale
2. Il servizio di provisioning Azure AD esegue sincronizzazioni pianificate di identità da giorni lavorativi EC e identifica le modifiche che devono essere elaborate per la sincronizzazione con Active Directory locale.
3. Il servizio di provisioning Azure AD determina la modifica e richiama l'operazione di creazione/aggiornamento/abilitazione/disabilitazione per l'utente in Azure AD.
4. Se l'app [writeback dei giorni lavorativi](workday-writeback-tutorial.md) è configurata, recupera gli attributi, ad esempio posta elettronica, nome utente e numero di telefono da Azure ad. 
5. Azure AD servizio di provisioning imposta la posta elettronica, il nome utente e il numero di telefono nella giornata lavorativa

## <a name="planning-your-deployment"></a>Pianificazione della distribuzione

La configurazione del provisioning utenti basato su cloud HR da giorni lavorativi a Azure AD richiede una pianificazione considerevole che copre aspetti diversi, ad esempio:

* Determinazione dell'ID corrispondente 
* Mapping degli attributi
* Trasformazione attributo 
* Filtri per la definizione dell'ambito

Per indicazioni complete su questi argomenti, vedere il [piano di distribuzione HR cloud](../app-provisioning/plan-cloud-hr-provision.md) . 

## <a name="configure-integration-system-user-in-workday"></a>Configurare un utente del sistema di integrazione in Workday

Vedere la sezione [configurare l'utente del sistema di integrazione](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) per la creazione di un account utente del sistema di integrazione lavorativa con le autorizzazioni per recuperare i dati di lavoro. 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>Configurare il provisioning utenti dalla giornata lavorativa al Azure AD

Le sezioni seguenti descrivono i passaggi per la configurazione del provisioning utenti da Workday ad Azure AD per le distribuzioni solo cloud.

* [Aggiungere l'app del connettore di provisioning in Azure AD e creare la connessione a Workday](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Configurare i mapping degli attributi di Workday e Azure AD](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Abilitare e avviare il provisioning utenti](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: aggiungere l'app del connettore di provisioning in Azure AD e creare la connessione a Workday

**Per configurare il provisioning da Workday ad Azure Active Directory per gli utenti solo cloud:**

1. Passare a <https://portal.azure.com>.

2. Nel portale di Azure cercare e selezionare **Azure Active Directory**.

3. Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4. Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5. Cercare la **giornata lavorativa per Azure ad il provisioning degli utenti**e aggiungere tale app dalla raccolta.

6. Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**.

7. Modificare **Modalità** di **provisioning** su **Automatico**.

8. Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente Workday**: immettere il nome utente dell'account del sistema di integrazione Workday, aggiungendo il nome di dominio del tenant. Dovrebbe essere simile a: username@contoso4

   * **Password Workday:** immettere la password dell'account del sistema di integrazione Workday

   * **URL dell'API servizi Web giorni lavorativi-** Immettere l'URL dell'endpoint dei servizi Web della giornata lavorativa per il tenant. L'URL determina la versione dell'API dei servizi Web della giornata lavorativa usata dal connettore. 
   
     | Formato di URL | Versione dell'API WWS usata | Modifiche XPATH richieste |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v 21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v 21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v # #. # | Sì |

      > [!NOTE]
     > Se nell'URL non è specificata alcuna informazione sulla versione, l'app usa i servizi Web della giornata lavorativa (WWS) v 21.1 e non sono necessarie modifiche alle espressioni API XPATH predefinite fornite con l'app. Per usare una versione specifica dell'API WWS, specificare il numero di versione nell'URL <br>
     > Esempio: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Se si usa un'API WWS v 30 +, prima di attivare il processo di provisioning, aggiornare le **espressioni API XPath** in **Mapping attributi-> opzioni avanzate-> elenco di attributi per la giornata lavorativa** che fa riferimento alla sezione [gestione della configurazione](workday-inbound-tutorial.md#managing-your-configuration) e informazioni di [riferimento sugli attributi](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30).  

   * **Messaggio di posta elettronica di notifica:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo per inviare una notifica di posta elettronica in caso di errore.

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

      * **Espressione**: consente di scrivere un valore personalizzato per l'attributo di Active Directory, in base a uno o più attributi di Workday. [Per altre informazioni, vedere questo articolo sulle espressioni](../app-provisioning/functions-for-customizing-application-data.md).

   * **Attributo di origine**: l'attributo utente in Workday. Se l'attributo che si sta cercando non è presente, vedere [Personalizzazione dell'elenco di attributi utente di Workday](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes).

   * **Valore predefinito**: facoltativo. Se l'attributo di origine ha un valore vuoto, il mapping eseguirà la scrittura di questo valore.
            Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

   * **Attributo di destinazione**: l'attributo utente in Azure AD.

   * **Match objects using this attribute** (Abbina gli oggetti in base a questo attributo): specifica se questo mapping deve essere usato per l'identificazione univoca degli utenti tra Workday e Azure AD. Questo valore è impostato in genere sul campo ID ruolo di lavoro per Workday, che solitamente è associato all'attributo ID dipendente (nuovo) o a un attributo di estensione in Azure AD.

   * **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Se sono presenti più attributi, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.

   * **Applica questo mapping**

     * **Sempre**: applica il mapping sia all'azione di creazione che all'azione di aggiornamento dell'utente

     * **Only during creation** (Solo durante la creazione): applica il mapping solo alle azioni di creazione dell'utente

6. Per salvare i mapping, fare clic su **Salva** nella parte superiore della sezione Mapping attributi.


## <a name="enable-and-launch-user-provisioning"></a>Abilitare e avviare il provisioning utenti

Dopo aver completato le configurazioni dell'app di provisioning Workday, è possibile attivare il servizio di provisioning nel portale di Azure.

> [!TIP]
> Per impostazione predefinita quando si attiva il servizio di provisioning, verranno avviate le operazioni di provisioning per tutti gli utenti nell'ambito. Se sono presenti errori di mapping o problemi di dati in Workday, il processo di provisioning potrebbe non riuscire e passare allo stato di quarantena. Come procedura consigliata per evitare questo problema è consigliabile configurare il filtro **Source Object Scope** (Ambito dell'oggetto di origine) e il test di mapping degli attributi con alcuni utenti test prima di avviare la sincronizzazione completa per tutti gli utenti. Dopo avere verificato che i mapping funzionino e che restituiscano i risultati desiderati è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

1. Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

2. Fare clic su **Salva**.

3. Questa operazione avvierà la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti nel tenant di Workday. È possibile controllare l'indicatore di stato per tenere traccia dello stato di avanzamento del ciclo di sincronizzazione. 

4. In qualsiasi momento è possibile controllare la scheda **Log di controllo** nel portale di Azure per vedere le azioni che sono state eseguite dal servizio di provisioning. I log di controllo elencano tutti gli eventi di sincronizzazione singoli eseguiti dal servizio di provisioning, ad esempio quali utenti vengono letti dalla giornata lavorativa e successivamente aggiunti o aggiornati a Azure Active Directory. 

5. Al termine della sincronizzazione iniziale, verrà scritto un report di riepilogo di controllo nella scheda **Provisioning**, come illustrato di seguito.

   > [!div class="mx-imgBorder"]
   > ![Indicatore di stato del provisioning](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sugli attributi di giornata lavorativa supportati per il provisioning in ingresso](../app-provisioning/workday-attribute-reference.md)
* [Informazioni su come configurare il writeback della giornata lavorativa](workday-writeback-tutorial.md)
* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
* [Informazioni su come configurare l'accesso Single Sign-On tra Workday e Azure Active Directory](workday-tutorial.md)
* [Informazioni sull'integrazione di altre applicazioni SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni su come esportare e importare le configurazioni di provisioning](../app-provisioning/export-import-provisioning-configuration.md)


