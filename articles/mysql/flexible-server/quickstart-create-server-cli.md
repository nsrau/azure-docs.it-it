---
title: 'Avvio rapido: Creare un server - Interfaccia della riga di comando di Azure - Database di Azure per MySQL - Server flessibile'
description: Questo argomento di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per creare un server flessibile di Database di Azure per MySQL in un gruppo di risorse di Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 65cc3d2fdcbdea934e80a5f0012ca4f3da157ca3
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843435"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Avvio rapido: Creare un server flessibile di Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure

Questo argomento di avvio rapido illustra come usare i comandi dell'[interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli) in [Azure Cloud Shell](https://shell.azure.com) per creare un server flessibile di Database di Azure per MySQL in cinque minuti. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile aprire Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere **INVIO** per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questo argomento di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Prerequisiti

È necessario accedere all'account con il comando [az login](/cli/azure/reference-index#az-login). Annotare la proprietà **id** che fa riferimento all'**ID sottoscrizione** per l'account Azure.

```azurecli-interactive
az login
```

Selezionare la sottoscrizione specifica nell'account tramite il comando [az account set](/cli/azure/account#az-account-set). Annotare il valore **id** dall'output **az login** da usare come valore per l'argomento **subscription** nel comando. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Per ottenere tutte le sottoscrizioni, usare [az account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Creare un server flessibile

Creare un [gruppo di risorse di Azure](../../azure-resource-manager/management/overview.md) usando il comando `az group create` e quindi creare il server flessibile MySQL in questo gruppo di risorse. È necessario specificare un nome univoco. L'esempio seguente consente di creare un gruppo di risorse denominato `myresourcegroup` nell'area `eastus2`.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Creare un server flessibile con il comando `az mysql flexible-server create`. Un server può contenere più database. Il comando seguente crea un server usando le impostazioni predefinite del servizio e i valori del [contesto locale](/cli/azure/local-context) dell'interfaccia della riga di comando di Azure: 

```azurecli
az mysql flexible-server create
```

Il server creato ha gli attributi seguenti: 
- Nome server generato automaticamente, nome utente amministratore, password amministratore, nome del gruppo di risorse (se non è già specificato nel contesto locale) e nello stesso percorso del gruppo di risorse 
- Impostazioni predefinite del servizio per le configurazioni server rimanenti: livello di calcolo (con possibilità di burst), dimensioni/SKU di calcolo (B1MS), periodo di conservazione dei backup (7 giorni) e versione di MySQL (5.7)
- Il metodo di connettività predefinito è l'accesso privato (integrazione rete virtuale) con una rete virtuale e una subnet generate automaticamente

> [!NOTE] 
> Il metodo di connettività non può essere modificato dopo la creazione del server. Ad esempio, se è stata selezionata l'opzione per l'*accesso privato (integrazione rete virtuale)* durante la creazione, non è possibile passare all'*accesso pubblico (indirizzi IP consentiti)* dopo la creazione. È consigliabile creare un server con accesso privato per accedere in modo sicuro al server tramite l'integrazione rete virtuale. Altre informazioni sull'accesso privato sono disponibili nell'[articolo sui concetti](./concepts-networking.md).

Per modificare le impostazioni predefinite, vedere la [documentazione di riferimento](/cli/azure/mysql/flexible-server) dell'interfaccia della riga di comando di Azure per l'elenco completo dei parametri dell'interfaccia della riga di comando configurabili. 

Di seguito è riportato un output di esempio: 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

Per modificare le impostazioni predefinite, vedere la [documentazione di riferimento](/cli/azure/mysql/flexible-server) dell'interfaccia della riga di comando di Azure per l'elenco completo dei parametri dell'interfaccia della riga di comando configurabili. 

> [!NOTE]
> Le connessioni al database di Azure per MySQL comunicano sulla porta 3306. Se si tenta di connettersi da una rete aziendale, il traffico in uscita sulla porta 3306 potrebbe non essere consentito. In questo caso, non è possibile connettersi al server a meno che il reparto IT non apra la porta 3306.

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Per connettersi al server, è necessario specificare le informazioni sull'host e le credenziali di accesso.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Il risultato è in formato JSON. Annotare il **fullyQualifiedDomainName** e l'**administratorLogin**. Di seguito è riportato un esempio di output JSON: 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-using-mysql-command-line-client"></a>Connettersi tramite il client della riga di comando mysql

Poiché il server flessibile è stato creato con l'opzione per l'*accesso privato (integrazione rete virtuale)* , sarà necessario connettersi al server da una risorsa all'interno della stessa rete virtuale usata dal server. È possibile creare una macchina virtuale e aggiungerla alla rete virtuale creata. 

Dopo aver creato la macchina virtuale, è possibile usare SSH per accedervi e installare uno strumento client molto diffuso, ovvero lo strumento da riga di comando **[mysql.exe](https://dev.mysql.com/downloads/)** .

Con mysql.exe, connettersi usando il comando seguente. Sostituire i valori con il nome server e la password effettivi. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se queste risorse non sono necessarie per un'altra guida introduttiva/esercitazione, è possibile eliminarle eseguendo il comando seguente:

```azurecli-interactive
az group delete --name myresourcegroup
```

Se si vuole eliminare solo il server appena creato, è possibile eseguire il comando `az mysql server delete`.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Creare un'app Web PHP (Laravel) con MySQL](tutorial-php-database-app.md)
