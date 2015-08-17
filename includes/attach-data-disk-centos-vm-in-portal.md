1. Nel [portale di gestione](http://manage.windowsazure.com) di Azure fare clic su **Macchine virtuali**, quindi selezionare la macchina virtuale appena creata (**testlinuxvm**).

2. Sulla barra dei comandi fare clic su **Attach** e quindi su **Attach Empty Disk**.

	Verrà visualizzata la finestra di dialogo **Connetti disco vuoto**.


3. Le impostazioni **Virtual Machine Name**, **Storage Location** e **File Name** sono definite automaticamente. È sufficiente immettere la dimensione desiderata per il disco. Digitare **5** nel campo **Size**.

	![Attach Empty Disk][Image2]

	**Nota**: tutti i dischi vengono creati da un file VHD in Archiviazione di Azure. È possibile specificare un nome per il file .vhd file che viene aggiunto alla risorsa di archiviazione. Il nome del disco, tuttavia, viene generato automaticamente in Azure.

4. Fare clic sul segno di spunta per collegare il disco dati alla macchina virtuale.

5. Fare clic sul nome della macchina virtuale per visualizzare il dashboard. In questo modo è possibile verificare se il disco dati è stato collegato correttamente alla macchina virtuale. Il disco collegato verrà visualizzato nella tabella **Disks**.

	Quando si collega un disco dati, questo potrà essere usato solo dopo che è stato eseguito l'accesso per completare la configurazione.

##Connettersi alla macchina virtuale tramite SSH o PuTTY e completare la configurazione
Accedere alla macchina virtuale per completare la configurazione del disco in modo da poter usare il disco per l'archiviazione dei dati.

1. Dopo aver effettuato il provisioning della macchina virtuale, connettersi tramite SSH o PuTTY ed eseguire l'accesso come **newuser** (come descritto nei passaggi precedenti).	


2. Nella finestra di SSH o PuTTY digitare il comando seguente e quindi immettere la password dell'account:

	`$ sudo grep SCSI /var/log/messages`

	L'identificatore dell'ultimo disco dati aggiunto sarà disponibile nei messaggi visualizzati (in questo esempio, **sdc**).

	![GREP][Image4]


3. Nella finestra di SSH o PuTTY immettere il comando seguente per effettuare la partizione del disco **/dev/sdc**:

	`$ sudo fdisk /dev/sdc`


4. Immettere **n** per creare una nuova partizione.

	![FDISK][Image5]


5. Premere **p** per impostare la partizione come primaria, digitare **1** per impostarla come prima partizione, quindi premere INVIO per accettare il valore predefinito (1) per il cilindro.

	![FDISK][Image6]


6. Digitare **p** per visualizzare i dettagli relativi al disco da suddividere in partizioni.

	![FDISK][Image7]


7. Digitare **w** per scrivere le impostazioni per il disco.

	![FDISK][Image8]


8. Formattare il nuovo disco mediante il comando **mkfs**:

	`$ sudo mkfs -t ext4 /dev/sdc1`

9. È quindi necessario disporre di una directory per il montaggio del nuovo file system. Digitare, ad esempio, il comando seguente per creare una nuova directory per il montaggio dell'unità, quindi immettere la password per l'account:

	`sudo mkdir /datadrive`


10. Digitare il comando seguente per montare l'unità:

	`sudo mount /dev/sdc1 /datadrive`

	Il disco dati è ora pronto per l'uso come **/datadrive**.


11. Aggiungere la nuova unità a /etc/fstab:

	Per assicurarsi che l'unità venga rimontata automaticamente dopo un riavvio, è necessario aggiungerla al file /etc/fstab. È inoltre consigliabile che l'UUID (Universally Unique IDentifier) usato in /etc/fstab faccia riferimento all'unità anziché al solo nome del dispositivo, ad esempio /dev/sdc1. Per individuare l'UUID della nuova unità, è possibile usare l'utilità **blkid**:
	
		`sudo -i blkid`

	L'output sarà simile al seguente:

		`/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
		`/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
		`/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`

	>[AZURE.NOTE]blkidw potrebbe non richiedere l'accesso sudo in tutti i casi ma potrebbe risultare più facile eseguirlo con `sudo -i` su distribuzioni in cui /sbin o /usr/sbin non sono presenti nei percorsi indicati in `$PATH`.

	**Attenzione**: se il file /etc/fstab non viene modificato in modo corretto, il sistema potrebbe diventare instabile. In caso di dubbi, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente questo file. È inoltre consigliabile creare una copia di backup del file /etc/fstab prima della modifica.

	Usare un editor di testo per immettere le informazioni sul nuovo file system alla fine del file /etc/fstab. In questo esempio verranno usati il valore UUID del nuovo dispositivo **/dev/sdc1** creato nella procedura precedente, nonché il punto di montaggio **/datadrive**:

		`UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

	Anche le eventuali altre unità o partizioni dati create dovranno essere inserite separatamente in /etc/fstab.

	Per verificare che il file system venga montato correttamente, è sufficiente smontarlo e rimontarlo, usando ad esempio il punto di montaggio `/datadrive` creato nei passaggi precedenti:

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	Se il secondo comando restituisce un errore, verificare il file /etc/fstab per ottenere la sintassi corretta.


	>[AZURE.NOTE]Se si rimuove successivamente un disco dati senza modificare fstab, è possibile che si verifichi un errore di avvio della macchina virtuale. Se si tratta di un errore ricorrente, nella maggior parte delle distribuzioni sono disponibili le opzioni `nofail` e/o `nobootwait` fstab, che consentono l'avvio di un sistema anche in caso di assenza del disco. Per altre informazioni su tali parametri, fare riferimento alla documentazione della distribuzione.


[Image2]: ./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM2.png
[Image4]: ./media/attach-data-disk-centos-vm-in-portal/GrepScsiMessages.png
[Image5]: ./media/attach-data-disk-centos-vm-in-portal/fdisk1.png
[Image6]: ./media/attach-data-disk-centos-vm-in-portal/fdisk2.png
[Image7]: ./media/attach-data-disk-centos-vm-in-portal/fdisk3.png
[Image8]: ./media/attach-data-disk-centos-vm-in-portal/fdisk4.png
[Image9]: ./media/attach-data-disk-centos-vm-in-portal/mkfs.png

<!---HONumber=August15_HO6-->