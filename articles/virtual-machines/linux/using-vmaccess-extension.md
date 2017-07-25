---
title: Ripristinare l'accesso con l'estensione VMAccess e l'interfaccia della riga di comando di Azure 2.0 | Documenti di Microsoft
description: Come gestire gli utenti e ripristinare l'accesso in VM Linux mediante l'estensione VMAccess e l'interfaccia della riga di comando di Azure 2.0
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
ms.date: 02/16/2017
ms.author: danlep
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e23e2b2d5f30a2ec30564287c96ffc9c671c0dbf
ms.contentlocale: it-it
ms.lasthandoff: 04/03/2017

---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>Gestire utenti, SSH e dischi di controllo o di ripristino in VM Linux mediante l'estensione VMAccess con l'interfaccia della riga di comando di Azure 2.0
Il disco della VM Linux genera errori. In qualche modo la password radice della VM Linux è stata reimpostata o la chiave privata SSH è stata eliminata accidentalmente. In passato, quando nel data center si verificava questa situazione, era necessario accedere all'unità e quindi aprire il KVM per raggiungere la console del server. L'estensione VMAccess di Azure può essere concepita come il commutatore tastiera, video e mouse che consente di accedere alla console per reimpostare l'accesso a Linux o eseguire la manutenzione a livello di disco.

Questo articolo illustra come usare l'estensione VMAccess di Azure per controllare o ripristinare un disco, reimpostare l'accesso utente, gestire gli account utente o reimpostare la configurazione dei dischi SSHD in Linux. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="ways-to-use-the-vmaccess-extension"></a>Modi di usare l'estensione VMAccess
Esistono due modi per usare l'estensione VMAccess nelle VM Linux:

* Usare l'interfaccia della riga di comando di Azure 2.0 e i parametri richiesti.
* [Usare file JSON non elaborati che l'estensione VMAccess elaborerà](#use-json-files-and-the-vmaccess-extension) e su cui baserà le sue operazioni.

Gli esempi seguenti usano [az vm access](/cli/azure/vm/access) con i parametri appropriati. Per eseguire questi passaggi è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver effettuato l'accesso a un account Azure con il comando [az login](/cli/azure/#login).

## <a name="reset-ssh-key"></a>Ripristinare la chiave SSH
L'esempio seguente ripristina la chiave SSH per l'utente `azureuser` nella VM denominata `myVM`:

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="reset-password"></a>Reimpostazione delle password
L'esempio seguente ripristina la password per l'utente `azureuser` nella VM denominata `myVM`:

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="reset-sshd"></a>Reimpostare un disco SSHD
L'esempio seguente ripristina la configurazione SSHD nella VM denominata `myVM`:

```azurecli
az vm access reset-linux-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-a-user"></a>Creare un utente
L'esempio seguente crea un utente denominato `myNewUser` usando una chiave SSH per l'autenticazione nella VM denominata `myVM`:

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="deletes-a-user"></a>Elimina un utente
L'esempio seguente elimina un utente denominato `myNewUser` nella VM denominata `myVM`:

```azurecli
az vm access delete-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>Usare file JSON e l'estensione VMAccess
Gli esempi seguenti usano file JSON non elaborati. Usare [az vm extension set](/cli/azure/vm/extension#set) per chiamare in seguito i file JSON. Questi file JSON possono essere chiamati anche dai modelli di Azure. 

### <a name="reset-user-access"></a>Ripristinare l'accesso utente
Se si è perso l'accesso alla radice della VM Linux è possibile avviare uno script VMAccess per reimpostare la password di un utente.

Per ripristinare la chiave SSH di un utente, creare un file denominato `reset_ssh_key.json` e aggiungere il seguente contenuto:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
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

Per reimpostare la password di un utente, creare un file denominato `reset_user_password.json` e aggiungere il seguente contenuto:

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

### <a name="reset-ssh"></a>Ripristinare SSH
Se si apportano modifiche alla configurazione del disco SSHD della VM Linux e si chiude la connessione SSH prima di verificare le modifiche, potrebbe essere impedita una nuova connessione SSH.  È possibile usare VMAccess per ripristinare una configurazione SSHD valida nota senza aver effettuato l'accesso tramite SSH.

Per ripristinare la configurazione SSH di un utente, creare un file denominato `reset_sshd.json` e aggiungere il seguente contenuto:

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

### <a name="manage-users"></a>Gestire gli utenti
VMAccess è uno script Python che può essere usato per gestire gli utenti nella VM Linux senza accedere e senza usare sudo o l'account radice.

Per creare un utente, creare un file denominato `create_new_user.json` e aggiungere il seguente contenuto:

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

Per eliminare un utente, creare un file denominato `delete_user.json` e aggiungere il seguente contenuto:

```json
{
  "remove_user":"myDeleteUser"
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
Con VMAccess è possibile eseguire il comando fsck sul disco della VM Linux. È inoltre possibile eseguire una verifica del disco e ripristinarlo utilizzando un VMAccess.

Per controllare e quindi riparare il disco, usare questo script VMAccess, creare un file denominato `disk_check_repair.json` e aggiungere il seguente contenuto:

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
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

[Informazioni sulle estensioni e sulle funzionalità delle macchine virtuali](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Creazione di modelli di Azure Resource Manager con le estensioni di VM Linux](../windows/extensions-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Uso di cloud-init per personalizzare una VM Linux durante la creazione](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


