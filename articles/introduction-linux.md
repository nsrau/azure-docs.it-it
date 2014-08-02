<properties linkid="manage-linux-fundamentals-intro-to-linux" urlDisplayName="Intro to Linux" pageTitle="Introduction to Linux in Azure - Azure Tutorial" metaKeywords="Azure Linux vm, Linux vm" description="Learn about using Linux virtual machines on Azure." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Introduction to Linux on Azure" authors="" solutions="" manager="" editor="" />

Introduzione a Linux in Azure
=============================

In questo argomento viene fornita una panoramica relativa ad alcuni aspetti dell'utilizzo di macchine virtuali Linux nel cloud di Azure. Se si utilizza un'immagine preesistente nella raccolta, la distribuzione di una macchina virtuale Linux è un processo estremamente semplice.

Sommario
--------

-   [Autenticazione: nomi utente, password e chiavi SSH.](#authentication)
-   [Generazione e utilizzo di chiavi SSH per l'accesso a macchine virtuali Linux.](#keygeneration)
-   [Utilizzo di sudo per ottenere privilegi utente avanzati](#superuserprivileges)
-   [Configurazione del firewall](#firewallconfiguration)
-   [Modifica del nome host](#hostnamechanges)
-   [Acquisizione di immagini di macchine virtuali](#virtualmachine)
-   [Collegamento di dischi](#attachingdisks)

Autenticazione: nomi utente, password e chiavi SSH
--------------------------------------------------

Quando si crea una macchina virtuale Linux utilizzando il portale di gestione di Azure viene richiesto di inserire un nome utente, una password e, facoltativamente, una chiave pubblica SSH. La scelta del nome utente per la distribuzione di una macchina virtuale Linux in Azure è soggetta a un vincolo: i nomi degli account di sistema già presenti nella macchina virtuale, ad esempio l'account ROOT, non sono consentiti. Se non si specifica la chiave pubblica SSH, sarà possibile accedere alla macchina virtuale utilizzando il nome utente e la password specificati. Se si sceglie di caricare una chiave pubblica SSH nel portale di gestione, l'autenticazione basata su password verrà disabilitata e verrà richiesto di utilizzare la chiave privata SSH corrispondente per eseguire l'autenticazione con la macchina virtuale e accedere.

Generazione di chiavi SSH
-------------------------

La versione corrente del portale di gestione accetta solo chiavi pubbliche SSH incapsulate in un certificato X509. Per generare e utilizzare chiavi SSH con Azure, attenersi alla procedura seguente.

1.  Utilizzare openssl per generare un certificato X509 con una coppia di chiavi RSA a 2048 bit.

         openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

    Rispondere alle domande poste da openssl oppure lasciare vuoti i campi. Il contenuto di questi campi non viene utilizzato dalla piattaforma.

2.  Cambiare le autorizzazioni per la chiave privata in modo da proteggerla.

         chmod 600 myPrivateKey.key

3.  Convertire myCert.pem in myCert.cer (certificato X509 con codifica DER)

         openssl  x509 -outform der -in myCert.pem -out myCert.cer

4.  Caricare myCert.pem durante la creazione della macchina virtuale Linux. Il processo di provisioning installerà automaticamente la chiave pubblica di questo certificato nel file authorized\_keys per l'utente specificato nella macchina virtuale.

5.  Connettersi alla macchina virtuale Linux tramite ssh.

         ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

    La prima volta che si effettua l'accesso, verrà richiesto di accettare l'ID digitale della chiave pubblica dell'host.

6.  Facoltativamente, è possibile copiare myPrivateKey.key in \~/.ssh/id\_rsa in modo che il client openssh possa automaticamente prelevare la chiave senza utilizzare l'opzione -i.

Utilizzo di sudo per ottenere privilegi utente avanzati
-------------------------------------------------------

L'account utente specificato durante la distribuzione di istanze di macchine virtuali in Azure è un account con privilegi. Tale account viene configurato dall'agente Linux di Azure con la capacità di elevare i privilegi al ruolo di utente ROOT (account utente con privilegi avanzati) tramite lo strumento sudo. Dopo aver eseguito l'accesso utilizzando questo account utente, sarà possibile eseguire comandi come utente ROOT utilizzando il comando "sudo". Facoltativamente, è possibile ottenere una shell di root utilizzando **sudo -s**.

Configurazione del firewall
---------------------------

In Azure è disponibile un filtro dei pacchetti in ingresso che limita la connettività alle porte specificate nel portale di gestione. Per impostazione predefinita, l'unica porta consentita è SSH. È possibile aprire l'accesso a porte aggiuntive nella macchina virtuale Linux mediante l'aggiunta di regole nel portale di gestione.

Le immagini Linux disponibili nella raccolta non abilitano il firewall Iptables all'interno delle macchine virtuali Linux. Se necessario, è possibile configurare il firewall Iptables per implementare funzionalità aggiuntive.

Modifica del nome host
----------------------

Quando si distribuisce un'istanza di un'immagine Linux è necessario specificare un nome host per la macchina virtuale. Quando la macchina virtuale è in esecuzione, il nome host viene pubblicato nei server DNS della piattaforma, in modo che più macchine virtuali connesse tra loro possano eseguire ricerche di indirizzi IP utilizzando i nomi host. Se, dopo la distribuzione di una macchina virtuale, si desidera modificarne il nome host, utilizzare il comando **hostname newname**. L'agente Linux di Azure include funzionalità per il rilevamento automatico delle modifiche del nome host, la configurazione della macchina virtuale per il salvataggio permanente della modifica e la pubblicazione della modifica nei server DNS della piattaforma. Per ulteriori dettagli e per le opzioni di configurazione relative a questa funzionalità, fare riferimento al file LEGGIMI dell'agente Linux di Azure.

Acquisizione di immagini di macchine virtuali
---------------------------------------------

Azure offre la possibilità di acquisire lo stato di una macchina virtuale esistente in un'immagine che potrà essere utilizzata in un secondo momento per distribuire ulteriori istanze di macchine virtuali. L'agente Linux di Azure può essere utilizzato per ripristinare lo stato precedente di alcune personalizzazioni effettuate durante il processo di provisioning. Per acquisire una macchina virtuale come immagine, eseguire la procedura seguente:

1.  Eseguire **waagent -deprovision** per annullare la personalizzazione del provisioning. Facoltativamente, eseguire **waagent -deprovision+user** per eliminare l'account utente specificato durante il provisioning e tutti i relativi dati.

2.  Arrestare/spegnere la macchina virtuale utilizzando il portale di gestione o gli strumenti.

3.  Fare clic su Capture nel portale di gestione oppure utilizzare gli strumenti per acquisire la macchina virtuale come immagine.

Collegamento di dischi
----------------------

Questa procedura è illustrata in dettaglio nell'esercitazione relativa alla [creazione di una macchina virtuale Linux](../virtual-machine-from-gallery/).

