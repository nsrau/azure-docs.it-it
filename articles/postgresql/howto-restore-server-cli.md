---
title: Come eseguire il backup e ripristino di un server nel Database di Azure per PostgreSQL - Server singolo
description: Informazioni su come eseguire il backup e ripristino di un server nel Database di Azure per PostgreSQL - singolo Server tramite la CLI di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 85fb00ad221ae982e4d3ddc9d2d5d20dd4f2793d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65069089"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Come eseguire il backup e ripristino di un server nel Database di Azure per PostgreSQL - singolo Server tramite la CLI di Azure

## <a name="backup-happens-automatically"></a>Il backup viene eseguito automaticamente
Il backup dei server Database di Azure per PostgreSQL viene eseguito periodicamente per abilitare le funzionalità di ripristino. L'uso di questa funzionalità consente di ripristinare il server e tutti i suoi database a un momento precedente nel nuovo server.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [server e un database di Database di Azure per PostgreSQL](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Questa guida dettagliata richiede l'uso dell'interfaccia della riga di comando di Azure 2.0 o versioni successive. Per verificare la versione, al prompt dei comandi dell'interfaccia della riga di comando di Azure immettere `az --version`. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Impostare la configurazione del backup

La decisione riguardo alla configurazione del server per il backup con ridondanza locale o il backup con ridondanza geografica viene presa al momento della creazione del server. 

> [!NOTE]
> Dopo aver creato il server, il tipo di ridondanza (locale o geografica) non può essere modificato.
>

Durante la creazione di un server tramite il comando `az postgres server create`, il parametro `--geo-redundant-backup` definisce l'opzione di ridondanza del backup. Se il valore è `Enabled`, vengono eseguiti backup con ridondanza geografica. Se invece il valore è `Disabled`, vengono eseguiti backup con ridondanza locale. 

Il periodo di conservazione dei backup è specificato dal parametro `--backup-retention-days`. 

Per altre informazioni sull'impostazione di questi valori durante la creazione, vedere [Guida introduttiva all'interfaccia della riga di comando del server Database di Azure per PostgreSQL](quickstart-create-server-database-azure-cli.md).

È possibile modificare il periodo di conservazione dei backup di un server nel modo seguente:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

L'esempio precedente modifica il periodo di conservazione dei backup di mydemoserver impostandolo su 10 giorni.

Il periodo di conservazione dei backup determina quanto è possibile tornare indietro nel tempo con un ripristino temporizzato, essendo il ripristino basato sui backup disponibili. Il ripristino temporizzato è descritto in modo più dettagliato nella sezione seguente.

## <a name="server-point-in-time-restore"></a>Ripristino temporizzato del server
È possibile ripristinare il server in base a una temporizzazione precedente. I dati ripristinati vengono copiati in un nuovo server e il server esistente viene lasciato invariato. Se, ad esempio, una tabella è stata involontariamente eliminata a mezzogiorno di oggi, è possibile eseguire il ripristino a un qualsiasi momento prima di mezzogiorno. È possibile quindi recuperare la tabella e i dati mancanti dalla copia ripristinata del server. 

Per ripristinare il server usare il comando [az postgres server restore](/cli/azure/postgres/server) dell'interfaccia della riga di comando di Azure.

### <a name="run-the-restore-command"></a>Eseguire il comando di ripristino

Per ripristinare il server, al prompt dei comandi dell'interfaccia della riga di comando di Azure immettere il comando seguente:

```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

Il comando `az postgres server restore` richiede i parametri seguenti:

| Impostazione | Valore consigliato | Descrizione  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Il gruppo di risorse in cui si trova il server di origine.  |
| name | mydemoserver-restored | Il nome del nuovo server creato con il comando di ripristino. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Selezionare un punto nel tempo per il ripristino. La data e l'ora devono trovarsi all'interno del periodo di memorizzazione dei backup del server di origine. Usare il formato ISO8601 per la data e l'ora. È possibile usare il proprio fuso orario locale, ad esempio `2018-03-13T05:59:00-08:00`. È anche possibile usare il formato UTC Zulu, ad esempio `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | Il nome o l'ID del server di origine da cui eseguire il ripristino. |

Quando si ripristina un server a un punto precedente nel tempo, viene creato un nuovo server. Il server originale e i database dal punto nel punto specificato vengono copiati nel nuovo server.

I valori relativi al percorso e al piano tariffario per il server ripristinato sono gli stessi del server di origine. 

Al termine del ripristino, individuare il nuovo server creato per verificare che il ripristino dei dati sia avvenuto come previsto.

Il nuovo server creato durante un ripristino non dispone delle regole del firewall presenti nel server originale. Per questo nuovo server, le regole del firewall devono essere impostate separatamente.

## <a name="geo-restore"></a>Ripristino geografico
Se il server è stato configurato per backup con ridondanza geografica, è possibile creare un nuovo server dal backup di quel server esistente. Questo nuovo server può essere creato in qualsiasi area in cui è disponibile Database di Azure per PostgreSQL.  

Per creare un server tramite un backup con ridondanza geografica, usare il comando `az postgres server georestore` dell'interfaccia della riga di comando di Azure.

> [!NOTE]
> Quando un server viene creato per la prima volta, potrebbe non essere subito disponibile per il ripristino geografico. Potrebbero essere necessarie alcune ore per popolare i metadati necessari.
>

Per eseguire un ripristino geografico del server, al prompt dei comandi dell'interfaccia della riga di comando di Azure immettere il comando seguente:

```azurecli-interactive
az postgres server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Questo comando crea un nuovo server denominato *mydemoserver-georestored* negli Stati Uniti orientali che apparterrà a *myresourcegroup*. Si tratta di un server per utilizzo generico di quarta generazione con otto vCore. Il server viene creato dal backup con ridondanza geografica di *mydemoserver*, che si trova anch'esso nel gruppo di risorse *myresourcegroup*.

Se si vuole creare il nuovo server in un gruppo di risorse diverso dal server esistente, nel parametro `--source-server` è necessario specificare il nome del server come nell'esempio seguente:

```azurecli-interactive
az postgres server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

Il comando `az postgres server georestore` richiede i parametri seguenti:

| Impostazione | Valore consigliato | Descrizione  |
| --- | --- | --- |
|resource-group| myresourcegroup | Nome del gruppo di risorse cui apparterrà il nuovo server.|
|name | mydemoserver-georestored | Nome del nuovo server. |
|source-server | mydemoserver | Nome del server esistente di cui vengono usati i backup con ridondanza geografica. |
|location | eastus | Posizione del nuovo server. |
|sku-name| GP_Gen4_8 | Questo parametro imposta il piano tariffario, la generazione delle risorse di calcolo e il numero di vCore del nuovo server. GP_Gen4_8 indica un server per utilizzo generico di quarta generazione con otto vCore.|


>[!Important]
>Quando si crea un nuovo server tramite un ripristino geografico, il server eredita le stesse dimensioni di archiviazione e lo stesso piano tariffario del server di origine. Questi valori non possono essere modificati durante la creazione. Dopo aver creato il nuovo server, le dimensioni di archiviazione possono essere aumentate.

Al termine del ripristino, individuare il nuovo server creato per verificare che il ripristino dei dati sia avvenuto come previsto.

Il nuovo server creato durante un ripristino non dispone delle regole del firewall presenti nel server originale. Per questo nuovo server, le regole del firewall devono essere impostate separatamente.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sui [backup](concepts-backup.md) del servizio.
- Altre informazioni sulle opzioni di [continuità aziendale](concepts-business-continuity.md).
