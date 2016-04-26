<properties
	pageTitle="Creare chiavi SSH in Linux e Mac | Microsoft Azure"
	description="Generare e utilizzare chiavi SSH su Linux e Mac per i modelli di Gestione risorse e quelli della distribuzione classica in Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/12/2016"
	ms.author="v-livech"/>

# Creare chiavi SSH in Linux e Mac per le VM Linux in Azure

Per creare una chiave pubblica e privata SSH protetta con password è necessario un terminale aperto nella workstation. Una volta disponibili le chiavi SSH, è possibile creare nuove VM con tale chiave per impostazione predefinita oppure aggiungere la chiave pubblica alle VM esistenti usando sia l'interfaccia della riga di comando di Azure che i modelli di Azure.

## Breve elenco di comandi

Negli esempi di comandi seguenti sostituire i valori compresi tra &lt; e &gt; con i valori dell'ambiente locale.

```bash
[ahmet@fedora ~]$ ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"

#Enter the name of the file that will be saved in the `~/.ssh/` directory.
<azure_fedora_id_rsa>

#Enter passphrase for azure_fedora_id_rsa:
<correct horse battery staple>

#Add the newly created key to `ssh-agent` on Linux and Mac (also added to OSX Keychain).
[ahmet@fedora ~]$ eval "$(ssh-agent -s)"
[ahmet@fedora ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa

#Copy the SSH public key to your Linux Server.
[ahmet@fedora ~]$ ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>

#Test the login using keys instead of a password.
[ahmet@fedora ~]$ ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>

Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
[ahmet@fedora ~]$

```

## Introduzione

L'uso di chiavi pubbliche e private SSH è il modo più semplice per accedere ai server Linux, ma la [crittografia a chiave pubblica](https://en.wikipedia.org/wiki/Public-key_cryptography) fornisce anche un modo molto più sicuro per l'accesso alla VM Linux o BSD in Azure rispetto alle password, che sono più facilmente soggette ad attacchi di forza bruta. La chiave pubblica può essere condivisa con chiunque, ma la chiave privata appartiene solo all'utente o all'infrastruttura di sicurezza locale. La chiave privata SSH creata avrà una [password sicura](https://www.xkcd.com/936/) per proteggerla. Questa password serve solo per accedere alla chiave SSH privata e **non è** la password dell'account utente. Chiunque sia in possesso di una chiave privata senza password può accedere a qualsiasi server in cui è installata la chiave pubblica. La chiave privata non può essere usata senza la password.


Questo articolo consente di creare file delle chiavi nel formato *ssh-rsa*, consigliati per distribuzioni in Azure Resource Manager e richiesti nel [portale](https://portal.azure.com) per le distribuzioni sia classica che Azure Resource Manager.


## Creare le chiavi SSH

Azure richiede chiavi pubbliche e private nel formato ssh-rsa almeno a 2048 bit. Per creare la coppia, si userà `ssh-keygen`, che presenta una serie di domande e quindi scrive una chiave privata e una pubblica corrispondente. Quando si crea la VM di Azure, si passa il contenuto della chiave pubblica, che viene copiato nella VM Linux e usato con la chiave privata locale e archiviata in modo sicuro per autenticare l'utente quando esegue l'accesso.

### Uso di `ssh-keygen`

Questo comando crea una coppia di chiavi SSH protette da password tramite RSA a 2048 bit che sarà impostata come commento per identificarla facilmente.

```
ahmet@fedora$ ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

##### Descrizione del comando

`ssh-keygen`: programma usato per creare le chiavi.

`-t rsa`: tipo di chiave da creare, ovvero il [formato RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)).

`-b 2048`: bit della chiave.

`-C "ahmet@fedoraVMAzure"`: commento aggiunto alla fine del file della chiave pubblica per identificarla facilmente. In genere come commento viene usato un indirizzo di posta elettronica, ma è possibile usare qualsiasi elemento, in base alle esigenze dell'infrastruttura.

#### Procedura dettagliata di `ssh-keygen`

```bash
ahmet@fedora$ ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
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

ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa` è il nome della coppia di chiavi per questo articolo. Avere una coppia di chiavi denominata **id\_rsa** è l'impostazione predefinita ed è possibile che alcuni strumenti prevedano un file della chiave privata con nome **id\_rsa**, quindi è opportuno averne uno. `~/.ssh/` è il percorso predefinito per tutte le coppie di chiavi SSH e il file config SSH.

`Enter passphrase (empty for no passphrase):` è consigliabile aggiungere una password (`ssh-keygen` la definisce "passphrase") alle coppie di chiavi. Senza una password che protegge la coppia di chiavi, chiunque disponga di una copia del file della chiave privata può usarlo per accedere a qualsiasi server dell'utente che ha la chiave pubblica corrispondente. L'aggiunta di una password offre quindi maggiore protezione nel caso in cui qualcuno riesca ad accedere al file della chiave privata, consentendo all'utente il tempo necessario per modificare le chiavi usate per l'autenticazione.

`ahmet@fedora$ ls -al ~/.ssh`: mostra le nuove coppie di chiavi e le relative autorizzazioni. `ssh-keygen` crea la directory `~/.ssh` se non è presente e imposta anche le modalità corrette per file e proprietà.

## Uso di ssh-agent per archiviare la password della chiave privata

Per evitare di digitare la password del file della chiave privata a ogni accesso SSH, è possibile usare `ssh-agent` per memorizzare nella cache la password del file della chiave privata consentendo accessi rapidi e sicuri alla VM Linux. Se si usa OSX, il portachiavi archivierà in modo sicuro le password delle chiavi private quando si richiama `ssh-agent`.

Verificare prima che `ssh-agent` sia in esecuzione.

`[ahmet@fedora ~]$ eval "$(ssh-agent -s)"`

Aggiungere ora la chiave privata a `ssh-agent` usando il comando `ssh-add`, che in OSX avvierà di nuovo il portachiavi che archivierà le credenziali.

`[ahmet@fedora ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa`

La password della chiave privata ora è archiviata e non sarà necessario digitare la password della chiave a ogni accesso SSH.

## Creare e configurare un file config SSH

Anche se non è assolutamente necessario iniziare subito a usare una VM Linux, è consigliabile creare e configurare un file `~/.ssh/config` per impedire l'uso accidentale di password per accedere alle VM, automatizzare l'uso di coppie di chiavi diverse per diverse VM di Azure e configurare anche altri programmi, ad esempio **git**, per connettersi a più server.

L'esempio seguente illustra una configurazione standard.

### Creare il file

```bash
ahmet@fedora$ touch ~/.ssh/config
```

### Modificare il file per aggiungere la nuova configurazione SSH

```bash
ahmet@fedora$ vim ~/.ssh/config

#Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
  PubkeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# GitHub keys
Host github.com
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
Host github.private
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/private_repo_azure_fedora_id_rsa
# ./Github Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /home/ahmet/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /home/ahmet/.ssh/id_rsa
  UseRoaming=no
```

Questo file config SSH include sezioni per ogni servizio, per consentire a ognuno di essi di avere una coppia di chiavi dedicata. Le impostazioni predefinite sono valide per tutti gli host a cui ci si connette e che non corrispondono ad alcuno dei gruppi indicati sopra. Il file config SSH consente anche di avere due account di accesso [GitHub](https://github.com) distinti, uno per le attività di lavoro pubbliche e un secondo solo per i repository privati che possono essere comuni nell'ambiente di lavoro.


##### Descrizione del file config

`Host`: nome dell'host chiamato sul terminale. `ssh fedora22` indica a `SSH` di usare i valori nel blocco di impostazioni con l'etichetta `Host fedora22` NOTA: può trattarsi di qualsiasi etichetta logica per uso personalizzato e non rappresenta il nome host effettivo di un server.

`Hostname 102.160.203.241`: indirizzo IP o nome DNS del server a cui si accede. Viene usato per il routing al server e può essere un indirizzo IP esterno che esegue il mapping a un indirizzo IP interno.

`User git`: account utente remoto da usare quando si accede alla VM di Azure.

`PubKeyAuthentication yes`: indica a SSH che si vuole usare una chiave SSH per l'accesso.

`IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa`: indica a SSH quale coppia di chiavi presentare al server per autenticare l'accesso.


## Usare SSH per accedere alla VM Linux senza password

Con una coppia di chiavi SSH e un file config SSH configurato è ora possibile accedere alla VM Linux in modo rapido e sicuro. La prima volta che si accede a un server con una chiave SSH, il comando richiede la passphrase per il file della chiave.

`ahmet@fedora$ ssh fedora22`

##### Descrizione del comando

Quando si esegue `ahmet@fedora$ ssh fedora22`, SSH trova e carica prima di tutto le impostazioni dal blocco `Host fedora22`, quindi carica tutte le impostazioni rimanenti dell'ultimo blocco `Host *`.

## Passaggi successivi

Ora è possibile usare i file delle chiavi SSH per:

- [Creare una VM Linux protetta usando un modello di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Creare una VM Linux protetta usando il portale di Azure](virtual-machines-linux-quick-create-portal.md)
- [Creare una VM Linux protetta usando l'interfaccia della riga di comando di Azure](virtual-machines-linux-quick-create-cli.md)

<!---HONumber=AcomDC_0420_2016-->