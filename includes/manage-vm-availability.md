<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />

# Gestione della disponibilità delle macchine virtuali

Per assicurare la disponibilità dell'applicazione, è consigliabile utilizzare più macchine virtuali che forniscano ridondanza, configurandone più di una per la stessa funzione o lo stesso ruolo. Questa ridondanza è necessaria per ottenere un livello di servizio garantito. Assicurarsi inoltre di considerare le dipendenze. Se ad esempio la macchina virtuale "IIS1" dipende dai servizi forniti dalla macchina virtuale "SQL1," la ridondanza per la macchina virtuale "SQL1" viene fornita da "SQL2" per evitare interruzioni del servizio. Per ulteriori informazioni sui contratti di servizio, vedere la sezione "Servizi cloud, macchine virtuali e rete virtuale" in [Contratti di servizio][1].

Con questo approccio è possibile garantire la disponibilità dell'applicazione in caso di errori della rete locale, guasti hardware di un disco locale ed eventuali tempi di inattività pianificati che possono essere necessari per la piattaforma.

Per gestire la disponibilità di un'applicazione che utilizza più macchine virtuali, aggiungere le macchine virtuali a un set di disponibilità. I set di disponibilità sono direttamente correlati ai domini di errore e ai domini di aggiornamento. In Azure un dominio di errore viene definito evitando singoli punti di errore, come il commutatore di rete o l'unità di alimentazione di un rack di server. In realtà, un dominio di errore è quasi equivalente a un rack di server fisici. Se in un servizio cloud sono connesse più macchine virtuali, tramite un set di disponibilità verranno inserite in domini di errore diversi. Nel diagramma seguente sono illustrati due set di disponibilità, ognuno con due macchine virtuali.

![Domini di
aggiornamento](./media/manage-vm-availability/UpdateDomains.png)

In Azure il sistema operativo che ospita le istanze di un'applicazione viene aggiornato periodicamente. Quando vengono applicati aggiornamenti, le macchine virtuali vengono arrestate. I domini di aggiornamento consentono di assicurarsi che le istanze di macchine virtuali non vengano aggiornate tutte contemporaneamente. Se si aggiungono più macchine virtuali a un set di disponibilità, Azure garantisce che vengano assegnate a domini di aggiornamento diversi. Nel diagramma precedente sono illustrate due macchine virtuali che eseguono Internet Information Services (IIS) in domini di aggiornamento distinti e due che eseguono SQL Server, anche in questo caso in domini di aggiornamento distinti.

Per assicurare la continua disponibilità e l'esecuzione efficiente dell'applicazione, è consigliabile utilizzare una combinazione di set di disponibilità ed endpoint con carico bilanciato. Per ulteriori informazioni sugli endpoint con carico bilanciato, vedere [Bilanciamento del carico delle macchine virtuali](../load-balance-virtual-machines).

Questa attività include i passaggi seguenti:

* [Passaggio 1: Creare una macchina virtuale e un set di
  disponibilità](#createset)
* [Passaggio 2: Aggiungere una macchina virtuale al servizio cloud e
  assegnarla al set di disponibilità durante il processo di
  creazione](#addmachine)
* [Passaggio 3: (facoltativo) Creare un set di disponibilità per le
  macchine virtuali create in precedenza](#previousmachine)
* [Passaggio 4: (facoltativo) Aggiungere una macchina virtuale creata in
  precedenza a un set di disponibilità](#existingset)

## <a id="createset"> </a>Passaggio 1: Creare una macchina virtuale e un set di disponibilità

Per creare un set di disponibilità contenente macchine virtuali, è possibile creare la prima macchina virtuale e il set di disponibilità contemporaneamente, quindi aggiungerne altre quando vengono create. È inoltre possibile creare le macchine virtuali, creare un set di disponibilità e quindi aggiungervi tutte le macchina virtuali.

**Per creare una macchina virtuale e un set di disponibilità**

1.  Accedere al portale di gestione di Azure se questa operazione non è già stata eseguita.

2.  Sulla barra dei comandi fare clic su **New**.
    
    ![Creazione di una macchina
    virtuale](./media/manage-vm-availability/Create.png)

3.  Fare clic su **Virtual Machine**, quindi su **From Gallery**.
    
    Verrà visualizzata la finestra di dialogo **Select the virtual machine operating system**.

4.  In **Platform Images** selezionare un'immagine e quindi fare clic sulla freccia per continuare.
    
    Verrà visualizzata la finestra di dialogo **Virtual machine configuration**.

5.  In **Virtual Machine Name** digitare il nome da utilizzare per la macchina virtuale.

6.  In **New User Name** digitare un nome per l'account amministrativo da utilizzare per la gestione del server.

7.  In **New Password** immettere una password complessa per l'account amministrativo. In **Confirm Password** ridigitare la password immessa in precedenza.

8.  In **Size** selezionare le dimensioni da utilizzare per la macchina virtuale. Il valore da selezionare dipende dal numero di core necessari per l'applicazione.

9.  Fare clic sulla freccia per continuare.
    
    Verrà visualizzata la finestra di dialogo **Virtual machine mode**.

10. Scegliere **Stand-alone virtual machine**.

11. In **DNS Name** immettere un nome per il servizio cloud creato per la macchina. Il nome può essere composto da un minimo di 3 a un massimo di 24 caratteri, tra lettere in minuscolo e numeri.

12. In **Storage Account** selezionare un account di archiviazione in cui verrà archiviato il file con estensione vhd oppure scegliere che venga creato automaticamente. Verrà creato automaticamente un solo account di archiviazione per ogni area. Tutte le altre macchine virtuali create con questa impostazione verranno inserite in questo account di archiviazione. È possibile creare un massimo di 20 account di archiviazione.

13. In **Region/Affinity Group/Virtual Network** selezionare l'area, il gruppo di affinità o la rete virtuale che dovrà contenere la macchina virtuale. Per ulteriori informazioni sui gruppi di affinità, vedere [Informazioni sui gruppi di affinità per Rete virtuale][2].

14. Fare clic sulla freccia per continuare.
    
    Verrà visualizzata la finestra di dialogo **Virtual machine options**.

15. In **Availability Set** selezionare **Create availability set**.

16. In **Availability Set Name** immettere il nome del set di disponibilità.

17. Fare clic sulla freccia per creare la macchina virtuale e il set di disponibilità.
    
    Nel dashboard della nuova macchina virtuale è possibile fare clic su **Configure** per verificare se è un membro del nuovo set di disponibilità.

## <a id="addmachine"> </a>Passaggio 2: Aggiungere una macchina virtuale al servizio cloud e assegnarla al set di disponibilità durante il processo di creazione

Nel passaggio precedente è stato illustrato come creare una macchina virtuale e un set di disponibilità contemporaneamente. A questo punto è possibile creare una nuova macchina virtuale, connetterla al servizio cloud della prima macchina virtuale e quindi aggiungerla al set di disponibilità creato in precedenza.

**Per connettere una nuova macchina virtuale e aggiungerla al set di disponibilità**

1.  Accedere al portale di gestione di Azure se questa operazione non è già stata eseguita.

2.  Sulla barra dei comandi fare clic su **New**.
    
    ![Creazione di una macchina
    virtuale](./media/manage-vm-availability/Create.png)

3.  Fare clic su **Virtual Machine**, quindi su **From Gallery**.
    
    ![Creazione da
    raccolta](./media/manage-vm-availability/CreateNew.png)
    
    Verrà visualizzata la finestra di dialogo **Select the virtual machine operating system**. È possibile selezionare un'immagine dalla Raccolta immagini.

4.  Fare clic su **Platform Images**, selezionare l'immagine della piattaforma da utilizzare e quindi fare clic sulla freccia per continuare.
    
    Verrà visualizzata la finestra di dialogo **Virtual machine configuration**.

5.  In **Virtual Machine Name** digitare il nome da utilizzare per la macchina virtuale.

6.  6\.In **New User Name** digitare un nome per l'account amministrativo da utilizzare per la gestione del server.

7.  In **New Password** digitare una password complessa per l'account amministrativo della macchina virtuale. In **Confirm Password** ridigitare la password.

8.  In **Size** selezionare le dimensioni da utilizzare per la macchina virtuale. Il valore da selezionare dipende dal numero di core necessari per l'applicazione.

9.  Per proteggere le macchine virtuali che eseguono il sistema operativo Linux, è possibile selezionare una chiave SSH.

10. Fare clic sulla freccia per continuare.
    
    Verrà visualizzata la finestra di dialogo **Virtual machine mode**.

11. Selezionare **Connect to existing Virtual Machine** per creare una nuova macchina virtuale che verrà connessa alla prima macchina virtuale nel set di disponibilità. Selezionare il servizio cloud contenente la macchina virtuale nel set di disponibilità.

12. In **Storage Account** selezionare l'account di archiviazione in cui è archiviato il file VHD.

13. In **Region/Affinity Group/Virtual Network** selezionare l'area che dovrà contenere la macchina virtuale.

14. Fare clic sulla freccia per continuare.
    
    Verrà visualizzata la finestra di dialogo **Virtual machine options**.

15. Selezionare il set di disponibilità creato contemporaneamente alla prima macchina virtuale.

16. Fare clic sul segno di spunta per creare la macchina virtuale connessa e aggiungerla al set di disponibilità.
    
    Nel dashboard della nuova macchina virtuale è possibile fare clic su **Configure** per verificare se è un membro del nuovo set di disponibilità.

## <a id="previousmachine"> </a>Passaggio 3: (facoltativo) Creare un set di disponibilità per le macchine virtuali create in precedenza

È possibile creare un set di disponibilità e aggiungervi in seguito una macchina virtuale. Dopo aver creato una macchina virtuale, è infatti possibile configurarne le dimensioni e specificare se è un membro di un set di disponibilità.

**Per creare un nuovo set di disponibilità**

1.  Accedere al portale di gestione di Azure se questa operazione non è già stata eseguita.

2.  Fare clic su **Virtual Machines** e quindi selezionare la macchina virtuale da configurare.

3.  Fare clic su **Configure**.

4.  Nella sezione **Availability Set** selezionare **Create Availability Set** e quindi immettere il relativo nome.

5.  Fare clic su **Save**.
    
    **Nota:** è possibile che la macchina virtuale venga riavviata per finalizzare l'appartenenza al set di disponibilità.

## <a id="existingset"> </a>Passaggio 4: (facoltativo) Aggiungere una macchina virtuale creata in precedenza a un set di disponibilità

È possibile aggiungere facilmente una macchina virtuale esistente a un set di disponibilità creato in precedenza. In questo caso, è necessario che la macchina virtuale sia connessa allo stesso servizio cloud delle altre macchine virtuali del set. Per ulteriori informazioni sulla connessione delle macchine virtuali, vedere [Come connettere macchine virtuali in un servizio cloud](../virtual-machines-connect-cloud-service).

**Per aggiungere una macchina virtuale esistente a un set di disponibilità**

1.  Accedere al portale di gestione di Azure se questa operazione non è già stata eseguita.

2.  Fare clic su **Virtual Machine** e quindi selezionare la macchina virtuale da aggiungere al set di disponibilità.

3.  Fare clic su **Configure**.

4.  Nella sezione **Availability Set** selezionare il set di disponibilità creato in precedenza.

5.  Fare clic su **Save**.
    
    **Nota:** è possibile che la macchina virtuale venga riavviata per finalizzare l'appartenenza al set di disponibilità.

<!-- LINKS -->



[1]: http://www.windowsazure.com/en-us/support/legal/sla/
[2]: http://msdn.microsoft.com/library/windowsazure/jj156085.aspx