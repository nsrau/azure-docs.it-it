---
title: Protezione avanzata dalle minacce per azure Cosmos DBAdvanced Threat Protection for Azure Cosmos DB
description: Informazioni su come Azure Cosmos DB fornisce la crittografia dei dati inattivi e come vengono implementati.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: bcc1c6ffe7cdec4aed325a67969235ae993a5109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614844"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Advanced Threat Protection for Azure Cosmos DB (anteprima)Advanced Threat Protection for Azure Cosmos DB (Preview)

Advanced Threat Protection for Azure Cosmos DB offre un ulteriore livello di informazioni sulla sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account di Azure Cosmos DB. Questo livello di protezione consente di affrontare le minacce, anche senza essere un esperto di sicurezza, e integrarle con i sistemi di monitoraggio della sicurezza centrale.

Gli avvisi di sicurezza vengono attivati quando si verificano anomalie nell'attività. Questi avvisi di sicurezza sono integrati con il [Centro sicurezza](https://azure.microsoft.com/services/security-center/)di Azure e vengono inoltre inviati tramite posta elettronica agli amministratori della sottoscrizione, con dettagli sulle attività sospette e suggerimenti su come analizzare e correggere le minacce.

> [!NOTE]
>
> * Advanced Threat Protection for Azure Cosmos DB è attualmente disponibile solo per l'API SQL.
> * Advanced Threat Protection for Azure Cosmos DB non è attualmente disponibile nelle aree cloud pubbliche e sovrane di Azure.Advanced Threat Protection for Azure Cosmos DB is currently not available in Azure government and sovereign cloud regions.

Per un'analisi completa degli avvisi di sicurezza, è consigliabile abilitare [la registrazione diagnostica in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging), che registra le operazioni sul database stesso, incluse le operazioni CRUD su tutti i documenti, i contenitori e i database.

## <a name="threat-types"></a>Tipi di minacce

Advanced Threat Protection for Azure Cosmos DB rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database. Attualmente può attivare i seguenti avvisi:

- **Accesso da posizioni insolite:** questo avviso viene attivato quando viene apportata una modifica al modello di accesso a un account Cosmos di Azure, in cui un utente si è connesso all'endpoint di Azure Cosmos DB da una posizione geografica insolita. In alcuni casi, l'avviso rileva un'azione legittima, ovvero un'operazione di manutenzione di una nuova applicazione o di uno sviluppatore. In altri casi, l'avviso rileva un'azione dannosa da un ex dipendente, un utente malintenzionato esterno e così via.

- **Estrazione dei dati insoliti:** questo avviso viene attivato quando un client estrae una quantità insolita di dati da un account di database Cosmos di Azure.Unusual data extraction : This alert is triggered when a client is extracting an unusual amount of data from an Azure Cosmos DB account. Questo può essere il sintomo di alcune esfiltrazioni di dati eseguite per trasferire tutti i dati memorizzati nell'account in un archivio dati esterno.

## <a name="set-up-advanced-threat-protection"></a>Configurare Advanced Threat Protection

### <a name="set-up-atp-using-the-portal"></a>Configurare ATP tramite il portale

1. Avviare il [https://portal.azure.com](https://portal.azure.com/)portale di Azure all'indirizzo .

2. Dall'account Azure Cosmos DB scegliere **Sicurezza avanzata**dal menu **Impostazioni** .

    ![Impostare ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. Nel pannello Configurazione **di sicurezza avanzata:In** the Advanced security configuration blade:

    * Fare clic sull'opzione **Advanced Threat Protection** per impostarla su **ON**.
    * Fare clic su **Salva** per salvare i criteri di Advanced Threat Protection nuovi o aggiornati.   

### <a name="set-up-atp-using-rest-api"></a>Configurare ATP con l'API RESTSet up ATP using REST API

Usare i comandi dell'API Rest per creare, aggiornare o ottenere l'impostazione Di protezione avanzata dalle minacce per un account di database Cosmos di Azure specifico.

* [Protezione avanzata dalle minacce - Crea](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Protezione avanzata dalle minacce - Ottenere](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Configurare ATP con Azure PowerShellSet up ATP using Azure PowerShell

Utilizzare i cmdlet di PowerShell seguenti:

* [Abilitare Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Ottieni protezione avanzata dalle minacce](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Disabilitare Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Uso dei modelli di Gestione risorse di Azure

Usare un modello di Azure Resource Manager per configurare Cosmos DB con Advanced Threat Protection abilitato.
Per ulteriori informazioni, vedere [Creare un account CosmosDB con Advanced Threat Protection](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="using-azure-policy"></a>Uso dei criteri di AzureUsing Azure Policy

Usare un criterio di Azure per abilitare Advanced Threat Protection for Cosmos DB.

1. Avviare la pagina Criteri di Azure **- Definizioni** e cercare i criteri **Distribuisci protezione avanzata dalle** minacce per il database del cosmo.

    ![Criteri di ricerca](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Fare clic sul criterio **Distribuisci protezione avanzata** dalle minacce per CosmosDB e quindi fare clic su **Assegna**.

    ![Seleziona sottoscrizione o gruppo](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. Nel campo **Ambito** fare clic sui tre puntini, selezionare una sottoscrizione o un gruppo di risorse di Azure e quindi fare clic su **Seleziona**.

    ![Pagina Definizioni dei criteri](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Immettere gli altri parametri e fare clic su **Assegna**.

## <a name="manage-atp-security-alerts"></a>Gestire gli avvisi di sicurezza atPManage ATP security alerts

Quando si verificano anomalie dell'attività di Azure Cosmos DB, viene attivato un avviso di sicurezza con informazioni sull'evento di sicurezza sospetto. 

 Dal Centro sicurezza di Azure è possibile esaminare e gestire gli avvisi di [sicurezza correnti.](../security-center/security-center-alerts-overview.md)  Fare clic su un avviso specifico nel [Centro sicurezza](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) per visualizzare le possibili cause e le azioni consigliate per analizzare e ridurre la potenziale minaccia. L'immagine seguente mostra un esempio di dettagli degli avvisi forniti nel Centro sicurezza.

 ![Dettagli sulle minacce](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Viene inoltre inviata una notifica tramite posta elettronica con i dettagli dell'avviso e le azioni consigliate. L'immagine seguente mostra un esempio di messaggio di posta elettronica di avviso.

 ![Dettagli dell'avviso](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Avvisi ATP Cosmos DB

 Per visualizzare un elenco degli avvisi generati durante il monitoraggio degli account di Azure Cosmos DB, vedere la sezione Avvisi cosmos DB nella documentazione del Centro sicurezza di Azure.To see a list of the alerts generated when monitoring Azure Cosmos DB accounts, see the [Cosmos DB alerts](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos) section in the Azure Security Center documentation.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [registrazione diagnostica in Azure Cosmos DBLearn](cosmosdb-monitor-resource-logs.md) more about Diagnostic logging in Azure Cosmos DB
* Altre informazioni sul Centro sicurezza di [AzureLearn](https://docs.microsoft.com/azure/security-center/security-center-intro) more about Azure Security Center
