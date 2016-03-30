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
	ms.date="03/13/2016"
	ms.author="v-livech"/>

# Creare chiavi SSH in Linux e Mac per le VM Linux in Azure

Questo argomento illustra come:

1. Creare una coppia di chiavi private e pubbliche protette da password con `ssh-keygen`.
2. Creare un file `~/.ssh/config` per velocizzare l'accesso e abilitare importanti impostazioni predefinite di configurazione e sicurezza.
3. Accedere a una VM Linux o BSD in Azure tramite SSH

## Prerequisiti

Un account Azure ([ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)) e un terminale Mac o Linux con installati i toolkit SSH. Impostare l'interfaccia della riga di comando in modalità risorsa digitando `azure config mode arm`.

## Introduzione

L'uso di chiavi SSH pubbliche e private è il modo più sicuro **e** semplice di accedere ai server Linux. [Crittografia a chiave pubblica](https://en.wikipedia.org/wiki/Public-key_cryptography) fornisce un modo molto più sicuro per accedere a una VM Linux o BSD in Azure rispetto all'uso di password, che possono essere molto più facilmente soggette ad attacchi di forza bruta. La chiave pubblica può essere condivisa con chiunque. La chiave privata appartiene solo all'utente o all'infrastruttura di sicurezza locale.

Questo articolo crea file delle chiavi nel formato *ssh-rsa*, consigliati per distribuzioni in Gestione risorse e richieste nel [portale](https://portal.azure.com) per le distribuzioni sia classica che di Gestione risorse.

> [AZURE.NOTE] Se si accede alle VM da un computer Windows, vedere [Come usare le chiavi SSH in Windows](virtual-machines-linux-ssh-from-windows.md).

## Breve elenco di comandi

Negli esempi di comandi seguenti sostituire i valori compresi tra &lt; e &gt; con i valori dell'ambiente locale.

```bash
[username@fedora22 ~]$ ssh-keygen -t rsa -b 4096 -C "<your_user@yourdomain.com>"

#Enter the name of the file that will be saved in the `~/.ssh/` directory.

azure_fedora_id_rsa

#Enter (twice) a [secure](https://www.xkcd.com/936/) password for the SSH key.

#Enter passphrase for github_id_rsa:

correct horse battery staple

#Add the newly created key to `ssh-agent` on Linux and Mac (also added to OSX Keychain).

[username@fedora22 ~]$ eval "$(ssh-agent -s)"

[username@fedora22 ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa

#Copy the SSH public key to your Linux Server.

[username@fedora22 ~]$ ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>

#Test the login using keys instead of a password.

[username@fedora22 ~]$ ssh -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>

Last login: Tue Dec 29 07:07:09 2015 from 66.215.21.201
[username@fedora22 ~]$

```

## Creare le chiavi SSH

Azure richiede chiavi pubbliche e private nel formato ssh-rsa almeno a 2048 bit. Per creare la coppia, usare `ssh-keygen`, che presenta una serie di domande e quindi scrive una chiave privata e una chiave pubblica corrispondente. Quando si crea la VM di Azure, si passa il contenuto della chiave pubblica, che viene copiato nella VM Linux e usato con la chiave privata locale e archiviata in modo sicuro per autenticare l'utente quando esegue l'accesso.



### Uso di `ssh-keygen`

Questo comando crea una coppia di chiavi SSH tramite RSA a 2048 bit che sarà impostata come commento per identificarla facilmente.

    username@macbook$ ssh-keygen -t rsa -b 4096 -C "username@fedoraVMAzure"

##### Descrizione del comando

`ssh-keygen`: programma usato per creare le chiavi.

`-t rsa`: tipo di chiave da creare, ovvero il [formato RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)).

`-b 2048`: bit della chiave.

`-C "username@fedoraVMAzure"`: commento per identificare facilmente la chiave. Il commento viene aggiunto alla fine del file della chiave pubblica. Un commento di uso comune è un indirizzo di posta elettronica, ma in questo articolo si intende abilitare l'uso di più chiavi SSH, quindi viene suggerito un commento generico.

#### Procedura dettagliata di `ssh-keygen`

```bash
username@macbook$ ssh-keygen -t rsa -b 4096 -C "username@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/steve/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:79:ad:25:cc:65:e9:0c:07:e5:d1:a9:08 username@fedoraVMAzure
The key's randomart image is:
+--[ RSA 4096]----+
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

username@macbook$ ls -al ~/.ssh
-rw------- 1 username staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 username staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```

`Enter file in which to save the key (/Users/steve/.ssh/id_rsa): azure_fedora_id_rsa` è il nome della coppia di chiavi per questo articolo. Avere una coppia di chiavi denominata **id\_rsa** è l'impostazione predefinita ed è possibile alcuni strumenti prevedano un file della chiave privata con nome **id\_rsa**, quindi è opportuno averne uno. (`~/.ssh/` è il percorso predefinito per tutte le coppie di chiavi SSH e il file config SSH).

`Enter passphrase (empty for no passphrase):` è consigliabile aggiungere una password (`ssh-keygen` la definisce "passphrase") alle coppie di chiavi. Senza una password che protegge la coppia di chiavi, chiunque disponga di una copia del file della chiave privata può usarlo per accedere a qualsiasi server dell'utente che ha la chiave pubblica corrispondente. L'aggiunta di una password offre quindi maggiore protezione nel caso in cui qualcuno riesca ad accedere al file della chiave privata, consentendo all'utente il tempo necessario per modificare le chiavi usate per l'autenticazione.

`username@macbook$ ls -al ~/.ssh`: mostra le nuove coppie di chiavi e le relative autorizzazioni. `ssh-keygen` crea la directory `~/.ssh` se non è presente e imposta anche le modalità corrette per file e proprietà.

## Creare e configurare un file config SSH

Anche se non è assolutamente necessario iniziare subito a usare una VM Linux, è consigliabile creare e configurare un file `~/.ssh/config` per impedire l'uso accidentale di password per accedere alle VM, automatizzare l'uso di coppie di chiavi diverse per diverse VM di Azure e configurare anche altri programmi, ad esempio **git**, per connettersi a più server.

L'esempio seguente illustra una configurazione standard.

### Creare il file

```bash
username@macbook$ touch ~/.ssh/config
```

### Modificare il file per aggiungere la nuova configurazione SSH

```bash
username@macbook$ vim ~/.ssh/config

#Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User username
  PubkeyAuthentication yes
  IdentityFile /Users/steve/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# GitHub keys
Host github.com
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /Users/steve/.ssh/github_id_rsa
Host github.private
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /Users/steve/.ssh/private_repo_github_id_rsa
# ./Github Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /Users/steve/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /Users/steve/.ssh/id_rsa
  UseRoaming=no
```

Questo file config SSH include sezioni per ogni servizio, per consentire a ognuno di essi di avere una coppia di chiavi dedicata. Le impostazioni predefinite sono valide per tutti gli host a cui ci si connette e che non corrispondono ad alcuno dei gruppi indicati sopra. Il file config SSH consente anche di avere due account di accesso [GitHub](https://github.com), uno per le attività di lavoro pubbliche e un secondo solo per i repository privati che possono essere comuni nell'ambiente di lavoro.


##### Descrizione del file config

`Host`: nome dell'host chiamato sul terminale. `ssh fedora22` indica a `SSH` di usare i valori nel blocco di impostazioni con l'etichetta `Host fedora22` NOTA: può trattarsi di qualsiasi etichetta logica per uso personalizzato e non rappresenta il nome host effettivo di un server.

`Hostname 102.160.203.241`: indirizzo IP o nome DNS del server a cui si accede. Viene usato per il routing al server e può essere un indirizzo IP esterno che esegue il mapping a un indirizzo IP interno.

`User git`: account utente remoto da usare quando si accede alla VM di Azure.

`PubKeyAuthentication yes`: indica a SSH che si vuole usare una chiave SSH per l'accesso.

`IdentityFile /Users/steve/.ssh/azure_fedora_id_rsa`: indica a SSH quale coppia di chiavi presentare al server per autenticare l'accesso.


## Usare SSH per accedere alla VM Linux senza password

Con una coppia di chiavi SSH e un file config SSH configurato è ora possibile accedere alla VM Linux in modo rapido e sicuro. La prima volta che si accede a un server con una chiave SSH, il comando richiede la passphrase per il file della chiave.

`username@macbook$ ssh fedora22`

##### Descrizione del comando

Quando si esegue `username@macbook$ ssh fedora22`, SSH trova e carica prima di tutto le impostazioni dal blocco `Host fedora22`, quindi carica tutte le impostazioni rimanenti dell'ultimo blocco `Host *`.

## Passaggi successivi

A questo punto è possibile usare i file delle chiavi per [creare una VM Linux protetta in Azure usando un modello](virtual-machines-linux-create-ssh-secured-vm-from-template.md).

<!---HONumber=AcomDC_0323_2016-->