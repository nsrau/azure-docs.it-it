<properties linkid="article" urlDisplayName="Use SSH" pageTitle="Use SSH to connect to Linux virtual machines in Azure" metaKeywords="Azure SSH keys Linux, Linux vm SSH" description="Learn how to generate and use SSH keys with a Linux virtual machine on Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Use SSH with Linux on Azure" authors="" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="" />

# Come usare SSH con Linux in Azure

In questo argomento vengono descritte le procedure da eseguire per generare chiavi SSH compatibili con Azure.

La versione corrente del portale di gestione di Azure accetta solo chiavi pubbliche SSH incapsulate in un certificato X509. Per generare e usare chiavi SSH con Azure, attenersi alla procedura seguente.

## Generare chiavi compatibili con Azure in Linux

1.  Installare l'utilità `openssl`, se necessario:

    **CentOS/Oracle Linux**

        `sudo yum install openssl`

    **Ubuntu**

        `sudo apt-get install openssl`

    **SLES e openSUSE**

        `sudo zypper install openssl`

2.  Usare `openssl` per generare un certificato X509 con una coppia di chiavi RSA a 2048 bit. Rispondere alle domande poste da `openssl` oppure lasciare vuoti i campi. Il contenuto di questi campi non viene usato dalla piattaforma:

            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  Cambiare le autorizzazioni per la chiave privata in modo da proteggerla.

            chmod 600 myPrivateKey.key

4.  Caricare `myCert.pem` durante la creazione della macchina virtuale Linux. Il processo di provisioning installerà automaticamente la chiave pubblica di questo certificato nel file `authorized_keys` per l'utente specificato nella macchina virtuale.

5.  Se si prevede di usare direttamente l'API, invece di usare il portale di gestione, convertire `myCert.pem` in `myCert.cer` (certificato X509 con codifica DER) con il comando seguente:

            openssl  x509 -outform der -in myCert.pem -out myCert.cer

## Generare una chiave da una chiave esistente compatibile con OpenSSH

Nell'esempio precedente è stato illustrato come creare una nuova chiave da usare con Azure. In alcuni casi, è possibile che gli utenti dispongano già di una coppia di chiavi pubblica e privata compatibili con OpenSSH e desiderino usare le stesse chiavi con Azure.

Le chiavi private OpenSSH possono essere lette direttamente dall'utilità `openssl`. Con il comando seguente verrà creata la chiave pubblica `.pem` necessaria per Windows Azure da una chiave privata SSH esistente (id\_rsa nell'esempio):

    # openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

Il file **myCert.pem** corrisponde alla chiave pubblica che può essere in seguito usata per il provisioning di una macchina virtuale Linux in Azure. Durante il provisioning, il file `.pem` verrà convertito in una chiave pubblica compatibile con `openssh` e inserito in `~/.ssh/authorized_keys`.

## Connettersi a una macchina virtuale di Azure da Linux

Ogni macchina virtuale Linux è dotata di SSH in una specifica porta che può essere diversa da quella standard abitualmente usata, quindi eseguire le operazioni seguenti:

1.  Individuare la porta da usare per connettersi alla macchina virtuale Linux dal portale di gestione.
2.  Connettersi alla macchina virtuale Linux tramite `ssh`. La prima volta che si effettua l'accesso, verrà richiesto di accettare l'ID digitale della chiave pubblica dell'host.

        ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.  (Facoltativo) È possibile copiare `myPrivateKey.key` in `~/.ssh/id_rsa` in modo che il client openssh possa automaticamente prelevare la chiave senza usare l'opzione `-i`.

## Ottenere OpenSSL in Windows

### Usare msysgit

1.  Scaricare e installare msysgit dal percorso seguente: [][]<http://msysgit.github.com/></a>
2.  Eseguire `msys` dalla directory installata, ad esempio: c:\\msysgit\\msys.exe
3.  Passare alla directory `bin` digitando `cd bin`

### Usare GitHub per Windows

1.  Scaricare e installare GitHub per Windows dal percorso seguente: [][1]<http://windows.github.com/></a>
2.  Eseguire Git Shell dal menu Start \> Tutti i programmi \> GitHub, Inc

### Usare cygwin

1.  Scaricare e installare Cygwin dal percorso seguente: [][2]<http://cygwin.com/></a>
2.  Assicurarsi che il pacchetto OpenSSL e tutte le relative dipendenze siano installati.
3.  Eseguire `cygwin`

## Creare una chiave privata in Windows

1.  Attenersi a uno dei set di istruzioni seguenti per poter eseguire `openssl.exe`.
2.  Digitare il comando seguente:

        openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  Verrà visualizzata una schermata simile alla seguente:

    ![linuxwelcomegit][linuxwelcomegit]

4.  Rispondere alle domande.
5.  Dovrebbero essere stati creati due file: `myPrivateKey.key` e `myCert.pem`.
6.  Se si prevede di usare direttamente l'API, invece di usare il portale di gestione, convertire `myCert.pem` in `myCert.cer` (certificato X509 con codifica DER) con il comando seguente:

        openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Creare una chiave privata PPK per Putty

1.  Scaricare e installare puttygen dal percorso seguente: [][3]<http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html></a>
2.  Eseguire `puttygen.exe`
3.  Fare clic su File \> Load a Private Key
4.  Trovare la chiave privata, denominata in questo caso `myPrivateKey.key`. Sarà necessario modificare il filtro dei file per poter visualizzare \*\*Tutti i file (\*.\*)
5.  Fare clic su **Apri**. Verrà visualizzato un prompt simile al seguente:

    ![linuxgoodforeignkey][linuxgoodforeignkey]

6.  Fare clic su **OK**.
7.  Fare clic su **Save Private Key**, ossia l'opzione evidenziata nella schermata seguente:

    ![linuxputtyprivatekey][linuxputtyprivatekey]

8.  Salvare il file in formato PPK.

## Usare Putty per connettersi a un computer Linux

1.  Scaricare e installare putty dal percorso seguente: [][3]<http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html></a>
2.  Eseguire putty.exe
3.  Inserire il nome host usando l'IP del portale di gestione.

    ![linuxputtyconfig][linuxputtyconfig]

4.  Prima di selezionare **Open**, fare clic su Connection \> SSH \> Auth per scegliere la chiave. Per informazioni sul campo da compilare, vedere la schermata seguente.

    ![linuxputtyprivatekey][4]

5.  Fare clic su **Open** per connettersi alla macchina virtuale.

  []: http://msysgit.github.com/
  [1]: http://windows.github.com/
  [2]: http://cygwin.com/
  [linuxwelcomegit]: ./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png
  [3]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [linuxgoodforeignkey]: ./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png
  [linuxputtyprivatekey]: ./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png
  [linuxputtyconfig]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png
  [4]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png
