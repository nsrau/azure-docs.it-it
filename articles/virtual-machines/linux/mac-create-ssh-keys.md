---
title: Creare e usare una coppia di chiavi SSH per le macchine virtuali Linux in Azure | Microsoft Docs
description: Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure per migliorare la sicurezza del processo di autenticazione.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: iainfou
ms.openlocfilehash: 137fb13ff286e5284b8e8910834913ec9f1d48a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2018
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Azioni rapide: Creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure
Con una coppia di chiavi SSH (Secure Shell) è possibile creare macchine virtuali in Azure che per impostazione predefinita usano le chiavi SSH per l'autenticazione, eliminando la necessità di password per l'accesso. Questo articolo illustra come generare rapidamente e usare una coppia di file di chiavi pubblica e privata per le macchine virtuali Linux. È possibile completare questa procedura con Azure Cloud Shell, un host Linux o macOS, il sottosistema Windows per Linux, e altri strumenti che supportano OpenSSH. 

Per altri scenari ed esempi, vedere la [procedura dettagliata per creare coppie di chiavi SSH](create-ssh-keys-detailed.md).

Per altri modi in cui generare e usare le chiavi SSH in un computer Windows, vedere [Come usare le chiavi SSH con Windows in Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH
Usare il comando `ssh-keygen` per creare file di chiavi SSH pubblica e privata, che per impostazione predefinita si trovano nella directory `~/.ssh`. È possibile specificare un percorso diverso e una passphrase aggiuntiva, ovvero una password per accedere al file di chiave privata, quando richiesto. Se nel percorso corrente è presente una coppia di chiavi SSH, questi file vengono sovrascritti.

```bash
ssh-keygen -t rsa -b 2048
```

Se si usa l'[interfaccia della riga di comando di Azure 2.0](/cli/azure) per creare la VM, facoltativamente è possibile creare i file della chiave SSH pubblica e privata eseguendo il comando [az vm create](/cli/azure/vm#az_vm_create) con l'opzione `--generate-ssh-keys`. Le chiavi vengono archiviate nella directory ~/.ssh. Si noti che questa opzione di comando non implica la sovrascrittura delle chiavi se sono già presenti in tale percorso.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Fornire la chiave SSH pubblica quando si distribuisce una VM
Per creare una VM Linux che utilizza le chiavi SSH per l'autenticazione, specificare la chiave SSH pubblica quando si crea la macchina virtuale tramite il portale di Azure, l'interfaccia della riga di comando, modelli di Resource Manager o altri metodi:

* [Creare una macchina virtuale Linux con il portale di Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una VM Linux usando un modello di Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Se non si ha familiarità con il formato della chiave SSH pubblica, è possibile visualizzare la chiave pubblica eseguendo `cat` come segue, sostituendo `~/.ssh/id_rsa.pub` con il proprio percorso del file della chiave pubblica:

```bash
cat ~/.ssh/id_rsa.pub
```

Se si copiano e si incollano i contenuti del file di chiave pubblica da usare nel portale di Azure o in un modello di Resource Manager, verificare di non copiare spazi aggiuntivi. Ad esempio, se si usa macOS, è possibile eseguire il pipe del file della chiave pubblica, che per impostazione predefinita è `~/.ssh/id_rsa.pub`, a **pbcopy** per copiare i contenuti. Anche altri programmi Linux eseguono questa operazione, ad esempio **xclip**.

Per impostazione predefinita, la chiave pubblica che si inserisce nella VM Linux in Azure viene archiviata in `~/.ssh/id_rsa.pub`, a meno che il percorso non sia stato modificato in fase di creazione. Se si usa l'[interfaccia della riga di comando di Azure 2.0](/cli/azure) per creare la macchina virtuale con una chiave pubblica esistente, specificare il valore o il percorso della chiave pubblica eseguendo il comando [az vm create](/cli/azure/vm#az_vm_create) con l'opzione `--ssh-key-value`. 

## <a name="ssh-to-your-vm"></a>Stabilire una connessione SSH alla VM
Con la chiave pubblica distribuita nella VM di Azure e la chiave privata nel sistema locale, stabilire una connessione SSH alla VM usando l'indirizzo IP o il nome DNS della VM. Sostituire *azureuser* e *myvm.westus.cloudapp.azure.com* nel comando seguente con il nome utente dell'amministratore e il nome di dominio completo (o indirizzo IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se è stata specificata una passphrase durante la creazione della coppia di chiavi, immettere la passphrase quando viene richiesta durante il processo di accesso. Il server viene aggiunto alla cartella `~/.ssh/known_hosts` e non verrà chiesto di connettersi di nuovo finché la chiave pubblica nella VM di Azure non viene modificata o il nome server viene rimosso da `~/.ssh/known_hosts`.

Per impostazione predefinita, le VM create con le chiavi SSH vengono configurate con le password disabilitate, per rendere i tentativi di attacco basati su forza bruta per scoprire le password molto più costosi e quindi difficili. 

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato la creazione di una coppia di chiavi SSH semplice per un utilizzo rapido. 

* Se si necessita di ulteriore assistenza relativa al funzionamento della coppia di chiavi SSH, vedere [Procedura dettagliata per creare e gestire coppie di chiavi SSH](create-ssh-keys-detailed.md).

* In caso di problemi con le connessioni SSH a una VM di Azure, vedere [Risolvere i problemi di connessione SSH a una macchina virtuale Linux di Azure](troubleshoot-ssh-connection.md).


