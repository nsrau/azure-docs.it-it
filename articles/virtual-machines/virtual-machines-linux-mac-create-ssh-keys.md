---
title: Creare una coppia di chiavi SSH per le macchine virtuali Linux in Azure | Documentazione Microsoft
description: Creare in modo sicuro una coppia di chiavi SSH pubblica e privata per le macchine virtuali di Linux in Azure.
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/6/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 6776fe5cf1faadfbcfc9bbd6c69ec1380c2429c7
ms.openlocfilehash: 67dce2c226f5ec5f396ef51cd59781d532d76795
ms.lasthandoff: 02/27/2017


---

# <a name="create-an-ssh-public-and-private-key-pair-for-linux-vms"></a>Creare una coppia di chiavi SSH pubblica e privata per le macchine virtuali di Linux

In questo articolo viene descritto come generare una coppia di chiavi SSH pubblica e privata da usare con le macchine virtuali di Linux.  Con una coppia di chiavi SSH è possibile creare macchine virtuali in Azure che per impostazione predefinita usano le chiavi SSH per l'autenticazione, eliminando la necessità di password per l'accesso.  Le password sono intuibili e le macchine virtuali sono esposte a inesorabili tentativi di attacchi massicci per scoprire la password. Le macchine virtuali create con i modelli di Azure o con `azure-cli` possono includere la chiave pubblica SSH durante la distribuzione, rimuovendo il passaggio della configurazione post-distribuzione di disattivazione degli account di accesso con password per SSH.

## <a name="quick-commands"></a>Comandi rapidi

Eseguire i comandi seguenti da un bash shell, sostituendo gli esempi con le proprie scelte.

Per impostazione predefinita, le chiavi SSH vengono conservate nella directory `~/.ssh`.  Se non si dispone di una directory `~/.ssh`, questa viene creata automaticamente dal comando `ssh-keygen` con le autorizzazioni corrette.  L'argomento `-N` specifica la password per crittografare la chiave SSH privata e *non* corrisponde alla password dell'utente.

```bash
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa -N mypassword
```

Aggiungere la chiave appena creata per `ssh-agent`:

```bash
ssh-add ~/.ssh/id_rsa
```

> [!NOTE] 
> I comandi precedenti funzionano nei sistemi operativi di Linux di quasi tutte le distribuzioni, ma non necessariamente in contenitori, perché l'ambiente può essere sottoposto a vincoli radicali.

## <a name="detailed-walkthrough"></a>Procedura dettagliata

L'uso di chiavi SSH pubbliche e private è il modo più semplice di accedere ai server Linux. [crittografia a chiave pubblica](https://en.wikipedia.org/wiki/Public-key_cryptography) fornisce un modo molto più sicuro per accedere a una VM Linux o BSD in Azure rispetto all'uso di password, che possono essere molto più facilmente soggette ad attacchi di forza bruta.

La chiave pubblica può essere condivisa con chiunque, ma la chiave privata appartiene solo all'utente o all'infrastruttura di sicurezza locale.  La chiave privata SSH deve essere protetta da una [password molto sicura](https://www.xkcd.com/936/) (origine: [xkcd.com](https://xkcd.com)).  Questa password serve solo per accedere alla chiave SSH privata e **non è** la password dell'account utente.  Quando si aggiunge una password alla chiave SSH, la chiave privata viene crittografata usando AES a 128 bit, in modo che non sia possibile usarla senza la password per decrittografarla.  Se un utente malintenzionato ruba una chiave privata priva di password, può usarla per accedere ai server che hanno la chiave pubblica corrispondente.  Una chiave privata protetta da password non può essere usata da utenti malintenzionati e rappresenta un livello di sicurezza aggiuntivo per l'infrastruttura in Azure.

Questo articolo consente di creare file delle chiavi nel formato *ssh-rsa* , consigliati per le distribuzioni in Resource Manager.  *ssh-rsa* sono necessarie nel [portale](https://portal.azure.com) per le distribuzioni sia classica che Resource Manager.

## <a name="disable-ssh-passwords-by-using-ssh-keys"></a>Disabilitare le password SSH usando le chiavi SSH

Azure richiede chiavi pubbliche e private nel formato ssh-rsa almeno a 2048 bit. Per creare le chiavi, usare `ssh-keygen`, che presenta una serie di domande e quindi scrive una chiave privata e una pubblica corrispondente. Quando viene creata una VM di Azure, la chiave pubblica viene copiata in `~/.ssh/authorized_keys`.  Le chiavi SSH in `~/.ssh/authorized_keys` vengono usate per fare in modo che il client trovi la chiave privata corrispondente in una connessione di accesso SSH.  Quando viene creata una macchina virtuale Linux di Azure usando le chiavi SSH per l'autenticazione, Azure configura il server SSHD per non consentire l'accesso con password, ma solo con le chiavi SSH.  La creazione di macchine virtuali Linux in Azure con chiavi SSH consente di proteggere la distribuzione delle VM e di evitare l'esecuzione del passaggio di post-distribuzione tipico relativo alla disabilitazione delle password nel file di configurazione sshd_config.

## <a name="using-ssh-keygen"></a>Uso di ssh-keygen

Questo comando crea una coppia di chiavi SSH protette da password (crittografate) usando RSA a 2048 bit impostata come commento per identificarla facilmente.  

Per impostazione predefinita, le chiavi SSH vengono conservate nella directory `~/.ssh`.  Se non si dispone di una directory `~/.ssh`, questa viene creata automaticamente dal comando `ssh-keygen` con le autorizzazioni corrette.

```bash
ssh-keygen \
-t rsa \
-b 2048 \
-C "ahmet@myserver" \
-f ~/.ssh/id_rsa \
-N mypassword
```

*Descrizione del comando*

`ssh-keygen`: programma usato per creare le chiavi.

`-t rsa` = tipo di chiave da creare, corrispondente al formato RSA [wikipedia](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048`: bit della chiave.

`-C "myusername@myserver"`: commento aggiunto alla fine del file della chiave pubblica per identificarla facilmente.  In genere come commento viene usato un indirizzo di posta elettronica, ma è possibile usare qualsiasi elemento, in base alle esigenze dell'infrastruttura.

## <a name="classic-portal-and-x509-certs"></a>Portale classico e certificati X.509

Se si usa il [portale classico](https://manage.windowsazure.com/) di Azure, sono necessari certificati X.509 per le chiavi SSH.  Non sono consentite altre chiavi pubbliche SSH, *devono* essere certificati X.509.

Per creare un certificato X.509 dalla chiave privata SSH-RSA:

```bash
openssl req -x509 \
-key ~/.ssh/id_rsa \
-nodes \
-days 365 \
-newkey rsa:2048 \
-out ~/.ssh/id_rsa.pem
```

## <a name="classic-deploy-using-asm"></a>Distribuzione classica tramite `asm`

Se si usa il modello di distribuzione classica (comando `asm` dell'interfaccia della riga di comando di gestione dei servizi di Azure), è possibile usare una chiave pubblica SSH-RSA o una chiave formattata RFC4716 in un contenitore PEM.  La chiave pubblica SSH-RSA corrisponde a quella creata in precedenza in questo articolo tramite `ssh-keygen`.

Per creare una chiave in formato RFC4716 da una chiave pubblica SSH esistente:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Esempio di ssh-keygen

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@myserver
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

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): ~/.ssh/id_rsa`

Nome della coppia di chiavi per questo articolo.  Una coppia di chiavi denominata **id_rsa** è disponibile per impostazione predefinita e alcuni strumenti potrebbero prevedere un file della chiave privata con nome **id_rsa**, quindi è opportuno averne uno. La directory `~/.ssh/` è la posizione predefinita per le coppie di chiavi SSH e il file config SSH.  Se non viene specificato con un percorso completo, `ssh-keygen` creerà le chiavi nella directory di lavoro corrente, non nel percorso `~/.ssh` predefinito.

Un listato della directory `~/.ssh` .

```bash
ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 rsa.pub
```

Password della chiave:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` fa riferimento a una password come "passphrase".  È *vivamente* consigliabile aggiungere una password alle coppie di chiavi. Senza una password che protegge la coppia di chiavi, chiunque abbia il file della chiave privata può usarlo per accedere a qualsiasi server che ha la chiave pubblica corrispondente. L'aggiunta di una password offre protezione nel caso in cui qualcuno riesca ad accedere al file della chiave privata, consentendo all'utente il tempo necessario per modificare le chiavi usate per l'autenticazione.

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

## <a name="using-ssh-copy-id-to-install-the-new-key"></a>Uso di `ssh-copy-id` per installare la nuova chiave
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

* [Creare una VM Linux protetta usando un modello di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una VM Linux protetta usando il portale di Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una VM Linux protetta usando l'interfaccia della riga di comando di Azure](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

