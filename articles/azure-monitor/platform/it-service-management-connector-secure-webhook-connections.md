---
title: IT Service Management Connector-esportazione sicura in monitoraggio di Azure
description: Questo articolo fornisce informazioni su come connettere i prodotti/servizi ITSM con l'esportazione protetta in monitoraggio di Azure per monitorare e gestire centralmente gli elementi di lavoro ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 160054e7e98dc2cb06c2c7daf325536766963daa
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568643"
---
# <a name="connect-azure-to-itsm-tools-using-secure-export"></a>Connettere Azure agli strumenti ITSM usando l'esportazione sicura

Questo articolo fornisce informazioni su come configurare la connessione tra il prodotto/servizio ITSM usando l'esportazione protetta.

L'esportazione protetta è una versione aggiornata di [connettore](./itsmc-overview.md) (it Service Management). Entrambe le versioni consentono di creare elementi di lavoro in uno strumento ITSM quando vengono generati avvisi di monitoraggio di Azure. La funzionalità include gli avvisi relativi a metriche, log e log attività.

[Connettore](./itsmc-overview.md) usa le credenziali utente e password, mentre l'esportazione protetta ha un'autenticazione più avanzata perché usa Azure Active Directory (Azure ad). Azure Active Directory (Azure AD) è il servizio Microsoft basato sul cloud per la gestione delle identità e dell'accesso. Consente agli utenti di accedere alle risorse interne o esterne. L'uso di Azure AD con ITSM consente di identificare gli avvisi di Azure (usando l'ID dell'applicazione Azure AD) inviati al sistema esterno.

> [!NOTE]
> Gli strumenti Connetti Azure a ITSM con l'esportazione sicura sono disponibili in anteprima

## <a name="secure-export-architecture"></a>Architettura di esportazione sicura

L'architettura di esportazione protetta introduce le nuove funzionalità seguenti:

* **Nuovo gruppo di azioni** : gli avvisi vengono inviati allo strumento ITSM usando il gruppo di azioni del webhook sicuro (anziché il gruppo di azioni ITSM usando in connettore).
* **Autenticazione Azure ad** : viene eseguita utilizzando Azure ad invece delle credenziali utente/password.

## <a name="secure-export-data-flow"></a>Flusso di dati di esportazione protetta

I passaggi del flusso di dati di esportazione protetta sono:

1) Un avviso configurato per usare gli incendi di esportazione protetti in monitoraggio di Azure
2) Il payload dell'avviso viene inviato da un'azione di Webhook sicura allo strumento ITSM.
3) L'applicazione ITSM verifica con Azure AD se l'avviso è autorizzato ad accedere allo strumento ITSM.
4) Se l'avviso è autorizzato all'applicazione:
    1) Consente di creare un elemento di lavoro, ad esempio un evento imprevisto, nello strumento ITSM.
    2) Associa l'ID dell'elemento di configurazione (CI) al database di gestione clienti (CMDB).
![Diagramma ITSM](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="connection-with-bmc-helix"></a>Connessione con BMC Helix

L'esportazione protetta supporta BMC Helix. Ecco alcuni vantaggi dell'integrazione:

* **Autenticazione migliore** : Azure ad offre un'autenticazione più sicura senza i timeout che in genere si verificano in connettore.
* **Avvisi risolti nello strumento ITSM** : gli avvisi delle metriche implementano uno stato "attivato" e "risolto". Quando la condizione viene soddisfatta, lo stato dell'avviso è "attivato". Quando la condizione non viene più soddisfatta, lo stato dell'avviso è "risolto". In connettore non è stato possibile risolvere automaticamente gli avvisi. Con l'esportazione sicura, lo stato risolto passa allo strumento ITSM, quindi gli aggiornamenti vengono eseguiti automaticamente.
* **[Schema comune consente](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)** , in connettore, lo schema del payload dell'avviso è diverso in base al tipo di avviso. Nell'esportazione sicura, si tratta di uno schema comune per tutti i tipi di avviso. Questo nuovo schema comune contiene la CI per tutti i tipi di avviso. Poiché tutti i tipi di avviso saranno in grado di associare l'integrazione continua con CMDB.

Per iniziare a usare il connettore ITSM, seguire questa procedura:

1. Registrare l'app con Azure Active Directory.
2. Creare un gruppo di azione di Webhook sicuro.
3. Configurare l'ambiente partner.

## <a name="register-with-azure-active-directory"></a>Registra con Azure Active Directory

Seguire questa procedura per registrare Azure AD applicazione con Azure Active Directory

1) [Creazione Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)
2) Nella Azure Active Directory selezionare "esporre l'applicazione"
3) Selezionare imposta nell'URI dell'ID applicazione [ ![ Azure ad](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4) Fare clic su Salva.

## <a name="create-a-secure-webhook-action-group"></a>Creare un gruppo di azione di Webhook sicuro

Una volta registrata la Azure AD, è possibile creare elementi di lavoro nello strumento ITSM in base agli avvisi di Azure, usando l'azione di Webhook sicura nei gruppi di azioni.
I gruppi di azioni forniscono un modo modulare e riutilizzabile per attivare le azioni per gli avvisi di Azure. È possibile usare i gruppi di azioni con avvisi relativi alle metriche, avvisi del log attività e avvisi di Azure Log Analytics nel portale di Azure.
Per altre informazioni sui gruppi di azione, vedere [Creare e gestire gruppi di azione nel portale di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).
Utilizzare la procedura seguente:

Nell'ambiente BMC Helix:

1. Accedere a Integration Studio.
2. Cercare il flusso di avvisi Crea evento imprevisto da Azure.
3. Copiare l'URL del webhook.
![URL BMC](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)

Per aggiungere un webhook a un'azione, seguire le istruzioni per un webhook sicuro:

1. Nel [portale di Azure](https://portal.azure.com/) cercare e selezionare **Monitoraggio**. Il riquadro **Monitoraggio** consolida tutte le impostazioni e i dati di monitoraggio in una vista.
2. Selezionare **Avvisi** e quindi **Gestisci azioni**.
3. Selezionare [Aggiungi gruppo di azione](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal) e compilare i campi.
4. Immettere un nome nella casella **Nome gruppo di azione** e un nome nella casella **Nome breve gruppo di azione**. Il nome breve viene usato al posto del nome completo di un gruppo di azione quando le notifiche vengono inviate usando questo gruppo.
5. Selezionare un **webhook protetto**
6. Selezionare Modifica dettagli. La figura seguente illustra un'azione webhook protetta di esempio:
    1. Selezionare l'ID oggetto corretto del Azure Active Directory registrato
    2. Incollare l'URL del webhook copiato dall'ambiente "BMC Helix" nel campo URI
    3. Impostare lo **schema di avviso comune** su **Sì**. 
7. La figura seguente mostra una configurazione di azione webhook protetta di esempio: ![ webhook sicuro](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-partner-environment"></a>Configurare l'ambiente partner

### <a name="connect-bmc-helix-to-azure-monitor"></a>Connettere BMC Helix a monitoraggio di Azure

La sezione seguente fornisce informazioni dettagliate su come connettere il prodotto BMC Helix ed esportare in modo sicuro in Azure.

### <a name="prerequisites"></a>Prerequisiti

Accertarsi di aver soddisfatto i prerequisiti seguenti:

* Azure AD è registrato.
* La versione supportata di BMC Helix multicloud Service Management è la versione 20,02 o successiva.

Per configurare la connessione all'elica BMC:

1) [Abilitazione dell'integrazione predefinita con monitoraggio di Azure per la versione 20,2](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)

2) Come parte della configurazione della connessione in BMC Helix, passare all'istanza di Integration BMC e seguire le istruzioni:

1. Selezione **Catalogo**
2. Selezionare gli **avvisi di Azure**
3. Selezionare i **connettori**
4. Seleziona **configurazione**
5. Selezionare **Aggiungi nuova configurazione connessione**
6. Inserire le informazioni per la sezione di configurazione.
    1. **Nome** : creare un proprio
    2. **Tipo di autorizzazione** -nessuno
    3. **Descrizione**-crearne una personalizzata
    4. **Sito**-cloud
    5. **Numero di istanze** -2: valore predefinito
    6. **Check** -selezionato per impostazione predefinita e Abilita utilizzo
    7. ID tenant di Azure. l'ID dell'applicazione Azure viene ricavato dall'applicazione definita nel passaggio "creato Azure Active Directory".
![Configurazione BMC](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare elementi di lavoro di Connettore di Gestione dei servizi IT da avvisi di Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
