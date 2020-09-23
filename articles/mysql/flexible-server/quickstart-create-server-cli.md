---
title: 'Avvio rapido: Creare un server - Interfaccia della riga di comando di Azure - Database di Azure per MySQL - Server flessibile'
description: Questo argomento di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per creare un server flessibile di Database di Azure per MySQL in un gruppo di risorse di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc
ms.openlocfilehash: bae6e9f04eced02130ae628d5308a87a1baaa8fa
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945037"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Avvio rapido: Creare un server flessibile di Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure

Questo argomento di avvio rapido illustra come usare i comandi dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) in [Azure Cloud Shell](https://shell.azure.com) per creare un server flessibile di Database di Azure per MySQL in cinque minuti. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile aprire Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere **INVIO** per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questo argomento di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Prerequisiti

È necessario accedere all'account con il comando [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login). Annotare la proprietà **id** che fa riferimento all'**ID sottoscrizione** per l'account Azure.

```azurecli-interactive
az login
```

Selezionare la sottoscrizione specifica nell'account tramite il comando [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set). Annotare il valore **id** dall'output **az login** da usare come valore per l'argomento **subscription** nel comando. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Per ottenere tutte le sottoscrizioni, usare [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Creare un server flessibile

Creare un [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) usando il comando `az group create` e quindi creare il server flessibile MySQL in questo gruppo di risorse. È necessario specificare un nome univoco. L'esempio seguente consente di creare un gruppo di risorse denominato `myresourcegroup` nell'area `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Creare un server flessibile con il comando `az mysql flexible-server create`. Un server può contenere più database. Il comando seguente crea un server usando le impostazioni predefinite del servizio e i valori del [contesto locale](https://docs.microsoft.com/cli/azure/local-context?view=azure-cli-latest) dell'interfaccia della riga di comando di Azure: 

```azurecli
az mysql flexible-server create
```

Il server creato ha gli attributi seguenti: 
- Nome server generato automaticamente, nome utente amministratore, password amministratore, nome del gruppo di risorse (se non è già specificato nel contesto locale) e nello stesso percorso del gruppo di risorse 
- Impostazioni predefinite del servizio per le configurazioni server rimanenti: livello di calcolo (con possibilità di burst), dimensioni/SKU di calcolo (B1MS), periodo di conservazione dei backup (7 giorni) e versione di MySQL (5.7)
- Il metodo di connettività predefinito è l'accesso privato (integrazione rete virtuale) con una rete virtuale e una subnet generate automaticamente

> [!NOTE] 
> Il metodo di connettività non può essere modificato dopo la creazione del server. Ad esempio, se è stata selezionata l'opzione per l'*accesso privato (integrazione rete virtuale)* durante la creazione, non è possibile passare all'*accesso pubblico (indirizzi IP consentiti)* dopo la creazione. È consigliabile creare un server con accesso privato per accedere in modo sicuro al server tramite l'integrazione rete virtuale. Altre informazioni sull'accesso privato sono disponibili nell'[articolo sui concetti](./concepts-networking.md).

Per modificare le impostazioni predefinite, vedere la documentazione di riferimento dell'interfaccia della riga di comando di Azure <!--FIXME --> per avere l'elenco completo dei parametri configurabili dell'interfaccia della riga di comando. 

> [!NOTE]
> Le connessioni al database di Azure per MySQL comunicano sulla porta 3306. Se si tenta di connettersi da una rete aziendale, il traffico in uscita sulla porta 3306 potrebbe non essere consentito. In questo caso, non è possibile connettersi al server a meno che il reparto IT non apra la porta 3306.

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Per connettersi al server, è necessario specificare le informazioni sull'host e le credenziali di accesso.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Il risultato è in formato JSON. Annotare il **fullyQualifiedDomainName** e l'**administratorLogin**.

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "name": "Standard_B1ms",
    "size": null,
    "tier": "Burstable"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "userVisibleState": "Ready",
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