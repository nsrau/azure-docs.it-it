---
title: IT Service Management Connector-esportazione sicura in monitoraggio di Azure
description: Questo articolo illustra come connettere i prodotti/servizi ITSM con l'esportazione sicura in monitoraggio di Azure per monitorare e gestire centralmente gli elementi di lavoro ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 5976b70825ac2854e67ddad968752fc87d9e8cea
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377140"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Connettere Azure agli strumenti ITSM usando l'esportazione sicura

Questo articolo illustra come configurare la connessione tra il prodotto o il servizio di gestione dei servizi IT (ITSM) usando l'esportazione sicura.

L'esportazione protetta è una versione aggiornata di [IT Service Management Connector (connettore)](./itsmc-overview.md). Entrambe le versioni consentono di creare elementi di lavoro in uno strumento ITSM quando monitoraggio di Azure invia avvisi. La funzionalità include gli avvisi relativi a metriche, log e log attività.

CONNETTORE usa le credenziali di nome utente e password. L'esportazione protetta ha un'autenticazione più avanzata perché usa Azure Active Directory (Azure AD). Azure AD è il servizio Microsoft basato sul cloud per la gestione delle identità e dell'accesso. Consente agli utenti di accedere alle risorse interne o esterne. L'uso di Azure AD con ITSM consente di identificare gli avvisi di Azure (tramite l'ID dell'applicazione Azure AD) inviati al sistema esterno.

> [!NOTE]
> La possibilità di connettere Azure agli strumenti ITSM tramite l'esportazione protetta è in anteprima.

## <a name="secure-export-architecture"></a>Architettura di esportazione sicura

L'architettura di esportazione protetta introduce le nuove funzionalità seguenti:

* **Nuovo gruppo di azioni** : gli avvisi vengono inviati allo strumento ITSM tramite il gruppo di azioni di Webhook sicuro, anziché il gruppo di azioni ITSM usato da connettore.
* **Autenticazione Azure ad** : viene eseguita l'autenticazione tramite Azure ad anziché le credenziali nome utente/password.

## <a name="secure-export-data-flow"></a>Flusso di dati di esportazione protetta

I passaggi del flusso di dati di esportazione protetta sono:

1. Monitoraggio di Azure invia un avviso configurato per l'uso dell'esportazione protetta.
2. Il payload dell'avviso viene inviato da un'azione webhook sicura allo strumento ITSM.
3. L'applicazione ITSM verifica con Azure AD se l'avviso è autorizzato ad accedere allo strumento ITSM.
4. Se l'avviso è autorizzato, l'applicazione:
   
   1. Consente di creare un elemento di lavoro, ad esempio un evento imprevisto, nello strumento ITSM.
   2. Associa l'ID dell'elemento di configurazione (CI) al database di gestione clienti (CMDB).

![Diagramma che illustra come lo strumento ITSM comunica con Azure A D, avvisi di Azure e un gruppo di azione.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>Vantaggi dell'esportazione sicura

I principali vantaggi dell'integrazione sono:

* **Autenticazione migliore** : Azure ad offre un'autenticazione più sicura senza i timeout che in genere si verificano in connettore.
* **Avvisi risolti nello strumento ITSM** : gli avvisi delle metriche implementano gli Stati "attivato" e "risolto". Quando la condizione viene soddisfatta, lo stato dell'avviso è "attivato". Quando la condizione non viene più soddisfatta, lo stato dell'avviso è "risolto". In connettore gli avvisi non possono essere risolti automaticamente. Con l'esportazione protetta, lo stato risolto passa allo strumento ITSM e pertanto viene aggiornato automaticamente.
* **[Schema di avviso comune](./alerts-common-schema.md)** : in connettore lo schema del payload dell'avviso è diverso in base al tipo di avviso. Nell'esportazione protetta esiste uno schema comune per tutti i tipi di avviso. Questo schema comune contiene la CI per tutti i tipi di avviso. Tutti i tipi di avviso saranno in grado di associare l'integrazione continua con CMDB.

Per iniziare a usare lo strumento ITSM Connector, seguire questa procedura:

1. Registrare l'app con Azure AD.
2. Creare un gruppo di azione di Webhook sicuro.
3. Configurare l'ambiente partner. 

L'esportazione protetta supporta le connessioni con gli strumenti di ITSM seguenti:
* [ServiceNow](https://docs.microsoft.com/azure/azure-monitor/platform/it-service-management-connector-secure-webhook-connections#connect-servicenow-to-azure-monitor)
* [Elica BMC](https://docs.microsoft.com/azure/azure-monitor/platform/it-service-management-connector-secure-webhook-connections#connect-bmc-helix-to-azure-monitor)

## <a name="register-with-azure-active-directory"></a>Registra con Azure Active Directory

Per registrare l'applicazione con Azure AD, attenersi alla procedura seguente:

1. Eseguire la procedura descritta in [registrare un'applicazione con la piattaforma di identità Microsoft](../../active-directory/develop/quickstart-register-app.md).
2. In Azure AD selezionare **Expose Application**.
3. Selezionare **imposta** per **URI ID applicazione**.

   [![Screenshot dell'opzione per l'impostazione di U R I dell'applicazione i D.](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4. Selezionare **Salva**.

## <a name="create-a-secure-webhook-action-group"></a>Creare un gruppo di azione di Webhook sicuro

Dopo che l'applicazione è stata registrata con Azure AD, è possibile creare elementi di lavoro nello strumento ITSM in base agli avvisi di Azure, usando l'azione webhook sicura nei gruppi di azioni.

I gruppi di azioni forniscono un metodo modulare e riutilizzabile per attivare le azioni per gli avvisi di Azure. È possibile usare i gruppi di azioni con avvisi metrica, avvisi del log attività e avvisi di Azure Log Analytics nel portale di Azure.
Per altre informazioni sui gruppi di azione, vedere [Creare e gestire gruppi di azione nel portale di Azure](./action-groups.md).

Per aggiungere un webhook a un'azione, seguire queste istruzioni per il webhook sicuro:

1. Nel [portale di Azure](https://portal.azure.com/) cercare e selezionare **Monitoraggio**. Il riquadro **Monitoraggio** consolida tutte le impostazioni e i dati di monitoraggio in una vista.
2. Selezionare **avvisi**  >  **Gestisci azioni**.
3. Selezionare [Aggiungi gruppo di azione](./action-groups.md#create-an-action-group-by-using-the-azure-portal) e compilare i campi.
4. Immettere un nome nella casella **Nome gruppo di azione** e un nome nella casella **Nome breve gruppo di azione**. Il nome breve viene usato al posto del nome completo di un gruppo di azione quando le notifiche vengono inviate usando questo gruppo.
5. Selezionare il **webhook protetto**.
6. Selezionare i dettagli seguenti:
   1. Consente di selezionare l'ID oggetto dell'istanza di Azure Active Directory registrata.
   2. Per l'URI, incollare l'URL del webhook copiato dall' [ambiente dello strumento ITSM](https://docs.microsoft.com/azure/azure-monitor/platform/it-service-management-connector-secure-webhook-connections#configure-the-partner-environment).
   3. Impostare **Abilita lo schema di avviso comune** su **Sì**. 

   La figura seguente illustra la configurazione di un'azione di Webhook sicura di esempio:

   ![Screenshot che mostra un'azione webhook sicura.](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>Configurare l'ambiente dello strumento ITSM

La configurazione contiene 2 passaggi:
1. Ottenere l'URI per la definizione di esportazione protetta.
2. Definizioni in base al flusso dello strumento ITSM.


### <a name="connect-servicenow-to-azure-monitor"></a>Connettere ServiceNow a monitoraggio di Azure

Le sezioni seguenti forniscono informazioni dettagliate su come connettere il prodotto ServiceNow ed esportare in modo sicuro in Azure.

### <a name="prerequisites"></a>Prerequisiti

Assicurarsi di aver soddisfatto i prerequisiti seguenti:

* Azure AD è registrato.
* Si dispone della versione supportata di ServiceNow Event Management-ITOM (versione Orlando o successiva).

### <a name="configure-the-servicenow-connection"></a>Configurare la connessione ServiceNow

1. utilizzare il collegamento <https:// <instance name> . service-now.com/api/sn_em_connector/em/inbound_event?source=azuremonitor> URI per la definizione di esportazione protetta.

2. Seguire le istruzioni in base alla versione:
   * [Parigi](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [Orlando](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/concept/azure-integration.html)

### <a name="connect-bmc-helix-to-azure-monitor"></a>Connettere BMC Helix a monitoraggio di Azure

Le sezioni seguenti forniscono informazioni dettagliate su come connettere il prodotto BMC Helix ed esportare in modo sicuro in Azure.

### <a name="prerequisites"></a>Prerequisiti

Assicurarsi di aver soddisfatto i prerequisiti seguenti:

* Azure AD è registrato.
* Si dispone della versione supportata di BMC Helix multicloud Service Management (versione 19,08 o successiva).

### <a name="configure-the-bmc-helix-connection"></a>Configurare la connessione a BMC Helix

1. per ottenere l'URI per l'esportazione protetta, attenersi alla procedura seguente nell'ambiente BMC Helix:

   1. Accedere a Integration Studio.
   2. Cercare il flusso di **avvisi Crea evento imprevisto da Azure** .
   3. Copiare l'URL del webhook.
   
   ![Screenshot del webhook U R L in Integration Studio.](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)
   
2. Seguire le istruzioni in base alla versione:
   * [Abilitazione dell'integrazione predefinita con monitoraggio di Azure per la versione 20,02](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html).
   * [Abilitazione dell'integrazione predefinita con monitoraggio di Azure per la versione 19,11](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html).

3. Come parte della configurazione della connessione in BMC Helix, passare all'istanza di Integration BMC e seguire queste istruzioni:

   1. Selezionare **Catalogo**.
   2. Selezionare **avvisi di Azure**.
   3. Selezionare **connettori**.
   4. Selezionare **configurazione**.
   5. Selezionare la configurazione **Aggiungi nuova connessione** .
   6. Immettere le informazioni per la sezione di configurazione:
      - **Nome** : creare un proprio.
      - **Tipo di autorizzazione** : **nessuno**
      - **Descrizione** : crearne una personalizzata.
      - **Sito** : **cloud**
      - **Numero di istanze** : **2** , il valore predefinito.
      - **Check** : selezionato per impostazione predefinita per abilitare l'utilizzo.
      - L'ID tenant di Azure e l'ID dell'applicazione Azure vengono ricavati dall'applicazione definita in precedenza.

![Screenshot che mostra la configurazione BMC.](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)




## <a name="next-steps"></a>Passaggi successivi

* [Creare elementi di lavoro di Connettore di Gestione dei servizi IT da avvisi di Azure](./itsmc-overview.md)
