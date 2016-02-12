<properties 
	pageTitle="Usare SSH in Windows per connettersi a macchine virtuali Linux | Microsoft Azure" 
description="Informazioni su come generare e usare chiavi SSH in un computer Windows per connettersi a una macchina virtuale Linux in Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/04/2016" 
	ms.author="rasquill"/>

#Come usare SSH con Windows in Azure

> [AZURE.SELECTOR]
- [Windows](../articles/virtual-machines/virtual-machines-windows-use-ssh-key.md)
- [Linux/Mac](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md)

In questo argomento viene descritto come creare e usare i file di chiavi pubbliche e private in formato **ssh rsa** e **pem** in Windows, utilizzabili per la connessione alle macchine virtuali Linux in Azure con il comando **ssh**. Se si dispone già di file **pem**, è possibile usarli per creare macchine virtuali Linux a cui è possibile connettersi con **ssh**. Molti altri comandi usano il protocollo **SSH** e i file di chiavi per eseguire operazioni in modo sicuro, in particolare **scp** o [Secure Copy](https://en.wikipedia.org/wiki/Secure_copy), che consente di copiare file in modo sicuro da e verso computer che supportano connessioni **SSH**.


## Quali programmi sono necessari per SSH e la creazione delle chiavi?

**SSH**, o [secure shell](https://en.wikipedia.org/wiki/Secure_Shell), è un protocollo per connessioni crittografate che consente accessi protetti su connessioni non sicure. È il protocollo di connessione predefinito per le macchine virtuali Linux ospitate in Azure, a meno che tali macchine virtuali non vengano configurate per consentire un altro meccanismo di connessione. Anche gli utenti di Windows possono connettersi e gestire le macchine virtuali Linux in Azure tramite un'implementazione client **ssh**, ma i computer Windows non includono in genere un client **ssh**, pertanto sarà necessario scegliere uno.

I client comuni che è possibile installare includono:

- [puTTY e puTTYgen]((http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)
- [Git per Windows](https://git-for-windows.github.io/), che include l'ambiente e gli strumenti

Gli appassionati sperimentatori possono provare la [nuova versione del set di strumenti **OpenSSH** per Windows](http://blogs.msdn.com/b/powershell/archive/2015/10/19/openssh-for-windows-update.aspx). Tenere presente, tuttavia, che si tratta di codice che è attualmente in fase di sviluppo e prima di usarlo in sistemi di produzione, è opportuno rivedere la codebase.

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Quali file di chiavi è necessario creare?

Una configurazione di base di SSH per Azure include una coppia di chiavi pubblica e privata **ssh-rsa** da 2.048 bit (per impostazione predefinita, **ssh keygen** archivia i file come **~/.ssh/id\_rsa** e **~/.ssh/id-rsa.pub**, a meno che non si modifichino le impostazioni predefinite), nonché un file `.pem` generato dal file di chiavi private **id\_rsa** da usare con il modello di distribuzione classica del portale classico.

Di seguito sono descritti gli scenari di distribuzione e i tipi di file da usare per ognuno:

1. Le chiavi **ssh-rsa** sono necessarie per qualsiasi distribuzione eseguita tramite il [portale di Azure](https://portal.azure.com), indipendentemente dal modello di distribuzione.
2. I file con estensione pem sono necessari per creare macchine virtuali utilizzando il [portale classico](https://manage.windowsazure.com). I file pem sono supportati anche nelle distribuzioni classiche che usano l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md).

> [AZURE.NOTE] Se si prevede di gestire il servizio distribuito con il modello di distribuzione classica, è inoltre possibile creare un file in formato **cer** da caricare nel portale, anche se ciò non riguarda **ssh** o la connessione a macchine virtuali Linux, ovvero l'oggetto di questo articolo. Per creare questi file in ambiente Linux o Mac, digitare

## Ottenere ssh-keygen e openssl in Windows ##

[In questa sezione](#What-SSH-and-key-creation-programs-do-you-need) precedente sono elencate diverse utilità che includono `ssh-keygen` e `openssl` per Windows. Alcuni esempi sono elencati di seguito:

### Usare Msysgit ###

1.	Scaricare e installare msysgit dal percorso seguente: [http://msysgit.github.com/](http://msysgit.github.com/)
2.	Eseguire `msys` dalla directory installata, ad esempio c:\\msysgit\\msys.exe
3.	Passare alla directory `bin` digitando `cd bin`


### Usare GitHub per Windows ###

1.	Scaricare e installare GitHub per Windows dal percorso seguente: [http://windows.github.com/](http://windows.github.com/)
2.	Eseguire Git Shell dal menu Start > Tutti i programmi > GitHub, Inc

> [AZURE.NOTE] Quando si eseguono i comandi `openssl` precedenti potrebbe verificarsi il seguente errore:

        Unable to load config info from /usr/local/ssl/openssl.cnf

Il modo più semplice per risolvere questo problema consiste nell'impostare la variabile di ambiente `OPENSSL_CONF`. Il processo di impostazione di questa variabile varia a seconda della shell configurata in Github:

**Powershell:**

        $Env:OPENSSL_CONF="$Env:GITHUB_GIT\ssl\openssl.cnf"

**CMD:**

        set OPENSSL_CONF=%GITHUB_GIT%\ssl\openssl.cnf

**Git Bash:**

        export OPENSSL_CONF=$GITHUB_GIT/ssl/openssl.cnf
	

###Usare Cygwin###

1.	Scaricare e installare Cygwin dal percorso seguente: [http://cygwin.com/](http://cygwin.com/)
2.	Assicurarsi che il pacchetto OpenSSL e tutte le relative dipendenze siano installati.
3.	Eseguire `cygwin`

## Creare una chiave privata##

1.	Attenersi a uno dei set di istruzioni seguenti per poter eseguire `openssl.exe`
2.	Digitare il comando seguente:

		# openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	Verrà visualizzata una schermata simile alla seguente:

	![linuxwelcomegit](./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png)

4.	Rispondere alle domande.
5.	Dovrebbero essere stati creati due file: `myPrivateKey.key` e `myCert.pem`.
6.	Se si prevede di usare direttamente l'API, anziché usare il portale di gestione, convertire `myCert.pem` in `myCert.cer` (certificato X509 con codifica DER) con il comando seguente:

		# openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Creare una chiave privata PPK per Putty ##

1. Scaricare e installare Puttygen dal percorso seguente: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Puttygen potrebbe non essere in grado di leggere la chiave privata creata in precedenza (`myPrivateKey.key`). Eseguire il comando seguente per convertirla in una chiave privata RSA riconosciuta da Puttygen:

		# openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
		# chmod 600 ./myPrivateKey_rsa

	Il comando precedente consente di ottenere una nuova chiave privata denominata myPrivateKey\_rsa.

3. Eseguire `puttygen.exe`

4. Fare clic sul menu: File > Load a Private Key

5. Individuare la chiave privata, a cui in precedenza è stato assegnato il nome `myPrivateKey_rsa`. Sarà necessario modificare il filtro dei file in modo da visualizzare **Tutti i file (*.*)**

6. Fare clic su **Apri**. Verrà visualizzato un prompt simile al seguente:

	![linuxgoodforeignkey](./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png)

7. Fare clic su **OK**.

8. Fare clic su **Save Private Key**, ossia l'opzione evidenziata nella schermata seguente:

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png)

9. Salvare il file in formato PPK


## Usare Putty per connettersi a un computer Linux ##

1.	Scaricare e installare putty dal percorso seguente: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Eseguire putty.exe
3.	Inserire il nome host usando l'IP del portale di gestione:

	![linuxputtyconfig](./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png)

4.	Prima di selezionare **Open**, fare clic su Connection > SSH > Auth per scegliere la chiave. Per informazioni sul campo da compilare, vedere la schermata seguente:

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png)

5.	Fare clic su **Open** per connettersi alla macchina virtuale.
 

<!---HONumber=AcomDC_0204_2016-->