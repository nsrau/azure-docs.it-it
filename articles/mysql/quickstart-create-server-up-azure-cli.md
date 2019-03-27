---
title: "Avvio rapido: Creare un'istanza di Database di Azure per MySQL con un semplice comando dell'interfaccia della riga di comando di Azure - az mysql up (anteprima)"
description: Guida di avvio rapido per creare un server Database di Azure per MySQL con un comando dell'interfaccia della riga di comando di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2019
ms.custom: mvc
ms.openlocfilehash: aa0d2a9e990faa8d99355744824f34e26aeb519e
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137913"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Avvio rapido: Creare un'istanza di Database di Azure per MySQL con un semplice comando dell'interfaccia della riga di comando di Azure - az mysql up (anteprima)

> [!IMPORTANT]
> Il comando [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) dell'interfaccia della riga di comando di Azure è disponibile in anteprima.

Il database di Azure per MySQL è un servizio gestito che consente di eseguire, gestire e scalare dei database MySQL a disponibilità elevata nel cloud. L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questo argomento di avvio rapido illustra come usare il comando [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) per creare un server Database di Azure per MySQL con l'interfaccia della riga di comando di Azure. Oltre a creare il server, il comando `az mysql up` crea un database di esempio e un utente ROOT nel database, apre il firewall per i servizi di Azure e crea regole del firewall predefinite per il computer client. In questo modo il processo di sviluppo risulta più rapido.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Per questo articolo è necessario eseguire in locale l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Per vedere la versione installata, eseguire il comando `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

È necessario accedere all'account con il comando [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in). Si noti la proprietà **id** dell'output del comando per il nome della sottoscrizione corrispondente.

```azurecli
az login
```

Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Selezionare l'ID sottoscrizione specifico sotto l'account tramite il comando [az account set](/cli/azure/account). Sostituire il segnaposto "subscription id" con la proprietà **subscription id** dell'output di **az login** per la sottoscrizione.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Creare un server Database di Azure per MySQL

Per usare i comandi, installare l'estensione [db-up](/cli/azure/ext/db-up). Se viene restituito un errore, verificare di aver installato l'ultima versione dell'interfaccia della riga di comando di Azure. Vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Per creare un server Database di Azure per MySQL, usare il comando seguente:

```azurecli
az mysql up
```

Il server viene creato con i valori predefiniti seguenti, a meno che non si sostituiscano manualmente:

**Impostazione** | **Valore predefinito** | **Descrizione**
---|---|---
server-name | Generata dal sistema | Nome univoco che identifica il database di Azure per il server MySQL.
resource-group | Generata dal sistema | Nuovo gruppo di risorse di Azure.
sku-name | GP_Gen5_2 | Il nome dello SKU. Segue la convenzione {piano tariffario}\_{generazione di calcolo}\_{Vcore} in sintassi abbreviata. L'impostazione predefinita è un server per utilizzo generico di quinta generazione con 2 vCore. Per altre informazioni sui livelli, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/mysql/).
backup-retention | 7 | Specifica per quanto tempo deve essere conservato un backup. L'unità è giorni.
geo-redundant-backup | Disabled | Indica se abilitare i backup con ridondanza geografica per questo server.
location | westus2 | Località di Azure per il server.
ssl-enforcement | Disabled | Indica se abilitare SSL per questo server.
storage-size | 5120 | Capacità di archiviazione del server (l'unità è MB).
version | 5.7 | La versione principale di MySQL.
admin-user | Generata dal sistema | Nome utente per l'account di accesso dell'amministratore.
admin-password | Generata dal sistema | Password dell'utente amministratore.

> [!NOTE]
> Per altre informazioni sul comando `az mysql up` e i relativi parametri aggiuntivi, vedere la [documentazione dell'interfaccia della riga di comando di Azure](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

Dopo la creazione, il server presenta le impostazioni seguenti:

- Viene creata una regola del firewall denominata "devbox". L'interfaccia della riga di comando di Azure prova a rilevare l'indirizzo IP del computer da cui viene eseguito il comando `az mysql up` e inserisce tale indirizzo IP nell'elenco elementi consentiti.
- Viene attivata l'opzione "Consenti l'accesso a Servizi di Azure". Questa impostazione configura il firewall del server in modo da accettare connessioni da tutte le risorse di Azure, incluse le risorse non incluse nella sottoscrizione dell'utente.
- Il parametro `wait_timeout` è impostato su 8 ore
- Viene creato un database vuoto denominato "sampledb".
- Viene creato un nuovo utente denominato "root" con i privilegi per "sampledb".

> [!NOTE]
> Database di Azure per MySQL comunica sulla porta 3306. Quando si esegue la connessione da una rete aziendale, il traffico in uscita sulla porta 3306 potrebbe non essere consentito dal firewall della rete. Richiedere al reparto IT di aprire la porta 3306 per la connessione al server.

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Al termine del comando `az mysql up`, viene restituito un elenco di stringhe di connessione per i linguaggi di programmazione più diffusi. Queste stringhe di connessione sono preconfigurate con gli attributi specifici del server Database di Azure per MySQL appena creato.

È possibile visualizzare di nuovo l'elenco di queste stringhe di connessione usando il comando [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string).

## <a name="clean-up-resources"></a>Pulire le risorse

Eseguire la pulizia di tutte le risorse create nell'avvio rapido con il comando seguente. Questo comando elimina il server Database di Azure per MySQL e il gruppo di risorse.

```azurecli
az mysql down --delete-group
```

Se si vuole eliminare solo il server appena creato, è possibile eseguire [az mysql down](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down).

```azurecli
az mysql down
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Progettare un database MySQL con l'interfaccia della riga di comando di Azure](./tutorial-design-database-using-cli.md)
