<properties urlDisplayName="Intro to Linux" pageTitle="Introduzione a Linux in Azure - Esercitazione di Azure" metaKeywords="Azure Linux vm, Linux vm" description="Learn about using Linux virtual machines on Azure." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Introduction to Linux on Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="09/13/2014" ms.author="szark" />





#Introduzione a Linux in Azure

In questo argomento viene fornita una panoramica relativa ad alcuni aspetti dell'uso di macchine virtuali Linux nel cloud di Azure. Se si usa un'immagine presente nella raccolta, la distribuzione di una macchina virtuale Linux è un processo estremamente semplice. 

## Sommario ##

* [Autenticazione: nomi utente, password e chiavi SSH](#authentication)
* [Generazione e uso di chiavi SSH per l'accesso a macchine virtuali Linux](#keygeneration)
* [Uso di sudo per ottenere privilegi utente avanzati](#superuserprivileges)
* [Configurazione del firewall](#firewallconfiguration)
* [Modifica del nome host](#hostnamechanges)
* [Acquisizione di immagini di macchine virtuali](#virtualmachine)
* [Collegamento di dischi](#attachingdisks)

## <a id="authentication"></a>Autenticazione: nomi utente, password e chiavi SSH

Quando si crea una macchina virtuale Linux usando il portale di gestione di Azure viene richiesto di inserire un nome utente, una password e, facoltativamente, una chiave pubblica SSH. La scelta del nome utente per la distribuzione di una macchina virtuale Linux in Azure è soggetta a un vincolo: i nomi degli account di sistema (UID <100) già presenti nella macchina virtuale, ad esempio l'account 'root', non sono consentiti.

 - Vedere [Come usare SSH con Linux in Azure](../linux-use-ssh-key/)


### <a id="keygeneration"></a>Generazione di chiavi SSH

La versione corrente del portale di gestione accetta solo chiavi pubbliche SSH incapsulate in un certificato X509. Per generare e usare chiavi SSH con Azure, attenersi alla procedura seguente.

1. Usare openssl per generare un certificato X509 con una coppia di chiavi RSA a 2048 bit.
		
		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

	Rispondere alle domande poste da openssl oppure lasciare vuoti i campi. Il contenuto di questi campi non viene usato dalla piattaforma.

2. Cambiare le autorizzazioni per la chiave privata in modo da proteggerla.

		chmod 600 myPrivateKey.key

3. Convertire `myCert.pem` in `myCert.cer` (certificato X509 con codifica DER)

		openssl  x509 -outform der -in myCert.pem -out myCert.cer

4. Caricare `myCert.cer` durante la creazione della macchina virtuale Linux. Il processo di provisioning installerà automaticamente la chiave pubblica di questo certificato nel file `~/.ssh/authorized_keys` per l'utente specificato nella macchina virtuale.

5. Connettersi alla macchina virtuale Linux tramite ssh.

		ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

	La prima volta che si effettua l'accesso, verrà richiesto di accettare l'ID digitale della chiave pubblica dell'host.

6. Facoltativamente, è possibile copiare `myPrivateKey.key` in `~/.ssh/id_rsa` in modo che il client openssh possa automaticamente prelevare la chiave senza usare l'opzione -i.
   In alternativa, è possibile modificare `~/.ssh/config` per includere una sezione per la macchina virtuale:

        Host servicename.cloudapp.net
          IdentityFile %d/.ssh/myPrivateKey.key


### Generare una chiave da una chiave esistente compatibile con OpenSSH
Nell'esempio precedente è stato illustrato come creare una nuova chiave da usare con Azure. In alcuni casi, è possibile che gli utenti dispongano già di una coppia di chiavi pubblica e privata compatibili con OpenSSH e desiderino usare le stesse chiavi con Azure.

Le chiavi private OpenSSH possono essere lette direttamente dall'utilità `openssl`. Con il comando seguente verrà creata la chiave pubblica `.pem` necessaria per Microsoft Azure da una chiave privata SSH esistente (id_rsa nell'esempio):

	# openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

Il file **myCert.pem** corrisponde alla chiave pubblica che può essere in seguito usata per il provisioning di una macchina virtuale Linux in Microsoft Azure. Durante il provisioning, il file `.pem` verrà convertito in una chiave pubblica compatibile con `openssh` e inserito in `~/.ssh/authorized_keys`.


## <a id="superuserprivileges"></a>Utilizzo di `sudo` per ottenere privilegi utente avanzati

L'account utente specificato durante la distribuzione di istanze di macchine virtuali in Azure è un account con privilegi. Tale account viene configurato dall'agente Linux di Azure in modo da elevare i privilegi al ruolo di utente ROOT (account utente con privilegi avanzati) tramite l'utilità `sudo`. Dopo aver eseguito l'accesso usando questo account utente, sarà possibile eseguire comandi come utente ROOT usando la sintassi del comando.

	# sudo <COMMAND>

Facoltativamente, è possibile ottenere una shell di root usando **sudo -s**.

- Vedere [Uso di privilegi root su Linux in Macchine virtuali di Azure](../virtual-machines-linux-use-root-privileges/)


## <a id="firewallconfiguration"></a>Configurazione del firewall

In Azure è disponibile un filtro dei pacchetti in ingresso che limita la connettività alle porte specificate nel portale di gestione. Per impostazione predefinita, l'unica porta consentita è SSH. È possibile aprire l'accesso a porte aggiuntive nella macchina virtuale Linux mediante la configurazione di endpoint nel portale di gestione.

 - Vedere: [Come configurare gli endpoint a una macchina virtuale](../virtual-machines-set-up-endpoints/)

Le immagini Linux disponibili nella Raccolta di Azure non abilitano il firewall *iptables* per impostazione predefinita. Se necessario, è possibile configurare il firewall per implementare funzionalità di filtro.


## <a id="hostnamechanges"></a>Modifica del nome host

Quando si distribuisce un'istanza di un'immagine Linux è necessario specificare un nome host per la macchina virtuale. Quando la macchina virtuale è in esecuzione, il nome host viene pubblicato nei server DNS della piattaforma, in modo che più macchine virtuali connesse tra loro possano eseguire ricerche di indirizzi IP usando i nomi host.

Se, dopo la distribuzione di una macchina virtuale, si vuole modificarne il nome host, usare il comando

	# sudo hostname <newname>

L'agente Linux di Azure include funzionalità per il rilevamento automatico delle modifiche del nome host, la configurazione della macchina virtuale per il salvataggio permanente della modifica e la pubblicazione della modifica nei server DNS della piattaforma.

 - [Guida dell'utente dell'agente Linux di Azure](../virtual-machines-linux-agent-user-guide/)

### Immagini Ubuntu
Le immagini Ubuntu usano cloud-init, che offre capacità aggiuntive per il bootstrap di una macchina virtuale.

 - Vedere il post di blog relativo ai [dati personalizzati e Cloud-Init in Microsoft Azure](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)


## <a id="virtualmachine"></a>Acquisizione di immagini di macchine virtuali

Azure offre la possibilità di acquisire lo stato di una macchina virtuale esistente in un'immagine che potrà essere usata in un secondo momento per distribuire altre istanze di macchine virtuali. L'agente Linux di Azure può essere usato per ripristinare lo stato precedente di alcune personalizzazioni effettuate durante il processo di provisioning. Per acquisire una macchina virtuale come immagine, eseguire la procedura seguente:

1. Eseguire **waagent -deprovision** per annullare la personalizzazione del provisioning. Facoltativamente, eseguire **waagent -deprovision+user** per eliminare l'account utente specificato durante il provisioning e tutti i relativi dati.

2. Arrestare/Spegnere la macchina virtuale.

3. Fare clic su *Acquisisci* nel portale di gestione oppure usare Powershell o gli strumenti CLI per acquisire la macchina virtuale come immagine.

 - Vedere: [Come acquisire una macchina virtuale Linux da usare come modello](../virtual-machines-linux-capture-image/)


## <a id="attachingdisks"></a>Collegamento di dischi

Ogni macchina virtuale ha un *disco risorse* temporaneo locale collegato. Poiché è possibile che i dati su un disco risorse non vengano mantenuti tra un riavvio e l'altro, questo tipo di disco viene spesso usato da applicazioni e processi in esecuzione nella macchina virtuale per l'archiviazione **temporanea** di dati. È anche usato per archiviare file di paging o di scambio per il sistema operativo.

In Linux il disco risorse è in genere gestito dall'agente Linux di Azure e viene montato automaticamente in **/mnt/resource** (o **/mnt** nelle immagini Ubuntu).

	>[WACOM.NOTE] Si noti che il disco risorse è un disco **temporaneo** e potrebbe essere eliminato e riformattato al riavvio della macchina virtuale.

In Linux il kernel potrebbe assegnare al disco dati il nome `/dev/sdc`; in tal caso gli utenti dovranno suddividere in partizioni, formattare e montare tale risorsa. Questa procedura è illustrata in modo dettagliato nell'esercitazione: [Come collegare un disco dati a una macchina virtuale](../virtual-machines-linux-how-to-attach-disk/).

 - Vedere anche la pagina relativa alla [Configurare RAID software in Linux](../virtual-machines-linux-configure-raid/)


<!--HONumber=35.1-->
