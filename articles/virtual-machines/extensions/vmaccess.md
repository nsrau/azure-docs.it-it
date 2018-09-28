---
title: Reimpostare l'accesso a una macchina virtuale Linux di Azure | Microsoft Docs
description: Come gestire gli utenti amministrativi e reimpostare l'accesso nelle macchine virtuali Linux usando l'estensione VMAccess e l'interfaccia della riga di comando di Azure
services: virtual-machines-linux
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: roiyz
ms.openlocfilehash: e878f5c9f923b55a1eb94cefb1ecf021c81e884e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998628"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Gestire gli utenti amministrativi e la configurazione SSH e verificare o riparare dischi in macchine virtuali Linux usando l'estensione VMAccess con l'interfaccia della riga di comando di Azure
## <a name="overview"></a>Panoramica
Il disco della VM Linux genera errori. In qualche modo la password radice della VM Linux è stata reimpostata o la chiave privata SSH è stata eliminata accidentalmente. In passato, quando nel data center si verificava questa situazione, era necessario accedere all'unità e quindi aprire il KVM per raggiungere la console del server. L'estensione VMAccess di Azure può essere concepita come il commutatore tastiera, video e mouse che consente di accedere alla console per reimpostare l'accesso a Linux o eseguire la manutenzione a livello di disco.

Questo articolo illustra come usare l'estensione VMAccess di Azure per controllare o ripristinare un disco, reimpostare l'accesso utente, gestire gli account di utenti amministrativi o aggiornare la configurazione SSH in Linux se in esecuzione come macchine virtuali di Azure Resource Manager. Se è necessario gestire macchine virtuali classiche, è possibile seguire le istruzioni disponibili nella [documentazione sulla VM classica](../linux/classic/reset-access-classic.md). 

## <a name="prerequisites"></a>Prerequisiti
### <a name="operating-system"></a>Sistema operativo

L'estensione di accesso alle VM può essere eseguita in queste distribuzioni di Linux:

| Distribuzione | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS e 12.04 LTS |
| Debian | Debian 7,9 +, 8.2 + |
| Red Hat | RHEL 6.7+, 7.1+ |
| Oracle Linux | 6.4+, 7.0+ |
| SUSE | 11 e 12 |
| OpenSUSE | openSUSE Leap 42.2+ |
| CentOS | CentOS 6.3+, 7.0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Modi di usare l'estensione VMAccess
Esistono due modi per usare l'estensione VMAccess nelle VM Linux:

* Usare l'interfaccia della riga di comando di Azure e i parametri richiesti.
* [Usare file JSON non elaborati che l'estensione VMAccess elaborerà](#use-json-files-and-the-vmaccess-extension) e su cui baserà le sue operazioni.

L'esempio seguente usa i comandi [az vm user](/cli/azure/vm/user). Per eseguire questi passaggi è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-az-cli2) e aver effettuato l'accesso a un account Azure con il comando [az login](/cli/azure/reference-index#az_login).

## <a name="update-ssh-key"></a>Aggiornare la chiave SSH
Nell'esempio seguente viene aggiornata la chiave SSH per l'utente `azureuser` nella macchina virtuale denominata `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **NOTA:** il comando `az vm user update` accoda il testo della nuova chiave pubblica al file `~/.ssh/authorized_keys` per l'utente amministratore della macchina virtuale. Questa operazione non sostituisce né rimuove le chiavi SSH esistenti. L'operazione non rimuove neanche le chiavi precedenti impostate in fase di distribuzione, né gli aggiornamenti successivi tramite l'estensione VMAccess.

## <a name="reset-password"></a>Reimposta password
L'esempio seguente ripristina la password per l'utente `azureuser` nella VM denominata `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Riavviare SSH
L'esempio seguente riavvia il daemon SSH e reimposta la configurazione SSH sui valori predefiniti in una macchina virtuale denominata `myVM`:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Creare un utente amministrativo/sudo
L'esempio seguente crea un utente denominato `myNewUser` con autorizzazioni **sudo**. L'account usa una chiave SSH per l'autenticazione nella macchina virtuale denominata `myVM`. Questo metodo è progettato per ottenere di nuovo l'accesso a una macchina virtuale nel caso in cui le credenziali attuali siano state perse o dimenticate. Come procedura consigliata gli account con autorizzazioni **sudo** devono presentare limitazioni.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Eliminare un utente
L'esempio seguente elimina un utente denominato `myNewUser` nella VM denominata `myVM`:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Usare file JSON e l'estensione VMAccess
Gli esempi seguenti usano file JSON non elaborati. Usare [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) per chiamare in seguito i file JSON. Questi file JSON possono essere chiamati anche dai modelli di Azure. 

### <a name="reset-user-access"></a>Ripristinare l'accesso utente
Se si è perso l'accesso alla radice della macchina virtuale Linux, è possibile avviare uno script VMAccess per aggiornare la chiave SSH o la password di un utente.

Per aggiornare la chiave pubblica SSH di un utente, creare un file denominato `update_ssh_key.json` e aggiungere le impostazioni nel formato seguente. Usare valori personalizzati per i parametri `username` e `ssh_key`:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Eseguire lo script VMAccess con:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Per reimpostare la password di un utente, creare un file denominato `reset_user_password.json` e aggiungere le impostazioni nel formato seguente. Usare valori personalizzati per i parametri `username` e `password`:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Eseguire lo script VMAccess con:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Riavviare SSH
Per riavviare il daemon SSH e reimpostare la configurazione SSH sui valori predefiniti, creare un file denominato `reset_sshd.json`. Aggiungere il contenuto seguente:

```json
{
  "reset_ssh": true
}
```

Eseguire lo script VMAccess con:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Gestire gli utenti amministrativi

Per creare un utente con autorizzazioni **sudo** che esegua l'autenticazione con una chiave SSH, creare un file denominato `create_new_user.json` e aggiungere impostazioni nel formato seguente. Usare valori personalizzati per i parametri `username` e `ssh_key`. Questo metodo è progettato per ottenere di nuovo l'accesso a una macchina virtuale nel caso in cui le credenziali attuali siano state perse o dimenticate. Come procedura consigliata gli account con autorizzazioni **sudo** devono presentare limitazioni.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Eseguire lo script VMAccess con:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Per eliminare un utente, creare un file denominato `delete_user.json` e aggiungere il contenuto seguente. Usare un valore personalizzato per il parametro `remove_user`:

```json
{
  "remove_user":"myNewUser"
}
```

Eseguire lo script VMAccess con:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Controllare o riparare il disco
Tramite VMAccess è anche possibile controllare e riparare un disco aggiunto alla macchina virtuale Linux.

Per controllare e quindi riparare il disco, creare un file denominato `disk_check_repair.json` e aggiungere impostazioni nel formato seguente. Usare un valore personalizzato per il nome di `repair_disk`:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Eseguire lo script VMAccess con:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite l'interfaccia della riga di comando di Azure. Per visualizzare lo stato di distribuzione delle estensioni per una determinata VM, eseguire il comando seguente nell'interfaccia della riga di comando di Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
