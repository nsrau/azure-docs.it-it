---
title: Reimpostare l'accesso a VM Linux di Azure tramite l'estensione VMAccess | Microsoft Docs
description: Reimpostare l'accesso a VM Linux di Azure tramite l'estensione VMAccess
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: v-livech
ms.openlocfilehash: 278bf1785aac71068ab94cf9916af69a204c44be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-10"></a>Gestire utenti, SSH e dischi di controllo o di ripristino in VM Linux di Azure tramite l'estensione VMAccess con l'interfaccia della riga di comando di Azure 1.0
Questo articolo illustra come usare l'estensione VMAccess di Azure per controllare o ripristinare un disco, reimpostare l'accesso utente, gestire gli account utente o reimpostare la configurazione dei dischi SSHD in Linux. L'articolo richiede:

* Un account Azure. È possibile [ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Accesso tramite `azure login` per l'[interfaccia della riga di comando di Azure](../../cli-install-nodejs.md).
* L'interfaccia della riga di comando di Azure *deve essere impostata obbligatoriamente* sulla modalità Azure Resource Manager `azure config mode arm`.


## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](#quick-commands): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa


## <a name="quick-commands"></a>Comandi rapidi
Esistono due modi per usare VMAccess nelle VM Linux:

* Usare l'interfaccia della riga di comando di Azure 1.0 e i parametri richiesti.
* Usare file JSON non elaborati che VMAccess elabora e su cui basa le sue operazioni.

Per la sezione dei comandi rapidi si userà il metodo `azure vm reset-access` dell'interfaccia della riga di comando di Azure 1.0. Negli esempi di comandi seguenti sostituire i valori contenenti "example" con i valori dell'ambiente locale.

## <a name="create-a-resource-group-and-linux-vm"></a>Creare un gruppo di risorse e una VM Linux
```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Creare una VM Debian
```azurecli
azure vm quick-create \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -g myResourceGroup \
  -l westus \
  -y Linux \
  -n myVM \
  -Q Debian
```

## <a name="reset-root-password"></a>Reimpostare la password radice
Per reimpostare la password radice:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u root \
  -p myNewPassword
```

## <a name="ssh-key-reset"></a>Reimpostazione della chiave SSH
Per reimpostare la chiave SSH di un utente non ROOT:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Creare un utente
Per creare un utente:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -p myAdminUserPassword
```

## <a name="remove-a-user"></a>Rimuovere un utente
```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -R myRemovedUser
```

## <a name="reset-sshd"></a>Reimpostare un disco SSHD
Per reimpostare la configurazione di un disco SSHD:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM
  -r
```


## <a name="detailed-walkthrough"></a>Procedura dettagliata
### <a name="vmaccess-defined"></a>Estensione VMAccess definita:
Il disco della VM Linux genera errori. In qualche modo la password radice della VM Linux è stata reimpostata o la chiave privata SSH è stata eliminata accidentalmente. In passato, quando nel data center si verificava questa situazione, era necessario accedere all'unità e quindi aprire il KVM per raggiungere la console del server. L'estensione VMAccess di Azure può essere concepita come il commutatore tastiera, video e mouse che consente di accedere alla console per reimpostare l'accesso a Linux o eseguire la manutenzione a livello di disco.

Per la procedura dettagliata si userà la forma estesa di VMAccess, che usa file JSON non elaborati.  Questi file JSON di VMAccess possono essere chiamati anche dai modelli di Azure.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Uso di VMAccess per controllare o riparare il disco di una VM Linux
Con VMAccess è possibile eseguire il comando fsck sul disco della VM Linux.  È inoltre possibile eseguire una verifica del disco e ripristinarlo utilizzando un VMAccess.

Per controllare e quindi ripristinare il disco, usare questo script VMAccess:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Eseguire lo script VMAccess con:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>Uso di VMAccess per reimpostare l'accesso utente a Linux
Se si è perso l'accesso alla radice della VM Linux è possibile avviare uno script VMAccess per reimpostare la password radice.

Per reimpostare la password radice, usare questo script VMAccess:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword"
}
```

Eseguire lo script VMAccess con:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_root_password.json
```

Per reimpostare la chiave SSH di un utente non ROOT usare questo script VMAccess:

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM" 
}
```

Eseguire lo script VMAccess con:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>Uso di VMAccess per gestire gli account utente in Linux
VMAccess è uno script Python che può essere usato per gestire gli utenti nella VM Linux senza accedere e senza usare sudo o l'account radice.

Per creare un utente, usare questo script VMAccess:

`create_new_user.json`

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Eseguire lo script VMAccess con:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path create_new_user.json
```

Per eliminare un utente, usare questo script VMAccess:

`remove_user.json`

```json
{
  "remove_user":"myDeletedUser"
}
```

Eseguire lo script VMAccess con:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>Uso di VMAccess per reimpostare la configurazione SSHD
Se si apportano modifiche alla configurazione del disco SSHD della VM Linux e si chiude la connessione SSH prima di verificare le modifiche, potrebbe essere impedita una nuova connessione SSH.  È possibile usare VMAccess per ripristinare una configurazione SSHD valida nota senza aver effettuato l'accesso tramite SSH.

Per reimpostare la configurazione SSHD usare questo script VMAccess:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Eseguire lo script VMAccess con:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_sshd.json
```

## <a name="next-steps"></a>Passaggi successivi
L'aggiornamento di Linux mediante le estensioni VMAccess di Azure è un metodo per apportare modifiche a una VM Linux in esecuzione.  È inoltre possibile usare strumenti come cloud-init e modelli di Azure per modificare la VM Linux in fase di avvio.

[Informazioni sulle estensioni e sulle funzionalità delle macchine virtuali](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Creazione di modelli di Azure Resource Manager con le estensioni di VM Linux](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Uso di cloud-init per personalizzare una VM Linux durante la creazione](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

