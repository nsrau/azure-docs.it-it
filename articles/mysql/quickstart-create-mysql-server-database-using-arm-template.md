---
title: 'Avvio rapido: Creare un database di Azure per MySQL - Modello di Resource Manager'
description: Questo argomento di avvio rapido illustra come creare un server di Database di Azure per MySQL con integrazione di rete virtuale usando un modello di Azure Resource Manager.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/19/2020
ms.openlocfilehash: 0e7fcf51d9c663ca4a289f54972f00ef037cb323
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542270"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql-server"></a>Avvio rapido: Usare un modello di Resource Manager per creare un server di Database di Azure per MySQL

Database di Azure per MySQL è un servizio gestito che consente di eseguire, gestire e dimensionare database MySQL a disponibilità elevata nel cloud. In questo argomento di avvio rapido si usa un modello di Azure Resource Manager (modello di Resource Manager) per creare un server di Database di Azure per MySQL con l'integrazione della rete virtuale. È possibile creare il server nel portale di Azure, l'interfaccia della riga di comando di Azure o Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

# <a name="portal"></a>[Portale](#tab/azure-portal)

Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/).
* Per eseguire il codice in locale, [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[CLI](#tab/CLI)

* Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/).
* Per eseguire il codice in locale, [interfaccia della riga di comando di Azure](/cli/azure/).

---

## <a name="review-the-template"></a>Rivedere il modello

Verrà creato un database di Azure per il server MySQL con un set definito di risorse di calcolo e di archiviazione. Per altre informazioni, vedere [Piani tariffari di Database di Azure per MySQL](concepts-pricing-tiers.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md).

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-managed-mysql-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-mysql-with-vnet/azuredeploy.json":::

Il modello definisce cinque risorse di Azure:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforMySQL/servers**](/azure/templates/microsoft.dbformysql/servers)
* [**Microsoft.DBforMySQL/servers/virtualNetworkRules**](/azure/templates/microsoft.dbformysql/servers/virtualnetworkrules)
* [**Microsoft.DBforMySQL/servers/firewallRules**](/azure/templates/microsoft.dbformysql/servers/firewallrules)

Altri esempi di modelli di Database di Azure per MySQL sono disponibili nella [raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformysql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuire il modello

# <a name="portal"></a>[Portale](#tab/azure-portal)

Selezionare il collegamento seguente per distribuire il modello di database di Azure per il server MySQL nel portale di Azure:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

Nella pagina **Deploy Azure Database for MySQL with VNet** (Distribuisci Database di Azure per MySQL con la rete virtuale):

1. In **Gruppo di risorse** selezionare **Crea nuovo**, immettere un nome per il nuovo gruppo di risorse e quindi fare clic su **OK**.

2. Se è stato creato un nuovo gruppo di risorse, selezionare un valore per **Località** per il gruppo di risorse e il nuovo server.

3. Immettere i valori per **Nome server**, **Account di accesso amministratore** e **Password di accesso amministratore**.

    :::image type="content" source="./media/quickstart-create-mysql-server-database-using-arm-template/deploy-azure-database-for-mysql-with-vnet.png" alt-text="Finestra Distribuisci Database di Azure per MySQL con la rete virtuale, modello di avvio rapido di Azure, portale di Azure":::

4. Modificare le altre impostazioni predefinite in base alle esigenze:

    * **Sottoscrizione**: sottoscrizione di Azure da usare per il server.
    * **Capacità SKU**: capacità di vCore, che può essere *2* (impostazione predefinita), *4*, *8*, *16*, *32* o *64*.
    * **Nome SKU**: prefisso del livello SKU, famiglia SKU e capacità dello SKU, uniti da caratteri di sottolineatura, ad esempio *B_Gen5_1*, *GP_Gen5_2* (impostazione predefinita) o *MO_Gen5_32*.
    * **Dimensioni SKU (MB)** : dimensioni di archiviazione in megabyte del database di Azure per il server MySQL (impostazione predefinita *5120*).
    * **Livello SKU**: livello di distribuzione, ad esempio *Basic*, *Utilizzo generico* (impostazione predefinita) o *Ottimizzato per la memoria*.
    * **Famiglia SKU**: *Gen4* o *Gen5* (impostazione predefinita), che indica la generazione hardware per la distribuzione del server.
    * **Versione di MySQL**: versione di MySQL server da distribuire, ad esempio *5.6* o *5.7* (impostazione predefinita).
    * **Giorni di conservazione backup**: periodo desiderato per la conservazione del backup con ridondanza geografica, in giorni (impostazione predefinita *7*).
    * **Backup con ridondanza geografica**: *Abilitato* o *Disabilitato* (impostazione predefinita), a seconda dei requisiti per il ripristino di emergenza geografico.
    * **Nome rete virtuale**: nome della rete virtuale (impostazione predefinita *azure_mysql_vnet*).
    * **Nome subnet**: nome della subnet (impostazione predefinita *azure_mysql_subnet*).
    * **Nome regola di rete virtuale**:nome della regola di rete virtuale che consente la subnet (impostazione predefinita *AllowSubnet*).
    * **Prefisso dell'indirizzo della rete virtuale**: prefisso dell'indirizzo della rete virtuale (impostazione predefinita *10.0.0.0/16*).
    * **Prefisso subnet**: prefisso dell'indirizzo per la subnet (impostazione predefinita *10.0.0.0/16*).

5. Leggere le condizioni, quindi selezionare **Accetto le condizioni riportate sopra**.

6. Selezionare **Acquisto**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Usare il codice interattivo seguente per creare un nuovo database di Azure per il server MySQL usando il modello. Il codice richiede il nome del nuovo server, il nome e la località di un nuovo gruppo di risorse e il nome e la password di un account amministratore.

Per eseguire il codice in Azure Cloud Shell, selezionare **Prova** nell'angolo superiore di qualsiasi blocco di codice.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

Usare il codice interattivo seguente per creare un nuovo database di Azure per il server MySQL usando il modello. Il codice richiede il nome del nuovo server, il nome e la località di un nuovo gruppo di risorse e il nome e la password di un account amministratore.

Per eseguire il codice in Azure Cloud Shell, selezionare **Prova** nell'angolo superiore di qualsiasi blocco di codice.

```azurecli-interactive
echo "Enter a name for the new Azure Database for MySQL server:" &&
read serverName &&
echo "Enter a name for the new resource group where the server will exist:" &&
read resourceGroupName &&
echo "Enter an Azure region (for example, centralus) for the resource group:" &&
read location &&
echo "Enter the Azure Database for MySQL server's administrator account name:" &&
read adminUser &&
echo "Enter the administrator password:" &&
read adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

# <a name="portal"></a>[Portale](#tab/azure-portal)

Seguire questa procedura per visualizzare una panoramica del nuovo database di Azure per il server MySQL:

1. Nel [portale di Azure](https://portal.azure.com) cercare e selezionare **Database di Azure per i server MySQL**.

2. Nell'elenco dei database selezionare il nuovo server. Verrà visualizzata la pagina **Panoramica** per il nuovo database di Azure per il server MySQL.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Eseguire il codice interattivo seguente per visualizzare i dettagli del database di Azure per il server MySQL. Sarà necessario immettere il nome del nuovo server.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/servers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

Eseguire il codice interattivo seguente per visualizzare i dettagli del database di Azure per il server MySQL. Sarà necessario immettere il nome e il gruppo di risorse del nuovo server.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/servers"
```

---

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse per eliminare tutte le risorse contenute al suo interno.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel [portale di Azure](https://portal.azure.com) cercare e selezionare **Gruppi di risorse**.

2. Nell'elenco dei gruppi di risorse scegliere il nome del gruppo di risorse.

3. Nella pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.

4. Nella finestra di dialogo di conferma, digitare il nome del gruppo di risorse e quindi selezionare **Elimina**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Passaggi successivi

Per un'esercitazione dettagliata che illustra il processo di creazione di un modello di Resource Manager, vedere:

> [!div class="nextstepaction"]
> [ Esercitazione: Creare e distribuire il primo modello di Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
