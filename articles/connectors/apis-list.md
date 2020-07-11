---
title: Connettori per App per la logica di Azure
description: Automatizzare i flussi di lavoro con i connettori per app per la logica di Azure, ad esempio i connettori predefiniti, gestiti, locali, di integrazione, ISE e Enterprise
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 06/11/2020
ms.openlocfilehash: 48d9990115a0e786d12915acf1eaadc196a00b0b
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170038"
---
# <a name="connectors-for-azure-logic-apps"></a>Connettori per App per la logica di Azure

I connettori consentono di accedere rapidamente da App per la logica di Azure a eventi, dati e azioni tra app, servizi, sistemi, protocolli e piattaforme diversi. Usando i connettori delle app per la logica è possibile espandere le capacità delle applicazioni locali e cloud per eseguire attività con i dati creati e già disponibili.

Sebbene app per la logica offra [centinaia di connettori](https://docs.microsoft.com/connectors), in questo articolo vengono descritti i connettori *più diffusi e comunemente usati* da migliaia di app e milioni di esecuzioni per l'elaborazione di dati e informazioni. Per trovare l'elenco completo dei connettori e le informazioni di riferimento di ogni connettore, ad esempio trigger, azioni e limiti, vedere le pagine di riferimento del connettore in [Panoramica dei connettori](https://docs.microsoft.com/connectors). Altre informazioni sui [trigger e le azioni](#triggers-actions), il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md)per la logica e [i dettagli prezzi di app](https://azure.microsoft.com/pricing/details/logic-apps/)per la logica.

> [!TIP]
> Per l'integrazione con un servizio o un'API che non dispone di connettore, è possibile chiamare direttamente il servizio tramite un protocollo, ad esempio HTTP, oppure creare un [connettore personalizzato](#custom).

## <a name="connector-types"></a>Tipi di connettore

I connettori sono disponibili come trigger e azioni predefiniti o come connettori gestiti.

<a name="built-in"></a>

* [**Incorporati**](#built-ins): i trigger e le azioni predefiniti sono "nativi" per le app per la logica di Azure e consentono di eseguire queste attività per le app per la logica:

  * Eseguire su pianificazioni personalizzate e avanzate.

  * Organizzare e controllare il flusso di lavoro dell'app per la logica, ad esempio cicli e condizioni, e anche per lavorare con le variabili e le operazioni sui dati.

  * Comunicare con altri endpoint.

  * Ricevere e rispondere alle richieste.

  * Chiama funzioni di Azure, app per le API di Azure (app Web), API personalizzate gestite e pubblicate con gestione API di Azure e app per la logica annidate che possono ricevere richieste.

<a name="managed-connectors"></a>

* [**Connettori gestiti**](#managed-api-connectors): distribuiti e gestiti da Microsoft, questi connettori forniscono trigger e azioni per l'accesso a servizi cloud, sistemi locali o entrambi, tra cui Office 365, archiviazione BLOB di Azure, SQL Server, Dynamics, Salesforce, SharePoint e altro ancora. Alcuni connettori supportano specificamente scenari di comunicazione business-to-business (B2B) e richiedono un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) collegato all'app per la logica. Prima di usare determinati connettori, potrebbe essere necessario creare prima le connessioni, gestite da app per la logica di Azure.

  Ad esempio, se si usa Microsoft BizTalk Server, le app per la logica possono connettersi e comunicare con i BizTalk Server usando il [BizTalk Server on-premises Connector](#on-premises-connectors). È quindi possibile estendere o eseguire operazioni di tipo BizTalk in App per la logica utilizzando [connettori dell'account di integrazione](#integration-account-connectors).

  I connettori sono classificati come standard o Enterprise. I [connettori aziendali](#enterprise-connectors) forniscono l'accesso ai sistemi aziendali, ad esempio SAP, IBM mq e IBM 3270, per un costo aggiuntivo. Per determinare se un connettore è standard o Enterprise, vedere i dettagli tecnici nella pagina di riferimento di ogni connettore in [Panoramica dei connettori](https://docs.microsoft.com/connectors).

  È anche possibile identificare i connettori usando queste categorie, sebbene alcuni connettori possano incrociare più categorie. Ad esempio, SAP è un connettore aziendale e un connettore locale:

  | Connettori | Descrizione |
  |---|---|
  | [**Connettori gestiti**](#managed-api-connectors) | Consentono di creare app per la logica che usano i servizi, ad esempio archiviazione BLOB di Azure, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online e molti altri. |
  | [**Connettori locali**](#on-premises-connectors) | Dopo aver installato e configurato il [gateway dati locale][gateway-doc], questi connettori consentono alle app per la logica di accedere a sistemi locali quali SQL Server, SharePoint Server, Oracle DB, condivisioni di file e altri. |
  | [**Connettori dell'account di integrazione**](#integration-account-connectors) | Disponibili quando si crea e si paga un account di integrazione, questi connettori trasformano e convalidano XML, codificano e decodificano file flat ed elaborano messaggi business-to-business (B2B) con i protocolli AS2, EDIFACT e X12. |
  |||

  > [!IMPORTANT]
  > Se si vuole usare il connettore Gmail, solo gli account G Suite Business possono usare questo connettore senza restrizioni nelle app per la logica. Se si dispone di un account Gmail consumer, è possibile usare questo connettore solo con servizi approvati da Google specifici oppure è possibile [creare un'app client Google da usare per l'autenticazione con il connettore Gmail](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). Per altre informazioni, vedere [Informative sulla privacy e sulla sicurezza dei dati per i connettori Google in App per la logica di Azure](../connectors/connectors-google-data-security-privacy-policy.md).

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment"></a>Connettersi da un ambiente del servizio di integrazione

Per le app per la logica che richiedono l'accesso diretto alle risorse in una rete virtuale di Azure, è possibile creare un [ambiente di Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) isolato in cui è possibile compilare, distribuire ed eseguire le app per la logica su risorse dedicate. Nella finestra di progettazione dell'app per la logica, quando si esplorano i connettori che si vuole usare per le app per la logica in un ISE, viene visualizzata un'etichetta di **base** in trigger e azioni predefiniti, mentre l'etichetta **ISE** viene visualizzata in alcuni connettori:

* **Core**: i trigger e le azioni predefiniti con questa etichetta vengono eseguiti nello stesso ISE delle app per la logica, ad esempio:

  ![Connettore ISE di esempio](./media/apis-list/example-core-connector.png)

* **ISE**: i connettori gestiti con questa etichetta vengono eseguiti nello stesso ISE delle app per la logica, ad esempio:

  ![Connettore ISE di esempio](./media/apis-list/example-ise-connector.png)

  Se si dispone di un sistema locale connesso a una rete virtuale di Azure, ISE consente alle app per la logica di accedere direttamente a tale sistema senza il [gateway dati locale](../logic-apps/logic-apps-gateway-connection.md). È invece possibile usare il connettore **ISE** del sistema, se disponibile, un'azione http o un [connettore personalizzato](#custom). Per i sistemi locali che non dispongono di connettori **ISE** , usare il gateway dati locale. Per esaminare i connettori ISE disponibili, vedere [connettori ISE](#ise-connectors).

* Tutti gli altri connettori senza l'etichetta **Core** o **ISE** , che è possibile continuare a usare, vengono eseguiti nel servizio app per la logica globale multi-tenant, ad esempio:

  ![Connettore multi-tenant di esempio](./media/apis-list/example-multi-tenant-connector.png)

Le app per la logica eseguite in un ISE e i relativi connettori, indipendentemente dalla posizione in cui vengono eseguiti i connettori, seguono un piano tariffario fisso rispetto al piano tariffario in base al consumo. Per ulteriori informazioni, vedere le pagine seguenti:

* [Modello di determinazione prezzi delle app per la logica](../logic-apps/logic-apps-pricing.md)
* [Dettagli prezzi di app per la logica](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Connettersi alle reti virtuali di Azure tramite le App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="built-ins"></a>

## <a name="built-in"></a>Predefinito

App per la logica fornisce trigger e azioni predefiniti che consentono di creare flussi di lavoro basati sulla pianificazione, consentire alle app per la logica di comunicare con altre app e servizi, controllare il flusso di lavoro attraverso le app per la logica e gestire o modificare i dati.

[![Icona API ][schedule-icon]<br> **pianificazione**][schedule-doc] 

- Eseguire un'app per la logica in una ricorrenza specificata, da pianificazioni di base a avanzate con il trigger di [ **ricorrenza** ][schedule-recurrence-doc].
- Eseguire un'app per la logica che deve gestire i dati in blocchi continui con il trigger della [ **finestra temporale scorrevole** ][schedule-sliding-window-doc].
- Sospendere l'app per la logica per una durata specificata con l'azione di [ **ritardo** ][schedule-delay-doc].
- Sospendere l'app per la logica fino alla data e all'ora specificate con il [ **ritardo fino** all'azione][schedule-delay-until-doc].

[![Icona API ][batch-icon]<br> **batch**][batch-doc]

- Elaborare i messaggi in batch con il trigger **messaggi batch** .
- Chiamare le app per la logica con trigger batch esistenti con l'azione **Invia messaggi a batch** .

[![Icona API ][http-icon]<br> **http**][http-doc]

Chiamare endpoint HTTP o HTTPS con trigger e azioni per HTTP. Altri trigger e azioni predefiniti HTTP includono [http + spavalderia][http-swagger-doc] e [http + webhook][http-webhook-doc].

[![][http-request-icon]<br>**Richiesta** icona API][http-request-doc]

- Rendere l'app per la logica richiamabile da altre app o servizi, attivare gli eventi delle risorse della griglia di eventi o attivare le risposte agli avvisi del Centro sicurezza di Azure con il trigger di **richiesta** . 
- Inviare risposte a un'app o a un servizio con l'azione di **risposta** .

[![Icona API ][azure-api-management-icon]<br> ** <br> gestione API di Azure**][azure-api-management-doc]

Richiamare trigger e azioni definite dalle proprie API che vengono gestite e pubblicate con Azure API Management.

[![Icona API ][azure-app-services-icon]<br> **app Azure <br> Services**][azure-app-services-doc]

Chiamare l'App per le API Azure o le app Web, ospitato nel servizio App di Azure. I trigger e le azioni definiti da queste app vengono visualizzati come qualsiasi altro trigger e azioni di prima classe quando si include spavalderia. |

[![Icona API ][azure-logic-apps-icon]<br> ** <br> app** per la logica di Azure][nested-logic-app-doc]

Chiamare altre app per la logica che iniziano con il trigger di **richiesta** .

### <a name="run-code-from-logic-apps"></a>Eseguire codice da app per la logica

App per la logica offre azioni predefinite per l'esecuzione di codice personalizzato nel flusso di lavoro dell'app per la logica:

[![Icona API ][azure-functions-icon]<br> **funzioni di Azure**][azure-functions-doc]

Richiamare le funzioni Azure che eseguono frammenti di codice personalizzati (C# o Node.js) dalle app per la logica.

[![Icona API ][inline-code-icon]<br> **codice inline**][inline-code-doc]

Aggiungere ed eseguire frammenti di codice JavaScript dalle app per la logica.

### <a name="control-workflow"></a>Controllare il flusso di lavoro

App per la logica offre azioni predefinite per la strutturazione e il controllo delle azioni nel flusso di lavoro dell'app per la logica:

[![][condition-icon]<br>**Condizione** icona predefinita][condition-doc]

Valutare una condizione ed eseguire diverse azioni in base al fatto che la condizione sia true o false.

[![Icona predefinita ][for-each-icon]<br> **per ogni**][for-each-doc]

Eseguire le stesse azioni su ogni elemento in una matrice.

[![Ambito dell'icona predefinito ][scope-icon]<br> **Scope**][scope-doc]

Raggruppare le azioni in *ambiti*, che ottengono un proprio stato al termine delle azioni nell'ambito.

[![][switch-icon]<br>**Opzione** icona predefinita][switch-doc]

Raggruppare le azioni in *case*, ai quali vengono assegnati valori univoci ad eccezione del case predefinito. Eseguire solo il case il cui valore assegnato corrisponde al risultato di un'espressione, oggetto o token. Se non è presente alcuna corrispondenza, eseguire il case predefinito.

[![][terminate-icon]<br>**Terminazione** icona incorporata][terminate-doc]

Arrestare un flusso di lavoro di un'applicazione logica in esecuzione attiva.

[![Icona predefinita ][until-icon]<br> **fino a**][until-doc]

Ripetere azioni fino a quando non viene soddisfatta la condizione specificata o è stato modificato uno stato.

### <a name="manage-or-manipulate-data"></a>Gestire o manipolare i dati

App per la logica offre azioni predefinite per l'uso degli output dei dati e dei relativi formati:

[![][data-operations-icon]<br>**Operazioni dati** icona predefinite][data-operations-doc]

Eseguire operazioni con i dati:

- **Componi**: crea un singolo output da più input con tipi diversi.
- **Crea tabella CSV**: creare una tabella CSV (valore separato da virgole) da una matrice con oggetti JSON.
- **Crea tabella HTML**: crea una tabella HTML da una matrice di oggetti JSON.
- **Filtra matrice**: creare una matrice da elementi di un'altra matrice che soddisfano i criteri specificati.
- **Join**: crea una stringa da tutti gli elementi in una matrice e separa gli elementi con il delimitatore specificato.
- **Parse JSON**: creare token semplici da proprietà e i relativi valori nel contenuto JSON in modo che sia possibile usare tali proprietà nel flusso di lavoro.
- **Seleziona**: crea una matrice con oggetti JSON trasformando elementi o valori in un’altra matrice ed eseguendo il mapping di tali elementi per proprietà specifiche.

![Icona predefinita][date-time-icon]

**Data/ora**

Eseguire operazioni con i timestamp:

- **Aggiungi a time**: aggiunge il numero di unità specificato a un timestamp.
- **Converti fuso orario**: converte un timestamp dal fuso orario di origine al fuso orario di destinazione.
- **Tempo corrente**: restituisce il timestamp corrente come stringa.
- **Recupera time futuro**: restituisce il timestamp corrente più le unità di tempo specificate.
- **Recupera time passato**: restituisce il timestamp corrente meno le unità di tempo specificate.
- **Sottrai da time**: sottrae un numero di unità di tempo da un timestamp.

[![Variabili di icona predefinite ][variables-icon]<br> **Variables**][variables-doc]

Eseguire operazioni con le variabili:

- **Accoda a variabile di matrice**: inserisce un valore come l'ultimo elemento nella matrice memorizzato da una variabile.
- **Accoda a variabile di stringa**: inserire un valore come l'ultimo carattere in una stringa archiviata da una variabile.
- **Decrementa variabile**: riduce una variabile di un valore costante.
- **Incrementa variabile**: aumenta una variabile di un valore costante.
- **Inizializza variabile**: crea una variabile e dichiara il tipo di dati e il valore iniziale.
- **Imposta variabile**: assegna un valore diverso ad una variabile esistente.

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>Connettori gestiti

App per la logica fornisce questi comuni connettori standard per l'automazione di attività, processi e flussi di lavoro con questi servizi o sistemi:

[![Icona API ][azure-service-bus-icon]<br> **bus di servizio di Azure**][azure-service-bus-doc]

Gestire i messaggi asincroni, le sessioni e le sottoscrizioni agli argomenti con il connettore più utilizzato nelle app per la logica.

[![Icona API ][sql-server-icon]<br> **SQL Server**][sql-server-doc]

Connettersi al SQL Server locale o a un database SQL di Azure nel cloud per poter gestire i record, eseguire stored procedure o eseguire query.

[![Icona API ][azure-blob-storage-icon]<br> ** <br> archiviazione BLOB di Azure**][azure-blob-storage-doc]

Connettersi all'account di archiviazione in modo che sia possibile creare e gestire il contenuto BLOB.

[![Icona API ][office-365-outlook-icon]<br> **Office 365 <br> Outlook**][office-365-outlook-doc]

Connettersi all'account di posta elettronica di Office 365 per poter creare e gestire messaggi di posta elettronica, attività, eventi del calendario e riunioni, contatti, richieste e altro ancora.

[![Icona API ][sftp-ssh-icon]<br> **SFTP-SSH**][sftp-ssh-doc]

Connettersi ai server SFTP a cui è possibile accedere da Internet usando SSH per poter usare i file e le cartelle.

[![Icona API ][sharepoint-online-icon]<br> **SharePoint <br> online**][sharepoint-online-doc]

Connettersi a SharePoint online in modo da poter gestire file, allegati, cartelle e altro ancora.

[![Icona API ][dynamics-365-icon]<br> **Dynamics 365 <br> **][dynamics-365-doc]

Connettersi all'account Dynamics 365 per poter creare e gestire record, elementi e altro ancora.

[![Icona API ][azure-queues-icon]<br> ** <br> code di Azure**][azure-queues-doc]

Connettersi all'account di archiviazione di Azure in modo che sia possibile creare e gestire code e messaggi

[![Icona API ][ftp-icon]<br> **FTP**][ftp-doc]

Connettersi ai server FTP a cui è possibile accedere da Internet in modo da poter usare i file e le cartelle.

[![Icona API ][file-system-icon]<br> **file <br> System**][file-system-doc]

Connettersi alla condivisione file locale in modo da poter creare e gestire i file.

[![Icona API ][azure-event-hubs-icon]<br> **Hub eventi di Azure**][azure-event-hubs-doc]

Utilizzare e pubblicare eventi tramite un hub eventi. È ad esempio possibile ottenere output dall'app per la logica con gli hub eventi e quindi inviare questo output a un provider di analisi in tempo reale.

[![Icona API ][azure-event-grid-icon]<br> griglia di**eventi di Azure** <br> **Grid**][azure-event-grid-doc]

Monitorare gli eventi pubblicati dalla griglia di eventi, ad esempio, quando le risorse di Azure o le risorse di terze parti vengono modificate.


[![Icona API ][salesforce-icon]<br> **Salesforce**][salesforce-doc]

Connettersi all'account di Salesforce per poter creare e gestire elementi quali record, processi, oggetti e altro ancora.

[![Icona API ][twitter-icon]<br> **Twitter**][twitter-doc]

Connettersi all'account Twitter per poter gestire Tweet, follower, sequenza temporale e altro ancora. Salvare i tweet in Excel, SQL o SharePoint.

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Connettori locali

Di seguito sono riportati alcuni connettori standard usati comunemente dalle app per la logica per accedere ai dati e alle risorse nei sistemi locali. Prima di creare una connessione a un sistema locale, è necessario innanzitutto [scaricare, installare e configurare un gateway dati locale][gateway-doc]. Questo gateway offre un canale di comunicazione sicuro senza dover configurare l'infrastruttura di rete necessaria.

[![Icona API ][biztalk-server-icon]<br> **BizTalk** <br> **Server**][biztalk-server-doc]

[![Icona API ][file-system-icon]<br> **file <br> System**][file-system-doc]

[![Icona API ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]

[![Icona API ][ibm-informix-icon]<br> **IBM** <br> **Informix**][ibm-informix-doc]

[![Icona API ][mysql-icon]<br> **MySQL**][mysql-doc]

[![Icona API ][oracle-db-icon]<br> **Oracle DB**][oracle-db-doc]

[![Icona API ][postgre-sql-icon]<br> **PostgreSQL**][postgre-sql-doc]

[![Icona API ][sharepoint-server-icon]<br> **SharePoint <br> server**][sharepoint-server-doc]

[![Icona API ][sql-server-icon]<br> **SQL <br> server**][sql-server-doc]

[![Icona API ][teradata-icon]<br> **Teradata**][teradata-doc]

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Connettori dell'account di integrazione

App per la logica offre connettori standard per la creazione di soluzioni business to business (B2B) con le app per la logica quando si crea e si paga un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), disponibile tramite il Enterprise Integration Pack (PEI) in Azure. Con questo account, è possibile creare e archiviare artefatti B2B, quali partner commerciali, contratti, mappe, schemi, certificati e così via. Per usare questi artefatti, associare le app per la logica con l'account di integrazione. Se attualmente si usa BizTalk Server, questi connettori potrebbero sembrare già familiari.

[![Icona API ][as2-icon]<br> ** <br> decodifica AS2**][as2-doc]

[![Icona API ][as2-icon]<br> ** <br> codifica AS2**][as2-doc]

[![Icona API ][edifact-icon]<br> ** <br> decodifica EDIFACT**][edifact-decode-doc]

[![Icona API ][edifact-icon]<br> ** <br> codifica EDIFACT**][edifact-encode-doc]

[![Icona API ][flat-file-decode-icon]<br> ** <br> decodifica file flat**][flat-file-decode-doc]

[![Icona API ][flat-file-encode-icon]<br> ** <br> codifica file flat**][flat-file-encode-doc]

[![Icona API ][integration-account-icon]<br> ** <br> account di integrazione**][integration-account-doc]

[![Icona API ][liquid-icon]<br> **Liquid** <br> **trasformazioni** Liquid][json-liquid-transform-doc]

[![Icona API ][x12-icon]<br> ** <br> decodifica X12**][x12-decode-doc]

[![Icona API ][x12-icon]<br> ** <br> codifica X12**][x12-encode-doc]

[![Icona API ][xml-transform-icon]<br> **XML** <br> **trasformazioni** XML][xml-transform-doc]

[![Icona API ][xml-validate-icon]<br> ** <br> convalida XML**][xml-validate-doc]

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Connettori aziendali

App per la logica fornisce questi connettori aziendali per l'accesso ai sistemi aziendali, ad esempio SAP e IBM MQ:

[![Icona API ][ibm-3270-icon]<br> **IBM 3270**][ibm-3270-doc]

[![Icona API ][ibm-mq-icon]<br> **IBM mq**][ibm-mq-doc]

[![Icona API ][sap-icon]<br> **SAP**][sap-connector-doc]

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>Connettori ISE

Per le app per la logica create ed eseguono un [ambiente di Integration Services (ISE)](#integration-service-environment)isolato, la finestra di progettazione dell'app per la logica identifica i trigger e le azioni predefiniti che vengono eseguiti in ISE usando l'etichetta **principale** . I connettori gestiti eseguiti in ISE visualizzano l'etichetta **ISE** , mentre i connettori eseguiti nel servizio app per la logica globale multi-tenant non visualizzano alcuna etichetta. Questo elenco Mostra i connettori che attualmente contengono versioni di ISE:

[![Icona API ][as2-icon]<br> **AS2**][as2-doc]

[![Icona API ][azure-automation-icon]<br> ** <br> automazione di Azure**][azure-automation-doc]

[![Icona API ][azure-blob-storage-icon]<br> ** <br> archiviazione BLOB di Azure**][azure-blob-storage-doc]

[![Icona API ][azure-cosmos-db-icon]<br> **Azure Cosmos <br> DB**][azure-cosmos-db-doc]

[![Icona API ][azure-event-hubs-icon]<br> ** <br> Hub eventi di Azure**][azure-event-hubs-doc]

[![Icona API ][azure-event-grid-icon]<br> ** <br> griglia di eventi di Azure**][azure-event-grid-doc]

[![Icona API ][azure-file-storage-icon]<br> ** <br> archiviazione file di Azure**][azure-file-storage-doc]

[![Icona API ][azure-key-vault-icon]<br> **Azure Key <br> Vault**][azure-key-vault-doc]

[![Icona API ][azure-monitor-logs-icon]<br> ** <br> log di monitoraggio di Azure**][azure-monitor-logs-doc]

[![Icona API ][azure-service-bus-icon]<br> ** <br> bus di servizio di Azure**][azure-service-bus-doc]

[![Icona API ][azure-sql-data-warehouse-icon]<br> **Azure SQL data <br> Warehouse**][azure-sql-data-warehouse-doc]

[![Icona API ][azure-table-storage-icon]<br> ** <br> archiviazione tabelle di Azure**][azure-table-storage-doc]

[![Icona API ][azure-queues-icon]<br> ** <br> code di Azure**][azure-queues-doc]

[![Icona API ][edifact-icon]<br> **EDIFACT**][edifact-doc]

[![Icona API ][file-system-icon]<br> **file <br> System**][file-system-doc]

[![Icona API ][ftp-icon]<br> **FTP**][ftp-doc]

[![Icona API ][ibm-3270-icon]<br> **IBM 3270**][ibm-3270-doc]

[![Icona API ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]

[![Icona API ][ibm-mq-icon]<br> **IBM mq**][ibm-mq-doc]

[![Icona API ][sap-icon]<br> **SAP**][sap-connector-doc]

[![Icona API ][sftp-ssh-icon]<br> **SFTP-SSH**][sftp-ssh-doc]

[![Icona API ][smtp-icon]<br> **SMTP**][smtp-doc]

[![Icona API ][sql-server-icon]<br> **SQL <br> server**][sql-server-doc]

[![Icona API ][x12-icon]<br> **X12**][x12-doc]

Per altre informazioni, vedere gli argomenti seguenti:

* [Accesso alle risorse di rete virtuale di Azure da app per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Modello di determinazione prezzi delle app per la logica](../logic-apps/logic-apps-pricing.md)
* [Connettersi alle reti virtuali di Azure tramite le App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>Trigger e tipi di azione

I connettori possono fornire *trigger*, *azioni*o entrambi. Un *trigger* è il primo passaggio di qualsiasi app per la logica, in genere specificando l'evento che attiva il trigger e avvia l'esecuzione dell'app per la logica. Ad esempio, il connettore FTP ha un trigger che avvia l'app per la logica "quando viene aggiunto o modificato un file". Alcuni trigger verificano periodicamente l'evento o i dati specificati e quindi vengono generati quando rilevano l'evento o i dati specificati. Altri trigger attendono ma vengono generati immediatamente quando si verifica un evento specifico o quando sono disponibili nuovi dati. I trigger passano anche tutti i dati necessari all'app per la logica. L'app per la logica può leggere e usare i dati in tutto il flusso di lavoro. Ad esempio, il connettore Twitter ha un trigger "quando viene pubblicato un nuovo Tweet", che passa il contenuto del Tweet al flusso di lavoro dell'app per la logica.

Quando viene attivato un trigger, app per la logica di Azure crea un'istanza dell'app per la logica e inizia a eseguire le *azioni* nel flusso di lavoro dell'app per la logica. Le azioni sono i passaggi che seguono il trigger ed eseguono attività nel flusso di lavoro dell'app per la logica. Ad esempio, è possibile creare un'app per la logica che ottiene i dati del cliente da un database SQL ed elabora tali dati in azioni successive.

Di seguito sono riportati i tipi generali di trigger offerti da app per la logica di Azure:

* *Trigger di ricorrenza*: questo trigger viene eseguito in base a una pianificazione specificata e non è strettamente associato a un particolare servizio o sistema.

* *Trigger di polling*: questo trigger esegue regolarmente il polling di un servizio o di un sistema specifico in base alla pianificazione specificata, verificando la presenza di nuovi dati o se si è verificato un evento specifico. Se sono disponibili nuovi dati o si è verificato l'evento specifico, il trigger crea ed esegue una nuova istanza dell'app per la logica, che ora può usare i dati passati come input.

* *Trigger di push*: questo trigger attende ed è in attesa di nuovi dati o di un evento. Quando sono disponibili nuovi dati o quando si verifica l'evento, il trigger crea ed esegue una nuova istanza dell'app per la logica, che ora può usare i dati passati come input.

<a name="connections"></a>

## <a name="connector-configuration"></a>Configurazione del connettore

I trigger e le azioni di ciascun connettore forniscono le proprie proprietà da configurare. Molti connettori richiedono anche la prima volta che si crea una *connessione* al servizio o al sistema di destinazione e si forniscono le credenziali di autenticazione o altri dettagli di configurazione prima di poter usare un trigger o un'azione nell'app per la logica. Ad esempio, è necessario autorizzare una connessione a un account Twitter per accedere ai dati o pubblicare per conto dell'utente.

Per i connettori che usano Azure Active Directory (Azure AD) OAuth, la creazione di una connessione significa accedere al servizio, ad esempio Office 365, Salesforce o GitHub, in cui il token di accesso è [crittografato](../security/fundamentals/encryption-overview.md) e archiviato in modo sicuro in un archivio segreto di Azure. Altri connettori, ad esempio FTP e SQL, richiedono una connessione con i dettagli di configurazione, ad esempio l'indirizzo del server, il nome utente e la password. Questi dettagli di configurazione della connessione vengono inoltre crittografati e archiviati in modo sicuro. Scopri di più sulla [crittografia in Azure](../security/fundamentals/encryption-overview.md).

Le connessioni possono accedere al servizio o al sistema di destinazione per il periodo di tempo consentito da tale servizio o sistema. Per i servizi che usano Azure AD connessioni OAuth, ad esempio Office 365 e Dynamics, app per la logica di Azure aggiorna i token di accesso per un periodo illimitato. Altri servizi potrebbero avere limiti per quanto tempo le app per la logica di Azure possono usare un token senza aggiornamento. In genere, alcune azioni invalidano tutti i token di accesso, ad esempio la modifica della password.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>API e connettori personalizzati

Per chiamare API che eseguono codice personalizzato o non sono disponibili come connettori, è possibile estendere la piattaforma di App per la logica [creando app per le API personalizzate](../logic-apps/logic-apps-create-api-app.md). È anche possibile [creare connettori personalizzati](../logic-apps/custom-connector-overview.md) per *qualsiasi* API basata su REST o SOAP, che rendono le API disponibili a qualsiasi app per la logica nella sottoscrizione di Azure. Per rendere pubblici i connettori o le app per le API personalizzate da utilizzare in Azure, è possibile [inviare i connettori per la certificazione Microsoft](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Le app per la logica distribuite ed eseguite in un [ambiente di Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) possono accedere direttamente alle risorse in una rete virtuale di Azure. Se sono presenti connettori personalizzati che richiedono il gateway dati locale e i connettori sono stati creati al di fuori di un ISE, le app per la logica in un ISE possono anche usare tali connettori.
>
> I connettori personalizzati creati in un ISE non funzionano con il gateway dati locale. Tuttavia, questi connettori possono accedere direttamente alle origini dati locali connesse a una rete virtuale di Azure che ospita ISE. Quindi, le app per la logica in un ISE probabilmente non necessitano del gateway dati durante la comunicazione con tali risorse.
>
> Per altre informazioni sulla creazione di ISEs, vedere [connettersi alle reti virtuali di Azure da app](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)per la logica di Azure.

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>Blocca la creazione di connessioni

Se l'organizzazione non consente la connessione a risorse specifiche usando i connettori in app per la logica di Azure, è possibile [bloccare la funzionalità per creare le connessioni](../logic-apps/block-connections-connectors.md) per connettori specifici nei flussi di lavoro delle app per la logica usando [criteri di Azure](../governance/policy/overview.md). Per altre informazioni, vedere [bloccare le connessioni create da connettori specifici in app](../logic-apps/block-connections-connectors.md)per la logica di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare l' [elenco di connettori completo](https://docs.microsoft.com/connectors)
* [Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Creare connettori personalizzati per app per la logica](https://docs.microsoft.com/connectors/custom-connectors/)
* [Creare API personalizzate per app per la logica](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Connettersi a origini dati in locale da app per la logica con il gateway dati locale"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Creare un'istanza del servizio Gestione API di Azure per gestire e pubblicare le proprie API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrare le app per la logica con le app per le API del servizio app"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrate logic apps with Azure Functions" (Integrazione delle app per la logica con Funzioni di Azure)
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Elaborare i messaggi in gruppi o come batch"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Valutare una condizione ed eseguire azioni diverse a seconda che la condizione sia true o false"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Eseguire le stesse azioni su ogni elemento in una matrice"
[http-doc]: ./connectors-native-http.md "Chiamare endpoint HTTP o HTTPS dalle app per la logica"
[http-request-doc]: ./connectors-native-reqres.md "Ricevere richieste HTTP nelle app per la logica"
[http-response-doc]: ./connectors-native-reqres.md "Rispondere alle richieste HTTP dalle app per la logica"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Chiamare gli endpoint REST dalle app per la logica"
[http-webhook-doc]: ./connectors-native-webhook.md "Attendi eventi specifici dagli endpoint HTTP o HTTPS"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Aggiungere ed eseguire frammenti di codice JavaScript dalle app per la logica"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrare le app per la logica con flussi di lavoro annidati"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Selezionare e filtrare matrici con l'azione di query"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Eseguire app per la logica in base a una pianificazione"
[schedule-delay-doc]: ./connectors-native-delay.md "Ritardo durante l'esecuzione dell'azione successiva"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Ritardo durante l'esecuzione dell'azione successiva"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Eseguire app per la logica in base a una pianificazione ricorrente"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Eseguire app per la logica che devono gestire i dati in blocchi contigui"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizza le azioni in gruppi, che ottengono un proprio stato dopo la fine delle azioni in gruppo"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organizzare le azioni nei case a cui vengono assegnati valori univoci. Eseguire solo il case il cui valore corrisponde al risultato di un'espressione, di un oggetto o di un token. Se non esiste alcuna corrispondenza, eseguire il case predefinito"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Arrestare o annullare un flusso di lavoro attivamente in esecuzione per l'app per la logica"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Ripete le azioni fino a quando la condizione specificata non è true o è stato modificato alcuno stato"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Eseguire operazioni sui dati, ad esempio il filtro di matrici o la creazione di tabelle CSV e HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Eseguire operazioni con variabili, ad esempio inizializzazione, set, incremento, decremento e accodare a variabile di stringa o matrice"

<!--Managed connector doc links-->
[azure-automation-doc]: https://docs.microsoft.com/connectors/azureautomation/ "Creare e gestire processi di automazione per l'infrastruttura cloud e locale"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Gestire i file in un contenitore BLOB con il connettore di archiviazione BLOB di Azure"
[azure-cosmos-db-doc]: https://docs.microsoft.com/connectors/documentdb/ "Connettersi a Azure Cosmos DB in modo che sia possibile accedere a documenti e stored procedure"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitorare gli eventi pubblicati da una griglia di eventi, ad esempio quando le risorse di Azure o le risorse di terze parti cambiano"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Connettersi a hub eventi di Azure in modo che sia possibile ricevere e inviare eventi tra app per la logica e hub eventi"
[azure-file-storage-doc]: https://docs.microsoft.com/connectors/azurefile/ "Connettersi all'account di archiviazione di Azure in modo da poter creare, aggiornare, ottenere ed eliminare i file"
[azure-key-vault-doc]: https://docs.microsoft.com/connectors/keyvault/ "Connettersi al Azure Key Vault per poter gestire i segreti e le chiavi"
[azure-monitor-logs-doc]: https://docs.microsoft.com/connectors/azuremonitorlogs/ "Eseguire query sui log di monitoraggio di Azure tra Log Analytics aree di lavoro e componenti Application Insights"
[azure-queues-doc]: https://docs.microsoft.com/connectors/azurequeues/ "Connettersi all'account di archiviazione di Azure in modo che sia possibile creare e gestire code e messaggi"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Inviare messaggi da code e argomenti del bus di servizio e ricevere messaggi da code e sottoscrizioni del bus di servizio"
[azure-sql-data-warehouse-doc]: https://docs.microsoft.com/connectors/sqldw/ "Connettersi a Azure SQL Data Warehouse in modo che sia possibile visualizzare i dati"
[azure-table-storage-doc]: https://docs.microsoft.com/connectors/azuretables/ "Connettersi all'account di archiviazione di Azure in modo che sia possibile creare, aggiornare ed eseguire query sulle tabelle e altro ancora"
[biztalk-server-doc]: https://docs.microsoft.com/connectors/biztalk/ "Connettersi al BizTalk Server in modo che sia possibile eseguire applicazioni basate su BizTalk side-by-side con le app per la logica di Azure"
[box-doc]: ./connectors-create-api-box.md "Connettersi a box. Caricare, ottenere, eliminare, elencare i file e altro ancora"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Connettersi a Dropbox. Caricare, ottenere, eliminare, elencare i file e altro ancora"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Connettersi a Dynamics CRM online per poter usare i dati di CRM Online"
[facebook-doc]: ./connectors-create-api-facebook.md "Connettersi a Facebook. Pubblicare un post in una sequenza temporale, ottenere un feed di pagina e altro ancora"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Connettersi a un file system locale"
[ftp-doc]: ./connectors-create-api-ftp.md "Connettersi a un server FTP/FTPS ed eseguire diverse attività FTP, come caricare, recuperare ed eliminare file e altro ancora"
[github-doc]: ./connectors-create-api-github.md "Connettersi a GitHub e tenere traccia dei problemi"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Si connette a Google Calendar e può gestire il calendario"
[google-drive-doc]: ./connectors-create-api-googledrive.md "Connettersi a GoogleDrive in modo da poter usare i dati"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Connettersi a fogli Google per poter modificare i fogli"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Si connette a Google Task per poter gestire le attività"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Connetti a app 3270 in mainframe IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Connettersi a IBM DB2 nel cloud o in locale. Aggiornare una riga, ottenere una tabella e altro ancora"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Connettersi a Informix nel cloud o in locale. Leggere una riga, elencare le tabelle e altro ancora"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Connettersi a IBM MQ in locale o in Azure per inviare e ricevere messaggi"
[instagram-doc]: ./connectors-create-api-instagram.md "Connettersi a Instagram. Attivare o agire sugli eventi"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Connettersi all'account MailChimp. Gestire e automatizzare i messaggi di posta elettronica"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Connettersi a Mandrill per la comunicazione"
[mysql-doc]: https://docs.microsoft.com/connectors/mysql/ "Connettersi al database MySQL locale in modo da poter leggere e scrivere dati"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Connettersi all'account Office 365 per poter inviare e ricevere messaggi di posta elettronica, gestire il calendario e i contatti e altro ancora"
[office-365-users-doc]: ./connectors-create-api-office365-users.md
[onedrive-doc]: ./connectors-create-api-onedrive.md "Connettiti a Microsoft OneDrive personali per poter caricare, eliminare, elencare file e altro ancora"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Connetti all'azienda Microsoft OneDrive per poter caricare, eliminare, elencare i file e altro ancora"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Connettersi a un database Oracle in modo da poter aggiungere, inserire, eliminare righe e altro ancora"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Connettersi alla cassetta postale di Outlook per poter gestire la posta elettronica, i calendari, i contatti e altro ancora"
[postgre-sql-doc]: https://docs.microsoft.com/connectors/postgresql/ "Connettersi al database PostgreSQL per poter leggere i dati dalle tabelle"
[project-online-doc]: ./connectors-create-api-projectonline.md "Connettersi a Microsoft Project online in modo da poter gestire i progetti, le attività, le risorse e altro ancora"
[rss-doc]: ./connectors-create-api-rss.md "Pubblicare e recuperare gli elementi del feed, attivare le operazioni quando viene pubblicato un nuovo elemento in un feed RSS"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Connettersi all'account di Salesforce. Gestisci account, lead, opportunità e altro ancora"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Connettersi a un sistema SAP locale"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Connettersi a SendGrid. Inviare messaggi di posta elettronica e gestire elenchi di destinatari"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Connettersi all'account SFTP tramite SSH. Caricare, ottenere, eliminare file e altro ancora"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Connettersi al server locale di SharePoint. Gestire documenti, elementi dell'elenco e altro ancora"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Connettersi a SharePoint Online. Gestire documenti, elementi dell'elenco e altro ancora"
[slack-doc]: ./connectors-create-api-slack.md "Connettersi a Slack e pubblicare messaggi nei relativi canali"
[smtp-doc]: ./connectors-create-api-smtp.md "Connettersi a un server SMTP e inviare messaggi di posta elettronica con allegati"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Connettersi a SparkPost per la comunicazione"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Connettersi al database SQL di Azure o SQL Server. Creare, aggiornare, ottenere ed eliminare voci in una tabella del database SQL"
[teradata-doc]: https://docs.microsoft.com/connectors/teradata/ "Connettersi al database Teradata per leggere i dati dalle tabelle"
[trello-doc]: ./connectors-create-api-trello.md "Connettersi a Trello. Gestisci i tuoi progetti e organizza qualsiasi elemento con chiunque"
[twilio-doc]: ./connectors-create-api-twilio.md "Connettersi a Twilio. Inviare e ricevere messaggi, ottenere i numeri disponibili, gestire i numeri di telefono in ingresso e altro ancora"
[twitter-doc]: ./connectors-create-api-twitter.md "Connettersi a Twitter. Ottenere sequenze temporali, pubblicare tweet e altro ancora"
[yammer-doc]: ./connectors-create-api-yammer.md "Connettersi a Yammer. Inviare messaggi, ottenere nuovi messaggi e altro ancora"
[youtube-doc]: ./connectors-create-api-youtube.md "Connettersi a YouTube. Gestione di video e canali"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Codificare e decodificare i messaggi che usano il protocollo AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Codificare e decodificare i messaggi che utilizzano il protocollo EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Decodifica dei messaggi che utilizzano il protocollo EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Codificare i messaggi che utilizzano il protocollo EDIFACT"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Informazioni sul file flat di Enterprise Integration"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Informazioni sul file flat di Enterprise Integration"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Gestire i metadati per gli artefatti dell'account di integrazione"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Trasformare JSON con modelli Liquid"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Codificare e decodificare i messaggi che usano il protocollo X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Decodifica dei messaggi che utilizzano il protocollo X12"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Codificare i messaggi che usano il protocollo X12"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Trasformazione di messaggi XML"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Convalida dei messaggi XML"
