---
title: Procedura dettagliata per creare una coppia di chiavi SSH per le macchine virtuali Linux in Azure | Microsoft Docs
description: Passaggi aggiuntivi per creare una coppia di chiavi SSH pubblica e privata per le VM Linux in Azure, con certificati specifici per i diversi casi d'uso.
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 6/28/2017
ms.author: danlep
ms.openlocfilehash: 1308812287fa4484e244c47497a7aef7aa994b14
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2017
---
# <a name="detailed-walk-through-to-create-an-ssh-key-pair-and-additional-certificates-for-a-linux-vm-in-azure"></a>Procedura dettagliata per creare una coppia di chiavi SSH e certificati aggiuntivi per una VM Linux in Azure
Con una coppia di chiavi SSH è possibile creare macchine virtuali in Azure che per impostazione predefinita usano le chiavi SSH per l'autenticazione, eliminando la necessità di password per l'accesso. Le password sono intuibili e le macchine virtuali sono esposte a inesorabili tentativi di attacchi massicci per scoprire la password. Le macchine virtuali create con l'interfaccia della riga di comando di Azure o i modelli di Resource Manager possono includere la chiave pubblica SSH durante la distribuzione, rimuovendo il passaggio della configurazione post-distribuzione di disattivazione degli account di accesso con password per SSH. Questo articolo illustra i passaggi dettagliati e offre esempi aggiuntivi per la generazione di certificati, ad esempio per l'uso con macchine virtuali di Linux. Per creare rapidamente e usare una coppia di chiavi SSH, vedere [Come creare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](mac-create-ssh-keys.md).

## <a name="understanding-ssh-keys"></a>Informazioni sulle chiavi SSH

L'uso di chiavi SSH pubbliche e private è il modo più semplice di accedere ai server Linux. [crittografia a chiave pubblica](https://en.wikipedia.org/wiki/Public-key_cryptography) fornisce un modo molto più sicuro per accedere a una VM Linux o BSD in Azure rispetto all'uso di password, che possono essere molto più facilmente soggette ad attacchi di forza bruta.

La chiave pubblica può essere condivisa con chiunque, ma la chiave privata appartiene solo all'utente o all'infrastruttura di sicurezza locale.  La chiave privata SSH deve essere protetta da una [password molto sicura](https://www.xkcd.com/936/) (origine: [xkcd.com](https://xkcd.com)).  Questa password serve solo per accedere al file della chiave SSH privata e **non è** la password dell'account utente.  Quando si aggiunge una password alla chiave SSH, la chiave privata viene crittografata usando AES a 128 bit, in modo che non sia possibile usarla senza la password per decrittografarla.  Se un utente malintenzionato ruba una chiave privata priva di password, può usarla per accedere ai server che hanno la chiave pubblica corrispondente.  Una chiave privata protetta da password non può essere usata da utenti malintenzionati e rappresenta un livello di sicurezza aggiuntivo per l'infrastruttura in Azure.

Questo articolo crea una coppia di file di chiavi pubblica e privata RSA protocollo SSH versione 2 (definite anche chiavi "ssh-rsa"), consigliate per le distribuzioni con Azure Resource Manager. *ssh-rsa* sono necessarie nel [portale](https://portal.azure.com) per le distribuzioni sia classica che Resource Manager.

## <a name="ssh-keys-use-and-benefits"></a>Uso e vantaggi delle chiavi SSH

Azure richiede almeno chiavi pubbliche e private in formato RSA protocollo SSH versione 2 a 2048 bit. Il file di chiave pubblica ha il formato di contenitore `.pub`. Per creare le chiavi, usare `ssh-keygen`, che presenta una serie di domande e quindi scrive una chiave privata e una pubblica corrispondente. Quando viene creata una VM di Azure, Azure copia la chiave pubblica nella cartella `~/.ssh/authorized_keys` sulla VM. Le chiavi SSH in `~/.ssh/authorized_keys` vengono usate per fare in modo che il client trovi la chiave privata corrispondente in una connessione di accesso SSH.  Quando viene creata una macchina virtuale Linux di Azure usando le chiavi SSH per l'autenticazione, Azure configura il server SSHD per non consentire l'accesso con password, ma solo con le chiavi SSH.  La creazione di macchine virtuali Linux in Azure con chiavi SSH consente di proteggere la distribuzione delle VM e di evitare l'esecuzione del passaggio di post-distribuzione tipico relativo alla disabilitazione delle password nel file **sshd_config**.

## <a name="using-ssh-keygen"></a>Uso di ssh-keygen

Questo comando crea una coppia di chiavi SSH protette da password (crittografate) usando RSA a 2048 bit impostata come commento per identificarla facilmente.  

Per impostazione predefinita, le chiavi SSH vengono conservate nella directory `~/.ssh`.  Se non si dispone di una directory `~/.ssh`, questa viene creata automaticamente dal comando `ssh-keygen` con le autorizzazioni corrette.

```bash
ssh-keygen \
    -t rsa \
    -b 2048 \
    -C "azureuser@myserver" \
    -f ~/.ssh/id_rsa \
    -N mypassword
```

*Descrizione del comando*

`ssh-keygen`: programma usato per creare le chiavi.

`-t rsa` = tipo di chiave da creare, in [formato RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem))
`-b 2048` = bit della chiave

`-C "azureuser@myserver"`: commento aggiunto alla fine del file della chiave pubblica per identificarla facilmente.  In genere come commento viene usato un indirizzo di posta elettronica, ma è possibile usare qualsiasi elemento, in base alle esigenze dell'infrastruttura.

## <a name="classic-deploy-using-asm"></a>Distribuzione classica tramite `asm`

Se si usa il modello di distribuzione classica (modalità `asm` nell'interfaccia della riga di comando), è possibile usare una chiave pubblica SSH-RSA o una chiave formattata RFC4716 in un contenitore PEM.  La chiave pubblica SSH-RSA corrisponde a quella creata in precedenza in questo articolo tramite `ssh-keygen`.

Per creare una chiave in formato RFC4716 da una chiave pubblica SSH esistente:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Esempio di ssh-keygen

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

File delle chiavi salvati:

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Nome della coppia di chiavi per questo articolo.  Una coppia di chiavi denominata **id_rsa** è disponibile per impostazione predefinita e alcuni strumenti potrebbero prevedere un file della chiave privata con nome **id_rsa**, quindi è opportuno averne uno. La directory `~/.ssh/` è la posizione predefinita per le coppie di chiavi SSH e il file config SSH.  Se non viene specificato con un percorso completo, `ssh-keygen` crea le chiavi nella directory di lavoro corrente, non nel percorso `~/.ssh` predefinito.

Un listato della directory `~/.ssh` .

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

Password della chiave:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` indica una password per il file di chiave privata come "passphrase".  È *vivamente* consigliabile aggiungere una password alla chiave privata. Senza una password che protegge il file di chiave, chiunque abbia il file può usarlo per accedere a qualsiasi server che ha la chiave pubblica corrispondente. L'aggiunta di una password (passphrase) offre protezione nel caso in cui qualcuno riesca ad accedere al file della chiave privata, consentendo all'utente il tempo necessario per modificare le chiavi usate per l'autenticazione.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Uso di ssh-agent per archiviare la password della chiave privata

Per evitare di digitare la password del file della chiave privata a ogni accesso SSH, è possibile usare `ssh-agent` per memorizzare nella cache la password del file della chiave privata. Se si usa un Mac, il portachiavi OSX archivia in modo sicuro le password delle chiavi private quando si richiama `ssh-agent`.

Verificare e usare ssh-agent e ssh-add per fornire informazioni al sistema SSH sui file delle chiavi, in modo che non sia necessario usare la passphrase in modo interattivo.

```bash
eval "$(ssh-agent -s)"
```

Ora aggiungere la chiave privata a `ssh-agent` usando il comando `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

La password della chiave privata viene ora archiviata in `ssh-agent`.

## <a name="using-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Uso di `ssh-copy-id` per copiare la chiave in una VM esistente
Se è già stata creata una macchina virtuale, è possibile installare la nuova chiave SSH pubblica nella VM Linux con:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ahmet@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Creare e configurare un file config SSH

È una procedura consigliata creare e configurare un file `~/.ssh/config` per velocizzare gli accessi e ottimizzare il comportamento del client SSH.

L'esempio seguente illustra una configurazione standard.

### <a name="create-the-file"></a>Creare il file

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Modificare il file aggiungendo la nuova configurazione SSH:

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>File `~/.ssh/config` di esempio:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Questo file config SSH include sezioni per ogni server, per consentire a ognuno di essi di avere una coppia di chiavi dedicata. Le impostazioni predefinite (`Host *`) sono valide per tutti gli host che non corrispondono ad alcuno degli host specifici indicati sopra nel file config.

### <a name="config-file-explained"></a>Descrizione del file config

`Host` : nome dell'host chiamato sul terminale.  `ssh fedora22` indica a `SSH` di usare i valori nel blocco di impostazioni con l'etichetta `Host fedora22`. NOTA: l'Host può essere qualsiasi etichetta logica per il proprio uso e non rappresenta il nome host effettivo di un server.

`Hostname 102.160.203.241`: indirizzo IP o nome DNS del server a cui si accede.

`User ahmet` = account utente remoto da usare quando si accede al server.

`PubKeyAuthentication yes`: indica a SSH che si vuole usare una chiave SSH per l'accesso.

`IdentityFile /home/ahmet/.ssh/id_id_rsa`: chiave privata SSH e chiave pubblica corrispondente da usare per l'autenticazione.

## <a name="ssh-into-linux-without-a-password"></a>Usare SSH per accedere a Linux senza password

Con una coppia di chiavi SSH e un file config SSH configurato è ora possibile accedere alla VM Linux in modo rapido e sicuro. La prima volta che si accede a un server con una chiave SSH, il comando richiede la passphrase per il file della chiave.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Descrizione del comando

Quando si esegue `ssh fedora22`, SSH trova e carica prima di tutto le impostazioni dal blocco `Host fedora22` e quindi carica tutte le impostazioni rimanenti dell'ultimo blocco `Host *`.

## <a name="next-steps"></a>Passaggi successivi

Il prossimo passaggio consiste nel creare VM Linux di Azure usando la nuova chiave pubblica SSH.  Le VM di Azure create con una chiave pubblica SSH come account di accesso sono più protette rispetto alle VM create con il metodo di accesso predefinito basato su password.  Per impostazione predefinita, le VM di Azure create con le chiavi SSH vengono configurate con le password disabilitate, evitando tentativi di attacco basati su forza bruta per scoprire le password.

* [Creare una VM Linux protetta usando un modello di Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una VM Linux protetta usando il portale di Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una VM Linux protetta usando l'interfaccia della riga di comando di Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
