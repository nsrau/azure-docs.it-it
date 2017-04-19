---
title: Spostamento di file da e verso una macchina virtuale Linux usando SCP | Documentazione Microsoft
description: Spostamento sicuro di file da e verso una macchina virtuale Linux usando SCP e una coppia di chiavi SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1935bde486d8f6f8e80a73447c4b0ea5d4671b45
ms.lasthandoff: 04/03/2017


---

# <a name="moving-files-to-and-from-a-linux-vm-using-scp"></a>Spostamento di file da e verso una macchina virtuale Linux usando SCP

Questo articolo illustra come spostare file da una workstation a una macchina virtuale Linux di Azure, o viceversa, usando Secure Copy (SCP).  A titolo di esempio, si sposteranno i file di configurazione di Azure in una macchina virtuale Linux e si scaricherà la directory di un file di log, in entrambi i casi usando SCP e chiavi SSH.   

Per questo articolo, i requisiti sono:

- [Un account di Azure](https://azure.microsoft.com/pricing/free-trial/)

- [File di chiavi SSH pubbliche e private](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Comandi rapidi

Copiare un file in una macchina virtuale Linux

```bash
scp file user@host:directory/targetfile
```

Copiare un file da una macchina virtuale Linux

```bash
scp user@host:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata

Il trasferimento di file tra una workstation e una macchina virtuale Linux in modo rapido e sicuro è un aspetto essenziale della gestione dell'infrastruttura di Azure.  Per la procedura descritta in questo articolo si userà SCP, uno strumento basato su SSH e incluso nella shell Bash predefinita di Linux, Mac e Windows.

## <a name="ssh-key-pair-authentication"></a>Autenticazione della coppia di chiavi SSH

SCP usa SSH per il livello di trasporto.  In questo modo, SSH può gestire l'autenticazione nell'host di destinazione e al tempo stesso spostare il file in un tunnel crittografato fornito per impostazione predefinita con SSH.  Per l'autenticazione SSH è possibile usare nomi utente e password, ma per una sicurezza ottimale è consigliabile usare l'autenticazione con chiavi SSH pubbliche e private. Dopo che SSH ha autenticato la connessione, SCP avvia il processo di copia del file.  Combinando un file `~/.ssh/config` correttamente configurato con chiavi SSH pubbliche e private, è possibile stabilire la connessione senza dover usare un nome utente ma solo un nome di server.  Se si ha una sola chiave SSH, SCP la cercherà nella directory `~/.ssh/` e, per impostazione predefinita, la userà per accedere alla macchina virtuale.

Per altre informazioni sulla configurazione del file `~/.ssh/config` e delle chiavi SSH pubbliche e private, vedere l'articolo [Creare chiavi SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>Copia sicura di un file in una macchina virtuale Linux

Nel primo esempio si copierà un file di credenziali di Azure in una macchina virtuale Linux usata per distribuire l'automazione.  Poiché questo file contiene credenziali di API di Azure, che includono informazioni riservate, la sicurezza è particolarmente importante e il tunnel crittografato fornito con SSH assicura la protezione dei contenuti del file.

```bash
scp ~/.azure/credentials myserver:/home/ahmet/.azure/credentials
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Copia sicura di una directory da una macchina virtuale Linux

In questo esempio si copierà una directory con molti file di log dalla macchina virtuale Linux in una workstation.  Poiché è possibile che un file di log contenga dati riservati, con SCP si ha la certezza che i contenuti dei file di log vengano crittografati.  L'uso di SCP per il trasferimento dei file costituisce il modo più semplice per spostare in totale sicurezza file e directory di log in una workstation.

```bash
scp -r myserver:/home/ahmet/logs/ /tmp/.
```

Il flag dell'interfaccia della riga di comando `-r` indica a SCP di copiare in modo ricorsivo i file e le directory dal punto della directory specificato nel comando.  Osservare inoltre come la sintassi della riga di comando sia simile al comando di copia `cp`.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire utenti, SSH e dischi di controllo o di ripristino in VM Linux di Azure tramite l'estensione VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Disabilitare le password SSH nella VM Linux configurando SSHD](mac-disable-ssh-password-usage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

