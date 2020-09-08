---
title: "Esercitazione: Gestire i backup dei database SAP HANA tramite l'interfaccia della riga di comando"
description: Questa esercitazione descrive come gestire i backup dei database SAP HANA in esecuzione in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3051eb9a6f0c395f8ec06d92d6501296ec39c768
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89007299"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Esercitazione: Gestire i database SAP HANA in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure consente di creare e gestire le risorse di Azure dalla riga di comando o tramite script. Questa documentazione illustra come gestire il backup di un database SAP HANA in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure. È anche possibile eseguire questi passaggi tramite il [portale di Azure](./sap-hana-db-manage.md).

Usare [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) per eseguire i comandi dell'interfaccia della riga di comando.

Al termine di questa esercitazione, sarà possibile:

> [!div class="checklist"]
>
> * Monitorare i processi di backup e ripristino
> * Proteggere i nuovi database aggiunti a un'istanza di SAP HANA
> * Modificare i criteri
> * Arresta protezione
> * Riprendere la protezione

Se è stata usata l'esercitazione [Eseguire il backup di un database SAP HANA in Azure tramite l'interfaccia della riga di comando](tutorial-sap-hana-backup-cli.md) per eseguire il backup del database SAP HANA, sono in uso le risorse seguenti:

* un gruppo di risorse denominato *saphanaResourceGroup*
* un insieme di credenziali denominato *saphanaVault*
* contenitore protetto denominato *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*
* database/elemento sottoposto a backup denominato *saphanadatabase;hxe;hxe*
* risorse nell'area *westus2*

L'interfaccia della riga di comando di Azure semplifica la gestione di un database SAP HANA in esecuzione in una macchina virtuale di Azure di cui viene eseguito il backup con Backup di Azure. Questa esercitazione descrive in modo dettagliato tutte le operazioni di gestione.

## <a name="monitor-backup-and-restore-jobs"></a>Monitorare i processi di backup e ripristino

Per monitorare i processi completati o attualmente in esecuzione (backup o ripristino), usare il cmdlet [az backup job list](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list). L'interfaccia della riga di comando consente inoltre di [sospendere un processo attualmente in esecuzione](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) o [attendere il completamento di un processo](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait).

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

Verrà visualizzato un risultato simile al seguente:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Modificare i criteri

Per modificare i criteri sottostanti la configurazione di backup di SAP HANA, usare il cmdlet [az backup policy set](/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-set). Il parametro Name in questo cmdlet fa riferimento all'elemento di backup di cui si vogliono modificare i criteri. Per questa esercitazione verrà sostituito il criterio del database SAP HANA *saphanadatabase;hxe;hxe* con un nuovo criterio *newsaphanaPolicy*. È possibile creare nuovi criteri usando il cmdlet [az backup policy create](/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create).

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

L'output dovrebbe essere simile al seguente:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Proteggere i nuovi database aggiunti a un'istanza di SAP HANA

[La registrazione di un'istanza di SAP HANA con un insieme di credenziali di Servizi di ripristino](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) comporta l'individuazione automatica di tutti i database in questa istanza.

Tuttavia, nei casi in cui vengono aggiunti nuovi database all'istanza di SAP HANA in un secondo momento, usare il cmdlet [az backup protectable-item initialize](/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-initialize). Questo cmdlet individua i nuovi database aggiunti.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Usare quindi il cmdlet [az backup protectable-item list](/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) per elencare tutti i database che sono stati individuati nell'istanza di SAP HANA. Questo elenco esclude tuttavia i database in cui è già stato configurato il backup. Una volta individuato il database di cui eseguire il backup, vedere [Abilitare il backup nel database SAP HANA](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database).

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Il nuovo database di cui si vuole eseguire il backup sarà incluso in questo elenco che avrà un aspetto simile al seguente:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Arrestare la protezione per un database SAP HANA

È possibile arrestare la protezione di un database SAP HANA in due modi:

* Interrompere tutti i processi di backup futuri ed eliminare tutti i punti di recupero.
* Arrestare tutti i processi di backup futuri mantenendo tuttavia invariati i punti di ripristino.

Se si sceglie di lasciare invariati i punti di ripristino, tenere presente quanto segue:

* Tutti i punti di ripristino rimarranno invariati per sempre e tutte le eliminazioni verranno arrestate in caso di arresto della protezione con conservazione dei dati.
* Verranno addebitati i costi per l'istanza protetta e lo spazio di archiviazione utilizzato.
* Se si elimina un'origine dati senza arrestare i backup, i nuovi backup avranno esito negativo.

Di seguito vengono esaminati più in dettaglio tutti i modi per arrestare la protezione.

### <a name="stop-protection-with-retain-data"></a>Arrestare la protezione conservando i dati

Per arrestare la protezione con conservazione dei dati, usare il cmdlet [az backup protection disable](/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable).

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

L'output dovrebbe essere simile al seguente:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Per controllare lo stato di questa operazione, usare il cmdlet [az backup job show](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show).

### <a name="stop-protection-without-retain-data"></a>Arrestare la protezione senza conservazione dei dati

Per arrestare la protezione senza conservazione dei dati, usare il cmdlet [az backup protection disable](/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable).

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

L'output dovrebbe essere simile al seguente:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Per controllare lo stato di questa operazione, usare il cmdlet [az backup job show](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show).

## <a name="resume-protection"></a>Riprendere la protezione

Quando si arresta la protezione per il database SAP HANA con conservazione dei dati, è possibile riprendere la protezione in un secondo momento. Se non si conservano i dati di backup, non sarà possibile riprendere la protezione.

Per riprendere la protezione, usare il cmdlet [az backup protection resume](/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume).

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

L'output dovrebbe essere simile al seguente:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Per controllare lo stato di questa operazione, usare il cmdlet [az backup job show](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come eseguire il backup di un database SAP HANA in esecuzione in una macchina virtuale di Azure tramite il portale di Azure, vedere [Eseguire il backup di database SAP HANA nelle macchine virtuali di Azure](./backup-azure-sap-hana-database.md)

* Per informazioni su come gestire il backup di un database SAP HANA in esecuzione in una macchina virtuale di Azure tramite il portale di Azure, vedere [Gestire i backup dei database SAP HANA nelle macchine virtuali di Azure](./sap-hana-db-manage.md)
