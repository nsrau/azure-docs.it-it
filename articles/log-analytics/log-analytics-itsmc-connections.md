---
title: Connettere i servizi/prodotti ITSM con IT Service Management Connector in Azure Log Analytics | Microsoft Docs
description: Il presente articolo fornisce informazioni su come connettere i prodotti/servizi ITSM con IT Service Management Connector (ITSMC) in OMS Log Analytics per monitorare e gestire centralmente gli elementi di lavoro di ITSM.
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: v-jysur
ms.openlocfilehash: e0cae844e8b7f3eab68ed02dbcd17f4d1cebfd25
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Connettere prodotti e servizi di Gestione dei servizi IT con Connettore di Gestione dei servizi IT
Questo articolo fornisce informazioni su come configurare la connessione tra un prodotto o servizio di Gestione dei servizi IT e Connettore di Gestione dei servizi IT in Log Analytics per gestire da una posizione centrale gli elementi di lavoro. Per altre informazioni su Connettore di Gestione dei servizi IT, vedere [Panoramica](log-analytics-itsmc-overview.md).

Sono supportati i prodotti/servizi ITSM seguenti. Selezionare il prodotto per visualizzare informazioni dettagliate su come connettere il prodotto a ITSMC.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]

> Connettore di Gestione dei servizi IT può connettersi solo a istanze di ServiceNow basate su cloud. Le istanze locali di ServiceNow non sono attualmente supportate.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Connettere System Center Service Manager a Connettore di Gestione dei servizi IT in Azure

Le sezioni seguenti forniscono informazioni dettagliate su come connettere il prodotto System Center Service Manager a Connettore di Gestione dei servizi IT in Azure.

### <a name="prerequisites"></a>prerequisiti

Accertarsi di aver soddisfatto i prerequisiti seguenti:

- Connettore di Gestione dei servizi IT installato. Altre informazioni: [Aggiunta della soluzione IT Service Management Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- L'applicazione Web (app Web) Service Manager è stata distribuita e configurata. Per informazioni sull'app Web, vedere[qui](#create-and-deploy-service-manager-web-app-service).
- Connessione ibrida è stata creata e configurata. Per altre informazioni, vedere [Configurare la connessione ibrida](#configure-the-hybrid-connection).
- Versioni supportate di Service Manager: 2012 R2 o 2016.
- Ruolo utente: [Operatore avanzato](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Procedura di connessione

Usare la procedura seguente per connettere l'istanza di System Center Service Manager a Connettore di Gestione dei servizi IT:

1. Nel portale di Azure passare a **Tutte le risorse** e cercare **ServiceDesk(NomeAreaDiLavoro)**

2.  In **ORIGINI DATI DELL'AREA DI LAVORO** fare clic su **Connessioni di Gestione dei servizi IT**.

    ![Nuova connessione](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. Nella parte superiore del riquadro destro fare clic su **Aggiungi**.

4. Specificare le informazioni come illustrato nella tabella seguente e quindi fare clic su **OK** per creare la connessione.

> [!NOTE]

> Tutti questi parametri sono obbligatori.

| **Campo** | **Descrizione** |
| --- | --- |
| **Connection Name** (Nome connessione)   | Digitare il nome dell'istanza di System Center Service Manager da connettere a Connettore di Gestione dei servizi IT.  Questo nome viene usato in seguito durante la configurazione degli elementi di lavoro in questa istanza o quando si visualizzano analisi del log dettagliate. |
| **Tipo di partner**   | Selezionare **System Center Service Manager**. |
| **URL del server**   | Digitare l'URL dell'app Web Service Manager. Per altre informazioni sull'app Web Service Manager, vedere [qui](#create-and-deploy-service-manager-web-app-service).
| **ID client**   | Digitare l'ID client generato tramite lo script automatico per l'autenticazione dell'app Web. Per altre informazioni sullo script automatico, vedere [qui](log-analytics-itsmc-service-manager-script.md).|
| **Segreto client**   | Digitare il segreto client, generato per questo ID.   |
| **Ambito sincronizzazione dati**   | Selezionare gli elementi di lavoro di Service Manager da sincronizzare tramite Connettore di Gestione dei servizi IT.  Questi elementi di lavoro vengono importati in Log Analytics. **Opzioni:** Eventi imprevisti, Richieste di modifica.|
| **Sincronizza dati** | Digitare il numero di giorni precedenti da cui si vogliono recuperare i dati. **Limite massimo**: 120 giorni. |
| **Create new configuration item in ITSM solution** (Crea nuovo elemento di configurazione nella soluzione ITSM) | Selezionare questa opzione se si vogliono creare gli elementi di configurazione nel prodotto ITSM. Se questa opzione è selezionata, OMS crea le integrazioni continue interessate come elementi di configurazione (in caso di integrazioni continue inesistenti) nel sistema ITSM supportato. **Impostazione predefinita**: disabilitata. |

![Connessione di Service Manager](./media/log-analytics-itsmc/service-manager-connection.png)

**Dopo la corretta connessione e la sincronizzazione**:

- Gli elementi di lavoro selezionati in Service Manager vengono importati in Azure **Log Analytics**. È possibile visualizzare il riepilogo di questi elementi di lavoro nel riquadro IT Service Management Connector.

- È possibile creare eventi imprevisti dagli avvisi o dai record di log di Log Analytics oppure dagli avvisi di Azure in questa istanza di Service Manager.


Altre informazioni: [Creare elementi di lavoro di Gestione dei servizi IT da avvisi di Log Analytics](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [Creare elementi di lavoro di Gestione dei servizi IT da record di log di Log Analytics](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) e [Creare elementi di lavoro di Gestione dei servizi IT da avvisi di Azure](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Creare e distribuire il servizio app Web di Service Manager

Per connettere l'istanza locale di Service Manager con Connettore di Gestione dei servizi IT in Azure, Microsoft ha creato un'app Web di Service Manager in GitHub.

Per configurare l'app Web ITSM per l'istanza di Service Manager, seguire questa procedura:

- **Distribuire l'app Web**: distribuire l'app Web, configurare le proprietà ed eseguire l'autenticazione con Azure AD. È possibile distribuire l'app Web usando lo [script automatico](log-analytics-itsmc-service-manager-script.md) fornito da Microsoft.
- **Configurare la connessione ibrida** - [Configurare questa connessione](#configure-the-hybrid-connection) manualmente.

#### <a name="deploy-the-web-app"></a>Distribuire l'app Web
Usare lo [script](log-analytics-itsmc-service-manager-script.md) automatico per distribuire l'app Web, configurare le proprietà ed eseguire l'autenticazione con Azure AD.

Eseguire lo script, fornendo i seguenti dettagli richiesti:

- Dettagli della sottoscrizione di Azure
- Nome del gruppo di risorse
- Località
- Dettagli del server di Service Manager (nome del server, dominio, nome utente e password)
- Prefisso del nome del sito per l'app Web
- Spazio dei nomi ServiceBus.

Lo script crea l'app Web usando il nome specificato insieme ad alcune stringhe aggiuntive per renderlo univoco. Genera **URL dell'app Web**, **ID del client** e **segreto client**.

Salvare questi valori perché serviranno al momento della creazione di una connessione a Connettore di Gestione dei servizi IT.

**Controllare l'installazione dell'app Web**

1. Passare al **portale di Azure** > **Risorse**.
2. Selezionare l'app Web, fare clic su **Impostazioni** > **Impostazioni applicazione**.
3. Confermare le informazioni sull'istanza di Service Manager specificate durante la distribuzione dell'app tramite lo script.

### <a name="configure-the-hybrid-connection"></a>Configurare la connessione ibrida

Usare la procedura seguente per configurare la connessione ibrida tra l'istanza di Service Manager e Connettore di Gestione dei servizi IT in Azure.

1. Trovare l'app Web di Service Manager in **Risorse di Azure**.
2. Fare clic su **Impostazioni** > **Rete**.
3. In **Connessioni ibride** fare clic su **Configurare gli endpoint della connessione ibrida**.

    ![Rete per la connessione ibrida](./media/log-analytics-itsmc/itsmc-hybrid-connection-networking-and-end-points.png)
4. Nel pannello **Connessioni ibride** fare clic su **Aggiungi connessione ibrida**.

    ![Aggiunta di una connessione ibrida](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-add.png)

5. Nel pannello **Aggiungi connessione ibrida** fare clic su **Crea nuova connessione ibrida**.

    ![Nuova connessione ibrida](./media/log-analytics-itsmc/itsmc-create-new-hybrid-connection.png)

6. Digitare i valori seguenti:

    - **Nome endpoint**: specificare un nome per la nuova connessione ibrida.
    -  **Host endpoint**: nome di dominio completo del server di gestione di Service Manager.
    - **Porta endpoint**: digitare 5724.
    - **Spazio dei nomi del bus di servizio**: usare uno spazio dei nomi del bus di servizio esistente o crearne uno nuovo.
    - **Località**: selezionare la località.
    -  **Nome**: specificare un nome per il bus di servizio, se lo si sta creando.

    ![Valori della connessione ibrida](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-values.png)
6. Fare clic su **OK** per chiudere il pannello **Crea connessione ibrida** e iniziare a creare la connessione ibrida.

    Al termine della creazione, la connessione ibrida viene visualizzata sotto il pannello.

7. Dopo la creazione della connessione ibrida, selezionare la connessione e fare clic su **Aggiungi connessione ibrida selezionata**.

    ![Nuova connessione ibrida](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Configurare le impostazioni del listener

Seguire questa procedura per configurare le impostazioni del listener per la connessione ibrida.

1. Nel pannello **Connessioni ibride** fare clic su **Scarica Gestione connessioni** ed eseguire l'installazione nella macchina virtuale in cui è in esecuzione l'istanza di System Center Service Manager.

    Al termine dell'installazione, l'opzione **Hybrid Connection Manager UI** (Interfaccia utente ibrida di Gestione connessioni) è disponibile nel menu **Start**.

2. Fare clic su **Hybrid Connection Manager UI** (Interfaccia utente ibrida di Gestione connessioni). Verranno richieste le credenziali di Azure.

3. Accedere con le credenziali di Azure e selezionare la sottoscrizione in cui è stata creata la connessione ibrida.

4. Fare clic su **Save**.

La connessione ibrida è stata completata.

![Connessione ibrida completata](./media/log-analytics-itsmc/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]

> Al termine della creazione della connessione ibrida, verificare e testare la connessione passando all'app Web di Service Manager distribuita. Assicurarsi che la connessione funzioni prima di provare a connettersi a Connettore di Gestione dei servizi IT in Azure.

L'esempio seguente mostra i dettagli di una connessione riuscita:

![Test della connessione ibrida](./media/log-analytics-itsmc/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Connettere ServiceNow a Connettore di Gestione dei servizi IT in Azure

Le sezioni seguenti forniscono informazioni dettagliate su come connettere il prodotto ServiceNow a Connettore di Gestione dei servizi IT in Azure.

### <a name="prerequisites"></a>prerequisiti
Accertarsi di aver soddisfatto i prerequisiti seguenti:
- Connettore di Gestione dei servizi IT installato. Altre informazioni: [Aggiunta della soluzione IT Service Management Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Versioni supportate di ServiceNow: Kingston, Giacarta, Istanbul, Helsinki, Ginevra.

**ServiceNow Admins deve eseguire le operazioni seguenti nell'istanza di ServiceNow**:
- Generare l'ID client e il segreto client per il prodotto ServiceNow. Per informazioni su come generare un ID client e un segreto client, vedere gli articoli seguenti:

    - [Set up OAuth for Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/concept/OAuth-setup.html) (Configurare OAuth per Kingston)
    - [Set up OAuth for Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per Giacarta)
    - [Set up OAuth for Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per Istanbul)
    - [Set up OAuth for Helsinki](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth for Helsinki)
    - [Set up OAuth for Geneva](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html) (Configurare OAuth per Ginevra)


- Installare l'app utente per l'integrazione Microsoft OMS (app ServiceNow). [Altre informazioni](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Creare un ruolo utente integrazione per l'app utente installata. Per informazioni su come creare il ruolo utente di integrazione, vedere [qui](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Procedura di connessione**
Seguire questa procedura per creare una connessione ServiceNow:


1. Nel portale di Azure passare a **Tutte le risorse** e cercare **ServiceDesk(NomeAreaDiLavoro)**

2.  In **ORIGINI DATI DELL'AREA DI LAVORO** fare clic su **Connessioni di Gestione dei servizi IT**.
    ![Nuova connessione](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. Nella parte superiore del riquadro destro fare clic su **Aggiungi**.

4. Specificare le informazioni come illustrato nella tabella seguente e quindi fare clic su **OK** per creare la connessione.


> [!NOTE]
> Tutti questi parametri sono obbligatori.

| **Campo** | **Descrizione** |
| --- | --- |
| **Connection Name** (Nome connessione)   | Digitare un nome per l'istanza di ServiceNow da connettere a Connettore di Gestione dei servizi IT.  Questo nome viene usato in seguito in OMS durante la configurazione degli elementi di lavoro in questa istanza di ITSM o quando si visualizzano analisi del log dettagliate. |
| **Tipo di partner**   | Selezionare **ServiceNow**. |
| **Nome utente**   | Digitare il nome utente di integrazione creato nell'app ServiceNow per supportare la connessione a Connettore di Gestione dei servizi IT. Per altre informazioni, vedere [Create ServiceNow app user role](#create-integration-user-role-in-servicenow-app) (Creare il ruolo utente dell'app ServiceNow).|
| **Password**   | Digitare la password associata a questo nome utente. **Nota**: il nome utente e la password vengono usati solo per generare i token di autenticazione e non vengono archiviati nel servizio Connettore di Gestione dei servizi IT.  |
| **URL del server**   | Digitare l'URL dell'istanza di ServiceNow da connettere a Connettore di Gestione dei servizi IT. |
| **ID client**   | Digitare l'ID client da usare per l'autenticazione OAuth2, generata in precedenza.  Per altre informazioni sulla generazione dell'ID client e del segreto, vedere [OAuth Setup](http://wiki.servicenow.com/index.php?title=OAuth_Setup) (Configurazione di OAuth). |
| **Segreto client**   | Digitare il segreto client, generato per questo ID.   |
| **Ambito sincronizzazione dati**   | Selezionare gli elementi di lavoro di ServiceNow da sincronizzare con Azure Log Analytics tramite Connettore di Gestione dei servizi IT.  I valori selezionati vengono importati in Log Analytics.   **Opzioni:** Eventi imprevisti e Richieste di modifica.|
| **Sincronizza dati** | Digitare il numero di giorni precedenti da cui si vogliono recuperare i dati. **Limite massimo**: 120 giorni. |
| **Create new configuration item in ITSM solution** (Crea nuovo elemento di configurazione nella soluzione ITSM) | Selezionare questa opzione se si vogliono creare gli elementi di configurazione nel prodotto ITSM. Se questa opzione è selezionata, Connettore di Gestione dei servizi IT crea le integrazioni continue interessate come elementi di configurazione (nel caso in cui non esistano) nel sistema di Gestione dei servizi IT supportato. **Impostazione predefinita**: disabilitata. |

![Connessione di ServiceNow](./media/log-analytics-itsmc/itsm-connection-servicenow-connection-latest.png)

**Dopo la corretta connessione e la sincronizzazione**:

- Gli elementi di lavoro selezionati nell'istanza di ServiceNow vengono importati in Azure **Log Analytics**. È possibile visualizzare il riepilogo di questi elementi di lavoro nel riquadro IT Service Management Connector.

- È possibile creare eventi imprevisti dagli avvisi o dai record di log di Log Analytics oppure dagli avvisi di Azure in questa istanza di ServiceNow.

Altre informazioni: [Creare elementi di lavoro di Gestione dei servizi IT da avvisi di Log Analytics](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [Creare elementi di lavoro di Gestione dei servizi IT da record di log di Log Analytics](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) e [Creare elementi di lavoro di Gestione dei servizi IT da avvisi di Azure](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Creare un ruolo utente di integrazione nell'app ServiceNow

Seguire questa procedura:

1.  Passare a [ServiceNow Store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) e installare l'**app utente per ServiceNow e per l'integrazione con Microsoft OMS** nell'istanza di ServiceNow.
2.  Dopo l'installazione, passare alla barra di spostamento sinistra dell'istanza di ServiceNow, eseguire una ricerca e selezionare Microsoft OMS Integrator.  
3.  Fare clic su **Installation Checklist** (Elenco di controllo installazione).

    Lo stato visualizzato è **Not complete** (Non completato) se il ruolo utente deve essere ancora creato.

4.  Nelle caselle di testo accanto a **Create integration user** (Crea utente di integrazione) immettere il nome dell'utente che può connettersi a Connettore di Gestione dei servizi IT in Azure.
5.  Immettere la password per questo utente e fare clic su **OK**.  

>[!NOTE]

> Queste credenziali vengono usate per creare la connessione di ServiceNow in Azure.

L'utente appena creato viene visualizzato con i ruoli predefiniti assegnati.

**Ruoli predefiniti**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

Al termine della creazione dell'utente, lo stato di **Check Installation Checklist** (Controllo elenco di controllo installazione) viene impostato su Completed (Completato) e vengono elencati i dettagli del ruolo utente creato per l'app.

> [!NOTE]

> Per consentire a un utente di creare **avvisi** ed **eventi** in ServiceNow da Azure:

> - Assicurarsi che il modulo di gestione eventi sia installato nell'istanza di ServiceNow.

> - Aggiungere i ruoli seguenti all'utente di integrazione:
>      - evt_mgmt_integration
>      - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Connettere Provance a Connettore di Gestione dei servizi IT in Azure

Le sezioni seguenti forniscono informazioni dettagliate su come connettere il prodotto Provance a Connettore di Gestione dei servizi IT in Azure.


### <a name="prerequisites"></a>prerequisiti

Accertarsi di aver soddisfatto i prerequisiti seguenti:


- Connettore di Gestione dei servizi IT installato. Altre informazioni: [Aggiunta della soluzione IT Service Management Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- L'app Provance deve essere registrata in Azure AD e l'ID client deve essere stato reso disponibile. Per informazioni dettagliate, vedere [Come configurare l'autenticazione di Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

- Ruolo utente: amministratore.

### <a name="connection-procedure"></a>Procedura di connessione

Seguire questa procedura per creare una connessione Provance:

1. Nel portale di Azure passare a **Tutte le risorse** e cercare **ServiceDesk(NomeAreaDiLavoro)**

2.  In **ORIGINI DATI DELL'AREA DI LAVORO** fare clic su **Connessioni di Gestione dei servizi IT**.
    ![Nuova connessione](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. Nella parte superiore del riquadro destro fare clic su **Aggiungi**.

4. Specificare le informazioni come illustrato nella tabella seguente e quindi fare clic su **OK** per creare la connessione.

> [!NOTE]

> Tutti questi parametri sono obbligatori.

| **Campo** | **Descrizione** |
| --- | --- |
| **Connection Name** (Nome connessione)   | Digitare un nome per l'istanza di Provance da connettere a Connettore di Gestione dei servizi IT.  Questo nome viene usato in seguito quando si configurano gli elementi di lavoro in questa istanza di Gestione dei servizi IT o quando si visualizzano analisi del log dettagliate. |
| **Tipo di partner**   | Selezionare **Provance**. |
| **Nome utente**   | Digitare il nome utente che può connettersi a Connettore di Gestione dei servizi IT.    |
| **Password**   | Digitare la password associata a questo nome utente. **Nota**: il nome utente e la password vengono usati solo per generare i token di autenticazione e non vengono archiviati nel servizio Connettore di Gestione dei servizi IT.|
| **URL del server**   | Digitare l'URL per l'istanza di Provance da connettere a Connettore di Gestione dei servizi IT. |
| **ID client**   | Digitare l'ID client per l'autenticazione di questa connessione, generato nell'istanza di Provance.  Per altre informazioni sull'ID client, vedere [Come configurare l'autenticazione di Active Directory](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md). |
| **Ambito sincronizzazione dati**   | Selezionare gli elementi di lavoro di Provance da sincronizzare con Azure Log Analytics tramite Connettore di Gestione dei servizi IT.  Questi elementi di lavoro vengono importati in Log Analytics.   **Opzioni:** Eventi imprevisti, Richieste di modifica.|
| **Sincronizza dati** | Digitare il numero di giorni precedenti da cui si vogliono recuperare i dati. **Limite massimo**: 120 giorni. |
| **Create new configuration item in ITSM solution** (Crea nuovo elemento di configurazione nella soluzione ITSM) | Selezionare questa opzione se si vogliono creare gli elementi di configurazione nel prodotto ITSM. Se questa opzione è selezionata, Connettore di Gestione dei servizi IT crea le integrazioni continue interessate come elementi di configurazione (nel caso in cui non esistano) nel sistema di Gestione dei servizi IT supportato. **Impostazione predefinita**: disabilitata.|

![Connessione Provance](./media/log-analytics-itsmc/itsm-connections-provance-latest.png)

**Dopo la corretta connessione e la sincronizzazione**:

- Gli elementi di lavoro selezionati nell'istanza di Provance vengono importati in Azure **Log Analytics**. È possibile visualizzare il riepilogo di questi elementi di lavoro nel riquadro IT Service Management Connector.

- È possibile creare eventi imprevisti dagli avvisi o dai record di log di Log Analytics oppure dagli avvisi di Azure in questa istanza di Provance.

Altre informazioni: [Creare elementi di lavoro di Gestione dei servizi IT da avvisi di Log Analytics](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [Creare elementi di lavoro di Gestione dei servizi IT da record di log di Log Analytics](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) e [Creare elementi di lavoro di Gestione dei servizi IT da avvisi di Azure](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Connettere Cherwell a Connettore di Gestione dei servizi IT in Azure

Le sezioni seguenti forniscono informazioni dettagliate su come connettere il prodotto Cherwell a Connettore di Gestione dei servizi IT in Azure.

### <a name="prerequisites"></a>prerequisiti

Accertarsi di aver soddisfatto i prerequisiti seguenti:

- Connettore di Gestione dei servizi IT installato. Altre informazioni: [Aggiunta della soluzione IT Service Management Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- L'ID client è stato generato. Per altre informazioni, vedere [Generare l'ID client per Cherwell](#generate-client-id-for-cherwell).
- Ruolo utente: amministratore.

### <a name="connection-procedure"></a>Procedura di connessione

Seguire questa procedura per creare una connessione Provance:

1. Nel portale di Azure passare a **Tutte le risorse** e cercare **ServiceDesk(NomeAreaDiLavoro)**

2.  In **ORIGINI DATI DELL'AREA DI LAVORO** fare clic su **Connessioni di Gestione dei servizi IT**.
    ![Nuova connessione](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. Nella parte superiore del riquadro destro fare clic su **Aggiungi**.

4. Specificare le informazioni come illustrato nella tabella seguente e quindi fare clic su **OK** per creare la connessione.

> [!NOTE]

> Tutti questi parametri sono obbligatori.

| **Campo** | **Descrizione** |
| --- | --- |
| **Connection Name** (Nome connessione)   | Digitare un nome per l'istanza di Cherwell da connettere a Connettore di Gestione dei servizi IT.  Questo nome viene usato in seguito quando si configurano gli elementi di lavoro in questa istanza di Gestione dei servizi IT o quando si visualizzano analisi del log dettagliate. |
| **Tipo di partner**   | Selezionare **Cherwell**. |
| **Nome utente**   | Digitare il nome utente di Cherwell che può connettersi a Connettore di Gestione dei servizi IT. |
| **Password**   | Digitare la password associata a questo nome utente. **Nota**: il nome utente e la password vengono usati solo per generare i token di autenticazione e non vengono archiviati nel servizio Connettore di Gestione dei servizi IT.|
| **URL del server**   | Digitare l'URL per l'istanza di Cherwell da connettere a Connettore di Gestione dei servizi IT. |
| **ID client**   | Digitare l'ID client per l'autenticazione di questa connessione, generato nell'istanza di Cherwell.   |
| **Ambito sincronizzazione dati**   | Selezionare gli elementi di lavoro di Cherwell da sincronizzare tramite Connettore di Gestione dei servizi IT.  Questi elementi di lavoro vengono importati in Log Analytics.   **Opzioni:** Eventi imprevisti, Richieste di modifica. |
| **Sincronizza dati** | Digitare il numero di giorni precedenti da cui si vogliono recuperare i dati. **Limite massimo**: 120 giorni. |
| **Create new configuration item in ITSM solution** (Crea nuovo elemento di configurazione nella soluzione ITSM) | Selezionare questa opzione se si vogliono creare gli elementi di configurazione nel prodotto ITSM. Se questa opzione è selezionata, Connettore di Gestione dei servizi IT crea le integrazioni continue interessate come elementi di configurazione (nel caso in cui non esistano) nel sistema di Gestione dei servizi IT supportato. **Impostazione predefinita**: disabilitata. |


![Connessione Provance](./media/log-analytics-itsmc/itsm-connections-cherwell-latest.png)

**Dopo la corretta connessione e la sincronizzazione**:

- Gli elementi di lavoro selezionati nell'istanza di Cherwell vengono importati in Azure **Log Analytics**. È possibile visualizzare il riepilogo di questi elementi di lavoro nel riquadro IT Service Management Connector.

- È possibile creare eventi imprevisti dagli avvisi o dai record di log di Log Analytics oppure dagli avvisi di Azure in questa istanza di Cherwell.

Altre informazioni: [Creare elementi di lavoro di Gestione dei servizi IT da avvisi di Log Analytics](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [Creare elementi di lavoro di Gestione dei servizi IT da record di log di Log Analytics](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) e [Creare elementi di lavoro di Gestione dei servizi IT da avvisi di Azure](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Generare l'ID client per Cherwell

Per generare l'ID client o la chiave per Cherwell, seguire questa procedura:

1. Accedere all'istanza di Cherwell come amministratore.
2. Fare clic su **Security** (Sicurezza)  > **Edit REST API client settings** (Modifica le impostazioni client dell'API REST).
3. Selezionare **Create new client** (Crea nuovo client)  > **client secret** (segreto client).

    ![ID utente di Cherwell](./media/log-analytics-itsmc/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Passaggi successivi
 - [Creare elementi di lavoro di Gestione dei servizi IT da avvisi di Log Analytics](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts)
 - [Creare elementi di lavoro di Gestione dei servizi IT da record di log di Log Analytics](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records)
 - [Creare elementi di lavoro di Connettore di Gestione dei servizi IT da avvisi di Azure](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
