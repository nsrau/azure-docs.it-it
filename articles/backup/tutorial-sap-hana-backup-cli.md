---
title: Esercitazione - Backup del database SAP HANA in Azure tramite l'interfaccia della riga di comando
description: Questa esercitazione illustra come eseguire il backup di database SAP HANA in esecuzione nelle macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino di Backup di Azure tramite l'interfaccia della riga di comando di Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f11e01c6af18cac956d58b9c692d7b57c8fe653a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324961"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Esercitazione: Eseguire il backup di database SAP HANA in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure consente di creare e gestire le risorse di Azure dalla riga di comando o tramite script. Questa documentazione descrive come eseguire il backup di un database SAP HANA e attivare i backup su richiesta usando l'interfaccia della riga di comando di Azure. È anche possibile eseguire questi passaggi tramite il [portale di Azure](./backup-azure-sap-hana-database.md).

Questo documento presuppone che sia già presente un database SAP HANA installato in una macchina virtuale di Azure. È anche possibile [creare una macchina virtuale usando l'interfaccia della riga di comando di Azure](../virtual-machines/linux/quick-create-cli.md). Al termine di questa esercitazione, sarà possibile:

> [!div class="checklist"]
>
> * Creare un insieme di credenziali di Servizi di ripristino
> * Registrare un'istanza di SAP HANA e individuare i database al suo interno
> * Abilitare il backup in un database SAP HANA
> * Attivare un backup su richiesta

Vedere gli [scenari attualmente supportati](./sap-hana-backup-support-matrix.md#scenario-support) per SAP HANA.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per installare e usare l'interfaccia della riga di comando in locale, è necessario eseguire l'interfaccia della riga di comando di Azure xx.xxx.x o versioni successive. Per determinare la versione dell'interfaccia della riga di comando, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un insieme di credenziali di Servizi di ripristino è un contenitore logico in cui vengono archiviati i dati di backup per ogni risorsa protetta, ad esempio le macchine virtuali di Azure, o i carichi di lavoro in esecuzione nelle macchine virtuali di Azure, ad esempio i database SQL o HANA. Quando viene eseguito, il processo di backup per una risorsa protetta crea un punto di ripristino all'interno dell'insieme di credenziali dei servizi di ripristino. È quindi possibile usare uno di questi punti di ripristino per ripristinare i dati a un dato momento.

Creare un insieme di credenziali dei servizi di ripristino con [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create). Specificare lo stesso gruppo di risorse e la stessa località della VM da proteggere. Per informazioni su come creare una macchina virtuale tramite l'interfaccia della riga di comando di Azure vedere questa guida di [avvio rapido per le macchine virtuali](../virtual-machines/linux/quick-create-cli.md).

Per questa esercitazione, si userà:

* un gruppo di risorse denominato *saphanaResourceGroup*
* una macchina virtuale denominata *saphanaVM*
* risorse nella località *westus2*.

Verrà creato un insieme di credenziali denominato *saphanaVault*.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

Per impostazione predefinita, l'insieme di credenziali di Servizi di ripristino è impostato per l'archiviazione con ridondanza geografica. Con l'archiviazione con ridondanza geografica ci si assicura che i dati di backup vengono replicati in un'area di Azure secondaria a centinaia di chilometri di distanza dall'area primaria. Se è necessario modificare l'impostazione di ridondanza dell'archiviazione, usare il cmdlet [az backup vault backup-properties set](/cli/azure/backup/vault/backup-properties#az-backup-vault-backup-properties-set).

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Per verificare che l'insieme di credenziali sia stato creato correttamente, usare il cmdlet [az backup vault list](/cli/azure/backup/vault#az-backup-vault-list). Verrà visualizzata la risposta seguente:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Registrare e proteggere l'istanza di SAP HANA

Per la corretta individuazione dell'istanza di SAP HANA, ovvero la macchina virtuale in cui è installato SAP HANA, da parte dei servizi di Azure, è necessario eseguire uno [script di registrazione preliminare](https://aka.ms/scriptforpermsonhana) nel computer SAP HANA. Prima di eseguire lo script, verificare che siano soddisfatti tutti i [prerequisiti](./tutorial-backup-sap-hana-db.md#prerequisites). Per altre informazioni sulle funzionalità deullo script, vedere la sezione [Funzionalità dello script di pre-registrazione](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

Una volta eseguito lo script, l'istanza di SAP HANA può essere registrata con l'insieme di credenziali di Servizi di ripristino creato in precedenza. Per registrare l'istanza, usare il cmdlet [az backup container register](/cli/azure/backup/container#az-backup-container-register). *VMResourceId* è l'ID risorsa della macchina virtuale creata per installare SAP HANA.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Se la macchina virtuale non risiede nello stesso gruppo di risorse dell'insieme di credenziali, *saphanaResourceGroup* fa riferimento al gruppo di risorse in cui è stato creato l'insieme di credenziali.

La registrazione dell'istanza di SAP HANA attiva l'individuazione automatica tutti i database correnti al suo interno. Tuttavia, per individuare i nuovi database che possono essere aggiunti in futuro, vedere la sezione [Individuazione dei nuovi database aggiunti all'istanza di SAP HANA registrata](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance).

Per verificare se l'istanza di SAP HANA è stata registrata correttamente nell'insieme di credenziali, usare il cmdlet [az backup container list](/cli/azure/backup/container#az-backup-container-list). Verrà visualizzata la risposta seguente:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> La colonna "Name" nell'output precedente fa riferimento al nome del contenitore. Questo nome di contenitore verrà usato nelle sezioni successive per abilitare i backup e attivarli. Il nome in questo caso è *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Abilitare il backup nel database SAP HANA

Il cmdlet [az backup protectable-item list](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) elenca tutti i database individuati nell'istanza di SAP HANA registrata nel passaggio precedente.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Il database di cui si vuole eseguire il backup sarà incluso in questo elenco che avrà un aspetto simile al seguente:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Come si può notare dall'output precedente, il SID del sistema SAP HANA è HXE. In questa esercitazione verrà configurato il backup per il database *saphanadatabase;hxe;hxe* che risiede nel server *hxehost*.

Per proteggere e configurare il backup in un database alla volta, si userà il cmdlet [az backup protection enable-for-azurewl](/cli/azure/backup/protection#az-backup-protection-enable-for-azurewl). Specificare il nome del criterio da usare. Per creare un criterio con l'interfaccia della riga di comando, usare il cmdlet [az backup policy create](/cli/azure/backup/policy#az-backup-policy-create). Per questa esercitazione verrà usato il criterio *sapahanaPolicy*.

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

È possibile verificare se la configurazione di backup precedente è stata completata usando il cmdlet [az backup job list](/cli/azure/backup/job#az-backup-job-list). L'output verrà visualizzato come segue:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

Il cmdlet [az backup job list](/cli/azure/backup/job#az-backup-job-list) elenca tutti i processi di backup (pianificati o su richiesta) che sono stati eseguiti o sono attualmente in esecuzione nel database protetto, oltre ad altre operazioni come la registrazione, la configurazione del backup e l'eliminazione dei dati di backup.

>[!NOTE]
>Backup di Azure non si adatta automaticamente al cambiamento dell'ora legale per il backup di un database SAP HANA in esecuzione in una VM di Azure.
>
>Modificare manualmente i criteri in base alle esigenze.

## <a name="trigger-an-on-demand-backup"></a>Attivare un backup su richiesta

Mentre nella sezione precedente è stato illustrato come configurare un backup pianificato, in questa sezione verrà descritto come attivare un backup su richiesta. A tale scopo, verrà usato il cmdlet [az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now).

>[!NOTE]
> I criteri di conservazione di un backup su richiesta sono determinati dai criteri di conservazione sottostanti per il database.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

L'output verrà visualizzato come segue:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

La risposta fornirà il nome del processo. Questo nome di processo può essere usato per tenere traccia dello stato del processo usando il cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

>[!NOTE]
>Oltre pianificare un backup completo o differenziale, è anche possibile attivare il backup manualmente. I backup del log vengono attivati e gestiti automaticamente da SAP HANA internamente.
>
> I backup incrementali non sono attualmente supportati da Backup di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come ripristinare un database SAP HANA in una macchina virtuale di Azure tramite l'interfaccia della riga di comando, continuare con l'esercitazione [Ripristinare un database SAP HANA in una macchina virtuale di Azure tramite l'interfaccia della riga di comando](tutorial-sap-hana-restore-cli.md)

* Per informazioni su come eseguire il backup di un database SAP HANA in esecuzione in una macchina virtuale di Azure tramite il portale di Azure, vedere [Eseguire il backup di database SAP HANA nelle macchine virtuali di Azure](./backup-azure-sap-hana-database.md)
