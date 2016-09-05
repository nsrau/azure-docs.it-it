<properties
		pageTitle="Aggiungere un utente a una macchina virtuale Linux in Azure | Microsoft Azure"
		description="Informazioni su come aggiungere un utente a una macchina virtuale Linux in Azure."
		services="virtual-machines-linux"
		documentationCenter=""
		authors="vlivech"
		manager="timlt"
		editor=""
		tags="azure-resource-manager"
/>

<tags
		ms.service="virtual-machines-linux"
		ms.workload="infrastructure-services"
		ms.tgt_pltfrm="vm-linux"
		ms.devlang="na"
		ms.topic="article"
		ms.date="08/18/2016"
		ms.author="v-livech"
/>  

# Aggiungere un utente a una macchina virtuale di Azure

Una delle prime attività su qualsiasi VM Linux consiste nel creare un nuovo utente. Questo articolo descrive la creazione di un account utente sudo, l'impostazione della password, l'aggiunta di chiavi pubbliche SSH e infine l'uso di `visudo` per consentire l'uso di sudo senza password.

Prerequisiti: [un account Azure](https://azure.microsoft.com/pricing/free-trial/), [chiavi pubbliche e private SSH](virtual-machines-linux-mac-create-ssh-keys.md), un gruppo di risorse di Azure e l’interfaccia della riga di comando di Azure installata e con la modalità Azure Resource Manager attivata tramite `azure config mode arm`.

## Comandi rapidi

```bash
# Add a new user on RedHat family distros
sudo useradd -G wheel exampleUser

# Add a new user on Debian family distros
sudo useradd -G sudo exampleUser

# Set a password
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully

# Copy the SSH Public Key to the new user
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver

# Change sudoers to allow no password
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL

# Verify everything
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```

## Procedura dettagliata

### Introduzione

Una delle prime e più comuni attività in un nuovo server consiste nell'aggiungere un account utente. Gli accessi radice devono essere disabilitati e l'account radice stesso non deve essere usato con il server Linux, solo sudo. L'assegnazione all'utente dei privilegi di escalation radice usando sudo rappresenta il metodo migliore per amministrare e usare Linux.

Usando il comando `useradd` vengono aggiunti gli account utente alla VM Linux. L'esecuzione di `useradd` modifica `/etc/passwd`, `/etc/shadow`, `/etc/group` e `/etc/gshadow`. Viene aggiunto un flag di riga di comando al comando `useradd` per aggiungere anche il nuovo utente al gruppo sudo corretto in Linux. Sebbene `useradd` crei una voce in `/etc/passwd`, il comando non assegna una password al nuovo account utente. Viene creata una password iniziale per il nuovo utente usando il semplice comando `passwd`. L'ultimo passaggio è costituito dalla modifica delle regole di sudo per consentire all'utente di eseguire comandi con i privilegi sudo senza dover immettere una password per ogni comando. L'accesso con la chiave pubblica fa presupporre che l'account utente sia privo di attori pericolosi, quindi viene consentito l'accesso a sudo senza password.

### Aggiunta di un singolo utente sudo a una macchina virtuale di Azure

Accedere alla VM di Azure usando le chiavi SSH. Se non è stato configurato l'accesso con chiave pubblica SSH, eseguire prima i passaggi descritti in [Uso dell'autenticazione con chiave pubblica con Azure](http://link.to/article).

Il comando `useradd` esegue le attività seguenti:

- creazione di un nuovo account utente
- creazione di un nuovo gruppo di utenti con lo stesso nome
- aggiunta di una voce vuota in `/etc/passwd`
- aggiunta di una voce vuota in `/etc/gpasswd`

Il flag della riga di comando `-G` aggiunge il nuovo account utente al gruppo Linux corretto assegnando al nuovo account utente i privilegi di escalation radice.

#### Aggiungere l'utente

```bash
# On RedHat family distros
sudo useradd -G wheel exampleUser

# On Debian family distros
sudo useradd -G sudo exampleUser
```

#### Impostare una password

Il comando `useradd` crea l'utente e aggiunge una voce in `/etc/passwd` e `/etc/gpasswd` ma non imposta effettivamente la password. La password viene aggiunta alla voce usando il comando `passwd`.

```bash
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

Un utente con privilegi sudo è ora disponibile nel server.

### Aggiungere la chiave pubblica SSH nel nuovo account utente

Nel computer usare il comando `ssh-copy-id` con la nuova password.

```bash
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver
```

### Uso di visudo per consentire l'utilizzo di sudo senza password

L'uso di `visudo` per la modifica del file `/etc/sudoers` aggiunge alcuni livelli di protezione dalla modifica non corretta di questo importante file. Dopo l'esecuzione di `visudo` il file `/etc/sudoers` viene bloccato per impedire che altri utenti apportino modifiche mentre viene modificato. Quando si tenta di salvare o uscire, `visudo` verifica anche che il file `/etc/sudoers` non contenga errori, quindi non è possibile salvare un file sudo corrotto.

Gli utenti sono già inseriti nel gruppo predefinito corretto per l'accesso a sudo. I gruppi vengono ora abilitati per l'uso di sudo senza password.

```bash
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL
```

### Verificare l'utente, le chiavi SSH e sudo

```bash
# Verify the SSH keys & User account
ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```

<!---HONumber=AcomDC_0824_2016-->