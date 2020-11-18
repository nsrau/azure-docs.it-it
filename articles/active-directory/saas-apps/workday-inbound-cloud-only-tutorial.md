---
title: 'Esercitazione: Configurare il provisioning in ingresso di Workday in Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare il provisioning in ingresso da Workday ad Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 197b7ff0a6c613a019007ba507d678b619c9afd4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358593"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>Esercitazione: Configurare il provisioning utenti da Workday ad Azure AD
Questa esercitazione illustra la procedura da eseguire per effettuare il provisioning dei dati dei lavoratori da Workday in Azure Active Directory. 

>[!NOTE]
>Usare questa esercitazione se gli utenti di cui si vuole effettuare il provisioning da Workday sono utenti solo cloud che non necessitano di un account AD locale. Se gli utenti hanno bisogno solo di un account AD locale o sia di un account AD locale che di un account Azure AD, vedere l'esercitazione [Configurare Workday per il provisioning utenti automatico](workday-inbound-tutorial.md). 

## <a name="overview"></a>Panoramica

Il [servizio di provisioning utenti di Azure Active Directory](../app-provisioning/user-provisioning.md) si integra con l'[API Human Resources di Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) per il provisioning degli account utente. I flussi di lavoro di provisioning utenti di Workday supportati dal servizio di provisioning utenti di Azure AD consentono l'automazione dei seguenti scenari di gestione delle risorse umane e del ciclo di vita delle identità:

* **Assunzione di nuovi dipendenti**: quando viene aggiunto un nuovo dipendente a Workday, viene creato automaticamente un account utente in Azure Active Directory e, facoltativamente, in Microsoft 365 e in [altre applicazioni SaaS supportate da Azure AD](../app-provisioning/user-provisioning.md), con il writeback dell'indirizzo di posta elettronica in Workday.

* **Aggiornamenti di attributi e profili dei dipendenti**: se il record di un dipendente viene aggiornato in Workday (ad esempio il nome, la qualifica o il manager), il relativo account utente verrà aggiornato automaticamente in Azure Active Directory e, facoltativamente, in Microsoft 365 e in [altre applicazioni SaaS supportate da Azure AD](../app-provisioning/user-provisioning.md).

* **Termine del rapporto con i dipendenti**: quando un dipendente viene eliminato da Workday, il relativo account utente viene disabilitato automaticamente in Azure Active Directory e, facoltativamente, in Microsoft 365 e in [altre applicazioni SaaS supportate da Azure AD](../app-provisioning/user-provisioning.md).

* **Riassunzioni di dipendenti**: quando un dipendente viene nuovamente aggiunto in Workday, il relativo account utente precedente può essere automaticamente riattivato o sottoposto di nuovo a provisioning (a seconda delle preferenze) in Azure Active Directory e, facoltativamente, in Microsoft 365 e in [altre applicazioni SaaS supportate da Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Per chi è più adatta questa soluzione di provisioning utenti?

Questa soluzione di provisioning utenti da Workday ad Azure Active Directory è idealmente la più appropriata per:

* Organizzazioni che desiderano una soluzione predefinita basata sul cloud per il provisioning utenti Workday

* Organizzazioni che richiedono il provisioning utenti diretto da Workday ad Azure Active Directory

* Organizzazioni che richiedono che il provisioning utenti venga effettuato tramite i dati ottenuti da modulo Workday

* Organizzazioni che usano Microsoft 365 per la posta elettronica

## <a name="solution-architecture"></a>Architettura della soluzione

Questa sezione descrive l'architettura della soluzione end-to-end di provisioning degli utenti per utenti solo cloud. Esistono due flussi correlati:

* **Flusso di dati HR autorevoli - da Workday ad Azure Active Directory:** in questo flusso gli eventi relativi ai lavoratori (ad esempio, nuove assunzioni, trasferimenti e risoluzioni del rapporto) si verificano prima in Workday nel cloud e quindi i dati degli eventi vengono trasmessi in Azure Active Directory. A seconda dell'evento, può determinare operazioni di creazione/aggiornamento/abilitazione o disabilitazione in Azure AD.
* **Flusso di writeback - da un'istanza locale di Active Directory a Workday:** al termine della creazione in Active Directory, l'account viene sincronizzato con Azure AD tramite Azure AD Connect ed è possibile eseguire il writeback in Workday di informazioni come l'indirizzo di posta elettronica, il nome utente e il numero di telefono.

  ![Panoramica](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>Flusso di dati end-to-end dell'utente

1. Il team delle risorse umane esegue transazioni relative ai lavoratori (nuovi ingressi/spostamenti/abbandoni oppure nuove assunzioni/trasferimenti/risoluzioni del rapporto) in Workday Employee Central
2. Il servizio di provisioning di Azure AD esegue sincronizzazioni pianificate delle identità da Workday Employee Central e identifica i cambiamenti che devono essere elaborati per la sincronizzazione con l'istanza locale di Active Directory.
3. Il servizio di provisioning di Azure AD determina il cambiamento e richiama un'operazione di creazione/aggiornamento/abilitazione/disabilitazione per l'utente in Azure AD.
4. Se è configurata, l'app [Writeback di Workday](workday-writeback-tutorial.md) recupera attributi come l'indirizzo di posta elettronica, il nome utente e il numero di telefono da Azure AD. 
5. Il servizio di provisioning di Azure AD imposta l'indirizzo di posta elettronica, il nome utente e il numero di telefono in Workday.

## <a name="planning-your-deployment"></a>Pianificazione della distribuzione

La configurazione del provisioning utenti basato su risorse umane nel cloud da Workday ad Azure AD richiede una pianificazione considerevole che tenga conto di diversi aspetti, quali:

* Determinazione dell'ID corrispondente 
* Mapping degli attributi
* Trasformazione degli attributi 
* Filtri per la definizione dell'ambito

Vedere il [piano di distribuzione dell'app HR basata sul cloud](../app-provisioning/plan-cloud-hr-provision.md) per indicazioni complete su questi argomenti. 

## <a name="configure-integration-system-user-in-workday"></a>Configurare un utente del sistema di integrazione in Workday

Vedere la sezione [Configurare un utente del sistema di integrazione](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) per informazioni su come creare un account utente del sistema di integrazione di Workday con le autorizzazioni per il recupero dei dati dei lavoratori. 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>Configurare il provisioning utenti da Workday ad Azure AD

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

5. Cercare **Workday to Azure AD user provisioning** (Provisioning utenti da Workday ad Azure AD) e aggiungere tale app dalla raccolta.

6. Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**.

7. Modificare **Modalità** di **provisioning** su **Automatico**.

8. Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente Workday**: immettere il nome utente dell'account del sistema di integrazione Workday, aggiungendo il nome di dominio del tenant. Dovrebbe essere simile a: username@contoso4

   * **Password Workday:** immettere la password dell'account del sistema di integrazione Workday

   * **URL dell'API Workday Web Services:** immettere l'URL dell'endpoint di Workday Web Services per il tenant. L'URL determina la versione dell'API Workday Web Services usata dal connettore. 
   
     | Formato di URL | Versione dell'API Workday Web Services usata | Modifiche XPATH necessarie |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v##.# | Sì |

      > [!NOTE]
     > Se non sono specificate informazioni sulla versione nell'URL, l'app usa Workday Web Services (WWS) v21.1 e non è necessario apportare modifiche alle espressioni predefinite dell'API XPATH fornite con l'app. Per usare una versione specifica dell'API WWS, specificare il numero di versione nell'URL <br>
     > Esempio: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Se si usa un'API WWS versione 30.0 e successive, prima di attivare il processo di provisioning aggiornare le **espressioni API XPATH** in **Mapping di attributi -> Opzioni avanzate -> Modifica elenco attributi per Workday** facendo riferimento alla sezione [Gestione della configurazione](workday-inbound-tutorial.md#managing-your-configuration) e alle [informazioni di riferimento sugli attributi di Workday](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30).  

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

3. Questa operazione avvierà la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti nel tenant di Workday. È possibile controllare l'indicatore di stato per monitorare lo stato del ciclo di sincronizzazione. 

4. In qualsiasi momento è possibile controllare la scheda **Log di controllo** nel portale di Azure per vedere le azioni che sono state eseguite dal servizio di provisioning. I log di controllo elencano tutti i singoli eventi di sincronizzazione eseguiti dal servizio di provisioning, ad esempio quali utenti vengono letti da Workday e successivamente aggiunti o aggiornati in Azure Active Directory. 

5. Al termine della sincronizzazione iniziale, verrà scritto un report di riepilogo di controllo nella scheda **Provisioning**, come illustrato di seguito.

   > [!div class="mx-imgBorder"]
   > ![Indicatore di stato del provisioning](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sugli attributi di Workday supportati per il provisioning in ingresso](../app-provisioning/workday-attribute-reference.md)
* [Informazioni su come configurare il writeback di Workday](workday-writeback-tutorial.md)
* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
* [Informazioni su come configurare l'accesso Single Sign-On tra Workday e Azure Active Directory](workday-tutorial.md)
* [Informazioni sull'integrazione di altre applicazioni SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni su come esportare e importare le configurazioni del provisioning](../app-provisioning/export-import-provisioning-configuration.md)


