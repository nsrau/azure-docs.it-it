---
title: Advanced Threat Protection per Azure Cosmos DB
description: Informazioni su come Azure Cosmos DB gestisce la crittografia dei dati inattivi e come viene implementata questa funzionalità.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: 0affd1660a88421f6df24bc5ef2e00497dae32a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85119271"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Advanced Threat Protection per Azure Cosmos DB (anteprima)

Advanced Threat Protection per Azure Cosmos DB offre un livello aggiuntivo di intelligence di sicurezza in grado di rilevare tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento di account Azure Cosmos DB. Questo livello di protezione consente di affrontare le minacce anche senza essere esperti di sicurezza e di integrarle con sistemi di monitoraggio della sicurezza di terze parti.

Gli avvisi di sicurezza vengono attivati quando si verifica un'anomalia nell'attività. Questi avvisi di sicurezza sono integrati con il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) e vengono inviati anche tramite posta elettronica agli amministratori della sottoscrizione, con informazioni dettagliate sull'attività sospetta e indicazioni su come analizzare e correggere le minacce.

> [!NOTE]
>
> * Advanced Threat Protection per Azure Cosmos DB è attualmente disponibile solo per l'API SQL.
> * La funzionalità Advanced Threat Protection per Azure Cosmos DB non è attualmente disponibile nelle aree di cloud sovrano e Azure per enti pubblici.

Per un'analisi completa degli avvisi di sicurezza, è consigliabile abilitare la [registrazione diagnostica in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging), che registra le operazioni effettuate nel database, incluse le operazioni CRUD su tutti i documenti, i contenitori e i database.

## <a name="threat-types"></a>Tipi di minacce

Advanced Threat Protection per Azure Cosmos DB rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database. Può attualmente attivare gli avvisi seguenti:

- **Accesso da una posizione insolita**: questo avviso viene attivato quando il modello di accesso a un account Azure Cosmos cambia oppure quando un utente si connette all'endpoint di Azure Cosmos DB da una posizione geografica insolita. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione o un'operazione di manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa da parte di un ex dipendente, un utente malintenzionato esterno e così via.

- **Estrazione insolita di dati**: questo avviso viene generato quando un client estrae una quantità insolita di dati da un account Azure Cosmos DB. Questo evento può essere il sintomo di un'esfiltrazione di dati eseguita per trasferire tutti i dati archiviati nell'account in un archivio dati esterno.



## <a name="configure-advanced-threat-protection"></a>Configurare Advanced Threat Protection

È possibile configurare Advanced Threat Protection in vari modi, descritti nelle sezioni seguenti.

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com/).

2. Dall'account Azure Cosmos DB, selezionare **Sicurezza avanzata** dal menu **Impostazioni**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png" alt-text="Configurare Advanced Threat Protection":::

3. Nel pannello di configurazione **Sicurezza avanzata**:

    * Fare clic sull'opzione **Advanced Threat Protection** per impostarla su **ON**.
    * Fare clic su **Salva** per salvare i criteri di Advanced Threat Protection nuovi o aggiornati.   

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Usare i comandi dell'API REST per creare, aggiornare o acquisire l'impostazione di Advanced Threat Protection per un account Azure Cosmos DB specifico.

* [Advanced Threat Protection - Creare](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Advanced Threat Protection - Ottenere](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Usare i cmdlet di PowerShell seguenti:

* [Abilitare Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Ottenere Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Disabilitare Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="arm-template"></a>[Modello ARM](#tab/arm-template)

Usare un modello di Azure Resource Manager per configurare Cosmos DB con la funzionalità Advanced Threat Protection abilitata.
Per altre informazioni, vedere [Create a CosmosDB Account with Advanced Threat Protection](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/) (Creare un account CosmosDB con Advanced Threat Protection).

### <a name="azure-policy"></a>[Criteri di Azure](#tab/azure-policy)

Usare i criteri di Azure per abilitare Advanced Threat Protection per Cosmos DB.

1. Avviare la pagina di Azure **Criteri - Definizioni** e cercare i criteri **Distribuisci Advanced Threat Protection per Cosmos DB**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db.png" alt-text="Configurare Advanced Threat Protection"::: 

1. Fare clic sui criteri **Distribuisci Advanced Threat Protection per Cosmos DB** e quindi fare clic su **Assegna**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png" alt-text="Configurare Advanced Threat Protection":::


1. Nel campo **Ambito** fare clic sui tre puntini di sospensione, selezionare un gruppo di risorse o una sottoscrizione di Azure e quindi fare clic su **Seleziona**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png" alt-text="Configurare Advanced Threat Protection":::


1. Immettere gli altri parametri e quindi fare clic su **Assegna**.




## <a name="manage-atp-security-alerts"></a>Gestire gli avvisi di sicurezza di Advanced Threat Protection

Quando si verificano anomalie dell'attività di Azure Cosmos DB, viene generato un avviso di sicurezza con informazioni sull'evento di sicurezza sospetto. 

 È possibile rivedere e gestire gli [avvisi di sicurezza](../security-center/security-center-alerts-overview.md) correnti in Centro sicurezza di Azure.  Fare clic su un avviso specifico in [Centro di sicurezza](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) per visualizzare le possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia. Nell'immagine seguente vengono illustrati i dettagli di un avviso di esempio in Centro sicurezza.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png" alt-text="Configurare Advanced Threat Protection":::

Viene inoltre inviata una notifica di posta elettronica con i dettagli dell'avviso e le azioni consigliate. Nella figura seguente viene illustrato un esempio di messaggio di posta elettronica di avviso.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png" alt-text="Configurare Advanced Threat Protection":::

## <a name="cosmos-db-atp-alerts"></a>Avvisi di Cosmos DB ATP

 Per visualizzare un elenco degli avvisi generati durante il monitoraggio di account Azure Cosmos DB, vedere la sezione sugli [avvisi di Cosmos DB](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos) nella documentazione di Centro sicurezza di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [registrazione diagnostica in Azure Cosmos DB](cosmosdb-monitor-resource-logs.md).
* Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
