<properties 
   pageTitle="Connessione remota al dispositivo StorSimple | Microsoft Azure"
   description="Viene illustrato come configurare il dispositivo per la gestione remota e come connettersi a Windows PowerShell per StorSimple tramite HTTP o HTTPS."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/08/2016"
   ms.author="alkohli" />

# Connettersi in remoto al dispositivo StorSimple

## Panoramica

È possibile utilizzare la comunicazione remota di Windows PowerShell per la connessione al dispositivo StorSimple. Quando ci si connette in questo modo, non verrà visualizzato un menu. (Verrà visualizzato un menu solo se si utilizza la console seriale del dispositivo per la connessione.) Con la comunicazione remota di Windows PowerShell, ci si connette a uno specifico spazio di esecuzione. È inoltre possibile specificare la lingua di visualizzazione.

Per ulteriori informazioni sull'utilizzo della comunicazione remota di Windows PowerShell per gestire il dispositivo, andare a [Utilizzare Windows PowerShell per StorSimple per gestire il dispositivo StorSimple](storsimple-windows-powershell-administration.md).

In questa esercitazione viene illustrato come configurare il dispositivo per la gestione remota, quindi come connettersi a Windows PowerShell per StorSimple. È possibile utilizzare HTTP o HTTPS per connettersi tramite la comunicazione remota di Windows PowerShell. Tuttavia, quando si decide come connettersi a Windows PowerShell per StorSimple, considerare quanto segue:

- La connessione diretta alla console seriale del dispositivo è protetta ma la connessione alla console seriale sugli switch di rete non lo è. Prestare attenzione al rischio di sicurezza quando ci si connette alla console seriale del dispositivo sugli switch di rete. 

- La connessione tramite una sessione HTTP potrebbe offrire maggiore sicurezza rispetto alla connessione tramite la console seriale sulla rete. Sebbene non sia il metodo più sicuro, è accettabile su reti attendibili.

- La connessione tramite una sessione HTTPS con un certificato autofirmato è l'opzione più sicura e consigliata.

È possibile connettersi in remoto all'interfaccia di Windows PowerShell. Tuttavia, l'accesso remoto al dispositivo StorSimple tramite l'interfaccia di Windows PowerShell non è abilitato per impostazione predefinita. È necessario abilitare innanzitutto la gestione remota sul dispositivo, quindi sul client che viene utilizzato per l'accesso al dispositivo.

I passaggi descritti in questo articolo sono stati eseguiti in un sistema host che esegue Windows Server 2012 R2.

## Connessione tramite HTTP

La connessione a Windows PowerShell per StorSimple tramite una sessione HTTP offre una maggiore protezione rispetto alla connessione tramite la console seriale del dispositivo StorSimple. Sebbene non sia il metodo più sicuro, è accettabile su reti attendibili.

Per configurare la gestione remota, è possibile utilizzare il portale di Azure classico o la console seriale. Selezionare una delle seguenti procedure:

- [Utilizzo del portale di Azure classico per abilitare la gestione remota su HTTP](#use-the-azure-classic-portal-to-enable-remote-management-over-http)

- [Utilizzo della console seriale per abilitare la gestione remota su HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Dopo aver abilitato la gestione remota, utilizzare la procedura seguente per preparare il client per una connessione remota.

- [Preparazione del client per la connessione remota](#prepare-the-client-for-remote-connection)

### Utilizzo del portale di Azure classico per abilitare la gestione remota su HTTP 

Eseguire i passaggi seguenti nel portale di Azure classico per abilitare la gestione remota su HTTP.

#### Per abilitare la gestione remota tramite il portale di Azure classico

1. Accedere a **Dispositivi** > **Configura** per il dispositivo.

2. Scorrere verso il basso fino alla sezione **Gestione remota**.

3. Impostare **Abilita la gestione remota** su **Sì**.

4. È ora possibile scegliere di connettersi tramite HTTP. (La connessione su HTTPS rappresenta la scelta predefinita.) Assicurarsi che HTTP sia selezionato.

    >[AZURE.NOTE] La connessione tramite HTTP dovrebbe essere eseguita solo su reti attendibili.

6. Fare clic su **Save** nella parte inferiore della pagina.

### Utilizzo della console seriale per abilitare la gestione remota su HTTP

Eseguire le operazioni seguenti nella console seriale del dispositivo per abilitare la gestione remota.

#### Per abilitare la gestione remota tramite la console seriale del dispositivo:

1. Nel menu della console seriale, selezionare l'opzione 1. Per ulteriori informazioni sull'utilizzo della console seriale del dispositivo, vedere [Connessione a Windows PowerShell per StorSimple tramite la console seriale del dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. Al prompt dei comandi, digitare: `Enable-HcsRemoteManagement –AllowHttp`

3. Si riceverà una notifica sulle vulnerabilità della sicurezza relative all'utilizzo di HTTP per la connessione al dispositivo. Quando richiesto, confermare digitando **Y**.

4. Verificare che HTTP sia abilitato digitando: `Get-HcsSystem`

5. Verificare che il campo **RemoteManagementMode** mostri **HttpsAndHttpEnabled**. Nella figura seguente vengono illustrate queste impostazioni in PuTTY.

     ![Seriali HTTPS e HTTP abilitati](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### Preparazione del client per la connessione remota

Eseguire le operazioni seguenti sul client per abilitare la gestione remota.

#### Per preparare il client per la connessione remota:

1. Avviare una sessione di Windows PowerShell come amministratore.

2. Digitare il comando seguente per aggiungere l'indirizzo IP del dispositivo StorSimple all'elenco di host attendibili del client:

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

     Sostituire <*ip\_dispositivo*> con l'indirizzo IP del dispositivo, ad esempio:

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Digitare il comando seguente per salvare le credenziali del dispositivo in una variabile:

     *$cred = Get-Credential*

4. Nella finestra di dialogo visualizzata:

    1. Digitare il nome utente nel formato: *ip\_dispositivo\\SSAdmin*.
    2. Digitare la password di amministratore del dispositivo impostata quando il dispositivo è stato configurato con la configurazione guidata. La password predefinita è *Password1*.

7. Avviare una sessione di Windows PowerShell sul dispositivo digitando questo comando:

     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`

     >[AZURE.NOTE] Per creare una sessione di Windows PowerShell per l'utilizzo con il dispositivo virtuale StorSimple, aggiungere il parametro `–Port` e specificare la porta pubblica configurata nella comunicazione remota per il dispositivo virtuale StorSimple.

     A questo punto, è necessario disporre di una sessione remota attiva di Windows PowerShell sul dispositivo.

    ![Comunicazione remota PowerShell tramite HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## Connessione tramite HTTPS

La connessione a Windows PowerShell per StorSimple tramite una sessione HTTPS è il metodo più sicuro e consigliato di connessione remota al dispositivo Microsoft Azure StorSimple. Nelle procedure seguenti viene illustrato come configurare la console seriale e i computer client, in modo che sia possibile utilizzare HTTPS per connettersi a Windows PowerShell per StorSimple.

Per configurare la gestione remota, è possibile utilizzare il portale di Azure classico o la console seriale. Selezionare una delle seguenti procedure:

- [Utilizzo del portale di Azure classico per abilitare la gestione remota sugli HTTP](#use-the-azure-classic-portal-to-enable-remote-management-over-https)

- [Utilizzo della console seriale per abilitare la gestione remota su HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Dopo aver abilitato la gestione remota, utilizzare le procedure seguenti per preparare l'host per la gestione remota e connettersi al dispositivo dall'host remoto.

- [Preparazione dell'host per la gestione remota](#prepare-the-host-for-remote-management)

- [Connessione al dispositivo dall'host remoto](#connect-to-the-device-from-the-remote-host)

### Utilizzo del portale di Azure classico per abilitare la gestione remota sugli HTTP

Eseguire i passaggi seguenti nel portale di Azure classico per abilitare la gestione remota sugli HTTP.

#### Per abilitare la gestione remota su HTTPS dal portale di Azure classico

1. Accedere a **Dispositivi** > **Configura** per il dispositivo.

2. Scorrere verso il basso fino alla sezione **Gestione remota**.

3. Impostare **Abilita la gestione remota** su **Sì**.

4. È ora possibile scegliere di connettersi tramite HTTPS. (La connessione su HTTPS rappresenta la scelta predefinita.) Assicurarsi che HTTPS sia selezionato.

5. Fare clic su **Scarica certificato di gestione remota**. Specificare un percorso per salvare il file. Sarà necessario installare questo certificato nel computer client o host che si utilizzerà per connettersi al dispositivo.

6. Fare clic su **Save** nella parte inferiore della pagina.

### Utilizzo della console seriale per abilitare la gestione remota su HTTPS

Eseguire le operazioni seguenti nella console seriale del dispositivo per abilitare la gestione remota.

#### Per abilitare la gestione remota tramite la console seriale del dispositivo:

1. Nel menu della console seriale, selezionare l'opzione 1. Per ulteriori informazioni sull'utilizzo della console seriale del dispositivo, vedere [Connessione a Windows PowerShell per StorSimple tramite la console seriale del dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. Al prompt dei comandi, digitare:

     `Enable-HcsRemoteManagement`

    Ciò dovrebbe abilitare HTTPS sul dispositivo.

3. Verificare che HTTPS sia abilitato digitando:

     `Get-HcsSystem`

    Assicurarsi che il campo **RemoteManagementMode** contenga **HttpsEnabled**. La figura seguente mostra queste impostazioni in PuTTY.

     ![Seriale HTTPS abilitato](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)

4. Dall'output di `Get-HcsSystem`, copiare il numero di serie del dispositivo e salvarlo per un utilizzo successivo.

    >[AZURE.NOTE] Il numero di serie esegue il mapping sul nome CN nel certificato.

5. Ottenere un certificato di gestione remota digitando:
 
     `Get-HcsRemoteManagementCert`

    Verrà visualizzato un certificato simile al seguente.

    ![Ottenimento del certificato di gestione remota](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)

5. Copiare le informazioni sul certificato da **-----BEGIN CERTIFICATE-----** a **-----END CERTIFICATE-----** in un editor di testo come Blocco note e salvarlo come file con estensione .cer. (Il file dovrà essere copiato sull'host remoto quando si prepara l'host.)

    >[AZURE.NOTE] Per generare un nuovo certificato, utilizzare il cmdlet `Set-HcsRemoteManagementCert`.

### Preparazione dell'host per la gestione remota

Per preparare il computer host per una connessione remota che utilizza una sessione HTTPS, eseguire le procedure seguenti:

- [Importazione del file con estensione .cer nell'archivio radice del client o dell'host remoto](#to-import-the-certificate-on-the-remote-host)

- [Aggiunta dei numeri di serie del dispositivo al file hosts sull'host remoto](#to-add-device-serial-numbers-to-the-remote-host)

Ognuna di queste procedure è descritta di seguito.

#### Per importare il certificato nell'host remoto:

1. Fare clic con il pulsante destro del mouse sul file con estensione .cer e selezionare **Installa certificato**. Verrà avviata l'importazione guidata del certificato.

    ![Impostazione guidata del certificato 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)

2. Per **Percorso archivio**, selezionare **Computer locale**, quindi fare clic su **Avanti**.

3. Selezionare **Colloca tutti i certificati nel seguente archivio**, quindi fare clic su **Sfoglia**. Passare all'archivio radice dell'host remoto, quindi fare clic su **Avanti**.

    ![Impostazione guidata del certificato 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)

4. Fare clic su **Finish**. Viene visualizzato un messaggio indicante che l'importazione è avvenuta correttamente.

    ![Impostazione guidata del certificato 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### Per aggiungere i numeri di serie del dispositivo all'host remoto:

1. Avviare Blocco note come amministratore, quindi aprire il file hosts che si trova in \\Windows\\System32\\Drivers\\etc.

2. Aggiungere le tre voci seguenti al file hosts: **DATA 0 IP address**, **Controller 0 Fixed IP address** e **Controller 1 Fixed IP address**.

3. Immettere il numero di serie salvato in precedenza. Eseguirne il mapping all'indirizzo IP come illustrato nella figura seguente. Per Controller 0 e Controller 1, aggiungere **Controller0** e **Controller1** alla fine del numero di serie (nome CN).

    ![Aggiunta del nome CN al file hosts](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)

4. Salvare il file hosts.

### Connessione al dispositivo dall'host remoto

Utilizzare Windows PowerShell e SSL per accedere a una sessione SSAdmin sul dispositivo da un client o un host remoto. Viene eseguito il mapping della sessione SSAdmin all'opzione 1 del menu [console seriale](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console) del dispositivo.

Eseguire la procedura seguente sul computer da cui si desidera effettuare la connessione remota di Windows PowerShell.

#### Per accedere a una sessione SSAdmin sul dispositivo tramite Windows PowerShell e SSL:

1. Avviare una sessione di Windows PowerShell come amministratore.

2. Aggiungere l'indirizzo IP del dispositivo agli host attendibili del client digitando:

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

    Dove <*ip\_dispositivo*> è l'indirizzo IP del dispositivo, ad esempio:

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Creare una nuova credenziale digitando:

     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`

    Dove <*IP del dispositivo di destinazione*> è l'indirizzo IP di DATA 0 per il dispositivo, ad esempio, **10.126.173.90** come illustrato nell'immagine precedente del file hosts. Inoltre, fornire la password di amministratore per il dispositivo.

4. Creare una sessione digitando:

     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`

    Per il parametro -ComputerName nel cmdlet, specificare il <*numero di serie del dispositivo di destinazione*>. Questo numero di serie è stato mappato all'indirizzo IP di DATA 0 nel file hosts sull'host remoto; ad esempio, **SHX0991003G44MT** come illustrato nella figura seguente.

5. Digitare:

     `Enter-PSSession $session`

6. Sarà necessario attendere alcuni minuti, quindi verrà effettuata la connessione al dispositivo tramite HTTPS su SSL. Verrà visualizzato un messaggio indicante che è stata effettuata la connessione al dispositivo.

    ![Comunicazione remota PowerShell tramite HTTPS e SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## Passaggi successivi

- Leggere ulteriori informazioni sull'[utilizzo di Windows PowerShell per amministrare il dispositivo StorSimple](storsimple-windows-powershell-administration.md).

- Ulteriori informazioni sull’[utilizzo del servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0615_2016-->