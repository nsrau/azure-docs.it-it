<properties 
   pageTitle="Uso di DNS dinamico per registrare i nomi host"
   description="Questa pagina offre informazioni dettagliate su come configurare DNS dinamico per registrare i nomi host nei propri server DNS."
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />

# Uso di DNS dinamico per registrare i nomi host
[Azure offre la risoluzione dei nomi](virtual-networks-name-resolution-for-vms-and-role-instances.md) per le VM e le istanze del ruolo. Quando la risoluzione dei nomi deve andare oltre quanto offerto da Azure, è possibile usare i propri server DNS. In questo modo è possibile personalizzare la soluzione DNS in base alle esigenze specifiche. Ad esempio, l'accesso al controller di dominio Active Directory locale.

Azure non è in grado (ad esempio, non offre le credenziali) di registrare direttamente i record nei server DNS, quindi è spesso necessario cercare soluzioni alternative. Di seguito sono riportati alcuni dettagli di alto livello per gli scenari più comuni.

## Client Windows ##
I client Windows non di dominio cercano di eseguire aggiornamenti DDNS non sicuri quando eseguono l'avvio o l'indirizzo IP viene modificato. Il nome DNS è il nome host unito al suffisso DNS primario. Azure lascia vuoto il suffisso DNS primario, ma può essere sovrascritto nella VM tramite l'[interfaccia utente](https://technet.microsoft.com/library/cc794784.aspx) o [con l'automazione](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

I client Windows appartenenti a un dominio registrano i relativi indirizzi IP con il controller di dominio usando DDNS sicuro. Il processo appartenente a un dominio imposta il suffisso DNS primario nel client e gestisce la relazione di trust.

## Client Linux ##
I client Linux solitamente non si registrano con il server DNS all'avvio, presuppongono che il server DHCP esegua l'operazione. Il pacchetto di binding fa parte di uno strumento denominato *nsupdate* che può essere usato per inviare aggiornamenti DDNS. Poiché il protocollo DDNS è standardizzato, *nsupdate* può essere usato anche quando non si usa il binding nel server DNS.

Il client DHCP Linux offre hook che consentono di eseguire script quando un indirizzo IP viene assegnato o modificato. Ad esempio, in */etc/dhcp/dhclient-exit-hooks.d/*. Questo può essere usato per registrare il nome host nel DNS. Ad esempio:
    
    	#!/bin/sh
    	requireddomain=mydomain.local
    
    	# only execute on the primary nic
    	if [ "$interface" != "eth0" ]
    	then
    		return
    	fi
    
		# when we have a new IP, perform nsupdate
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

		#done
		exit 0;

Anche il comando *nsupdate* può eseguire aggiornamenti DDNS sicuri. Ad esempio, quando si usa un server DNS di binding viene [generata](http://linux.yyz.us/nsupdate/) una coppia di chiavi pubbliche/private e il server DNS viene [configurato](http://linux.yyz.us/dns/ddns-server.html) con la parte pubblica della chiave, che consente di verificare la firma della richiesta. Per firmare la richiesta di aggiornamento DDNS, è necessario indicare la coppia di chiavi a *nsupdate* usando la relativa opzione *-k*.

Quando si usa un server DNS di Windows, usare l'autenticazione Kerberos con lo switch *-g* di nsupdate (non disponibile nella versione Windows di *nsupdate*). A questo scopo, usare *kinit* per caricare le credenziali (ad esempio da un [file keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)) e quindi *nsupdate -g* acquisirà le credenziali dalla cache.

Se necessario, aggiungere un suffisso di ricerca DNS alle VM. Il suffisso DNS è specificato nel file */etc/resolv.conf*. La maggior parte delle distribuzioni Linux gestiscono automaticamente il contenuto di questo file, quindi solitamente non può essere modificato. Tuttavia, il suffisso può essere sovrascritto usando il comando *supersede* del client DHCP. In */etc/dhcp/dhclient.conf* aggiungere:

		supersede domain-name <required-dns-suffix>;

<!---HONumber=AcomDC_0204_2016-->