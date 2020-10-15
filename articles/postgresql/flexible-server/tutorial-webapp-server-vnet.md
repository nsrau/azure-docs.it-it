---
title: "Esercitazione: Creare un server flessibile di Database di Azure per PostgreSQL e un'app Web del servizio app di Azure nella stessa rete virtuale"
description: Guida di avvio rapido alla creazione di un server flessibile di Database di Azure per PostgreSQL con un'app Web in una rete virtuale
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 20401a3c96a9a20399c07d1a30370d27f2858e29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90929521"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>Esercitazione: Creare un server flessibile di Database di Azure per PostgreSQL con un'app Web del servizio app in una rete virtuale

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Questa esercitazione illustra come creare un'app Web del servizio app di Azure con il server flessibile di Database di Azure per PostgreSQL (anteprima) all'interno di una [rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

In questa esercitazione si apprenderà come
>[!div class="checklist"]
> * Creare un server flessibile PostgreSQL in una rete virtuale
> * Creare un'app Web
> * Aggiungere l'app Web alla rete virtuale
> * Connettersi a Postgres dall'app Web 

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Per questo articolo è necessario eseguire in locale l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Per vedere la versione installata, eseguire il comando `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

È necessario accedere all'account con il comando [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in). Si noti la proprietà **id** dell'output del comando per il nome della sottoscrizione corrispondente.

```azurecli
az login
```

Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Selezionare l'ID sottoscrizione specifico sotto l'account tramite il comando [az account set](/cli/azure/account). Sostituire il segnaposto "subscription ID" con la proprietà **subscription ID** dell'output di **az login** per la sottoscrizione.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Creare un server flessibile PostgreSQL in una nuova rete virtuale

Creare un server flessibile privato all'interno di una rete virtuale con il comando seguente:
```azurecli
az postgres flexible-server create --resource-group myresourcegroup --location westus2
```
Questo comando esegue le azioni seguenti, che possono richiedere alcuni minuti:

- Creare il gruppo di risorse se non esiste già.
- Genera un nome di server, se non specificato.
- Crea una nuova rete virtuale per il nuovo server PostgreSQL. Prendere nota del nome della rete virtuale e del nome della subnet creati per il server perché sarà necessario aggiungere l'app Web alla stessa rete virtuale.
- Crea il nome utente e la password dell'amministratore per il server, se non specificato.
- Crea un database vuoto denominato **postgres**

> [!NOTE]
> - Prendere nota della password che verrà generata se non è stata specificata. Se si dimentica la password, è necessario reimpostarla usando il comando ``` az postgres flexible-server update```
> - Se non si usa un ambiente del servizio app, è necessario autorizzare l'accesso da qualsiasi indirizzo IP di Azure usando questo comando. 
>  ```azurecli
>  az postgres flexible-server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
>  ```


## <a name="create-a-web-app"></a>Creare un'app Web
In questa sezione si crea l'host delle app nell'app del Servizio app, si connette questa app al database Postgres e quindi si distribuisce il codice in tale host. Nel terminale assicurarsi di trovarsi nella radice del repository del codice dell'applicazione.

Creare un'app del servizio app (processo host) con il comando az webapp up

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku B1 --name mywebapp
```

> [!NOTE]
> - Per l'argomento --location, usare la stessa posizione specificata per il database nella sezione precedente.
> - Sostituire <app-name> con un nome univoco in tutto Azure (l'endpoint server è https://\<app-name>.azurewebsites.net). I caratteri consentiti per <app-name> sono A-Z, 0-9 e -. Un criterio valido consiste nell'usare una combinazione del nome della società e di un identificatore dell'app.

Questo comando esegue le azioni seguenti, che possono richiedere alcuni minuti:

- Creare il gruppo di risorse se non esiste già. In questo comando viene usato lo stesso gruppo di risorse in cui in precedenza è stato creato il database.
- Creare il piano di servizio app ```testappserviceplan``` nel piano tariffario Basic (B1), se non esiste. --plan e --sku sono facoltativi.
- Crea l'app del Servizio app se non esiste.
- Abilita la registrazione predefinita per l'app, se non è già abilitata.
- Carica il repository usando la distribuzione ZIP con l'automazione della compilazione abilitata.

## <a name="add-the-web-app-to-the-virtual-network"></a>Aggiungere l'app Web alla rete virtuale
Usare il comando **az webapp vnet-integration** per aggiungere l'integrazione di una rete virtuale di area all'app Web. Sostituire <vnet-name> e <subnet-name> con i nomi della rete virtuale e della subnet usati dal server flessibile.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Configurare le variabili di ambiente per la connessione del database
Con il codice ora distribuito nel servizio app, il passaggio successivo consiste nel connettere l'app al server flessibile in Azure. Il codice dell'app prevede di trovare informazioni sul database in una serie di variabili di ambiente. Per impostare le variabili di ambiente nel servizio app, è necessario creare "impostazioni dell'app" usando il comando ```az webapp config appsettings``` set.

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```


- Sostituire ```postgres-server-name```, ```username``` e ```password``` con i valori per il comando del nuovo server flessibile creato.
- Sostituire <username> e <password> con le credenziali generate anch'esse automaticamente dal comando.
- Il gruppo di risorse e il nome dell'app vengono ricavate a partire dai valori memorizzati nella cache nel file .azure/config.
- Il comando crea impostazioni denominate ```DBHOST```, ```DBNAME```, ```DBUSER``` e ```DBPASS```. Se il codice dell'applicazione usa nomi diversi per le informazioni del database, usare questi nomi per le impostazioni dell'app come indicato nel codice.

## <a name="clean-up-resources"></a>Pulire le risorse

Pulire tutte le risorse create nell'esercitazione con il comando seguente. Questo comando elimina tutte le risorse incluse in questo gruppo di risorse.

```azurecli
az group delete -n myresourcegroup
```


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire il mapping di un nome DNS personalizzato esistente al Servizio app di Azure](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)