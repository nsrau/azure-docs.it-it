---
title: Spostare file da e verso macchine virtuali Linux di Azure usando SCP | Microsoft Docs
description: Spostare in sicurezza file da e verso una macchina virtuale Linux in Azure usando SCP e una coppia di chiavi SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 8eb958bb5c8450baecb0b7b4c24db1778a1dec1d
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Spostare file da e verso una macchina virtuale Linux usando SCP

Questo articolo illustra come spostare file da una workstation a una macchina virtuale Linux di Azure, o viceversa, usando Secure Copy (SCP). Il trasferimento di file tra una workstation e una macchina virtuale Linux in modo rapido e sicuro è un aspetto essenziale della gestione dell'infrastruttura di Azure. 

Per questo articolo è necessaria una macchina virtuale Linux distribuita in Azure tramite [file di chiavi SSH pubbliche e private](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). È necessario anche un client SCP per il computer locale: uno strumento basato su SSH e incluso nella shell Bash predefinita della maggior parte dei computer Mac e Linux e in alcune shell di Windows.

## <a name="quick-commands"></a>Comandi rapidi

Copiare un file in una macchina virtuale Linux

```bash
scp file azureuser@azurehost:directory/targetfile
```

Copiare un file da una macchina virtuale Linux

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata

A titolo di esempio, si sposterà un file di configurazione di Azure in una macchina virtuale Linux e si scaricherà la directory di un file di log, usando in entrambi i casi SCP e chiavi SSH.   

## <a name="ssh-key-pair-authentication"></a>Autenticazione della coppia di chiavi SSH

SCP usa SSH per il livello di trasporto. SSH gestisce l'autenticazione nell'host di destinazione e sposta il file in un tunnel crittografato fornito per impostazione predefinita con SSH. Per l'autenticazione SSH è possibile usare nomi utente e password. Per una maggiore sicurezza, tuttavia, è consigliabile eseguire l'autenticazione tramite file di chiavi SSH pubbliche e private. Dopo che SSH ha autenticato la connessione, SCP avvia il processo di copia del file. Combinando un file `~/.ssh/config` correttamente configurato con chiavi SSH pubbliche e private, è possibile stabilire la connessione SCP usando solo un nome di server (o un indirizzo IP). Se si ha una sola chiave SSH, SCP la cerca nella directory `~/.ssh/` e, per impostazione predefinita, la usa per accedere alla macchina virtuale.

Per altre informazioni sulla configurazione del file `~/.ssh/config` e delle chiavi SSH pubbliche e private, vedere [Creare chiavi SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>Copia sicura di un file in una macchina virtuale Linux

Nel primo esempio si copia un file di configurazione di Azure in una macchina virtuale Linux usata per distribuire l'automazione. Poiché questo file contiene credenziali di API di Azure, che includono informazioni riservate, la sicurezza è particolarmente importante e il tunnel crittografato fornito con SSH assicura la protezione dei contenuti del file.

Il comando seguente consente di copiare il file *.azure/config* locale in una macchina virtuale di Azure con FQDN: *myserver.eastus.cloudapp.azure.com*. Il nome utente dell'amministratore nella macchina virtuale di Azure è *azureuser*. Il file è destinato ad essere inserito nella directory */home/azureuser/*. Nel comando sostituire i valori predefiniti con quelli personali.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Copia sicura di una directory da una macchina virtuale Linux

In questo esempio si copia una directory di file di log dalla macchina virtuale Linux in una workstation. Poiché è possibile che un file di log contenga dati riservati, con SCP si ha la certezza che i contenuti dei file di log vengano crittografati. L'uso di SCP per il trasferimento dei file costituisce il modo più semplice per spostare in totale sicurezza file e directory di log in una workstation.

Il comando seguente consente di copiare i file contenuti nella directory */home/azureuser/logs/* nella directory /tmp locale della macchina virtuale di Azure:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

Il flag dell'interfaccia della riga di comando `-r` indica a SCP di copiare in modo ricorsivo i file e le directory dal punto della directory specificato nel comando.  Osservare inoltre come la sintassi della riga di comando sia simile al comando di copia `cp`.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire utenti, SSH e dischi di controllo o di ripristino in VM Linux di Azure tramite l'estensione VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Disabilitare le password SSH nella VM Linux configurando SSHD](mac-disable-ssh-password-usage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

