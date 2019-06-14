---
title: Procedura dettagliata - Coppia di chiavi SSH per VM Linux di Azure | Microsoft Docs
description: Informazioni sulla procedura dettagliata per creare e gestire una coppia di chiavi SSH pubblica e privata per le VM Linux in Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: 3784dd701b3ac44971e134f1b160fcfe2de2d9b3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328698"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Procedura dettagliata: Creare e gestire chiavi SSH per l'autenticazione in una macchina virtuale Linux in Azure 
Con una coppia di chiavi SSH (Secure Shell) è possibile creare una macchina virtuale Linux in Azure che per impostazione predefinita usa le chiavi SSH per l'autenticazione, eliminando la necessità di password per l'accesso. Le VM create con il portale di Azure, l'interfaccia della riga di comando di Azure, i modelli di Resource Manager o altri strumenti possono includere la chiave pubblica SSH come parte della distribuzione, configurando in questo modo l'autenticazione con chiave SSH per le connessioni SSH. 

Questo articolo riporta informazioni e procedure dettagliate per creare e gestire una coppia di file della chiave pubblica e privata RSA SSH per le connessioni client SSH. Per i comandi rapidi, vedere [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](mac-create-ssh-keys.md).

Per altri modi in cui generare e usare le chiavi SSH in un computer Windows, vedere [Come usare le chiavi SSH con Windows in Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Passphrase della chiave privata
La chiave privata SSH deve essere protetta da una passphrase molto sicura. Questa passphrase serve solo per accedere al file della chiave SSH privata e *non è* la password dell'account utente. Quando si aggiunge una passphrase alla chiave SSH, la chiave privata viene crittografata con AES a 128 bit, in modo che sia inutilizzabile senza la passphrase per decrittografarla. Se un utente malintenzionato riesce a sottrarre una chiave privata priva di passphrase, può usarla per accedere ai server che hanno la chiave pubblica corrispondente. Se una chiave privata è protetta da passphrase, non può essere usata da utenti malintenzionati e rappresenta un livello di sicurezza aggiuntivo per l'infrastruttura in Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Uso e vantaggi delle chiavi SSH

Quando si crea una VM di Azure specificando la chiave pubblica, Azure copia la chiave pubblica (nel formato `.pub`) nella cartella `~/.ssh/authorized_keys` nella VM. Le chiavi SSH in `~/.ssh/authorized_keys` vengono usate per fare in modo che il client trovi la chiave privata corrispondente in una connessione SSH. In una macchina virtuale Linux di Azure che usa chiavi SSH per l'autenticazione, Azure configura il server SSHD in modo da non consentire l'accesso con password, ma solo con le chiavi SSH. La creazione di una VM Linux in Azure con chiavi SSH consente di proteggere la distribuzione della VM e di evitare il passaggio tipico di post-distribuzione di disabilitare le password nel file `sshd_config`.

Se non si vogliono usare chiavi SSH, è possibile configurare la VM Linux in modo che usi l'autenticazione mediante password. Se la VM non è connessa a Internet, l'utilizzo di password può essere sufficiente. È comunque necessario gestire le password per ogni VM Linux e mantenere criteri e prassi di integrità per le password stesse, quali una lunghezza minima e l'aggiornamento periodico. L'uso delle chiavi SSH riduce la complessità di gestione delle credenziali individuali tra più VM.

## <a name="generate-keys-with-ssh-keygen"></a>Generare chiavi con ssh-keygen

Per creare le chiavi, un comando preferito è `ssh-keygen`, disponibile con le utilità OpenSSH in Azure Cloud Shell, un host macOS o Linux, il [sottosistema Windows per Linux](https://docs.microsoft.com/windows/wsl/about) e altri strumenti. `ssh-keygen` presenta una serie di domande e quindi scrive una chiave privata e una chiave pubblica corrispondente. 

Per impostazione predefinita, le chiavi SSH vengono conservate nella directory `~/.ssh`.  Se non si dispone di una directory `~/.ssh`, questa viene creata automaticamente dal comando `ssh-keygen` con le autorizzazioni corrette.

### <a name="basic-example"></a>Esempio di base

Il seguente comando `ssh-keygen` genera i file della chiave pubblica e privata SSH RSA a 2048 bit nella directory `~/.ssh` per impostazione predefinita. Se nel percorso corrente è presente una coppia di chiavi SSH, questi file vengono sovrascritti.

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>Esempio dettagliato
L'esempio seguente mostra opzioni di comando aggiuntive per creare una coppia di chiavi SSH RSA. Se nel percorso corrente è presente una coppia di chiavi SSH, questi file vengono sovrascritti. 

```bash
ssh-keygen \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Descrizione del comando**

`ssh-keygen`: programma usato per creare le chiavi.

`-t rsa` = tipo di chiave da creare, in questo caso nel formato RSA

`-b 4096` = numero di bit nella chiave, in questo caso 4096

`-C "azureuser@myserver"`: commento aggiunto alla fine del file della chiave pubblica per identificarla facilmente. Come commento viene usato in genere un indirizzo di posta elettronica, ma è possibile usare qualsiasi elemento, in base alle esigenze dell'infrastruttura.

`-f ~/.ssh/mykeys/myprivatekey` = il nome del file della chiave privata, se si sceglie di non usare il nome predefinito. Un file di chiave pubblica corrispondente con finale `.pub` viene generato nella stessa directory. La directory deve esistere.

`-N mypassphrase` = una passphrase aggiuntiva necessaria per accedere al file della chiave privata. 

### <a name="example-of-ssh-keygen"></a>Esempio di ssh-keygen

```bash
ssh-keygen -t rsa -b 2048 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 azureuser@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

#### <a name="saved-key-files"></a>I file delle chiavi salvati

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Nome della coppia di chiavi per questo articolo. Per impostazione predefinita viene creata una coppia di chiavi denominata `id_rsa`; alcuni strumenti potrebbero aspettarsi un file della chiave privata con il nome `id_rsa`, quindi è utile averlo. La directory `~/.ssh/` è la posizione predefinita per le coppie di chiavi SSH e il file config SSH. Se non viene specificato con un percorso completo, `ssh-keygen` crea le chiavi nella directory di lavoro corrente, non nel percorso `~/.ssh` predefinito.

#### <a name="list-of-the-ssh-directory"></a>Elenco della directory `~/.ssh`

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Passphrase della chiave

`Enter passphrase (empty for no passphrase):`

È *vivamente* consigliabile aggiungere una passphrase alla chiave privata. Senza una passphrase che protegga il file della chiave, chiunque abbia il file può usarlo per accedere a qualsiasi server che abbia la chiave pubblica corrispondente. L'aggiunta di una passphrase offre protezione nel caso in cui qualcuno riesca ad accedere al file della chiave privata, lasciando all'utente il tempo necessario per cambiare le chiavi.

## <a name="generate-keys-automatically-during-deployment"></a>Generare chiavi automaticamente durante la distribuzione

Se si usa l'[interfaccia della riga di comando di Azure](/cli/azure) per creare la VM, facoltativamente è possibile creare i file della chiave SSH pubblica e privata eseguendo il comando [creare az vm ](/cli/azure/vm) con l'opzione `--generate-ssh-keys`. Le chiavi vengono archiviate nella directory ~/.ssh. Si noti che questa opzione di comando non implica la sovrascrittura delle chiavi se sono già presenti in tale percorso.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Fornire la chiave SSH pubblica quando si distribuisce una VM

Per creare una VM Linux che usa le chiavi SSH per l'autenticazione, fornire la chiave SSH pubblica quando si crea la VM tramite il portale di Azure, l'interfaccia della riga di comando, i modelli di Resource Manager o altri metodi. Quando si usa il portale, immettere la chiave pubblica stessa. Se si usa l'[interfaccia della riga di comando di Azure](/cli/azure) per creare la macchina virtuale con una chiave pubblica esistente, specificare il valore o il percorso della chiave pubblica eseguendo il comando [creare az vm](/cli/azure/vm) con l'opzione `--ssh-key-value`. 

Se non si ha familiarità con il formato della chiave SSH pubblica, è possibile visualizzare la chiave pubblica eseguendo `cat` come segue, sostituendo `~/.ssh/id_rsa.pub` con il proprio percorso del file della chiave pubblica:

```bash
cat ~/.ssh/id_rsa.pub
```

L'output è simile al seguente (con alcune modifiche):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Se si copiano e si incollano i contenuti del file della chiave pubblica nel portale di Azure o in un modello di Resource Manager, verificare di non introdurre spazi o interruzioni di riga aggiuntive. Se ad esempio si usa macOS, è possibile inviare tramite pipe il file della chiave pubblica, che per impostazione predefinita è `~/.ssh/id_rsa.pub`, a **pbcopy** per copiare i contenuti. Anche altri programmi Linux eseguono questa operazione, ad esempio `xclip`.

Se si preferisce usare una chiave pubblica in un formato a più righe, è possibile generare una chiave RFC4716 formattata in un contenitore con estensione pem dalla chiave pubblica creata in precedenza.

Per creare una chiave in formato RFC4716 da una chiave pubblica SSH esistente:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>Connessione SSH alla VM con un client SSH
Con la chiave pubblica distribuita nella VM di Azure e la chiave privata nel sistema locale, stabilire una connessione SSH alla VM usando l'indirizzo IP o il nome DNS della VM. Sostituire *azureuser* e *myvm.westus.cloudapp.azure.com* nel comando seguente con il nome utente dell'amministratore e il nome di dominio completo (o indirizzo IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se durante la creazione della coppia di chiavi è stata specificata una passphrase, immetterla quando viene richiesta durante la procedura di accesso. Il server viene aggiunto alla cartella `~/.ssh/known_hosts` e non verrà chiesto di connettersi di nuovo finché la chiave pubblica nella VM di Azure non viene modificata o il nome server viene rimosso da `~/.ssh/known_hosts`.

Se la macchina virtuale usa i criteri di accesso JIT, è necessario richiedere l'accesso per connettersi alla macchina virtuale. Per altre informazioni sui criteri JIT, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Usare ssh-agent per archiviare la passphrase della chiave privata

Per evitare di digitare la passphrase del file della chiave privata a ogni accesso SSH, è possibile usare `ssh-agent` per memorizzare nella cache la passphrase del file della chiave privata. Se si usa un Mac, il portachiavi macOS archivia in modo sicuro le passphrase delle chiavi private quando si chiama `ssh-agent`.

Verificare e usare `ssh-agent` e `ssh-add` per fornire informazioni al sistema SSH sui file delle chiavi, in maniera che non sia necessario usare la passphrase in modo interattivo.

```bash
eval "$(ssh-agent -s)"
```

Ora aggiungere la chiave privata a `ssh-agent` usando il comando `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

La passphrase della chiave privata è ora archiviata in `ssh-agent`.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Uso di ssh-copy-id per copiare la chiave in una VM esistente
Se è già stata creata una VM, è possibile installare la nuova chiave SSH pubblica nella VM Linux con un comando simile al seguente:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Creare e configurare un file config SSH

Si può creare e configurare un file di configurazione SSH (`~/.ssh/config`) per velocizzare gli accessi e ottimizzare il comportamento del client SSH. 

L'esempio seguente mostra una semplice configurazione che è possibile usare per accedere rapidamente come utente a una macchina virtuale specifica usando la chiave privata SSH predefinita. 

### <a name="create-the-file"></a>Creare il file

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Modificare il file per aggiungere la nuova configurazione SSH

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Configurazione di esempio

Aggiungere le impostazioni di configurazione appropriate per la VM host.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

È possibile aggiungere configurazioni per host aggiuntivi per consentire a ciascuno di usare la propria coppia di chiavi dedicata. Per altre opzioni di configurazione avanzate, vedere [File di configurazione SSH](https://www.ssh.com/ssh/config/).

Con una coppia di chiavi SSH e un file config SSH configurato, è ora possibile accedere alla macchina virtuale Linux in modo rapido e sicuro. Quando si esegue il comando seguente, SSH individua e carica tutte le impostazioni dal blocco `Host myvm` nel file di configurazione SSH.

```bash
ssh myvm
```

La prima volta che si accede a un server usando una chiave SSH, il comando richiede la passphrase per il file della chiave.

## <a name="next-steps"></a>Passaggi successivi

Il prossimo passaggio consiste nel creare VM Linux di Azure usando la nuova chiave pubblica SSH. Le macchine virtuali di Azure create con una chiave pubblica SSH per l'accesso sono più protette rispetto alle macchine virtuali create con il metodo di accesso predefinito basato su password.

* [Creare una macchina virtuale Linux con il portale di Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una VM Linux usando un modello di Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
