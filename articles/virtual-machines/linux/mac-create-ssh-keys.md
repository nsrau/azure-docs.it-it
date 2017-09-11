---
title: Creare e usare una coppia di chiavi SSH per le macchine virtuali Linux in Azure | Microsoft Docs
description: Come creare e usare una coppia di chiavi SSH pubblica e privata per le VM Linux in Azure per migliorare la sicurezza del processo di autenticazione.
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/14/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 6fcdcc96c7762e2362aebf909ef25f4a5ab62f99
ms.contentlocale: it-it
ms.lasthandoff: 08/30/2017

---

# <a name="how-to-create-and-use-an-ssh-public-and-private-key-pair-for-linux-vms-in-azure"></a>Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure
Con una coppia di chiavi SSH (Secure Shell) è possibile creare macchine virtuali in Azure che per impostazione predefinita usano le chiavi SSH per l'autenticazione, eliminando la necessità di password per l'accesso. Questo articolo illustra come generare rapidamente e usare una coppia di file di chiavi pubblica e privata RSA protocollo SSH versione 2 per le VM Linux. Per una procedura più dettagliata ed esempi aggiuntivi, vedere la [procedura dettagliata per creare coppie di chiavi SSH e certificati](create-ssh-keys-detailed.md).

## <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH
Usare il comando `ssh-keygen` per creare i file di chiave pubblica e privata SSH che, per impostazione predefinita, vengono creati nella directory `~/.ssh`, ma è possibile specificare un percorso diverso e una passphrase (una password per accedere al file della chiave privata) aggiuntiva, quando viene richiesto. Usare il comando seguente da una shell Bash, rispondendo ai prompt con le proprie informazioni.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="use-the-ssh-key-pair"></a>Usare la coppia di chiavi SSH
Per impostazione predefinita, la chiave pubblica che si inserisce nella VM Linux in Azure viene archiviata in `~/.ssh/id_rsa.pub`, a meno che il percorso non sia stato modificato in fase di creazione. Se si usa l'[interfaccia della riga di comando di Azure 2.0](/cli/azure) per creare la VM, specificare il percorso di questa chiave pubblica quando si usa [az vm create](/cli/azure/vm#create) con l'opzione `--ssh-key-path`. Se si copiano e si incollano i contenuti del file di chiave pubblica da usare nel portale di Azure o in un modello di Resource Manager, verificare di non copiare spazi aggiuntivi. Se, ad esempio, si usa OS X, è possibile inviare pipe del file di chiave pubblica (per impostazione predefinita, **~/.ssh/id_rsa.pub**) a **pbcopy** per copiare i contenuti. Anche altri programmi Linux eseguono questa operazione, ad esempio `xclip`.

Se non si ha familiarità con le chiavi pubbliche SSH, è possibile visualizzare la chiave pubblica eseguendo `cat` come segue, sostituendo `~/.ssh/id_rsa.pub` con il proprio percorso file della chiave pubblica:

```bash
cat ~/.ssh/id_rsa.pub
```

Con la chiave pubblica nella VM di Azure, stabilire una connessione SSH alla VM usando l'indirizzo IP o il nome DNS della VM. Ricordare di sostituire `azureuser` e `myvm.westus.cloudapp.azure.com` con il nome utente amministratore e il nome di dominio completo o l'indirizzo IP:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se è stata specificata una passphrase durante la creazione della coppia di chiavi, immettere la passphrase quando viene richiesta durante il processo di accesso. Il server viene aggiunto alla cartella `~/.ssh/known_hosts` e non verrà chiesto di connettersi di nuovo finché la chiave pubblica nella VM di Azure non viene modificata o il nome server viene rimosso da `~/.ssh/known_hosts`.

## <a name="next-steps"></a>Passaggi successivi

Per impostazione predefinita, le VM create con le chiavi SSH vengono configurate con le password disabilitate, per rendere i tentativi di attacco basati su forza bruta per scoprire le password molto più costosi e quindi difficili. Questo argomento descrive la creazione di una coppia di chiavi SSH semplice per un utilizzo rapido. Se serve maggiore assistenza durante la creazione della coppia di chiavi SSH o sono necessari certificati aggiuntivi, [Detailed steps to create SSH key pairs and certificates](create-ssh-keys-detailed.md) (Procedura dettagliata per creare coppie di chiavi SSH e certificati).

È possibile creare VM che usano la coppia di chiavi SSH con il portale di Azure, l'interfaccia della riga di comando e i modelli:

* [Creare una VM Linux protetta usando il portale di Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una VM Linux protetta usando l'interfaccia della riga di comando di Azure 2.0](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una VM Linux protetta usando un modello di Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

