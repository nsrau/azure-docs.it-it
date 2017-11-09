---
title: Crittografare i dischi di una macchina virtuale Linux | Documentazione Microsoft
description: Come crittografare i dischi virtuali in una VM di Linux per una maggiore sicurezza tramite l'interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/05/2017
ms.author: iainfou
ms.openlocfilehash: 172b4c8f5c098d776cb689543f5d8f163b8895b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-encrypt-virtual-disks-on-a-linux-vm"></a>Come crittografare i dischi virtuali in una VM di Linux
Per migliorare gli aspetti di sicurezza e conformità delle macchine virtuali (VM), i dischi virtuali in Azure possono essere crittografati. I dischi vengono crittografati usando chiavi di crittografia protette in un insieme di credenziali delle chiavi di Azure. È possibile controllare queste chiavi di crittografia e il loro uso. Questo articolo descrive come crittografare i dischi virtuali in una VM di Linux tramite l'interfaccia della riga di comando di Azure 2.0. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](encrypt-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-commands"></a>Comandi rapidi
Se si necessita di eseguire rapidamente l'attività, nella sezione seguente sono illustrati i comandi di base per crittografare i dischi rigidi della VM. Altre informazioni dettagliate e il contesto per ogni passaggio sono disponibili nelle sezioni successive del documento, [a partire da qui](#overview-of-disk-encryption).

È necessario aver installato l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure tramite il comando [az login](/cli/azure/#login). Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *myKey* e *myVM*.

Per prima cosa, abilitare il provider dell'insieme di credenziali delle chiavi di Azure nella sottoscrizione di Azure con [az provider register](/cli/azure/provider#register) e creare un gruppo di risorse con [az group create](/cli/azure/group#create). L'esempio seguente crea un nome del gruppo di risorse *myResourceGroup* nella località *stati uniti orientali*:

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

Creare un insieme di credenziali delle chiavi di Azure con [az keyvault create](/cli/azure/keyvault#create) e abilitare l'insieme di credenziali delle chiavi per l'utilizzo con crittografia del disco. Specificare un nome univoco di Key Vault per *keyvault_name* come indicato di seguito:

```azurecli
keyvault_name=mykeyvaultikf
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Creare una chiave di crittografia nell'insieme di credenziali delle chiavi con [az keyvault key create](/cli/azure/keyvault/key#create). Nell'esempio seguente viene creata una chiave denominata *myKey*:

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```

Creare un'entità servizio usando Azure Active Directory con [az ad sp creare-per-rbac](/cli/azure/ad/sp#create-for-rbac). L'entità servizio gestisce l'autenticazione e lo scambio di chiavi di crittografia dall'insieme di credenziali delle chiavi. Nell'esempio seguente vengono letti i valori per l'ID e la password dell'entità servizio per l'uso nei comandi successivi:

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

La password viene restituita solo quando si crea l'entità servizio. Se lo si desidera, visualizzare e registrare la password (`echo $sp_password`). È possibile elencare le entità servizio con [az ad sp list](/cli/azure/ad/sp#list) e visualizzare informazioni aggiuntive su un'entità servizio specifica con [az ad sp show](/cli/azure/ad/sp#show).

Impostare le autorizzazioni per l'insieme di credenziali delle chiavi con [az keyvault set-policy](/cli/azure/keyvault#set-policy). Nell'esempio seguente l'ID dell'entità servizio viene fornita dal comando precedente:

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
    --key-permissions wrapKey \
    --secret-permissions set
```

Creare una macchina virtuale con [az vm create](/cli/azure/vm#create) e collegare un disco dati di 5 Gb. Solo determinate immagini di marketplace supportano la crittografia del disco. Nell'esempio seguente viene creata una VM denominata `myVM` usando un'immagine **CentOS 7.2n**:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image OpenLogic:CentOS:7.2n:7.2.20160629 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

SSH per la macchina virtuale tramite il valore `publicIpAddress` indicato nell'output dal comando precedente. Creare una partizione e un file system, quindi montare il disco dati. Per ulteriori informazioni, vedere [Connect to a Linux VM to mount the new disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk) (Connettersi a una VM Linux per montare il nuovo disco). Chiudere la sessione SSH.

Crittografare la macchina virtuale con [az vm encryption enable](/cli/azure/vm/encryption#enable). Nell'esempio seguente vengono usate le variabili `$sp_id` e `$sp_password` dal comando precedente `ad sp create-for-rbac`:

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Il completamento del processo di crittografia del disco richiede qualche istante. Monitorare lo stato del processo con [z vm encryption show](/cli/azure/vm/encryption#show):

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Lo stato mostra **EncryptionInProgress**. Attendere che lo stato per il sistema operativo del disco indichi **VMRestartPending**, quindi riavviare la macchina virtuale con [az vm restart](/cli/azure/vm#restart):

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

Il processo di crittografia del disco è finalizzato durante il processo di avvio, pertanto, attendere alcuni minuti prima di verificare di nuovo lo stato della crittografia con **az vm encryption show**:

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Lo stato dovrebbe ora segnalare sia il disco del sistema operativo che il disco dati come **Crittografato**.

## <a name="overview-of-disk-encryption"></a>Panoramica della crittografia dei dischi
I dischi virtuali delle VM Linux vengono crittografati a riposo mediante [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Non è previsto alcun addebito per la crittografia dei dischi virtuali in Azure. Le chiavi di crittografia vengono archiviate nell'insieme di credenziali delle chiavi di Azure che usano la protezione del software oppure è possibile importare o generare le chiavi in moduli di protezione hardware certificati per gli standard FIPS 140-2 di livello 2. È possibile esercitare il controllo su queste chiavi di crittografia e sul loro uso. Queste chiavi di crittografia vengono usate per crittografare e decrittografare i dischi virtuali collegati alla VM. Un'entità servizio di Azure Active Directory offre un meccanismo protetto per il rilascio delle chiavi di crittografia all'accensione e allo spegnimento delle VM.

Il processo per la crittografia di una VM si articola nel modo seguente:

1. Creare una chiave di crittografia in un insieme di credenziali delle chiavi di Azure.
2. Configurare la chiave di crittografia in modo da renderla utilizzabile per la crittografia dei dischi.
3. Per leggere la chiave di crittografia dall'insieme di credenziali delle chiavi di Azure, creare un'entità servizio di Azure Active Directory con le autorizzazioni appropriate.
4. Eseguire il comando per crittografare i dischi virtuali, specificando l'entità servizio di Azure Active Directory e la chiave di crittografia appropriata da usare.
5. L'entità servizio di Azure Active Directory richiede la chiave di crittografia necessaria dall'insieme di credenziali delle chiavi di Azure.
6. I dischi virtuali vengono crittografati tramite la chiave di crittografia fornita.

## <a name="encryption-process"></a>Processo di crittografia
La crittografia del disco si basa sui componenti aggiuntivi seguenti:

* **Insieme di credenziali delle chiavi di Azure**, che consente di proteggere le chiavi crittografiche e private usate per il processo di crittografia/decrittografia dei dischi.
  * Se disponibile, è possibile usare un insieme di credenziali delle chiavi di Azure esistente. Non è necessario dedicare un insieme di credenziali delle chiavi alla crittografia dei dischi.
  * Per separare i limiti amministrativi e la visibilità delle chiavi, è possibile creare un insieme di credenziali delle chiavi dedicato.
* **Azure Active Directory** gestisce lo scambio protetto delle chiavi di crittografia necessarie e dell'autenticazione per le azioni richieste.
  * In genere, è possibile inserire l'applicazione in un'istanza esistente di Azure Active Directory.
  * L'entità servizio offre un meccanismo protetto per richiedere e consentire il rilascio delle chiavi di crittografia appropriate. Non si sta procedendo a sviluppare un'applicazione reale integrata con Azure Active Directory.

## <a name="requirements-and-limitations"></a>Requisiti e limitazioni
Requisiti relativi alla crittografia dei dischi e scenari supportati:

* I seguenti SKU dei server Linux: Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES) e Red Hat Enterprise Linux.
* Tutte le risorse, come l'insieme di credenziali delle chiavi, l'account di archiviazione e la VM, devono risiedere nella stessa area e nella stessa sottoscrizione di Azure.
* VM standard delle serie A, D, DS, G e GS.

La crittografia del disco non è attualmente supportata negli scenari seguenti:

* VM di base.
* VM create con il modello di distribuzione classica.
* Disabilitare la crittografia del disco del sistema operativo nelle VM Linux.
* Aggiornare le chiavi di crittografia in una VM Linux già crittografata.

## <a name="create-azure-key-vault-and-keys"></a>Creare le chiavi e l'insieme di credenziali delle chiavi di Azure
È necessario aver installato l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure tramite il comando [az login](/cli/azure/#login). Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *myKey* e *myVM*.

Il primo passaggio consiste nel creare un insieme di credenziali delle chiavi di Azure in cui archiviare le chiavi di crittografia. L'insieme di credenziali delle chiavi di Azure consente di archiviare chiavi, chiavi private o password da implementare in tutta sicurezza in applicazioni e servizi. Per la crittografia del disco virtuale, usare l'insieme di credenziali delle chiavi per archiviare una chiave di crittografia usata per crittografare o decrittografare i dischi virtuali.

Abilitare il provider dell'insieme di credenziali delle chiavi di Azure nella sottoscrizione di Azure con [az provider register](/cli/azure/provider#register) e creare un gruppo di risorse con [az group create](/cli/azure/group#create). L'esempio seguente crea un nome del gruppo di risorse *myResourceGroup* nella posizione `eastus`:

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

L'insieme di credenziali delle chiavi di Azure contenente le chiavi di crittografia e le risorse di calcolo associate, come l'archiviazione e la VM stessa, devono risiedere nella stessa area. Creare un insieme di credenziali delle chiavi di Azure con [az keyvault create](/cli/azure/keyvault#create) e abilitare l'insieme di credenziali delle chiavi per l'utilizzo con crittografia del disco. Specificare un nome univoco di Key Vault per *keyvault_name* come indicato di seguito:

```azurecli
keyvault_name=myUniqueKeyVaultName
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

È possibile archiviare le chiavi di crittografia usando il software o il modulo di protezione hardware. L'uso di un modulo di protezione hardware richiede un insieme di credenziali delle chiavi premium. Esiste un costo aggiuntivo per creare un insieme di credenziali delle chiavi premium, anziché standard, in cui archiviare le chiavi protette tramite software. Per creare un insieme di credenziali delle chiavi premium, aggiungere `--sku Premium` al comando del passaggio precedente. L'esempio seguente usa chiavi protette tramite software, poiché viene creato un insieme di credenziali delle chiavi standard.

Per entrambi i modelli di protezione, è necessario consentire alla piattaforma Azure di accedere all'avvio della VM per richiedere le chiavi di crittografia con cui decrittografare i dischi virtuali. Creare una chiave di crittografia nell'insieme di credenziali delle chiavi con [az keyvault key create](/cli/azure/keyvault/key#create). Nell'esempio seguente viene creata una chiave denominata *myKey*:

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-the-azure-active-directory-service-principal"></a>Creare un'entità servizio di Azure Active Directory
Al momento di crittografare o decrittografare i dischi virtuali, specificare un account per gestisce l'autenticazione e lo scambio delle chiavi di crittografia dall'insieme di credenziali delle chiavi. Tale account, un'entità servizio di Azure Active Directory, consente alla piattaforma Azure di richiedere le chiavi di crittografia appropriate per conto della VM. Un'istanza di Azure Active Directory predefinita è già disponibile nella sottoscrizione, anche se molte organizzazioni hanno directory di Azure Active Directory dedicate.

Creare un'entità servizio usando Azure Active Directory con [az ad sp creare-per-rbac](/cli/azure/ad/sp#create-for-rbac). Nell'esempio seguente vengono letti i valori per l'ID e la password dell'entità servizio per l'uso nei comandi successivi:

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

La password viene visualizzata solo quando si crea l'entità servizio. Se lo si desidera, visualizzare e registrare la password (`echo $sp_password`). È possibile elencare le entità servizio con [az ad sp list](/cli/azure/ad/sp#list) e visualizzare informazioni aggiuntive su un'entità servizio specifica con [az ad sp show](/cli/azure/ad/sp#show).

Per crittografare o decrittografare correttamente i dischi virtuali, le autorizzazioni per la chiave di crittografia archiviata nell'insieme di credenziali delle chiavi devono essere impostate in modo tale da consentire all'entità servizio di Azure Active Directory di leggere le chiavi. Impostare le autorizzazioni per l'insieme di credenziali delle chiavi con [az keyvault set-policy](/cli/azure/keyvault#set-policy). Nell'esempio seguente l'ID dell'entità servizio viene fornita dal comando precedente:

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions wrapKey \
  --secret-permissions set
```


## <a name="create-virtual-machine"></a>Crea macchina virtuale
Per crittografare alcuni dischi virtuali, creare una VM e aggiungere un disco dati. Creare una macchina virtuale da crittografare con [az vm create](/cli/azure/vm#create) e collegare un disco dati di 5 Gb. Solo determinate immagini di marketplace supportano la crittografia del disco. Nell'esempio seguente viene creata una VM denominata *myVM* tramite un'immagine di **CentOS 7.2n**:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image OpenLogic:CentOS:7.2n:7.2.20160629 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

SSH per la macchina virtuale tramite il valore `publicIpAddress` indicato nell'output dal comando precedente. Creare una partizione e un file system, quindi montare il disco dati. Per ulteriori informazioni, vedere [Connect to a Linux VM to mount the new disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk) (Connettersi a una VM Linux per montare il nuovo disco). Chiudere la sessione SSH.


## <a name="encrypt-virtual-machine"></a>Crittografare una macchina virtuale
Per crittografare i dischi virtuali, è necessario riunire tutti i componenti precedenti:

1. Specificare un'entità servizio e la password di Azure Active Directory.
2. Specificare l'insieme di credenziali delle chiavi in cui memorizzare i metadati dei dischi crittografati.
3. Specificare le chiavi di crittografia da usare per la crittografia e la decrittografia effettive.
4. Specificare se si desidera crittografare il disco del sistema operativo, i dischi dati o tutti i dischi.

Crittografare la macchina virtuale con [az vm encryption enable](/cli/azure/vm/encryption#enable). Nell'esempio seguente vengono usate le variabili `$sp_id` e `$sp_password` dal comando precedente `ad sp create-for-rbac`:

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Il completamento del processo di crittografia del disco richiede qualche istante. Monitorare lo stato del processo con [z vm encryption show](/cli/azure/vm/encryption#show):

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

L'output è simile all'esempio troncato seguente:

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress",
]
```

Attendere che lo stato per il sistema operativo del disco indichi **VMRestartPending**, quindi riavviare la macchina virtuale con [az vm restart](/cli/azure/vm#restart):

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

Il processo di crittografia del disco è finalizzato durante il processo di avvio, pertanto, attendere alcuni minuti prima di verificare di nuovo lo stato della crittografia con **az vm encryption show**:

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Lo stato dovrebbe ora segnalare sia il disco del sistema operativo che il disco dati come **Crittografato**.


## <a name="add-additional-data-disks"></a>Aggiungere ulteriori dischi dati
Una volta crittografati i dischi dati, in un secondo momento è possibile aggiungere alla VM ulteriori dischi virtuali e anche crittografarli. Ad esempio, consente di aggiungere alla VM un secondo disco virtuale, come di seguito:

```azurecli
az vm disk attach-new --resource-group myResourceGroup --vm-name myVM --size-in-gb 5
```

Eseguire nuovamente il comando per crittografare i dischi virtuali come indicato di seguito:

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```


## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla gestione dell'insieme di credenziali delle chiavi di Azure, tra cui come eliminare chiavi crittografiche e insiemi di credenziali, vedere [Gestire l'insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando](../../key-vault/key-vault-manage-with-cli2.md).
* Per altre informazioni sulla crittografia del disco, tra cui come preparare una VM personalizzata con crittografia da caricare in Azure, vedere [Crittografia dischi di Azure](../../security/azure-security-disk-encryption.md).
