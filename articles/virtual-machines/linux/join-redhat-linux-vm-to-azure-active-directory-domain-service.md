---
title: Aggiungere una macchina virtuale RedHat Linux a un servizio di dominio Active Directory di Azure | Documentazione Microsoft
description: Come aggiungere una macchina virtuale Red Hat Enterprise Linux 7 esistente a un servizio di dominio Active Directory di Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2016
ms.author: v-livech
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 2e46a0f3c9bdbe267d121b4bf62e25d5d411fcc2
ms.contentlocale: it-it
ms.lasthandoff: 08/11/2017

---

# <a name="join-a-redhat-linux-vm-to-an-azure-active-directory-domain-service"></a>Aggiungere una VM RedHat Linux a un servizio di dominio Active Directory di Azure

Questo articolo illustra come aggiungere una macchina virtuale di Red Hat Enterprise Linux (RHEL) 7 a un dominio gestito di Servizi di dominio Active Directory di Azure.  I requisiti sono:

- [Un account di Azure](https://azure.microsoft.com/pricing/free-trial/)

- [File di chiavi SSH pubbliche e private](mac-create-ssh-keys.md)

- [Controller di dominio del servizio di dominio Active Directory di Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Comandi rapidi

_Sostituire gli esempi con le impostazioni desiderate._

### <a name="switch-the-azure-cli-to-classic-deployment-mode"></a>Passare da azure-cli alla modalità di distribuzione classica

```azurecli
azure config mode asm
```

### <a name="search-for-a-rhel-version-and-image"></a>Cercare una versione e un'immagine di RHEL

```azurecli
azure vm image list | grep "Red Hat"
```

### <a name="create-a-redhat-linux-vm"></a>Creare una macchina virtuale Redhat Linux

```azurecli
azure vm create myVM \
-o a879bbefc56a43abb0ce65052aac09f3__RHEL_7_2_Standard_Azure_RHUI-20161026220742 \
-g ahmet \
-p myPassword \
-e 22 \
-t "~/.ssh/id_rsa.pub" \
-z "Small" \
-l "West US"
```

### <a name="ssh-to-the-vm"></a>Eseguire SSH sulla VM

```bash
ssh -i ~/.ssh/id_rsa ahmet@myVM
```

### <a name="update-yum-packages"></a>Aggiornare i pacchetti YUM

```bash
sudo yum update
```

### <a name="install-packages-needed"></a>Installare i pacchetti necessari

```bash
sudo yum -y install realmd sssd krb5-workstation krb5-libs
```

Ora che i pacchetti sono installati nella macchina virtuale Linux, l'attività successiva consiste nell'aggiungere la macchina virtuale al dominio gestito.

### <a name="discover-the-aad-domain-services-managed-domain"></a>Individuare il dominio gestito di Servizi di dominio Active Directory di Azure

```bash
sudo realm discover mydomain.com
```

### <a name="initialize-kerberos"></a>Inizializzare kerberos

Verificare che l'utente specificato appartenga al gruppo AAD DC Administrators. Solo questi utenti possono aggiungere computer al dominio gestito.

```bash
kinit ahmet@mydomain.com
```

### <a name="join-the-machine-to-the-domain"></a>Aggiungere il computer al dominio

```bash
sudo realm join --verbose mydomain.com -U 'ahmet@mydomain.com'
```

### <a name="verify-the-machine-is-joined-to-the-domain"></a>Verificare che il computer sia stato aggiunto al dominio

```bash
ssh -l ahmet@mydomain.com mydomain.cloudapp.net
```

## <a name="next-steps"></a>Passaggi successivi

* [Red Hat Update Infrastructure (RHUI) per macchine virtuali Red Hat Enterprise Linux su richiesta in Azure](update-infrastructure-redhat.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Configurare l'insieme di credenziali delle chiavi per le macchine virtuali in Azure Resource Manager](key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Distribuire e gestire le macchine virtuali usando modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

