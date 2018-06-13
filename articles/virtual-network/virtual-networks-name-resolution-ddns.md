---
title: Uso di DNS dinamico per registrare i nomi host in Azure | Microsoft Docs
description: Informazioni su come configurare DNS dinamico per registrare i nomi host nei propri server DNS.
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: bbbce45b7c321fd4934374c76f2a4421b125d46f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
ms.locfileid: "31600955"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Usare DNS dinamico per registrare i nomi host nel proprio server DNS

[Azure offre la risoluzione dei nomi](virtual-networks-name-resolution-for-vms-and-role-instances.md) per le macchine virtuali (VM) e le istanze del ruolo. Quando la risoluzione dei nomi offerta dal server DNS predefinito di Azure non è sufficiente, è possibile usare i propri server DNS. L'uso di propri server DNS consente di personalizzare la soluzione DNS in base alle esigenze specifiche. Ad esempio, può essere necessario accedere a risorse locali tramite il controller di dominio Active Directory.

Quando i server DNS personalizzati vengono ospitati come VM di Azure, è possibile inoltrare query per i nomi host per la stessa rete virtuale ad Azure per risolvere i nomi host. Se non si vuole usare questa opzione, è possibile registrare i nomi host delle VM nel server DNS tramite DNS dinamico (DDNS). Poiché Azure non ha le credenziali per creare direttamente i record nei server DNS, è spesso necessario ricorrere a soluzioni alternative. Di seguito sono riportati alcuni scenari comuni con alternative:

## <a name="windows-clients"></a>Client Windows
I client Windows non di dominio cercano di eseguire aggiornamenti DDNS non protetti all'avvio o alla modifica dell'indirizzo IP. Il nome DNS è il nome host unito al suffisso DNS primario. Azure lascia vuoto il suffisso DNS primario, ma è possibile impostare il suffisso nella VM tramite l'[interfaccia utente](https://technet.microsoft.com/library/cc794784.aspx) o [PowerShell](/powershell/module/dnsclient/set-dnsclient).

I client Windows appartenenti a un dominio registrano i relativi indirizzi IP con il controller di dominio usando DDNS sicuro. Il processo appartenente a un dominio imposta il suffisso DNS primario nel client e crea e gestisce la relazione di trust.

## <a name="linux-clients"></a>Client Linux
I client Linux solitamente non si registrano con il server DNS all'avvio, presuppongono che il server DHCP esegua l'operazione. I server DHCP di Azure non hanno le credenziali per registrare i record nel server DNS. È possibile usare uno strumento denominato `nsupdate`, incluso nel pacchetto Bind, per inviare gli aggiornamenti del DDNS. Poiché il protocollo DDNS è standardizzato, è possibile usare `nsupdate` anche quando non si usa il pacchetto Bind sul server DNS.

È possibile usare gli hook messi a disposizione dal client DHCP per creare e gestire il nome host nel server DNS. Durante il ciclo DHCP, il client esegue gli script in */etc/dhcp/dhclient-exit-hooks.d/*. È possibile usare gli hook per registrare il nuovo indirizzo IP usando `nsupdate`. Ad esempio: 

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

Si può anche usare il comando `nsupdate` per eseguire gli aggiornamenti di DDNS sicuro. Ad esempio, quando si usa un server DNS con Bind, viene [generata](http://linux.yyz.us/nsupdate/)una coppia di chiavi pubblica/privata. Il server DNS è [configurato](http://linux.yyz.us/dns/ddns-server.html) con la parte pubblica della chiave, che consente di verificare la firma della richiesta. Per fornire la coppia di chiavi a `nsupdate`, usare l'opzione `-k` per la richiesta di aggiornamento DDNS da firmare.

Quando si usa un server DNS Windows, si può usare l'autenticazione Kerberos con il parametro `-g` in `nsupdate`, ma non è disponibile nella versione Windows di `nsupdate`. Per usare Kerberos, caricare le credenziali con `kinit`. Ad esempio, è possibile caricare le credenziali da un [file keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html), quindi `nsupdate -g` preleva le credenziali dalla cache.

Se necessario, aggiungere un suffisso di ricerca DNS alle VM. Il suffisso DNS è specificato nel file */etc/resolv.conf* . La maggior parte delle distribuzioni Linux gestiscono automaticamente il contenuto di questo file, quindi solitamente non può essere modificato. Tuttavia si può ignorare il suffisso usando il comando `supersede` del client DHCP. Per ignorare il suffisso, aggiungere la seguente riga al file */etc/dhcp/dhclient.conf*:

```
supersede domain-name <required-dns-suffix>;
```
