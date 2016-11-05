---
title: Creare chiavi SSH in Linux e Mac | Microsoft Docs
description: Generare e utilizzare chiavi SSH su Linux e Mac per i modelli di Gestione risorse e quelli della distribuzione classica in Azure.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: timlt
editor: ''
tags: ''

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/06/2016
ms.author: v-livech

---
# <a name="create-ssh-keys-on-linux-and-mac-for-linux-vms-in-azure"></a>Creare chiavi SSH in Linux e Mac per le VM Linux in Azure
Con una coppia di chiavi SSH è possibile creare macchine virtuali in Azure che per impostazione predefinita usano le chiavi SSH per l'autenticazione, eliminando la necessità di password per l'accesso.  Le password sono intuibili e le VM sono esposte a inesorabili tentativi di attacchi di forza bruta per scoprire la password. Le VM create con i modelli di Azure o con `azure-cli` possono includere la chiave pubblica SSH durante la distribuzione, rimuovendo una configurazione post-distribuzione.  Se ci si connette a una VM Linux da Windows, vedere [questo documento](virtual-machines-linux-ssh-from-windows.md)

## <a name="quick-command-listing"></a>Breve elenco di comandi
Negli esempi di comandi seguenti sostituire i valori compresi tra &lt; e &gt; con i valori dell'ambiente in uso.  Passare prima di tutto alla directory `cd ~/.ssh/` in modo che tutte le chiavi SSH vengano create in tale directory.

```bash
ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"
```

Immettere il nome del file che viene salvato nella directory `~/.ssh/`:

```bash
<azure_fedora_id_rsa>
```

Immettere la passphrase per azure_fedora_id_rsa:

```bash
<correct horse battery staple>
```

Aggiungere la chiave appena creata a `ssh-agent` in Linux e Mac (aggiunta anche nel portachiavi di OSX):

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/azure_fedora_id_rsa
```

Copiare la chiave pubblica SSH nel server Linux:

```bash
ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>
```

Verificare l'accesso usando le chiavi invece di una password:

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## <a name="introduction"></a>Introduzione
L'uso di chiavi SSH pubbliche e private è il modo più semplice di accedere ai server Linux. [crittografia a chiave pubblica](https://en.wikipedia.org/wiki/Public-key_cryptography) fornisce un modo molto più sicuro per accedere a una VM Linux o BSD in Azure rispetto all'uso di password, che possono essere molto più facilmente soggette ad attacchi di forza bruta. La chiave pubblica può essere condivisa con chiunque, ma la chiave privata appartiene solo all'utente o all'infrastruttura di sicurezza locale.  La chiave privata SSH deve essere protetta da una [password molto sicura](https://www.xkcd.com/936/) (fonte: [xkcd.com](https://xkcd.com)).  Questa password serve solo per accedere alla chiave SSH privata e **non è** la password dell'account utente.  Quando si aggiunge una password alla chiave SSH, la chiave privata viene crittografata in modo che non sia utilizzabile senza la password per sbloccarla.  Se un utente malintenzionato ruba una chiave privata priva di password, può usarla per accedere ai server che hanno la chiave pubblica corrispondente.  Una chiave privata protetta da password non può essere usata da utenti malintenzionati e rappresenta un livello di sicurezza aggiuntivo per l'infrastruttura in Azure.

Questo articolo consente di creare file delle chiavi nel formato *ssh-rsa* , consigliati per le distribuzioni in Resource Manager.  *ssh-rsa* sono necessarie nel [portale](https://portal.azure.com) per le distribuzioni sia classica che Resource Manager.

## <a name="create-the-ssh-keys"></a>Creare le chiavi SSH
Azure richiede chiavi pubbliche e private nel formato ssh-rsa almeno a 2048 bit. Per creare le chiavi, usare `ssh-keygen`, che presenta una serie di domande e quindi scrive una chiave privata e una pubblica corrispondente. Quando viene creata una VM di Azure, la chiave pubblica viene copiata in `~/.ssh/authorized_keys`.  Le chiavi SSH in `~/.ssh/authorized_keys` vengono usate per fare in modo che il client trovi la chiave privata corrispondente in una connessione di accesso SSH.

## <a name="using-ssh-keygen"></a>Uso di ssh-keygen
Questo comando crea una coppia di chiavi SSH protette da password (crittografate) usando RSA a 2048 bit che viene impostata come commento per identificarla facilmente.  Passare prima di tutto alla directory `cd ~/.ssh/` in modo che tutte le chiavi SSH vengano create in tale directory.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

*Descrizione del comando*

`ssh-keygen`: programma usato per creare le chiavi.

`-t rsa` = tipo di chiave da creare, corrispondente al [formato RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048`: bit della chiave.

`-C "ahmet@fedoraVMAzure"`: commento aggiunto alla fine del file della chiave pubblica per identificarla facilmente.  In genere come commento viene usato un indirizzo di posta elettronica, ma è possibile usare qualsiasi elemento, in base alle esigenze dell'infrastruttura.

### <a name="using-pem-keys"></a>Uso di chiavi PEM
Se si usa il modello di distribuzione classica (portale di Azure classico o `asm` dell'interfaccia della riga di comando di Azure Service Management), potrebbe essere necessario usare chiavi SSH in formato PEM per accedere alle VM Linux.  Ecco come creare una chiave PEM da una chiave pubblica SSH esistente e da un certificato x509 esistente.

Per creare una chiave in formato PEM da una chiave pubblica SSH esistente:

```bash
ssh-keygen -f ~/.ssh/id_rsa.pub -e > ~/.ssh/id_ssh2.pem
```

## <a name="walkthrough-of-ssh-keygen"></a>Procedura dettagliata di ssh-keygen
Ogni passaggio è descritto in dettaglio.  Passare prima di tutto alla directory `~/.ssh` e quindi eseguire `ssh-keygen`.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@fedoraVMAzure
The key's randomart image is:
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

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa`

Nome della coppia di chiavi per questo articolo.  Una coppia di chiavi denominata **id_rsa** è disponibile per impostazione predefinita e alcuni strumenti potrebbero prevedere un file della chiave privata con nome **id_rsa**, quindi è opportuno averne uno. La directory `~/.ssh/` è la posizione predefinita per le coppie di chiavi SSH e il file config SSH.

```bash
ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```
Un listato della directory `~/.ssh` . `ssh-keygen` crea la directory `~/.ssh`, se non è presente, e imposta anche le modalità corrette per file e proprietà.

Password della chiave:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` fa riferimento a una password come "passphrase".  È *vivamente* consigliabile aggiungere una password alle coppie di chiavi. Senza una password che protegge la coppia di chiavi, chiunque abbia il file della chiave privata può usarlo per accedere a qualsiasi server che ha la chiave pubblica corrispondente. L'aggiunta di una password offre protezione nel caso in cui qualcuno riesca ad accedere al file della chiave privata, consentendo all'utente il tempo necessario per modificare le chiavi usate per l'autenticazione.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Uso di ssh-agent per archiviare la password della chiave privata
Per evitare di digitare la password del file della chiave privata a ogni accesso SSH, è possibile usare `ssh-agent` per memorizzare nella cache la password del file della chiave privata. Se si usa un Mac, il portachiavi OSX archivia in modo sicuro le password delle chiavi private quando si richiama `ssh-agent`.

Verificare prima di tutto che `ssh-agent` sia in esecuzione.

```bash
eval "$(ssh-agent -s)"
```

Ora aggiungere la chiave privata a `ssh-agent` usando il comando `ssh-add`.

```bash
ssh-add ~/.ssh/azure_fedora_id_rsa
```

La password della chiave privata viene ora archiviata in `ssh-agent`.

## <a name="create-and-configure-an-ssh-config-file"></a>Creare e configurare un file config SSH
È una procedura consigliata creare e configurare un file `~/.ssh/config` per velocizzare gli accessi e ottimizzare il comportamento del client SSH.

L'esempio seguente illustra una configurazione standard.

### <a name="create-the-file"></a>Creare il file
```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration:"></a>Modificare il file aggiungendo la nuova configurazione SSH:
```bash
vim ~/.ssh/config
```

### <a name="example-`~/.ssh/config`-file:"></a>File `~/.ssh/config` di esempio:
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
`Host` : nome dell'host chiamato sul terminale.  `ssh fedora22` indica a `SSH` di usare i valori nel blocco di impostazioni con l'etichetta `Host fedora22`. NOTA: può trattarsi di qualsiasi etichetta logica per il proprio utilizzo e non rappresenta il nome host effettivo di un server.

`Hostname 102.160.203.241`: indirizzo IP o nome DNS del server a cui si accede.

`User git`: account utente remoto da usare.

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

* [Creare una VM Linux protetta usando un modello di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
* [Creare una VM Linux protetta usando il portale di Azure](virtual-machines-linux-quick-create-portal.md)
* [Creare una VM Linux protetta usando l'interfaccia della riga di comando di Azure](virtual-machines-linux-quick-create-cli.md)

<!--HONumber=Oct16_HO2-->


