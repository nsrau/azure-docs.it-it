---
title: Reimpostare l'accesso a una macchina virtuale Linux di Azure | Microsoft Docs
description: Come gestire gli utenti amministrativi e ripristinare l'accesso nelle macchine virtuali Linux usando l'estensione VMAccess e l'interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 08/04/2017
ms.author: danlep
ms.openlocfilehash: 235a6367ad317945cfeaaa6aae4e060208fb8e8e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>Informazioni su come gestire utenti amministrativi, SSH e dischi di controllo o di ripristino in macchine virtuali Linux usando l'estensione VMAccess con l'interfaccia della riga di comando di Azure 2.0
Il disco della VM Linux genera errori. In qualche modo la password radice della VM Linux è stata reimpostata o la chiave privata SSH è stata eliminata accidentalmente. In passato, quando nel data center si verificava questa situazione, era necessario accedere all'unità e quindi aprire il KVM per raggiungere la console del server. L'estensione VMAccess di Azure può essere concepita come il commutatore tastiera, video e mouse che consente di accedere alla console per reimpostare l'accesso a Linux o eseguire la manutenzione a livello di disco.

Questo articolo illustra come usare l'estensione VMAccess di Azure per controllare o ripristinare un disco, reimpostare l'accesso utente, gestire gli account di utenti amministrativi o reimpostare la configurazione dei dischi SSH in Linux. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="ways-to-use-the-vmaccess-extension"></a>Modi di usare l'estensione VMAccess
Esistono due modi per usare l'estensione VMAccess nelle VM Linux:

* Usare l'interfaccia della riga di comando di Azure 2.0 e i parametri richiesti.
* [Usare file JSON non elaborati che l'estensione VMAccess elaborerà](#use-json-files-and-the-vmaccess-extension) e su cui baserà le sue operazioni.

L'esempio seguente usa i comandi [az vm user](/cli/azure/vm/user). Per eseguire questi passaggi è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver effettuato l'accesso a un account Azure con il comando [az login](/cli/azure/reference-index#az_login).

## <a name="reset-ssh-key"></a>Ripristinare la chiave SSH
L'esempio seguente ripristina la chiave SSH per l'utente `azureuser` nella VM denominata `myVM`:

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="reset-password"></a>Reimposta password
L'esempio seguente ripristina la password per l'utente `azureuser` nella VM denominata `myVM`:

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Riavviare SSH
L'esempio seguente riavvia il daemon SSH e reimposta la configurazione SSH sui valori predefiniti in una macchina virtuale denominata `myVM`:

```azurecli
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Creare un utente amministrativo/sudo
L'esempio seguente crea un utente denominato `myNewUser` con autorizzazioni **sudo**. L'account usa una chiave SSH per l'autenticazione nella macchina virtuale denominata `myVM`. Questo metodo è progettato per ottenere di nuovo l'accesso a una macchina virtuale nel caso in cui le credenziali attuali siano state perse o dimenticate. Come procedura consigliata gli account con autorizzazioni **sudo** devono presentare limitazioni.

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```



## <a name="delete-a-user"></a>Eliminare un utente
L'esempio seguente elimina un utente denominato `myNewUser` nella VM denominata `myVM`:

```azurecli
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>Usare file JSON e l'estensione VMAccess
Gli esempi seguenti usano file JSON non elaborati. Usare [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) per chiamare in seguito i file JSON. Questi file JSON possono essere chiamati anche dai modelli di Azure. 

### <a name="reset-user-access"></a>Ripristinare l'accesso utente
Se si è perso l'accesso alla radice della VM Linux è possibile avviare uno script VMAccess per reimpostare la chiave SSH o la password di un utente.

Per reimpostare la chiave pubblica SSH di un utente, creare un file denominato `reset_ssh_key.json` e aggiungere le impostazioni nel formato seguente. Usare valori personalizzati per i parametri `username` e `ssh_key`:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Eseguire lo script VMAccess con:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_ssh_key.json
```

Per reimpostare la password di un utente, creare un file denominato `reset_user_password.json` e aggiungere le impostazioni nel formato seguente. Usare valori personalizzati per i parametri `username` e `password`:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Eseguire lo script VMAccess con:

```azurecli
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

```azurecli
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

```azurecli
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

```azurecli
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

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```

## <a name="next-steps"></a>Passaggi successivi
L'aggiornamento di Linux mediante l'estensione VMAccess di Azure è un metodo per apportare modifiche a una VM Linux in esecuzione. È inoltre possibile usare strumenti come cloud-init e modelli di Azure Resource Manager per modificare la VM Linux in fase di avvio.

[Estensioni e funzionalità delle macchine virtuali per Linux](extensions-features.md)

[Creazione di modelli di Azure Resource Manager con le estensioni di VM Linux](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Uso di cloud-init per personalizzare una VM Linux durante la creazione](using-cloud-init.md)

