---
title: Crittografare dischi in una macchina virtuale Linux con l'interfaccia della riga di comando di Azure 1.0 | Documentazione Microsoft
description: Come crittografare i dischi di una macchina virtuale Linux tramite l'interfaccia della riga di comando di Azure 1.0 e il modello di distribuzione di Resource Manager
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2017
ms.author: iainfou
ms.openlocfilehash: b436f2d43c41000f4385889edb3fa3983d4a8c66
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli-10"></a>Crittografare i dischi di una VM Linux usando l'interfaccia della riga di comando di Azure 1.0
Per migliorare gli aspetti di sicurezza e conformità delle macchine virtuali (VM), i dischi virtuali in Azure possono essere crittografati a riposo. I dischi vengono crittografati usando chiavi di crittografia protette in un insieme di credenziali delle chiavi di Azure. È possibile controllare queste chiavi di crittografia e il loro uso. In questo articolo viene spiegato come crittografare i dischi virtuali di una VM Linux tramite l'interfaccia della riga di comando di Azure 1.0 e il modello di distribuzione di Resource Manager.

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](#quick-commands): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa

## <a name="quick-commands"></a>Comandi rapidi
Se si necessita di eseguire rapidamente l'attività, nella sezione seguente sono illustrati i comandi di base per crittografare i dischi rigidi della VM. Altre informazioni dettagliate e il contesto per ogni passaggio sono disponibili nelle sezioni successive del documento, [a partire da qui](#overview-of-disk-encryption).

È necessario installare e registrare l'[interfaccia della riga di comando Azure 1.0 più recente](../../xplat-cli-install.md) usando la modalità di Resource Manager nel modo seguente:

```azurecli
azure config mode arm
```

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. Alcuni esempi di nomi dei parametri sono `myResourceGroup`, `myKeyVault` e `myVM`.

Per prima cosa, abilitare il provider dell'insieme di credenziali delle chiavi di Azure nella sottoscrizione di Azure e creare un gruppo di risorse. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella posizione `WestUS`:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Creare un insieme di credenziali delle chiavi di Azure. Nell'esempio seguente viene creato un insieme di credenziali delle chiavi denominato `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Creare una chiave di crittografia nell'insieme di credenziali delle chiavi e abilitarlo per la crittografia del disco. Nell'esempio seguente viene creata una chiave denominata `myKey`:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Creare un endpoint tramite Azure Active Directory per gestire l'autenticazione e lo scambio di chiavi di crittografia dall'insieme di credenziali delle chiavi. `--home-page` e `--identifier-uris` non devono necessariamente essere indirizzi instradabili effettivi. Per la massima sicurezza, è preferibile usare i segreti client, anziché le password. Tuttavia, al momento i segreti client non possono essere generati tramite l'interfaccia della riga di comando di Azure, ma solo nel portale di Azure. Nell'esempio seguente viene creato un endpoint di Azure Active Directory denominato `myAADApp` con la password `myPassword`. Specificare una password personalizzata come di seguito:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Si noti il valore `applicationId` indicato nell'output dal comando precedente. Questo ID applicazione è usato nei passaggi seguenti:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Aggiungere un disco dati a una VM esistente. Nell'esempio seguente viene aggiunto un disco dati a una VM denominata `myVM`:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Esaminare i dettagli dell'insieme di credenziali delle chiavi e della chiave creata. Nel passaggio finale sono necessari l'ID dell'insieme di credenziali delle chiavi, l'URI e l'URL chiave. Nell'esempio seguente vengono esaminati i dettagli di un insieme di credenziali delle chiavi denominato `myKeyVault` e della chiave denominata `myKey`:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Crittografare i dischi come di seguito, immettendo i propri nomi di parametro:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

L'interfaccia della riga di comando di Azure non fornisce gli errori dettagliati durante il processo di crittografia. Per ulteriori informazioni sulla risoluzione dei problemi, vedere `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Dal momento che il comando precedente dispone di molte variabili e potrebbero non essere fornite molte indicazioni sul perché il processo non riesce, un esempio di comando completo potrebbe essere il seguente:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Riesaminare infine lo stato della crittografia per controllare che ora i dischi virtuali siano crittografati. Nell'esempio seguente viene controllato lo stato di una VM denominata `myVM` nel gruppo di risorse `myResourceGroup`:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Panoramica della crittografia dei dischi
I dischi virtuali delle VM Linux vengono crittografati a riposo mediante [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Non è previsto alcun addebito per la crittografia dei dischi virtuali in Azure. Le chiavi di crittografia vengono archiviate nell'insieme di credenziali delle chiavi di Azure che usano la protezione del software oppure è possibile importare o generare le chiavi in moduli di protezione hardware certificati per gli standard FIPS 140-2 di livello 2. È possibile esercitare il controllo su queste chiavi di crittografia e sul loro uso. Queste chiavi di crittografia vengono usate per crittografare e decrittografare i dischi virtuali collegati alla VM. Un endpoint di Azure Active Directory fornisce un meccanismo protetto per il rilascio di queste chiavi di crittografia all'accensione e allo spegnimento delle VM.

Il processo per la crittografia di una VM si articola nel modo seguente:

1. Creare una chiave di crittografia in un insieme di credenziali delle chiavi di Azure.
2. Configurare la chiave di crittografia in modo da renderla utilizzabile per la crittografia dei dischi.
3. Per leggere la chiave di crittografia dall'insieme di credenziali delle chiavi di Azure, creare un endpoint che usa Azure Active Directory con le autorizzazioni appropriate.
4. Eseguire il comando per crittografare i dischi virtuali, specificando l'endpoint di Azure Active Directory e la chiave di crittografia appropriata da usare.
5. L'endpoint di Azure Active Directory richiede la chiave di crittografia necessaria dall'insieme di credenziali delle chiavi di Azure.
6. I dischi virtuali vengono crittografati tramite la chiave di crittografia fornita.

## <a name="supporting-services-and-encryption-process"></a>Servizi di supporto e processo di crittografia
La crittografia del disco si basa sui componenti aggiuntivi seguenti:

* **Insieme di credenziali delle chiavi di Azure**, che consente di proteggere le chiavi crittografiche e private usate per il processo di crittografia/decrittografia dei dischi.
  * Se disponibile, è possibile usare un insieme di credenziali delle chiavi di Azure esistente. Non è necessario dedicare un insieme di credenziali delle chiavi alla crittografia dei dischi.
  * Per separare i limiti amministrativi e la visibilità delle chiavi, è possibile creare un insieme di credenziali delle chiavi dedicato.
* **Azure Active Directory** gestisce lo scambio protetto delle chiavi di crittografia necessarie e dell'autenticazione per le azioni richieste.
  * In genere, è possibile inserire l'applicazione in un'istanza esistente di Azure Active Directory.
  * L'applicazione è sostanzialmente un endpoint tramite cui i servizi delle macchine virtuali e dell'insieme di credenziali delle chiavi possono richiedere e far emettere le chiavi di crittografia appropriate. Non si sta procedendo a sviluppare un'applicazione reale integrata con Azure Active Directory.

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

## <a name="create-the-azure-key-vault-and-keys"></a>Creare le chiavi e l'insieme di credenziali delle chiavi di Azure
Per completare il resto della presente guida, è necessario installare e registrare l'[ultima versione dell'interfaccia della riga di comando di Azure 1.0](../../xplat-cli-install.md) usando la modalità di Resource Manager nel modo seguente:

```azurecli
azure config mode arm
```

Negli esempi di comandi sostituire tutti i parametri di esempio con i propri valori di nome, percorso e chiave. Negli esempi viene usata una convenzione di `myResourceGroup`, `myKeyVault`, `myAADApp` e così via.

Il primo passaggio consiste nel creare un insieme di credenziali delle chiavi di Azure in cui archiviare le chiavi di crittografia. L'insieme di credenziali delle chiavi di Azure consente di archiviare chiavi, chiavi private o password da implementare in tutta sicurezza in applicazioni e servizi. Per la crittografia del disco virtuale, usare l'insieme di credenziali delle chiavi per archiviare una chiave di crittografia usata per crittografare o decrittografare i dischi virtuali.

Abilitare il provider dell'insieme di credenziali delle chiavi di Azure nella sottoscrizione di Azure, quindi creare un gruppo di risorse. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `WestUS`:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

L'insieme di credenziali delle chiavi di Azure contenente le chiavi di crittografia e le risorse di calcolo associate, come l'archiviazione e la VM stessa, devono risiedere nella stessa area. Nell'esempio seguente viene creato un insieme di credenziali delle chiavi di Azure denominato `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

È possibile archiviare le chiavi di crittografia usando il software o il modulo di protezione hardware. L'uso di un modulo di protezione hardware richiede un insieme di credenziali delle chiavi premium. Esiste un costo aggiuntivo per creare un insieme di credenziali delle chiavi premium, anziché standard, in cui archiviare le chiavi protette tramite software. Per creare un insieme di credenziali delle chiavi premium, aggiungere `--sku Premium` al comando del passaggio precedente. L'esempio seguente usa chiavi protette tramite software, poiché viene creato un insieme di credenziali delle chiavi standard.

Per entrambi i modelli di protezione, è necessario consentire alla piattaforma Azure di accedere all'avvio della VM per richiedere le chiavi di crittografia con cui decrittografare i dischi virtuali. Creare una chiave di crittografia all'interno dell'insieme di credenziali delle chiavi, quindi abilitarne l'uso con la crittografia del disco virtuale. Nell'esempio seguente viene creata una chiave denominata `myKey`, successivamente abilitata per la crittografia del disco:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Creare l'applicazione di Azure Active Directory
Al momento di crittografare o decrittografare i dischi virtuali, un endpoint gestisce l'autenticazione e lo scambio delle chiavi di crittografia dall'insieme di credenziali delle chiavi. Questo endpoint, un'applicazione di Azure Active Directory, consente alla piattaforma Azure di richiedere le chiavi di crittografia appropriate per conto della VM. Un'istanza di Azure Active Directory predefinita è già disponibile nella sottoscrizione, anche se molte organizzazioni hanno directory di Azure Active Directory dedicate.

Poiché non si intende creare un'applicazione di Azure Active Directory completa, nell'esempio seguente i parametri `--home-page` e `--identifier-uris` non devono necessariamente essere indirizzi instradabili effettivi. Nell'esempio seguente viene inoltre specificata una chiave privata basata su password, anziché generare chiavi dal portale di Azure. Attualmente non è possibile generare chiavi dall'interfaccia della riga di comando di Azure.

Creare l'applicazione di Azure Active Directory. Nell'esempio seguente viene creata un'applicazione denominata `myAADApp` che usa la password `myPassword`. Specificare una password personalizzata come di seguito:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Prendere nota del valore `applicationId` restituito nell'output del comando precedente. L'ID applicazione verrà usato in alcuni dei passaggi rimanenti. Creare quindi un nome dell'entità servizio per rendere accessibile l'applicazione all'interno dell'ambiente. Per crittografare o decrittografare correttamente i dischi virtuali, le autorizzazioni per la chiave di crittografia archiviata nell'insieme di credenziali delle chiavi devono essere impostate in modo tale da consentire all'applicazione di Azure Active Directory di leggere le chiavi.

Creare il nome dell'entità servizio e impostare le autorizzazioni appropriate, come di seguito:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Aggiungere un disco virtuale ed esaminare lo stato di crittografia
Per crittografare alcuni dischi virtuali, è possibile aggiungere un disco a una VM esistente. Aggiungere un disco dati da 5 GB a una VM esistente come di seguito:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

I dischi virtuali non sono attualmente crittografati. Controllare l'attuale stato di crittografia della VM come di seguito:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Crittografare i dischi virtuali
A questo punto, per crittografare i dischi virtuali, vengono riuniti tutti i componenti precedenti:

1. Specificare l'applicazione di Azure Active Directory e la relativa password.
2. Specificare l'insieme di credenziali delle chiavi in cui memorizzare i metadati dei dischi crittografati.
3. Specificare le chiavi di crittografia da usare per la crittografia e la decrittografia effettive.
4. Specificare se si desidera crittografare il disco del sistema operativo, i dischi dati o tutti i dischi.

Controllare i dettagli dell'insieme di credenziali delle chiavi di Azure e la chiave creata, in quanto nel passaggio finale sono necessari l'ID dell'insieme di credenziali delle chiavi, l'URI e l'URL della chiave:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Crittografare il disco virtuale usando l'output dei comandi `azure keyvault show` e `azure keyvault key show` nel modo seguente:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Poiché il comando precedente ha molte variabili, l'esempio seguente contiene il comando completo come riferimento:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

L'interfaccia della riga di comando di Azure non fornisce gli errori dettagliati durante il processo di crittografia. Per ulteriori informazioni sulla risoluzione dei problemi, vedere `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` sulla VM da crittografare.

Riesaminare infine lo stato della crittografia per controllare che ora i dischi virtuali siano crittografati:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>Aggiungere ulteriori dischi dati
Una volta crittografati i dischi dati, in un secondo momento è possibile aggiungere alla VM ulteriori dischi virtuali e anche crittografarli. Quando si esegue il comando `azure vm enable-disk-encryption`, incrementare la versione di sequenza usando il parametro `--sequence-version`. Questo parametro della versione di sequenza consente di eseguire ripetutamente operazioni sulla stessa VM.

Ad esempio, consente di aggiungere alla VM un secondo disco virtuale, come di seguito:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Eseguire nuovamente il comando per crittografare i dischi virtuali, questa volta aggiungendo il parametro `--sequence-version` e incrementando il valore della prima esecuzione, come di seguito:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla gestione dell'insieme di credenziali delle chiavi di Azure, tra cui come eliminare chiavi crittografiche e insiemi di credenziali, vedere [Gestire l'insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando](../../key-vault/key-vault-manage-with-cli2.md).
* Per altre informazioni sulla crittografia del disco, tra cui come preparare una VM personalizzata con crittografia da caricare in Azure, vedere [Crittografia dischi di Azure](../../security/azure-security-disk-encryption.md).
