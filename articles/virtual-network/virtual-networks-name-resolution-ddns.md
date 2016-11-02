<properties
   pageTitle="Uso di DNS dinamico per registrare i nomi host"
   description="Questa pagina offre informazioni dettagliate su come configurare DNS dinamico per registrare i nomi host nei propri server DNS."
   services="dns"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="" />
<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# Uso di DNS dinamico per registrare i nomi host nel proprio server DNS

[Azure offre la risoluzione dei nomi](virtual-networks-name-resolution-for-vms-and-role-instances.md) per le macchine virtuali e le istanze del ruolo. Tuttavia, quando la risoluzione dei nomi offerta da Azure non è sufficiente, è possibile usare i propri server DNS. In questo modo è possibile personalizzare la soluzione DNS in base alle esigenze specifiche. Ad esempio, può essere necessario accedere a risorse locali tramite il controller di dominio Active Directory.

Quando i server DNS personalizzati vengono ospitati come VM di Azure è possibile inoltrare le query di nome host per la stessa rete virtuale in Azure per risolvere i nomi host. Se non si desidera usare questa route, è possibile registrare i nomi host delle VM nel server DNS tramite DNS dinamico. Poiché Azure non è in grado (ad esempio, non offre le credenziali) di creare direttamente i record nei server DNS, è spesso necessario ricorrere a soluzioni alternative. Di seguito sono riportati alcuni scenari comuni con alternative.

## Client Windows

I client Windows non di dominio cercano di eseguire aggiornamenti DNS dinamici non protetti (DDNS) all'avvio o alla modifica dell'indirizzo IP. Il nome DNS è il nome host unito al suffisso DNS primario. Azure lascia vuoto il suffisso DNS primario, ma può essere impostato nella VM tramite l'[interfaccia utente](https://technet.microsoft.com/library/cc794784.aspx) o [con l'automazione](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

I client Windows appartenenti a un dominio registrano i relativi indirizzi IP con il controller di dominio usando DNS dinamico protetto. Il processo appartenente a un dominio imposta il suffisso DNS primario nel client e crea e gestisce la relazione di trust.

## Client Linux

I client Linux solitamente non si registrano con il server DNS all'avvio, presuppongono che il server DHCP esegua l'operazione. I server DHCP di Azure non sono in grado o non dispongono delle credenziali per registrare i record nel server DNS. È possibile usare uno strumento denominato *nsupdate*, incluso nel pacchetto di binding, per inviare gli aggiornamenti del DNS dinamico. Poiché il protocollo DNS dinamico è standardizzato, è possibile usare *nsupdate* anche quando non si usa il pacchetto Bind sul server DNS.

È possibile usare gli hook messi a disposizione dal client DHCP per creare e gestire il nome host nel server DNS. Durante il ciclo DHCP, il client esegue gli script in */etc/dhcp/dhclient-exit-hooks.d/*. Questo può essere usato per registrare il nuovo indirizzo IP tramite *nsupdate*. Ad esempio:

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

È anche possibile usare il comando *nsupdate* per eseguire gli aggiornamenti dinamici protetti DNS. Ad esempio, quando si usa un server DNS con Bind, viene [generata](http://linux.yyz.us/nsupdate/) una coppia di chiavi pubblica/privata. Il server DNS è [configurato](http://linux.yyz.us/dns/ddns-server.html) con la parte pubblica della chiave, che consente di verificare la firma della richiesta. È necessario usare l'opzione *-k* per indicare la coppia di chiavi a *nsupdate*, in modo da firmare la richiesta di aggiornamento DNS dinamico.

Quando si usa un server DNS Windows, è possibile usare l'autenticazione Kerberos con il parametro *-g* in *nsupdate* (non disponibile nella versione Windows di *nsupdate*). A tale scopo, usare *kinit* per caricare le credenziali (ad esempio, da un [file keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). Quindi, *nsupdate -g* acquisirà le credenziali dalla cache.

Se necessario, aggiungere un suffisso di ricerca DNS alle VM. Il suffisso DNS è specificato nel file */etc/resolv.conf*. La maggior parte delle distribuzioni Linux gestiscono automaticamente il contenuto di questo file, quindi solitamente non può essere modificato. Tuttavia, è possibile ignorare il suffisso usando il comando *supersede* del client DHCP. A tale scopo, in */etc/dhcp/dhclient.conf* aggiungere:

        supersede domain-name <required-dns-suffix>;

<!---HONumber=AcomDC_0907_2016-->