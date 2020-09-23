---
title: Eseguire il backup e il ripristino per i gruppi di server di scalabilità di database
description: Eseguire il backup e il ripristino per i gruppi di server di scalabilità di database
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d300f3e02d2a1a83410d5b7d981298a4743fb223
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939786"
---
# <a name="backup-and-restore-for-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Backup e ripristino per i gruppi di server con iperscalabilità PostgreSQL abilitati per Azure Arc

È possibile eseguire il backup completo o il ripristino del gruppo di server di scalabilità PostgreSQL abilitato per Azure Arc. Quando si esegue questa operazione, viene eseguito il backup e/o il ripristino dell'intero set di database in tutti i nodi del gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc.
Per eseguire un backup e ripristinarlo, è necessario assicurarsi che sia configurata una classe di archiviazione di backup per il gruppo di server. Per il momento, è necessario indicare una classe di archiviazione di backup al momento della creazione del gruppo di server. Non è ancora possibile configurare il gruppo di server per l'uso di una classe di archiviazione di backup dopo che è stata creata.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!CAUTION]
> L'anteprima non supporta il backup/ripristino per la versione 11 del motore postgres. Supporta solo backup/ripristino per Postgres versione 12.

## <a name="verify-configuration"></a>Verificare la configurazione

Verificare innanzitutto se il gruppo di server esistente è stato configurato per l'utilizzo della classe di archiviazione di backup.

Dopo aver impostato il nome del gruppo di server, eseguire il comando seguente:
```console
 azdata arc postgres server show -n postgres01
```
Esaminare la sezione relativa all'archiviazione dell'output:
```console
...
"storage": {
      "backups": {
        "className": "local-storage"
      },
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
...
```
Se viene visualizzata una sezione "backups", significa che il gruppo di server è stato configurato per l'uso di una classe di archiviazione di backup ed è pronto per eseguire i backup e i ripristini. Se non viene visualizzata una sezione "backup", è necessario eliminare e ricreare il gruppo di server per configurare la classe di archiviazione di backup. A questo punto, non è ancora possibile configurare una classe di archiviazione di backup dopo la creazione del gruppo di server.

>[!IMPORTANT]
>Se il gruppo di server è già configurato per l'uso di una classe di archiviazione di backup, ignorare il passaggio successivo e passare direttamente al passaggio "Esegui backup manuale completo".

## <a name="create-a-server-group"></a>Crea un gruppo di server 

Successivamente, creare un gruppo di server configurato per il backup o il ripristino.

Per poter eseguire i backup e ripristinarli, è necessario creare un server configurato con una classe di archiviazione.

Per ottenere un elenco delle classi di archiviazione disponibili nel cluster Kubernetes, eseguire il comando seguente:

```console
kubectl get sc
```

<!--The general format of create server group command is documented [here](create-postgresql-instances.md)-->

```console
azdata arc postgres server create -n <name> --workers 2 --storage-class-backups <storage class name> [--storage-class-data <storage class name>] [--storage-class-logs <storage class name>]
```

Ad esempio, se è stato creato un ambiente semplice basato su kubeadm:
```console
azdata arc postgres server create -n postgres01 --workers 2 --storage-class-backups local-storage
```

## <a name="take-manual-full-backup"></a>Esegui backup completo manuale

Eseguire quindi un backup manuale completo.

Per eseguire un backup completo dell'intera cartella di dati e log del gruppo di server, eseguire il comando seguente:

```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Dove:
- __nome__ indica il nome di un backup
- __nome-server__ indica un gruppo di server
- __No-wait__ indica che la riga di comando non attende il completamento del backup per poter continuare a usare questa finestra della riga di comando

>**Nota**: il comando che consente di elencare i backup disponibili per il ripristino non viene ancora visualizzato, ovvero la data/ora in cui è stato creato il backup. È quindi consigliabile assegnare un nome al backup (usando il parametro--Name) che include le informazioni relative a data e ora.

Questo comando consente di coordinare un backup completo distribuito in tutti i nodi che costituiscono il gruppo di server di scalabilità PostgreSQL abilitato per Azure Arc. In altre parole, eseguirà il backup di tutti i dati nel coordinatore e nei nodi di lavoro.

Ad esempio:
```console
azdata arc postgres backup create --name MyBackup_Aug31_0730amPST --server-name postgres01
```

Al termine del backup, verranno restituiti l'ID, il nome e lo stato del backup. Ad esempio:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "name": "MyBackup_Aug31_0730amPS",
  "state": "Done"
}
```

> [!NOTE]
> Non è ancora possibile:
> - Pianificare backup automatici
> - Mostra lo stato di avanzamento di un backup mentre viene eseguito

## <a name="list-backups"></a>Elencare i backup

Elencare i backup disponibili per il ripristino.

Per elencare i backup disponibili per il ripristino, eseguire il comando seguente:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Ad esempio:
```console
azdata arc postgres backup list --server-name postgres01
```

Verrà restituito un output simile al seguente:
```console
ID                                Name                      State
--------------------------------  ------------------------  -------
d134f51aa87f4044b5fb07cf95cf797f  MyBackup_Aug31_0730amPST  Done
```

## <a name="restore-a-backup"></a>Ripristinare un backup

Per ripristinare il backup di un intero gruppo di server, eseguire il comando:

```console
azdata arc postgres backup restore --server-name <server group name> --backup-id <backup id>
```

Dove:
- __backup-ID__ è l'ID del backup visualizzato nel comando list backup (fare riferimento al passaggio 3).
Verrà coordinata un'operazione di ripristino completo distribuito in tutti i nodi che costituiscono il gruppo di server di scalabilità PostgreSQL abilitato per Azure Arc. In altre parole, ripristinerà tutti i dati nel coordinatore e nei nodi di lavoro.

Ad esempio:
```console
azdata arc postgres backup restore --server-name postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Al termine dell'operazione di ripristino, verrà restituito un output simile al seguente alla riga di comando:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```
> [!NOTE]
> Non è ancora possibile:
> - Ripristinare un backup indicando il nome
> - Ripristinare un gruppo di server con un nome diverso o in un gruppo di server diverso
> - Mostra lo stato di avanzamento di un'operazione di ripristino

## <a name="delete-backups"></a>Eliminare i backup
Impossibile impostare la conservazione dei backup in anteprima. Tuttavia, è possibile eliminare manualmente i backup che non sono necessari.
Il comando generale per eliminare i backup è:
```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```
dove:
- `--server-name` nome del gruppo di server da cui l'utente desidera eliminare un backup
- `--name` nome del backup da eliminare
- `-id`ID del backup da eliminare

> [!NOTE]
> `--name` e si escludono a `-id` vicenda.

È possibile recuperare il nome e l'ID dei backup eseguendo il comando list backup come illustrato nel paragrafo precedente.

Si consideri, ad esempio, che sono elencati i backup seguenti:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
5b0481dfc1c94b4cac79dd56a1bb21f4  MyBackup091720200110am  Done
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```
e si desidera eliminare il primo, eseguire il comando seguente:
```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```
Per elencare i backup a questo punto, si otterrebbe l'output seguente:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```

Per ulteriori informazioni sul comando DELETE, eseguire:
```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sul [ridimensionamento (aggiunta di nodi di lavoro)](scale-out-postgresql-hyperscale-server-group.md) al gruppo di server
- Per informazioni su come [aumentare o ridurre la scalabilità (aumento/riduzione della memoria/VCore)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) del gruppo di server
