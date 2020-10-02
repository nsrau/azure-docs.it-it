---
title: Backup/ripristino completo e ripristino selettivo per il modulo di protezione hardware gestito di Azure
description: Questo documento illustra come eseguire il backup/ripristino completo e il ripristino selettivo
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 3d999375d746bb359acdccf9bf48f8b77d509776
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90992475"
---
# <a name="full-backup-and-restore"></a>Backup e ripristino completo

> [!NOTE]
> Questa funzionalità è disponibile solo per il modulo di protezione hardware gestito del tipo di risorsa.

Il modulo di protezione hardware gestito supporta la creazione di un backup completo dell'intero contenuto del modulo di protezione hardware, incluse tutte le chiavi, le versioni, gli attributi, i tag e le assegnazioni di ruolo. Il backup viene crittografato con le chiavi di crittografia associate al dominio di sicurezza del modulo di protezione hardware.

Il backup è un'operazione del piano dati. Il chiamante che avvia l'operazione di backup deve avere l'autorizzazione per eseguire l'azione dati **Microsoft.KeyVault/managedHsm/backup/start/action**.

Solo i ruoli predefiniti seguenti sono autorizzati a eseguire il backup completo:
- Managed HSM Administrator
- Managed HSM Backup

Per eseguire un backup completo, è necessario specificare le informazioni seguenti:
- Nome o URL del modulo di protezione hardware
- Nome account di archiviazione
- Contenitore di archiviazione BLOB dell'account di archiviazione
- Token di firma di accesso condiviso del contenitore di archiviazione con autorizzazioni `crdw`

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="full-backup"></a>Backup completo

Il backup è un'operazione a esecuzione prolungata ma restituirà immediatamente un ID processo, che potrà essere usato per controllare lo stato del processo di backup. Il processo di backup crea all'interno del contenitore designato una cartella il cui nome è conforme al formato di denominazione **`mhsm-{HSM_NAME}-{YYYY}{MM}{DD}{HH}{mm}{SS}`** , in cui HSM_NAME è il nome del modulo di protezione hardware gestito di cui viene eseguito il backup e YYYY, MM, DD, HH, MM, mm, SS sono rispettivamente l'anno, il mese, il giorno, l'ora, i minuti e i secondi in formato ora UTC in cui è stato ricevuto il comando di backup.

Durante l'esecuzione del backup, il modulo di protezione hardware potrebbe non funzionare alla velocità effettiva, perché alcune partizioni del modulo di protezione hardware sono occupate con l'esecuzione dell'operazione di backup.

```azurecli-interactive
# time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions crdw --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Backup HSM

az keyvault backup start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --subscription 361da5d4-a47a-4c79-afdd-d66f684f4070
```

## <a name="full-restore"></a>Ripristino completo

Il ripristino completo consente di ripristinare completamente l'intero contenuto del modulo di protezione hardware con un backup precedente, incluse tutte le chiavi, le versioni, gli attributi, i tag e le assegnazioni di ruolo. Tutto il contenuto attualmente archiviato nel modulo di protezione hardware verrà cancellato e verrà ripristinato allo stato in cui si trovava quando è stato creato il backup di origine.

> [!IMPORTANT]
> Il ripristino completo è un'operazione estremamente distruttiva. È quindi obbligatorio aver completato un backup completo entro gli ultimi 30 minuti prima di poter eseguire un'operazione `restore`.

Il ripristino è un'operazione del piano dati. Il chiamante che avvia l'operazione di ripristino deve avere l'autorizzazione per eseguire l'azione dati **Microsoft.KeyVault/managedHsm/restore/start/action**. Il modulo di protezione hardware di origine in cui è stato creato il backup e il modulo di protezione hardware di destinazione in cui verrà eseguito il ripristino **devono** avere lo stesso dominio di sicurezza. Per altre informazioni, vedere [Dominio di sicurezza del modulo di protezione hardware gestito](security-domain.md).

Per eseguire un ripristino completo, è necessario specificare le informazioni seguenti:
- Nome o URL del modulo di protezione hardware
- Nome account di archiviazione
- Contenitore BLOB dell'account di archiviazione
- Token di firma di accesso condiviso del contenitore di archiviazione con autorizzazioni `rl`
- Nome della cartella del contenitore di archiviazione in cui è archiviato il backup di origine

Il ripristino è un'operazione a esecuzione prolungata ma restituirà immediatamente un ID processo, che potrà essere usato per controllare lo stato del processo di ripristino. Durante il processo di ripristino, il modulo di protezione hardware passa alla modalità di ripristino e tutti i comandi del piano dati (eccetto quello per il controllo dello stato del ripristino) sono disabilitati.

```azurecli-interactive
#### time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions rl --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)
```

## <a name="backup-hsm"></a>Eseguire il backup del modulo di protezione hardware

```
az keyvault restore start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-mhsmdemo-2020083120161860
```

## <a name="next-steps"></a>Passaggi successivi
- Vedere [Gestire un modulo di protezione hardware gestito con l'interfaccia della riga di comando di Azure](key-management.md).
- Per altre informazioni, vedere [Dominio di sicurezza del modulo di protezione hardware gestito](security-domain.md)