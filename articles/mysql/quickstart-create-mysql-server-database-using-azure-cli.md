---
title: 'Guida introduttiva: Creare un server - Interfaccia della riga di comando di Azure - Database di Azure per MySQL'
description: Questa guida di avvio rapido descrive come usare l'interfaccia della riga di comando di Azure per creare un database di Azure per il server MySQL in un gruppo di risorse di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc
ms.openlocfilehash: 638fa5af3af1e81020e79c7c70f0c91f06676daf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076683"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Guida introduttiva: Creare un database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure

> [!TIP]
> Può essere opportuno usare il comando dell'interfaccia della riga di comando di Azure più semplice [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) (attualmente in anteprima). Provare l'[argomento di avvio rapido](./quickstart-create-server-up-azure-cli.md).

Questa guida di avvio rapido descrive come usare i comandi dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) in [Azure Cloud Shell](https://shell.azure.com) per creare un server di Database di Azure per MySQL in cinque minuti. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

> [!NOTE]
> Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

## <a name="prerequisites"></a>Prerequisiti
Per questo articolo è necessario eseguire in locale l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Per vedere la versione installata, eseguire il comando `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

È necessario accedere all'account con il comando [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login). Annotare la proprietà **id** che fa riferimento all'**ID sottoscrizione** per l'account Azure. 

```azurecli-interactive
az login
```

Selezionare la sottoscrizione specifica nell'account tramite il comando [az account set](/cli/azure/account). Annotare il valore **id** dall'output **az login** da usare come valore per l'argomento **subscription** nel comando. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Per ottenere tutte le sottoscrizioni, usare [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Creare un server Database di Azure per MySQL
Creare un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) usando il comando [az group create](/cli/azure/group) e quindi creare il server MySQL in questo gruppo di risorse. È necessario specificare un nome univoco. L'esempio seguente consente di creare un gruppo di risorse denominato `myresourcegroup` nell'area `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Creare un database di Azure per il server MySQL con il comando [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create). Un server può contenere più database.

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

Ecco i dettagli per gli argomenti indicati: 

**Impostazione** | **Valore di esempio** | **Descrizione**
---|---|---
name | mydemoserver | Immettere un nome univoco per il server di Database di Azure per MySQL. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) e deve avere una lunghezza compresa tra 3 e 63 caratteri.
resource-group | myresourcegroup | Specificare il nome del gruppo di risorse di Azure.
posizione | westus | Località di Azure per il server.
admin-user | myadmin | Nome utente per l'account di accesso dell'amministratore. Non può essere **azure_superuser**, **admin**, **administrator**, **root**, **guest** o **public**'.
admin-password | *password di protezione* | Password dell'utente amministratore. Deve contenere tra 8 e 128 caratteri. La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri e caratteri non alfanumerici.
sku-name|GP_Gen5_2|Immettere il nome del piano tariffario e della configurazione delle risorse di calcolo. Segue la convenzione {piano tariffario} _{generazione di calcolo}_ {Vcore} in sintassi abbreviata. Per altre informazioni, vedere i [piani tariffari](./concepts-pricing-tiers.md).

>[!IMPORTANT] 
>- La versione predefinita di MySQL nel server è 5.7. Sono attualmente disponibili anche le versioni 5.6 e 8.0.
>- Per visualizzare tutti gli argomenti per il comando **az mysql server create**, vedere questo [documento di riferimento](/cli/azure/mysql/server#az-mysql-server-create).
>- SSL viene abilitato per impostazione predefinita nel server. Per altre informazioni su SSL, vedere [Configurare la connettività SSL](howto-configure-ssl.md)

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server 
Per impostazione predefinita, il nuovo server creato viene protetto con regole del firewall e non è accessibile pubblicamente. È possibile configurare la regola del firewall nel server usando il comando [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule). Ciò consentirà di connettersi al server localmente.

L'esempio seguente crea una regola firewall denominata `AllowMyIP` che consente connessioni da un indirizzo IP specifico, 192.168.0.1. Sostituire l'indirizzo IP da cui si effettuerà la connessione. Se necessario, è possibile usare un intervallo di indirizzi IP. Se non si sa come cercare l'indirizzo IP, passare a [https://whatismyipaddress.com/](https://whatismyipaddress.com/) per ottenere l'indirizzo IP.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Le connessioni al database di Azure per MySQL comunicano sulla porta 3306. Se si tenta di connettersi da una rete aziendale, il traffico in uscita sulla porta 3306 potrebbe non essere consentito. In questo caso, non è possibile connettersi al server a meno che il reparto IT non apra la porta 3306.

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Per connettersi al server, è necessario specificare le informazioni sull'host e le credenziali di accesso.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Il risultato è in formato JSON. Annotare il **fullyQualifiedDomainName** e l'**administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Connettersi al server di Database di Azure per MySQL usando il client da riga di comando mysql
È possibile connettersi al server usando uno strumento client noto: strumento da riga di comando **[mysql.exe](https://dev.mysql.com/downloads/)** con [Azure Cloud Shell](../cloud-shell/overview.md). In alternativa, è possibile usare la riga di comando mysql nell'ambiente locale.
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>Pulire le risorse
Se queste risorse non sono necessarie per un'altra guida introduttiva/esercitazione, è possibile eliminarle eseguendo il comando seguente: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Se si vuole eliminare solo il server appena creato, è possibile eseguire il comando [az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete).

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Creare un'app PHP in Windows con MySQL](../app-service/app-service-web-tutorial-php-mysql.md)
>[Creare un'app PHP in Linux con MySQL](../app-service/containers/tutorial-php-mysql-app.md)
>[Creare un'app Spring basata su Java con MySQL](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)
