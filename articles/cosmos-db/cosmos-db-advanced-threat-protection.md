---
title: Advanced Threat Protection per Azure Cosmos DB
description: Informazioni su come Azure Cosmos DB fornisce la crittografia dei dati inattivi e la relativa implementazione.
author: monhaber
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: v-mohabe
ms.custom: seodec18
ms.openlocfilehash: 0504da45cbbd60629954d3e3ca3230e05761c1d6
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640351"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db"></a>Advanced Threat Protection per Azure Cosmos DB

Advanced Threat Protection per Azure Cosmos DB fornisce un ulteriore livello di intelligence per la sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit Azure Cosmos DB account. Questo livello di protezione consente di risolvere le minacce, anche senza essere un esperto di sicurezza e di integrarle con i sistemi di monitoraggio della sicurezza centralizzati.

Gli avvisi di sicurezza vengono attivati quando si verificano anomalie nelle attività. Questi avvisi di sicurezza sono integrati con il  [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/)e vengono inviati anche tramite posta elettronica agli amministratori della sottoscrizione, con informazioni dettagliate sull'attività sospetta e indicazioni su come analizzare e correggere le minacce.

> [!NOTE]
>
> * Advanced Threat Protection per Azure Cosmos DB è attualmente disponibile solo per l'API SQL.
> * Advanced Threat Protection per Azure Cosmos DB non è attualmente disponibile in Azure per enti pubblici e aree cloud sovrane.

Per un'analisi completa degli avvisi di sicurezza, è consigliabile abilitare la [registrazione diagnostica in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging), che registra le operazioni nel database stesso, incluse le operazioni CRUD su tutti i documenti, i contenitori e i database.

## <a name="set-up-advanced-threat-protection"></a>Configurare Advanced Threat Protection

### <a name="set-up-atp-using-the-portal"></a>Configurare ATP usando il portale

1. Avviare il portale di Azure all'  [https://portal.azure.com](https://portal.azure.com/)indirizzo.

2. Dall'account Azure Cosmos DB scegliere **sicurezza avanzata**dal menu **Impostazioni** .

    ![Configurare ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. Nel pannello **Advanced Security** Configuration:

    * Fare clic sull'opzione **Advanced Threat Protection** per impostarla **su on**.
    * Fare clic su **Salva** per salvare i criteri avanzati di protezione dalle minacce nuovi o aggiornati.   

### <a name="set-up-atp-using-rest-api"></a>Configurare ATP con l'API REST

Usare i comandi dell'API REST per creare, aggiornare o ottenere l'impostazione di Advanced Threat Protection per un account Azure Cosmos DB specifico.

* [Advanced Threat Protection-crea](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Advanced Threat Protection-Ottieni](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Configurare ATP usando Azure PowerShell

Usare i cmdlet di PowerShell seguenti:

* [Abilita Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Ottenere Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Disabilitare Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

## <a name="manage-atp-security-alerts"></a>Gestione degli avvisi di sicurezza ATP

Quando si verificano Azure Cosmos DB anomalie dell'attività, viene generato un avviso di sicurezza con informazioni sull'evento di sicurezza sospetto. 

 Dal centro sicurezza di Azure è possibile esaminare e gestire gli [avvisi di sicurezza](../security-center/security-center-alerts-overview.md)correnti.  Fare clic su un avviso specifico nel [Centro sicurezza](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) per visualizzare le possibili cause e le azioni consigliate per analizzare e ridurre la potenziale minaccia. Nell'immagine seguente viene illustrato un esempio di dettagli dell'avviso disponibili nel centro sicurezza.

 ![Dettagli minaccia](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Viene inoltre inviata una notifica di posta elettronica con i dettagli dell'avviso e le azioni consigliate. Nell'immagine seguente viene illustrato un esempio di messaggio di posta elettronica di avviso.

 ![Dettagli dell'avviso](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Avvisi di Cosmos DB ATP

 Per visualizzare un elenco degli avvisi generati durante il monitoraggio di account Azure Cosmos DB, vedere la sezione [avvisi Cosmos DB](../security-center/security-center-alerts-data-services.md#cosmos-db) nella documentazione del Centro sicurezza.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [registrazione diagnostica in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging#turn-on-logging-in-the-azure-portal)
* Scopri di più sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)