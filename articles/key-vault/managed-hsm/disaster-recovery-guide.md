---
title: Operazioni da eseguire in caso di interruzione di un servizio Azure con impatto sul modulo di protezione hardware gestito - Azure Key Vault | Microsoft Docs
description: Informazioni sulle operazioni da eseguire in caso di interruzione di un servizio Azure con impatto sul modulo di protezione hardware gestito.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 08c1b415ac075429a9bc89098233fffb8c25b710
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369257"
---
# <a name="managed-hsm-disaster-recovery"></a>Ripristino di emergenza del modulo di protezione hardware gestito

È opportuno creare una replica esatta del modulo di protezione hardware se l'originale è andato perso o risulta non disponibile a causa di uno dei motivi seguenti:

- È stato eliminato e quindi rimosso definitivamente.
- Si è verificato un errore irreversibile nell'area che ha comportato l'eliminazione definitiva di tutte le partizioni membro.

È possibile ricreare l'istanza del modulo di protezione hardware nella stessa area o in un'area diversa se sono disponibili gli elementi seguenti:
- [Dominio di sicurezza](security-domain.md) del modulo di protezione hardware di origine.
- Chiavi private (almeno il numero del quorum) che crittografano il dominio di sicurezza.
- [Backup](backup-restore.md) completo e più recente del modulo di protezione hardware di origine.

Ecco la procedura da seguire per il ripristino di emergenza:

1. Creare una nuova istanza del modulo di protezione hardware.
1. Attivare il ripristino del dominio di sicurezza. Verrà generata una nuova coppia di chiavi RSA (chiave di scambio del dominio di sicurezza) per il trasferimento del dominio di sicurezza. Tale coppia di chiavi verrà inviata nella risposta e verrà scaricata come elemento SecurityDomainExchangeKey (chiave pubblica).
1. Creare e caricare il file di trasferimento del dominio di sicurezza. Saranno necessarie le chiavi private che crittografano il dominio di sicurezza. Le chiavi private vengono usate in locale e non vengono mai trasferite in nessuna parte di questo processo.
1. Eseguire un backup del nuovo modulo di protezione hardware. È necessario eseguire un backup prima di qualsiasi ripristino, anche quando il modulo di protezione hardware è vuoto. I backup consentono di eseguire facilmente il rollback.
1. Ripristinare il backup recente del modulo di protezione hardware dal modulo di protezione hardware di origine

I contenuti dell'insieme di credenziali delle chiavi vengono replicati all'interno dell'area e in un'area secondaria distante almeno 250 chilometri, ma all'interno della stessa area geografica. Questa funzionalità garantisce un'elevata durabilità delle chiavi e dei segreti. Per informazioni dettagliate su coppie di aree specifiche, vedere il documento [Aree abbinate di Azure](../../best-practices-availability-paired-regions.md).

## <a name="create-a-new-managed-hsm"></a>Creare un nuovo modulo di protezione hardware gestito

Usare il comando `az keyvault create` per creare un modulo di protezione hardware gestito. Questo script ha tre parametri obbligatori: nome del gruppo di risorse, nome del modulo di protezione hardware e località geografica.

Per creare una risorsa del modulo di protezione hardware gestito, è necessario fornire gli input seguenti:

- Nome del modulo di protezione hardware.
- Gruppo di risorse in cui verrà inserito il modulo nella sottoscrizione.
- Località di Azure.
- Elenco di amministratori iniziali.

L'esempio seguente crea un modulo di protezione hardware denominato **ContosoMHSM** nel gruppo di risorse **ContosoResourceGroup** , nella località **Stati Uniti orientali 2** e con l' **utente connesso corrente** come unico amministratore.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> L'esecuzione del comando di creazione può richiedere alcuni minuti. Al termine, è possibile attivare il modulo di protezione hardware.

L'output di questo comando mostra le proprietà del modulo di protezione hardware gestito creato. Le due proprietà più importanti sono:

* **name** : nell'esempio il nome è ContosoMHSM. Questo nome verrà usato per altri comandi di Key Vault.
* **hsmUri** : nell'esempio l'URI è 'https://contosohsm.managedhsm.azure.net '. Le applicazioni che usano il modulo di protezione hardware tramite l'API REST devono usare questo URI.

L'account Azure ora è autorizzato a eseguire qualsiasi operazione su questo modulo di protezione hardware gestito. Per ora, nessun altro è autorizzato.

## <a name="activate-the-security-domain-recovery-mode"></a>Attivare la modalità di ripristino del dominio di sicurezza

In un processo di creazione normale a questo punto viene inizializzato e scaricato un nuovo dominio di sicurezza. Tuttavia, dal momento che viene eseguita una procedura di ripristino di emergenza, il modulo di protezione hardware deve passare alla modalità di ripristino del dominio di sicurezza e scaricare una chiave di scambio del dominio di sicurezza. La chiave di scambio del dominio di sicurezza è una chiave pubblica RSA che verrà usata per crittografare il dominio di sicurezza prima di caricarlo nel modulo di protezione hardware. La chiave privata corrispondente è protetta all'interno del modulo di protezione hardware, per salvaguardare il contenuto del dominio di sicurezza durante il trasferimento.

```azurecli-interactive
az keyvault security-domain init-recovery --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM2-SDE.cer
```

## <a name="upload-security-domain-to-destination-hsm"></a>Caricare il dominio di sicurezza nel modulo di protezione hardware di destinazione

Per questo passaggio sono necessari gli elementi seguenti:
- Chiave di scambio del dominio di sicurezza scaricata nel passaggio precedente.
- Dominio di sicurezza del modulo di protezione hardware di origine.
- Almeno il numero del quorum delle chiavi private usate per crittografare il dominio di sicurezza.

Il comando `az keyvault security-domain upload` esegue le operazioni seguenti:

- Decrittografa il dominio di sicurezza del modulo di protezione hardware di origine con le chiavi private fornite. 
- Crea un BLOB di caricamento del dominio di sicurezza crittografato con la chiave di scambio del dominio di sicurezza scaricata nel passaggio precedente.
- Carica il BLOB di caricamento del dominio di sicurezza nel modulo di protezione hardware per completare il ripristino del dominio di sicurezza.

Nell'esempio seguente viene usato il dominio di sicurezza di **ContosoMHSM** , la numero 2 delle chiavi private corrispondenti, caricandolo quindi in **ContosoMHSM2** , che è in attesa di ricevere un dominio di sicurezza. 

```azurecli-interactive
az keyvault security-domain upload --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM-SDE.cer --sd-file ContosoMHSM-SD.json --sd-wrapping-keys cert_0.key cert_1.key
```

A questo punto sia il modulo di protezione hardware di origine (ContosoMHSM) che il modulo di protezione hardware di destinazione (ContosoMHSM2) hanno lo stesso dominio di sicurezza. È ora possibile ripristinare un backup completo dal modulo di protezione hardware di origine nel modulo di protezione hardware di destinazione.

## <a name="create-a-backup-as-a-restore-point-of-your-new-hsm"></a>Creare un backup (come punto di ripristino) del nuovo modulo di protezione hardware

Prima di eseguire un ripristino completo del modulo di protezione hardware, è sempre consigliabile eseguire un backup completo, in modo da avere un punto di ripristino in caso di problemi con il ripristino.

Per creare un backup del modulo di protezione hardware, sono necessari gli elementi seguenti:
- Un account di archiviazione in cui verrà archiviato il backup
- Un contenitore di archiviazione BLOB in questo account di archiviazione in cui il processo di backup creerà una nuova cartella per l'archiviazione del backup crittografato

Nell'esempio seguente verrà usato il comando `az keyvault backup` per il backup del modulo di protezione hardware nel contenitore di archiviazione **mhsmbackupcontainer** che si trova nell'account di archiviazione **ContosoBackup**. Viene creato un token di firma di accesso condiviso che scade dopo 30 minuti e viene fornito a un modulo di protezione hardware gestito per scrivere il backup.

```azurecli-interactive
end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
az storage container create --account-name  mhsmdemobackup --name mhsmbackupcontainer  --account-key $skey
sas=$(az storage container generate-sas -n mhsmbackupcontainer --account-name ContosoBackup --permissions crdw --expiry $end --account-key $skey -o tsv)
az keyvault backup start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas

```

## <a name="restore-backup-from-source-hsm"></a>Ripristinare il backup del modulo di protezione hardware di origine

Per questo passaggio sono necessari gli elementi seguenti:

- L'account di archiviazione e il contenitore BLOB in cui sono archiviati i backup del modulo di protezione hardware di origine.
- Il nome della cartella da cui ripristinare il backup. Se si creano backup regolari, nel contenitore saranno presenti numerose cartelle.


```azurecli-interactive
end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name ContosoBackup --permissions rl --expiry $end --account-key $skey -o tsv)
az keyvault restore start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-ContosoMHSM-2020083120161860
```

A questo punto è stato completato un processo di ripristino di emergenza completo. Il contenuto del modulo di protezione hardware di origine relativo a quando è stato effettuato il backup viene copiato nel modulo di protezione hardware di destinazione, inclusi tutti i codici, le versioni, gli attributi, i tag e le assegnazioni di ruolo.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sul dominio di sicurezza, vedere [Informazioni sul dominio di sicurezza del modulo di protezione hardware gestito](security-domain.md)
- Vedere le [procedure consigliate per il modulo di protezione hardware gestito](best-practices.md)
