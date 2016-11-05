---
title: Disabilitare le password SSH nella VM Linux configurando SSHD | Microsoft Docs
description: Proteggere la VM Linux in Azure disabilitando l'accesso tramite password per SSH.
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
ms.topic: article
ms.date: 08/26/2016
ms.author: v-livech

---
# Disabilitare le password SSH nella VM Linux configurando SSHD
In questo articolo viene illustrato come bloccare la sicurezza di accesso della VM Linux. Appena la porta SSH 22 viene aperta, i robot iniziano a tentare l'accesso mediante individuazione della password. In questo articolo si disabiliteranno gli accessi tramite password mediante SSH. Rimuovendo completamente la possibilità di usare password si protegge la VM Linux da questo tipo di attacco di forza bruta. Il vantaggio aggiuntivo è che l'SSHD Linux verrà configurato in modo da consentire l'accesso tramite chiave pubblica e privata SSH. Questo è di gran lunga il modo più sicuro per accedere a Linux. Il numero di possibili combinazioni per individuare la chiave privata è immenso, quindi rende inutile per i robot anche solo tentare un attacco di forza bruta alle chiavi SSH.

## Obiettivi
* Configurare SSHD per impedire:
  * Accessi tramite password
  * Accesso dell'utente ROOT
  * Autenticazione In attesa/Risposta
* Configurare SSHD per consentire:
  * solo accessi tramite chiave SSH
* Riavviare SSHD durante la connessione
* Testare la nuova configurazione SSHD

## Introduzione
[Definizione di SSH](https://en.wikipedia.org/wiki/Secure_Shell)

SSHD è il server SSH eseguito nella VM Linux. SSH è un client eseguito da una shell nella workstation MacBook o Linux. SSH è anche il protocollo usato per proteggere e crittografare le comunicazioni tra la workstation e la VM Linux.

Per questo articolo è molto importante mantenere aperto un accesso alla VM Linux per l'intera procedura. Per questo motivo verranno aperti due terminali che useranno entrambi SSH nelle comunicazioni con la VM Linux. Il primo terminale verrà usato per apportare modifiche al file di configurazione degli SSHD e riavviare il servizio SSHD. Il secondo terminal verrà usato per testare tali modifiche dopo il riavvio del servizio. Poiché si eseguirà la disabilitazione delle password SSH e ci si affiderà esclusivamente alle chiavi SSH, se le chiavi SSH non sono corrette e si chiude la connessione alla VM, quest'ultima verrà definitivamente bloccata. Nessuno sarà più in grado di accedervi e la macchina virtuale dovrà essere eliminata e ricreata.

## Prerequisiti
* [Creare chiavi SSH in Linux e Mac per le VM Linux in Azure](virtual-machines-linux-mac-create-ssh-keys.md)
* Account Azure
  * [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)
  * [Portale di Azure](http://portal.azure.com)
* VM Linux in esecuzione in Azure
* Coppia di chiavi SSH pubblica e privata in `~/.ssh/`
* Chiave pubblica SSH in `~/.ssh/authorized_keys` nella VM Linux
* Diritti sudo per la VM
* Porta 22 aperta

## Comandi rapidi
*Gli amministratori Linux esperti che vogliono solo la versione TLDR possono iniziare qui. Gli altri utenti che vogliono una spiegazione e una procedura dettagliate possono ignorare questa sezione.*

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no

# On the Debian Family
username@macbook$ sudo service ssh restart

# On the RedHat Family
username@macbook$ sudo service sshd restart
```

## Procedura dettagliata
Accedere alla VM Linux dal terminale 1 (T1). Accedere alla VM Linux dal terminale 2 (T2).

Da T2 verrà modificato il file di configurazione SSHD.

```
username@macbook$ sudo vim /etc/ssh/sshd_config
```

Da qui verranno modificate solo le impostazioni per la disabilitazione delle password e l'abilitazione dell'accesso tramite chiave SSH. In questo file esistono molte impostazioni da analizzare e modificare per garantire la sicurezza di Linux ed SSH corrispondente alle proprie esigenze.

#### Disabilitare gli accessi tramite password
```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### Abilitare l'autenticazione con chiave pubblica
```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### Disabilitare l'accesso root
```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin to this:
PermitRootLogin no
```

#### Disabilitare l'autenticazione In attesa/Risposta
```
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### Riavviare SSHD
Dalla shell di T1 verificare di essere ancora connessi. Questo è fondamentale per non rimanere esclusi dalla VM nel caso in cui le chiavi SSH non siano corrette, dato che ora le password sono disabilitate. In caso di errori nelle impostazioni della VM Linux, è possibile usare T1 per correggere sshd\_config, dato che si è ancora connessi ed SSH mantiene attiva la connessione durante il riavvio del servizio SSHD.

Da T2 eseguire:

##### Per la famiglia Debian
```
username@macbook$ sudo service ssh restart
```

##### Per la famiglia RedHat
```
username@macbook$ sudo service sshd restart
```

Le password sono ora disabilitate nella VM, che a questo punto è protetta dai tentativi di accesso tramite password da parte di attacchi di forza bruta. Con le sole chiavi SSH consentite è possibile effettuare l'accesso in modo più veloce e molto più sicuro.

<!---HONumber=AcomDC_0831_2016-->