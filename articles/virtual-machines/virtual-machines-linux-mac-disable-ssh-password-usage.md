---
title: Disabilitare le password SSH nella VM Linux configurando SSHD | Microsoft Docs
description: Proteggere la VM Linux in Azure disabilitando l&quot;accesso tramite password per SSH.
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 46137640-a7d2-40e5-a1e9-9effef7eb190
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 5e5eb2a4821918e88ea3f566c7f3203dab5a68b9


---
# <a name="disable-ssh-passwords-on-your-linux-vm-by-configuring-sshd"></a>Disabilitare le password SSH nella VM Linux configurando SSHD
In questo articolo viene illustrato come bloccare la sicurezza di accesso della VM Linux.  Appena la porta SSH 22 viene aperta, i robot iniziano a tentare l'accesso mediante individuazione della password.  In questo articolo si disabiliteranno gli accessi tramite password mediante SSH.  Rimuovendo completamente la possibilità di usare password si protegge la VM Linux da questo tipo di attacco di forza bruta.  Il vantaggio aggiuntivo è che l'SSHD Linux verrà configurato in modo da consentire l'accesso tramite chiave pubblica e privata SSH. Questo è di gran lunga il modo più sicuro per accedere a Linux.  Il numero di possibili combinazioni per individuare la chiave privata è immenso, quindi rende inutile per i robot anche solo tentare un attacco di forza bruta alle chiavi SSH.

## <a name="goals"></a>Obiettivi
* Configurare SSHD per impedire:
  * Accessi tramite password
  * Accesso dell'utente ROOT
  * Autenticazione In attesa/Risposta
* Configurare SSHD per consentire:
  * solo accessi tramite chiave SSH
* Riavviare SSHD durante la connessione
* Testare la nuova configurazione SSHD

## <a name="introduction"></a>Introduzione
[Definizione di SSH](https://en.wikipedia.org/wiki/Secure_Shell)

SSHD è il server SSH eseguito nella VM Linux.  SSH è un client eseguito da una shell nella workstation MacBook o Linux.  SSH è anche il protocollo usato per proteggere e crittografare le comunicazioni tra la workstation e la VM Linux.

Per questo articolo è molto importante mantenere aperto un accesso alla VM Linux per l'intera procedura.  Per questo motivo verranno aperti due terminali che useranno entrambi SSH nelle comunicazioni con la VM Linux.  Il primo terminale verrà usato per apportare modifiche al file di configurazione degli SSHD e riavviare il servizio SSHD.  Il secondo terminal verrà usato per testare tali modifiche dopo il riavvio del servizio.  Poiché si eseguirà la disabilitazione delle password SSH e ci si affiderà esclusivamente alle chiavi SSH, se le chiavi SSH non sono corrette e si chiude la connessione alla VM, quest'ultima verrà definitivamente bloccata. Nessuno sarà più in grado di accedervi e la macchina virtuale dovrà essere eliminata e ricreata.

## <a name="prerequisites"></a>Prerequisiti
* [Creare chiavi SSH in Linux e Mac per le VM Linux in Azure](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Account Azure
  * [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)
  * [Portale di Azure](http://portal.azure.com)
* VM Linux in esecuzione in Azure
* Coppia di chiavi SSH pubblica e privata in `~/.ssh/`
* Chiave pubblica SSH in `~/.ssh/authorized_keys` nella VM Linux
* Diritti sudo per la VM
* Porta 22 aperta

## <a name="quick-commands"></a>Comandi rapidi
*Gli amministratori Linux esperti che vogliono solo la versione TLDR possono iniziare qui.  Gli altri utenti che vogliono una spiegazione e una procedura dettagliate possono ignorare questa sezione.*

```bash
sudo vim /etc/ssh/sshd_config
```

Modificare il file di configurazione come segue:

```sh
# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

Riavviare il servizio SSHD. Nelle distribuzioni basate su Debian:

```bash
sudo service ssh restart
```

Nelle distribuzioni basate su Red Hat:

```bash
sudo service sshd restart
```

## <a name="detailed-walk-through"></a>Procedura dettagliata
Accedere alla VM Linux dal terminale 1 (T1).  Accedere alla VM Linux dal terminale 2 (T2).

Da T2 verrà modificato il file di configurazione SSHD.  

```bash
sudo vim /etc/ssh/sshd_config
```

Da qui verranno modificate solo le impostazioni per la disabilitazione delle password e l'abilitazione dell'accesso tramite chiave SSH.  In questo file esistono molte impostazioni da analizzare e modificare per garantire la sicurezza di Linux ed SSH corrispondente alle proprie esigenze.

#### <a name="disable-password-logins"></a>Disabilitare gli accessi tramite password

```sh
# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### <a name="enable-public-key-authentication"></a>Abilitare l'autenticazione con chiave pubblica

```sh
# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### <a name="disable-root-login"></a>Disabilitare l'accesso root

```sh
# Change PermitRootLogin to this:
PermitRootLogin no
```

#### <a name="disable-challenge-response-authentication"></a>Disabilitare l'autenticazione In attesa/Risposta
```sh
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### <a name="restart-sshd"></a>Riavviare SSHD
Dalla shell di T1 verificare di essere ancora connessi.  Questo è fondamentale per non rimanere esclusi dalla VM nel caso in cui le chiavi SSH non siano corrette, dato che ora le password sono disabilitate.  In caso di errori nelle impostazioni della VM Linux, è possibile usare T1 per correggere sshd_config, dato che si è ancora connessi ed SSH mantiene attiva la connessione durante il riavvio del servizio SSHD.

Da T2 eseguire:

##### <a name="on-the-debian-family"></a>Per la famiglia Debian
```bash
sudo service ssh restart
```

##### <a name="on-the-redhat-family"></a>Per la famiglia RedHat
```bash
sudo service sshd restart
```

Le password sono ora disabilitate nella VM, che a questo punto è protetta dai tentativi di accesso tramite password da parte di attacchi di forza bruta.  Con le sole chiavi SSH consentite è possibile effettuare l'accesso in modo più veloce e molto più sicuro.




<!--HONumber=Nov16_HO3-->


