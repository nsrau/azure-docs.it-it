---
title: Configurare SSHD nelle macchine virtuali Linux di Azure | Documentazione Microsoft
description: Configurare SSHD per le procedure di sicurezza consigliate e per bloccare SSH nelle macchine virtuali Linux di Azure.
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0195c385b00ab92b2b92ce8ff00983a0d91bf3a1
ms.lasthandoff: 04/03/2017


---

# <a name="configure-sshd-on-azure-linux-vms"></a>Configurare SSHD nelle macchine virtuali Linux di Azure

Questo articolo illustra come bloccare il server SSH in Linux, come fornire procedure di sicurezza consigliate e come accelerare l'accesso al server SSH con chiavi SSH anziché password.  Per bloccare SSHD viene disabilitata la capacità di accesso dell'utente root, vengono limitati gli utenti autorizzati ad accedere tramite l'elenco di gruppi approvati, viene disabilitata la versione 1 del protocollo SSH, viene impostato un numero minimo di bit per la chiave e infine configurata la disconnessione automatica degli utenti inattivi.  Prerequisiti necessari: un account Azure (è possibile [ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)) e i [file delle chiavi SSH pubbliche e private](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-commands"></a>Comandi rapidi

Configurare `/etc/ssh/sshd_config` con le seguenti impostazioni:

### <a name="disable-password-logins"></a>Disabilitare gli accessi tramite password

```bash
PasswordAuthentication no
```

### <a name="disable-login-by-the-root-user"></a>Disabilitare l'accesso per l'utente root

```bash
PermitRootLogin no
```

### <a name="allowed-groups-list"></a>Elenco di gruppi consentiti

```bash
AllowGroups wheel
```

### <a name="allowed-users-list"></a>Elenco di utenti consentiti

```bash
AllowUsers ahmet ralph
```

### <a name="disable-ssh-protocol-version-1"></a>Disabilitare la versione 1 del protocollo SSH

```bash
Protocol 2
```

### <a name="minimum-key-bits"></a>Bit minimi della chiave

```bash
ServerKeyBits 2048
```

### <a name="disconnect-idle-users"></a>Disconnettere gli utenti inattivi

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata

SSHD è il server SSH eseguito nella VM Linux.  SSH è un client eseguito da una shell nella workstation MacBook o Linux o tramite bash in Windows.  SSH è anche il protocollo usato per proteggere e crittografare le comunicazioni tra la workstation e la VM Linux, ed è pertanto anche una rete privata virtuale (VPN, Virtual Private Network).

Ai fini di questo articolo è molto importante mantenere aperto un accesso alla VM Linux per l'intera procedura.  Dopo aver stabilito la connessione SSH, la sessione rimane aperta fino a quando la finestra non viene chiusa.  La connessione di un terminale consente di apportare le modifiche al servizio SSHD senza rimanere bloccati se viene apportata una modifica di rilievo.  Se la VM Linux viene bloccata da una configurazione SSHD interrotta, Azure consente di reimpostare la configurazione SSHD interrotta con l'[estensione dell'accesso alle macchine virtuali di Azure](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per questo motivo vengono aperti due terminali che usano entrambi SSH per comunicare con la VM Linux.  Il primo terminale viene usato per apportare modifiche al file di configurazione SSHD e per riavviare il servizio SSHD.  Il secondo terminale viene usato per testare le modifiche apportate dopo il riavvio del servizio.  Poiché si eseguirà la disabilitazione delle password SSH e ci si affiderà esclusivamente alle chiavi SSH, se le chiavi SSH non sono corrette e si chiude la connessione alla VM, quest'ultima verrà definitivamente bloccata. Nessuno sarà più in grado di accedervi e la macchina virtuale dovrà essere eliminata e ricreata.

## <a name="disable-password-logins"></a>Disabilitare gli accessi tramite password

Il modo più rapido per proteggere la VM Linux è la disabilitazione degli accessi tramite password.  Quando gli accessi tramite password sono abilitati, i robot che circolano in rete tentano immediatamente di indovinare la password della VM Linux che usa SSH.  Disabilitando completamente l'accesso tramite password, il server SSH ignora tutti i tentativi di accesso di questo tipo.

```bash
PasswordAuthentication no
```

## <a name="disable-login-by-the-root-user"></a>Disabilitare l'accesso per l'utente root

In base alle procedure Linux consigliate, l'`root`utente non dovrebbe mai accedere tramite SSH o con `sudo su`.  Tutti i comandi che richiedono autorizzazioni root devono sempre essere eseguiti tramite il comando `sudo`, che registra ogni azione per eventuali controlli successivi.  La disabilitazione dell'accesso dell'utente `root` a SSH è una procedura di sicurezza consigliata che garantisce l'accesso a SSH ai soli utenti autorizzati.

```bash
PermitRootLogin no
```

## <a name="allowed-groups-list"></a>Elenco di gruppi consentiti

SSH offre un metodo per limitare gli utenti e i gruppi dotati o meno di autorizzazioni per l'accesso a SSH.  Questa funzionalità usa gli elenchi per concedere o negare l'accesso a utenti e gruppi specifici.  Inserendo il gruppo wheel nell'elenco `AllowGroups` si limitano gli accessi a SSH solo agli account utente presenti in tale gruppo.

```bash
AllowGroups wheel
```

## <a name="allowed-users-list"></a>Elenco di utenti consentiti

Limitare gli accessi a SSH solo agli utenti è un metodo più specifico rispetto ad `AllowGroups` per ottenere lo stesso risultato.  

```bash
AllowUsers ahmet ralph
```

## <a name="disable-ssh-protocol-version-1"></a>Disabilitare la versione 1 del protocollo SSH

La versione 1 del protocollo SSH non è sicura ed è consigliabile disabilitarla.  La versione 2 è la versione corrente, e offre una modalità di connessione SSH sicura al server.  Disabilitando la versione 1 di SSH si impedisce ai client SSH che tentano di connettersi al server SSH di usare la versione 1.  Solo le connessioni che avvengono tramite la versione 2 di SSH possono così negoziare la connessione al server SSH.

```bash
Protocol 2
```

## <a name="minimum-key-bits"></a>Bit minimi della chiave

Secondo le procedure di sicurezza consigliate, gli accessi tramite password SSH sono disattivati e l'autenticazione al server SSH è consentita solo tramite l'uso delle chiavi SSH.  È possibile creare le chiavi SSH usando chiavi di lunghezza diversa misurate in bit.  Le procedure di sicurezza consigliate indicano che la lunghezza minima per ottenere una complessità accettabile della chiave è di 2048 bit.  Chiavi inferiori a 2048 bit potrebbero teoricamente non funzionare.  Impostare `ServerKeyBits` su `2048` consente qualsiasi connessione che utilizza chiavi pari o superiori a 2048 bit e impedisce quelle con un numero di bit inferiori a 2048.

```bash
ServerKeyBits 2048
```

## <a name="disconnect-idle-users"></a>Disconnettere gli utenti inattivi

SSH consente di disconnettere gli utenti con connessioni aperte che restano inattivi per un numero di secondi superiore rispetto a quello stabilito.  Mantenere aperte le sessioni ai soli utenti attivi limita l'esposizione delle VM Linux.

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="restart-sshd"></a>Riavviare SSHD

Per abilitare le impostazioni in `/etc/ssh/sshd_config`, riavviare il processo SSHD, che a sua volta riavvia il server SSH.  La finestra del terminale usata per riavviare il server SSH resta aperta, senza perdere la sessione SSH aperta.  Per testare le nuove impostazioni del server SSH, usare una seconda finestra o scheda del terminale.  L'utilizzo di un terminale distinto per testare la connessione SSH consente di tornare indietro e apportare modifiche aggiuntive a `/etc/ssh/sshd_config`, senza rimanere bloccati se viene apportata una modifica di rilievo a SSHD.  

### <a name="on-redhat-centos-and-fedora"></a>Per Redhat, Centos e Fedora

```bash
service sshd restart
```

### <a name="on-debian--ubuntu"></a>Per Debian e Ubuntu

```bash
service ssh restart
```

## <a name="reset-sshd-using-azure-reset-access"></a>Reimpostare SSHD usando il comando reset-access di Azure

Se si viene bloccati da una modifica di rilievo alla configurazione SSHD è possibile usare l'estensione di accesso della macchina virtuale di Azure per ripristinare la configurazione SSHD a quella originale.

Sostituire i nomi nell'esempio con quelli personalizzati.

```azurecli
azure vm reset-access \
--resource-group myResourceGroup \
--name myVM \
--reset-ssh
```

## <a name="install-fail2ban"></a>Installare Fail2ban

È consigliabile installare e configurare l'applicazione open source Fail2ban, che blocca i tentativi ripetuti di accesso alla VM Linux tramite SSH con l'uso della forza bruta.  Fail2ban registra i ripetuti tentativi non riusciti di accedere tramite SSH e quindi crea regole firewall per bloccare l'indirizzo IP dal quale hanno origine gli attacchi.

* [Homepage di Fail2ban](http://www.fail2ban.org/wiki/index.php/Main_Page)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato e bloccato il server SSH nella VM Linux, è possibile eseguire alcune procedure di sicurezza aggiuntive.  

* [Gestire utenti, SSH e dischi di controllo o di ripristino in VM Linux di Azure tramite l'estensione VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [Crittografare i dischi di una VM Linux usando l'interfaccia della riga di comando di Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [Accesso e sicurezza nei modelli di Azure Resource Manager](dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

