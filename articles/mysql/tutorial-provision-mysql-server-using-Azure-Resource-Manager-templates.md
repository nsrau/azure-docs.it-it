---
title: 'Esercitazione: Effettuare il provisioning di un server di Database di Azure per MySQL usando il modello di Azure Resource Manager'
description: Questa esercitazione illustra come effettuare il provisioning e automatizzare le distribuzioni di server di Database di Azure per MySQL usando il modello di Azure Resource Manager.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/21/2018
ms.custom: mvc
ms.openlocfilehash: 5d5398f4da7563c6f53c17d0305f54c4360f1c65
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58076854"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Esercitazione: Effettuare il provisioning di un server di Database di Azure per MySQL usando il modello di Azure Resource Manager

L'[API REST di Database di Azure per MySQL](https://docs.microsoft.com/rest/api/mysql/) consente ai tecnici DevOps di automatizzare e integrare il provisioning, la configurazione e le operazioni di server e database MySQL gestiti in Azure.  L'API consente la creazione, l'enumerazione, la gestione e l'eliminazione di server e database MySQL nel servizio Database di Azure per MySQL.

Azure Resource Manager usa l'API REST sottostante per dichiarare e programmare le risorse di Azure necessarie per le distribuzioni su larga scala, in linea con il concetto di infrastruttura distribuita come codice. Il modello usa come parametri il nome della risorsa di Azure, lo SKU, la rete, la configurazione del firewall e le impostazioni, consentendone la creazione una sola volta e il riutilizzo più volte.  È possibile creare modelli di Azure Resource Manager con facilità tramite il [portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal) o [Visual Studio Code](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code?tabs=CLI), che consentono la creazione di pacchetti, la standardizzazione e l'automazione della distribuzione dell'applicazione, che può essere integrata nella pipeline CI/CD DevOps.  Se ad esempio si vuole distribuire rapidamente un'app Web con il back-end di Database di Azure per MySQL, è possibile eseguire la distribuzione end-to-end usando questo [modello di avvio rapido](https://azure.microsoft.com/en-us/resources/templates/101-webapp-managed-mysql/) della raccolta GitHub.

In questa esercitazione si useranno il modello di Azure Resource Manager e altre utilità e si apprenderà come:

> [!div class="checklist"]
> * Creare un server di Database di Azure per MySQL con un endpoint servizio rete virtuale usando un modello di Azure Resource Manager
> * Usare lo [strumento della riga di comando di MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Creare un server di Database di Azure per MySQL con un endpoint servizio rete virtuale usando un modello di Azure Resource Manager

Per ottenere le informazioni di riferimento del modello JSON per un server di Database di Azure per MySQL, passare alle informazioni di riferimento del modello di [server Microsoft.DBforMySQL](/azure/templates/microsoft.dbformysql/servers). Di seguito è riportato il modello JSON di esempio che è possibile usare per creare un nuovo server che esegua Database di Azure per MySQL con un endpoint servizio rete virtuale.
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
In questa richiesta, i valori da personalizzare sono:
+   `name`: specificare il nome del server MySQL (senza nome di dominio).
+   `location`: specificare un'area di data center di Azure valida per il server MySQL, ad esempio, westus2.
+   `properties/version`: specificare la versione del server MySQL da distribuire, ad esempio, 5.6 o 5.7.
+   `properties/administratorLogin`: specificare l'account di accesso amministratore MySQL per il server. Il nome di accesso dell'amministratore non può essere azure_superuser, admin, administrator, root, guest o public.
+   `properties/administratorLoginPassword`: specificare la password per l'utente amministratore MySQL specificato in precedenza.
+   `properties/sslEnforcement`: specificare Enabled o Disabled per abilitare o disabilitare sslEnforcement.
+   `storageProfile/storageMB`: specificare la dimensione massima dello spazio di archiviazione di cui effettuare il provisioning per il server, in megabyte, ad esempio, 5120.
+   `storageProfile/backupRetentionDays`: specificare il periodo di conservazione dei backup desiderato, in giorni, ad esempio, 7. 
+   `storageProfile/geoRedundantBackup`: specificare Enabled o Disabled a seconda dei requisiti del ripristino di emergenza geografico.
+   `sku/tier`: specificare il livello Basic, GeneralPurpose o MemoryOptimized per la distribuzione.
+   `sku/capacity`: specificare la capacità vCore. Valori possibili: 2, 4, 8, 16, 32 o 64.
+   `sku/family`: specificare Gen5 per scegliere la generazione hardware per la distribuzione del server.
+   `sku/name`: specificare TierPrefix_family_capacity, ad esempio B_Gen5_1, GP_Gen5_16, MO_Gen5_32. Per comprendere i valori validi per area e livello, vedere la documentazione relativa ai [piani tariffari](./concepts-pricing-tiers.md).
+   `resources/properties/virtualNetworkSubnetId`: specificare l'identificatore Azure della subnet nella rete virtuale in cui deve essere inserito il server MySQL di Azure. 
+   `tags(optional)`: specificare tag facoltativi come coppie chiave-valore da usare per classificare, ad esempio, le risorse per la fatturazione.

Se si intende compilare un modello di Azure Resource Manager per automatizzare le distribuzioni di Database di Azure per MySQL per l'organizzazione, è consigliabile iniziare dal [modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) di esempio nella raccolta GitHub di avvio rapido per Azure e sviluppare tale modello. 

Se si vuole provare questo modello ma si è alle prime armi con i modelli di Azure Resource Manager, è possibile iniziare seguendo questa procedura:
+   Clonare o scaricare il [modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) di esempio dalla raccolta Avvio rapido di Azure.  
+   Modificare azuredeploy.parameters.json per aggiornare i valori dei parametri in base alle preferenze e salvare il file. 
+   Tramite l'interfaccia della riga di comando di Azure creare il server MySQL di Azure usando i comandi seguenti

È possibile usare Azure Cloud Shell nel browser o installare l'interfaccia della riga di comando di Azure nel computer in uso per eseguire i blocchi di codice di questa esercitazione.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l “West US2”
az group deployment create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
```

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione
Per connettersi al server, è necessario specificare le informazioni sull'host e le credenziali di accesso.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Il risultato è in formato JSON. Annotare il **fullyQualifiedDomainName** e l'**administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus2",
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

## <a name="connect-to-the-server-using-mysql"></a>Connettersi al server usando mysql
Usare lo [strumento da riga di comando di MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) per stabilire una connessione al server Database di Azure per MySQL. In questo esempio, il comando è:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Creazione di un database vuoto
Una volta connessi al server, creare un database vuoto.
```sql
mysql> CREATE DATABASE mysampledb;
```

Nel prompt, eseguire il comando seguente per cambiare la connessione nel database appena creato:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Creare tabelle nel database
Dopo aver appreso come connettersi al database di Database di Azure per MySQL, completare alcune attività di base.

In primo luogo, creare una tabella e caricarvi alcuni dati. Creare una tabella che contenga le informazioni riguardanti l'inventario.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Caricare i dati nelle tabelle
Dopo aver creato una tabella, inserire alcuni dati. Nella finestra del prompt dei comandi aperta, eseguire la query seguente per inserire alcune righe di dati.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

A questo punto, ci sono due righe di dati di esempio nella tabella creata in precedenza.

## <a name="query-and-update-the-data-in-the-tables"></a>Eseguire una query e aggiornare i dati nelle tabelle
Eseguire la query seguente per recuperare informazioni dalla tabella del database.
```sql
SELECT * FROM inventory;
```

Si possono anche aggiornare query e aggiornare i dati nelle tabelle.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

La riga viene aggiornata di conseguenza quando si recuperano dati.
```sql
SELECT * FROM inventory;
```

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:
> [!div class="checklist"]
> * Creare un server di Database di Azure per MySQL con un endpoint servizio rete virtuale usando un modello di Azure Resource Manager
> * Usare lo [strumento della riga di comando di MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> 
> [Come connettere le applicazioni a Database di Azure per MySQL](./howto-connection-string.md)
