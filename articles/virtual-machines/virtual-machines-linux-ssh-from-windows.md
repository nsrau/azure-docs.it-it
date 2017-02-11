---
title: Usare le chiavi SSH con Windows per le VM Linux | Documentazione Microsoft
description: Informazioni su come generare e usare chiavi SSH in un computer Windows per connettersi a una macchina virtuale Linux in Azure.
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: d991801d6e22a4bc541c1a6c4766ff36a381585b


---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Come usare le chiavi SSH con Windows in Azure
> [!div class="op_single_selector"]
> * [Windows](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> * [Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

Quando ci si connette a macchine virtuali (VM) Linux in Azure, è necessario usare la [crittografia a chiave pubblica](https://wikipedia.org/wiki/Public-key_cryptography) per fornire un modo più sicuro per accedere alla VM Linux. Questo processo comporta uno scambio di chiavi pubbliche e private mediante il comando Sicure Shell (SSH) per l'autenticazione, anziché nome utente e password. Le password sono vulnerabili agli attacchi di forza bruta, soprattutto nelle VM con connessione Internet, ad esempio i server Web. Questo articolo fornisce una panoramica delle chiavi SSH e di come generare le chiavi appropriate in un computer Windows.

## <a name="overview-of-ssh-and-keys"></a>Panoramica di SSH e delle chiavi
È possibile accedere in modo sicuro alla VM Linux mediante una chiave pubblica e una chiave privata:

* La **chiave pubblica** si trova nella VM Linux o in qualsiasi altro servizio che si desidera usare con la crittografia a chiave pubblica.
* La **chiave privata** è quella che si presenta alla VM Linux quando si effettua l'accesso, per verificare la propria identità. Sulla chiave privata è necessario mantenere la massima riservatezza, evitando di condividerla.

La chiave pubblica e la chiave privata sono utilizzabili in più VM e servizi. Non è necessaria una coppia di chiavi per ogni VM o servizio a cui si desidera accedere. Per una panoramica più dettagliata, vedere [crittografia a chiave pubblica](https://wikipedia.org/wiki/Public-key_cryptography).

SSH è un protocollo per connessioni crittografate che consente accessi protetti su connessioni non sicure. È il protocollo di connessione predefinito per le VM Linux ospitate in Azure. Sebbene SSH stessa fornisca una connessione crittografata, se si usano password con le connessioni SSH la VM rimane vulnerabile agli attacchi di forza bruta o di individuazione password. Un metodo più sicuro e preferito per la connessione a una VM mediante SSH è tramite chiave pubblica e privata, anche dette chiavi SSH.

Se non si desidera usare chiavi SSH, è possibile comunque accedere alle VM Linux mediante una password. Se la VM non è connessa a Internet, l'utilizzo di password può essere sufficiente. Tuttavia è comunque necessario gestire le password per ogni VM Linux e mantenere criteri e procedure relative alle password integre, quali una lunghezza minima delle password e aggiornamenti regolari. L'utilizzo delle chiavi SSH riduce la complessità di gestione delle credenziali individuali tra più VM.

## <a name="windows-packages-and-ssh-clients"></a>Pacchetti Windows e client SSH
Per la connessione a VM Linux e la loro gestione in Azure si usa un client **ssh**. I computer Windows tipicamente non hanno un client **ssh** installato. Comuni client SSH Windows che è possibile installare sono inclusi nei pacchetti seguenti:

* [Git per Windows](https://git-for-windows.github.io/)
* [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

> [!NOTE]
> L'ultimo aggiornamento Windows 10 Anniversary Update include Bash per Windows. Questa funzionalità consente di eseguire il sottosistema Windows per Linux e di accedere a utilità come un client SSH. Bash per Windows è ancora in fase di sviluppo e viene considerato una versione beta. Per altre informazioni su Bash per Windows, vedere [Bash in Ubuntu in Windows](https://msdn.microsoft.com/commandline/wsl/about).
> 
> 

## <a name="which-key-files-do-you-need-to-create"></a>Quali file di chiavi è necessario creare?
Azure richiede chiavi pubbliche e private almeno nel formato **ssh-rsa** a 2048 bit. Se si gestiscono risorse di Azure usando il modello di distribuzione classico, è necessario anche generare un PEM (file `.pem`).

Di seguito sono descritti gli scenari di distribuzione e i tipi di file da usare per ognuno:

1. Le chiavi **ssh-rsa** sono necessarie per qualsiasi distribuzione che usa il [portale di Azure](https://portal.azure.com) e le distribuzioni di Resource Manager che usano l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md).
   * Queste chiavi sono in genere tutto ciò che serve alla maggior parte degli utenti.
2. Il file `.pem`è necessario per creare VM mediante il [portale classico](https://manage.windowsazure.com). Queste chiavi sono supportate anche nelle distribuzioni classiche che usano l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md).
   * È necessario creare questi certificati e chiavi aggiuntive solo se si gestiscono risorse create usando il modello di distribuzione classica.

## <a name="install-git-for-windows"></a>Installare Git per Windows
Nella sezione precedente sono elencati diversi pacchetti che includono lo strumento `openssl` per Windows. Questo strumento è necessario per creare chiavi pubbliche e private. L'esempio seguente illustra come installare e usare **Git per Windows**, ma è possibile scegliere qualsiasi pacchetto desiderato. **GIT per Windows** consente di accedere ad alcuni strumenti software open-source ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) e utilità aggiuntive che possono essere utili quando si lavora con le VM Linux.

1. Scaricare e installare **Git per Windows** dal percorso seguente: [https://git-for-windows.github.io/](https://git-for-windows.github.io/).
2. Accettare le opzioni predefinite durante il processo di installazione, a meno che sia necessario modificarle.
3. Eseguire **Git Bash** dal **Menu Start** > **Git** > **Git Bash**. La console è simile all'esempio seguente:
   
    ![Shell Bash di GIT per Windows](./media/virtual-machines-linux-ssh-from-windows/git-bash-window.png)

## <a name="create-a-private-key"></a>Creare una chiave privata
1. Nella finestra **Git Bash** usare `openssl.exe` per creare una chiave privata. Nell'esempio seguente vengono creati una chiave denominata `myPrivateKey` e un certificato denominato `myCert.pem`:
   
    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```
   
    L'output è simile al seguente esempio:
   
    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```
2. Rispondere ai prompt per il nome del paese, la località, il nome dell'organizzazione e così via.
3. La nuova chiave privata e certificato vengono creati nella directory di lavoro corrente. Per procedure ottimali di protezione, è necessario impostare le autorizzazioni per la chiave privata in modo da essere l'unica persona a potervi accedere:
   
    ```bash
    chmod 0600 myPrivateKey.key
    ```

4. La [sezione successiva](#create-a-private-key-for-putty) descrive nei dettagli l'uso di PuTTYgen sia per visualizzare che per utilizzare la chiave pubblica nonché per creare una chiave privata specifica per l'impiego di PuTTY con SSH in VM Linux. Il comando seguente genera un file di chiave pubblica denominato `myPublicKey.key` che è possibile usare subito:

    ```bash
    openssl.exe rsa -pubout -in myPrivateKey.key -out myPublicKey.key
    ```

5. Se è necessario anche gestire le risorse classiche, convertire `myCert.pem` in `myCert.cer` (certificato X509 con codifica DER). Eseguire questo passaggio facoltativo solo se è necessario gestire in modo specifico risorse classiche precedenti. 
   
    Convertire il certificato mediante il comando seguente:
   
    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Creare una chiave privata per PuTTY
PuTTY è un comune client SSH per Windows. È possibile usare qualsiasi client SSH desiderato. Per usare PuTTY è necessario creare un tipo di chiave aggiuntivo, una chiave privata PuTTY (PPK). Se non si desidera usare PuTTY, ignorare questa sezione.

L'esempio seguente crea questa chiave privata aggiuntiva appositamente per l'uso da parte di PuTTY:

1. Usare **Git Bash** per convertire la propria chiave privata in una chiave privata RSA che PuTTYgen possa comprendere. Nell'esempio seguente viene creata una chiave denominata `myPrivateKey_rsa` dalla chiave esistente denominata `myPrivateKey`:
   
    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```
   
    Per procedure ottimali di protezione, è necessario impostare le autorizzazioni per la chiave privata in modo da essere l'unica persona a potervi accedere:
   
    ```bash
    chmod 0600 myPrivateKey_rsa
    ```
2. Scaricare ed eseguire PuTTYgen dal percorso seguente: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
3. Fare clic sul menu: **File** > **Load a Private Key**
4. Individuare la chiave privata (`myPrivateKey_rsa` nell'esempio precedente). La directory predefinita quando si avvia **Git Bash** è `C:\Users\%username%`. Modificare il filtro dei file in modo da visualizzare **Tutti i file (\*.\*)**:
   
    ![Caricare la chiave privata esistente in PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/load-private-key.png)
5. Fare clic su **Apri**. Un messaggio indica che la chiave è stata importata correttamente:
   
    ![Chiave importata correttamente in PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/successfully-imported-key.png)
6. Fare clic su **OK** per chiudere la finestra del messaggio.
7. La chiave pubblica viene visualizzata nella parte superiore della finestra di **PuTTYgen**. Questa chiave pubblica potrà essere copiata e incollata nel portale di Azure o in un modello di Azure Resource Manager quando si crea una VM Linux. È inoltre possibile fare clic su **Salva chiave pubblica** per salvare una copia nel computer:
   
    ![Salvare un file di chiave pubblica PuTTY](./media/virtual-machines-linux-ssh-from-windows/save-public-key.png)
   
    L'esempio seguente illustra come copiare e incollare questa chiave pubblica nel portale di Azure quando si crea una VM Linux. Quindi la chiave pubblica viene generalmente archiviata in `~/.ssh/authorized_keys` nella nuova VM.
   
    ![Usare la chiave pubblica quando si crea una VM nel portale di Azure](./media/virtual-machines-linux-ssh-from-windows/use-public-key-azure-portal.png)
8. Quando si torna in **PuTTYgen**, fare clic su **Save private Key**:
   
    ![Salvare il file di chiave privata PuTTY](./media/virtual-machines-linux-ssh-from-windows/save-ppk-file.png)
   
   > [!WARNING]
   > Un messaggio chiede se si desidera continuare senza immettere una passphrase per la chiave. Una passphrase è come una password associata alla chiave privata. Anche se qualcuno riuscisse a ottenere la chiave privata, la sola chiave non gli consentirebbe comunque di autenticarsi. Per farlo avrebbe bisogno anche della passphrase. Quando non si imposta una passphrase, se un utente ottiene la chiave privata potrà accedere a qualsiasi VM o servizio che usa tale chiave. Creare una passphrase è altamente consigliato. Tuttavia, se si dimentica la passphrase, non è possibile recuperarla.
   > 
   > 
   
    Se si desidera immettere una passphrase, fare clic su **No**, immettere una passphrase nella finestra principale di PuTTYgen e quindi fare clic di nuovo su **Save private key**. In caso contrario, fare clic su **Sì** per continuare senza fornire la passphrase facoltativa.
9. Immettere un nome e un percorso in cui salvare il file PPK.

## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Usare Putty per connettere SSH a un computer Linux
PuTTY, come indicato in precedenza, è un comune client SSH per Windows. È possibile usare qualsiasi client SSH desiderato. La procedura seguente illustra come usare la chiave privata per autenticarsi con la VM Azure tramite SSH. I passaggi sono simili in altri client con chiave SSH per quanto riguarda la necessità di caricare la chiave privata per autenticare la connessione SSH.

1. Scaricare ed eseguire putty dal percorso seguente: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Specificare il nome host o l'indirizzo IP della VM dal portale di Azure:
   
    ![Aprire una nuova connessione PuTTY](./media/virtual-machines-linux-ssh-from-windows/putty-new-connection.png)
3. Prima di selezionare **Apri**, fare clic sulla scheda **Connessione** > **SSH** > **Aut**. Individuare e selezionare la chiave privata:
   
    ![Selezionare la chiave privata PuTTY per l'autenticazione](./media/virtual-machines-linux-ssh-from-windows/putty-auth-dialog.png)
4. Fare clic su **Open** per connettersi alla macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi
È possibile generare chiavi pubbliche e private anche [con OS X e Linux](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per altre informazioni su Bash per Windows e sui vantaggi di avere strumenti OSS disponibili nel computer Windows, vedere [Bash in Ubuntu in Windows](https://msdn.microsoft.com/commandline/wsl/about).

Se si verificano problemi nell'uso di SSH per connettersi alle VM Linux, vedere [Risoluzione dei problemi di connessione SSH a una VM Linux di Azure](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).




<!--HONumber=Nov16_HO3-->


