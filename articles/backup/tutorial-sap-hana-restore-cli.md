---
title: Esercitazione - Ripristino del database SAP HANA in Azure tramite l'interfaccia della riga di comando
description: Questa esercitazione illustra come eseguire il ripristino di database SAP HANA in esecuzione in una macchina virtuale di Azure in un insieme di credenziali di Servizi di ripristino di Backup di Azure tramite l'interfaccia della riga di comando di Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75470406"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Esercitazione: Ripristinare i database SAP HANA in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure consente di creare e gestire le risorse di Azure dalla riga di comando o tramite script. Questa documentazione illustra come ripristinare un database SAP HANA sottoposto a backup in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure. È anche possibile eseguire questi passaggi tramite il [portale di Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore).

Usare [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) per eseguire i comandi dell'interfaccia della riga di comando.

Al termine di questa esercitazione, sarà possibile:

> [!div class="checklist"]
>
> * Visualizzare i punti di ripristino per un database sottoposto a backup
> * Ripristinare un database

Questa esercitazione presuppone che sia presente un database SAP HANA in esecuzione nella macchina virtuale di Azure di cui viene eseguito il backup con Backup di Azure. Se è stata usata l'esercitazione [Eseguire il backup di un database SAP HANA in Azure tramite l'interfaccia della riga di comando](tutorial-sap-hana-backup-cli.md) per eseguire il backup del database SAP HANA, sono in uso le risorse seguenti:

* un gruppo di risorse denominato *saphanaResourceGroup*
* un insieme di credenziali denominato *saphanaVault*
* contenitore protetto denominato *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*
* database/elemento sottoposto a backup denominato *saphanadatabase;hxe;hxe*
* risorse nell'area *westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Visualizzare i punti di ripristino per un database sottoposto a backup

Per visualizzare l'elenco di tutti i punti di ripristino per un database, usare il cmdlet [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) come indicato di seguito:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

L'elenco dei punti di ripristino avrà un aspetto simile al seguente:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Come si può notare, l'elenco precedente contiene tre punti di ripristino: uno per il backup completo, uno per il backup differenziale e uno per il backup del log.

>[!NOTE]
>È anche possibile visualizzare i punti di inizio e di fine di ogni catena di backup del log non interrotta usando il cmdlet [az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain).

## <a name="prerequisites-to-restore-a-database"></a>Prerequisiti per il ripristino di un database

Prima di ripristinare un database, verificare che siano soddisfatti i prerequisiti seguenti:

* È possibile ripristinare il database solo in un'istanza SAP HANA che risiede nella stessa area
* L'istanza di destinazione deve essere registrata con lo stesso insieme di credenziali dell'origine
* Backup di Azure non può identificare due istanze di SAP HANA diverse nella stessa macchina virtuale. Non è quindi possibile ripristinare i dati da un'istanza a un'altra nella stessa macchina virtuale.

## <a name="restore-a-database"></a>Ripristinare un database

Backup di Azure può ripristinare database SAP HANA in esecuzione nelle macchine virtuali di Azure seguendo questa procedura:

* Ripristinare una data specifica o un'ora (al secondo), usando i backup del log. In base ai tempi di ripristino specificati, Backup di Azure determina automaticamente i backup completi, differenziali e la catena di backup del log necessari per ripristinare in base all'ora selezionata.
* Ripristinare un backup completo o differenziale specifico per il ripristino a un punto di ripristino specifico.

Per ripristinare un database, usare il cmdlet [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl), che richiede un oggetto configurazione di ripristino come uno degli input. Questo oggetto può essere generato usando il cmdlet [az backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show). L'oggetto configurazione di ripristino contiene tutti i dettagli per eseguire un ripristino. Uno di essi è la modalità di ripristino, **OriginalWorkloadRestore** o **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore**: consente di ripristinare i dati nella stessa istanza di SAP HANA dell'origine. Questa opzione sovrascrive il database originale. <br>
> **AlternateWorkloadRestore**: consente di ripristinare il database in un percorso alternativo conservando il database di origine.

## <a name="restore-to-alternate-location"></a>Ripristinare in un percorso alternativo

Per ripristinare un database in un percorso alternativo, usare **AlternateWorkloadRestore** come modalità di ripristino. È quindi necessario scegliere il punto di ripristino, che può essere un punto nel tempo precedente o uno dei punti di ripristino precedenti.

In questa esercitazione verrà ripristinato un punto di ripristino precedente. [Visualizzare l'elenco dei punti di ripristino](#view-restore-points-for-a-backed-up-database) per il database e scegliere il punto in cui si desidera eseguire il ripristino. Questa esercitazione userà il punto di ripristino con il nome *7660777527047692711*.

Usando il nome del punto di ripristino e la modalità di ripristino precedenti, verrà creato l'oggetto configurazione di ripristino usando il cmdlet [az backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show). Verrà ora esaminato il significato di ognuno dei parametri rimanenti di questo cmdlet:

* **--target-item-name**: nome che verrà usato dal database ripristinato. In questo caso, è stato usato il nome *restored_database*.
* **--target-server-name**: nome di un server SAP HANA registrato in un insieme di credenziali di servizi di ripristino e che risiede nella stessa area del database da ripristinare. Per questa esercitazione, il database verrà ripristinato nello stesso server SAP HANA protetto, denominato *hxehost*.
* **--target-server-type**: per il ripristino dei database SAP HANA, è necessario usare **SapHanaDatabase**.

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

La risposta alla query precedente sarà un oggetto configurazione di ripristino con un aspetto simile al seguente:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

A questo punto, per ripristinare il database, eseguire il cmdlet [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl). Per usare questo comando, sarà necessario immettere l'output JSON precedente salvato in un file denominato *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

L'output dettagliato sarà simile al seguente:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

La risposta fornirà il nome del processo. Questo nome di processo può essere usato per tenere traccia dello stato del processo usando il cmdlet [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show).

## <a name="restore-and-overwrite"></a>Ripristinare e sovrascrivere

Per eseguire il ripristino nel percorso originale, verrà usata la modalità di ripristino **OriginalWorkloadRestore**. È quindi necessario scegliere il punto di ripristino, che può essere un punto nel tempo precedente o uno dei punti di ripristino precedenti.

Per questa esercitazione verrà scelto il ripristino temporizzato "28-11-2019-09:53:00". È possibile specificare questo punto di ripristino nei formati seguenti: gg-mm-aaaa, gg-mm-aaaa-hh: mm:ss. Per scegliere un ripristino temporizzato valido, usare il cmdlet [az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain), che elenca gli intervalli di backup della catena di log non interrotti.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

La risposta alla query precedente sarà un oggetto configurazione di ripristino con un aspetto simile al seguente:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

A questo punto, per ripristinare il database, eseguire il cmdlet [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl). Per usare questo comando, sarà necessario immettere l'output JSON precedente salvato in un file denominato *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

L'output dettagliato sarà simile al seguente:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

La risposta fornirà il nome del processo. Questo nome di processo può essere usato per tenere traccia dello stato del processo usando il cmdlet [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come gestire i database SAP HANA di cui viene eseguito il backup tramite l'interfaccia della riga di comando di Azure, continuare con l'esercitazione [Gestire un database SAP HANA in una macchina virtuale di Azure tramite l'interfaccia della riga di comando](tutorial-sap-hana-backup-cli.md)

* Per informazioni su come ripristinare un database SAP HANA in esecuzione in una macchina virtuale di Azure tramite il portale di Azure, vedere [Ripristinare un database SAP HANA nelle macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
