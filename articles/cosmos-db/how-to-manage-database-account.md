---
title: Informazioni su come gestire gli account di database in Azure Cosmos DB
description: Informazioni su come gestire Azure Cosmos DB risorse usando i modelli portale di Azure, PowerShell, CLI e Azure Resource Manager
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/18/2020
ms.author: mjbrown
ms.openlocfilehash: 98210f26072504c129ba32f765cf6bab74fef604
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570720"
---
# <a name="manage-an-azure-cosmos-account"></a>Gestire un account Azure Cosmos

Questo articolo descrive come gestire varie attività relative a un account Azure Cosmos usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure e i modelli di Azure Resource Manager.

## <a name="create-an-account"></a>Creare un account

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Portale di Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>

Vedere [creare un account di Azure Cosmos DB con l'interfaccia della riga di comando di Azure](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

Vedere [creare un account Azure Cosmos DB con PowerShell](manage-with-powershell.md#create-account)

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Modello di Azure Resource Manager

Vedere [creare un account di Azure Cosmos DB con modelli di Azure Resource Manager](manage-sql-with-resource-manager.md)

## <a name="addremove-regions-from-your-database-account"></a>Aggiungere o rimuovere aree dall'account di database

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Passare all'account Azure Cosmos e aprire il menu **Replica i dati a livello globale**.

1. Per aggiungere aree, selezionare gli esagoni sulla mappa con l' **+** etichetta corrispondente alle aree desiderate. In alternativa, per aggiungere un'area, selezionare l'opzione **+ Aggiungi area** e scegliere un'area dal menu a discesa.

1. Per rimuovere aree, deselezionare una o più aree nella mappa selezionando gli esagoni blu con segni di spunta. In alternativa, selezionare l'icona del cestino (🗑) accanto all'area sul lato destro.

1. Selezionare **OK** per salvare le modifiche.

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="Aggiungere o rimuovere aree":::

In modalità di scrittura in una singola area non è possibile rimuovere l'area di scrittura. È necessario eseguire il failover in un'area diversa prima di poter eliminare l'area di scrittura corrente.

In modalità di scrittura in più aree è possibile aggiungere o rimuovere qualsiasi area, purché resti almeno un'area.

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>

Vedere [aggiungere o rimuovere aree con l'interfaccia della riga di comando di Azure](manage-with-cli.md#add-or-remove-regions)

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

Vedere [aggiungere o rimuovere aree con PowerShell](manage-with-powershell.md#update-account)

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Configurare più aree di scrittura

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Portale di Azure

Aprire la scheda **Replica i dati a livello globale** e selezionare **Abilita** per abilitare le operazioni di scrittura in più aree. Dopo aver abilitato tali operazioni, tutte le aree di lettura di cui si dispone attualmente nell'account diventeranno aree di lettura e scrittura.

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="Aggiungere o rimuovere aree":::

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>

Vedere [abilitare aree con più scritture con l'interfaccia della riga di comando di Azure](manage-with-cli.md#enable-multiple-write-regions)

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Vedere [abilitare aree con più scritture con PowerShell](manage-with-powershell.md#multi-region-writes)

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Modello di Resource Manager

È possibile eseguire la migrazione di un account da un'area di scrittura singola a più aree di scrittura distribuendo il modello di Gestione risorse usato per creare l'account e l'impostazione `enableMultipleWriteLocations: true` . Il modello di Azure Resource Manager seguente è il modello minimo essenziale che distribuirà un account Azure Cosmos per l'API SQL con due aree e più località di scrittura abilitate.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Abilitare il failover automatico per l'account Azure Cosmos

L'opzione di failover automatico consente ad Azure Cosmos DB di eseguire il failover all'area con la priorità di failover più alta senza alcuna azione da parte dell'utente nel caso in cui un'area diventasse non disponibile. Quando il failover automatico è abilitato, è possibile modificare la priorità delle aree. Per abilitare il failover automatico, è necessario che l'account abbia almeno due aree.

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Portale di Azure

1. Dall'account Azure Cosmos aprire il riquadro **Replica i dati a livello globale**.

2. Nella parte superiore del riquadro fare clic sul pulsante **Failover automatico**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Aggiungere o rimuovere aree":::

3. Nel riquadro **Failover automatico** assicurarsi che **Abilita failover automatico** sia impostato su **SÌ**. 

4. Selezionare **Salva**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Aggiungere o rimuovere aree":::

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>

Vedere [abilitare il failover automatico con l'interfaccia della riga di comando di Azure](manage-with-cli.md#enable-automatic-failover)

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Vedere [abilitare il failover automatico con PowerShell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Impostare le priorità di failover per l'account Azure Cosmos

Dopo aver configurato un account Cosmos per il failover automatico, è possibile modificare la priorità di failover per le aree.

> [!IMPORTANT]
> Se l'account è configurato per il failover automatico, non è possibile modificare l'area di scrittura (priorità di failover zero). Per cambiare l'area di scrittura, è necessario disabilitare il failover automatico ed eseguire un failover manuale.

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Portale di Azure

1. Dall'account Azure Cosmos aprire il riquadro **Replica i dati a livello globale**.

2. Nella parte superiore del riquadro fare clic sul pulsante **Failover automatico**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Aggiungere o rimuovere aree":::

3. Nel riquadro **Failover automatico** assicurarsi che **Abilita failover automatico** sia impostato su **SÌ**.

4. Per modificare la priorità di failover, trascinare le aree di lettura tramite i tre puntini a sinistra nella riga, visualizzati al passaggio del mouse.

5. Selezionare **Salva**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Aggiungere o rimuovere aree":::

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>

Vedere [impostare priorità di failover con l'interfaccia della riga di comando di Azure](manage-with-cli.md#set-failover-priority)

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Vedere [impostare la priorità di failover con PowerShell](manage-with-powershell.md#modify-failover-priority)

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Eseguire il failover manuale su un account Azure Cosmos

> [!IMPORTANT]
> Affinché questa operazione abbia esito positivo, è necessario configurare l'account Azure Cosmos per il failover manuale.

Il processo di esecuzione di un failover manuale prevede la modifica dell'area di scrittura dell'account (priorità di failover = 0) mediante l'impostazione di un'altra area configurata per l'account.

> [!NOTE]
> Non è possibile eseguire manualmente il failover degli account con più aree di scrittura. Per le applicazioni che usano l'SDK di Azure Cosmos, l'SDK rileverà quando un'area diventa non disponibile e quindi eseguirà automaticamente il reindirizzamento all'area successiva più vicina, se nell'SDK viene usata l'API multihosting.

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Portale di Azure

1. Passare all'account Azure Cosmos e aprire il menu **Replica i dati a livello globale**.

2. Nella parte superiore del menu selezionare **Failover manuale**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Aggiungere o rimuovere aree":::

3. Nel menu **Failover manuale** selezionare la nuova area di scrittura. Selezionare la casella di controllo per indicare di aver compreso che questa opzione modifica l'area di scrittura.

4. Per attivare il failover, selezionare **OK**.

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="Aggiungere o rimuovere aree":::

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>

Vedere [attivare il failover manuale con l'interfaccia della riga di comando di Azure](manage-with-cli.md#trigger-manual-failover)

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Vedere [attivare il failover manuale con PowerShell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni ed esempi su come gestire l'account Azure Cosmos, nonché il database e i contenitori, leggere gli articoli seguenti:

* [Gestire Azure Cosmos DB usando Azure PowerShell](manage-with-powershell.md)
* [Gestire Azure Cosmos DB usando l'interfaccia della riga di comando di Azure](manage-with-cli.md)
