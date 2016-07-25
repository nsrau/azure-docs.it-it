


Un set di disponibilità aiuta a mantenere disponibili le macchine virtuali durante il tempo di inattività, ad esempio durante la manutenzione. L'inserimento di due o più macchine virtuali con configurazione simile in un set di disponibilità crea la ridondanza necessaria a mantenere la disponibilità delle applicazioni o dei servizi eseguiti sulla macchina virtuale. Per i dettagli sul funzionamento, vedere [Gestione della disponibilità delle macchine virtuali][].

Per assicurare la continua disponibilità e l'esecuzione efficiente dell'applicazione, è buona norma usare sia set di disponibilità sia endpoint con carico bilanciato. Per informazioni sugli endpoint con bilanciamento del carico, vedere [Bilanciamento del carico per i servizi di infrastruttura di Azure][].

È possibile aggiungere le macchine virtuali in un set di disponibilità usando una di queste due opzioni:

- [Opzione 1: Creare una macchina virtuale e un set di disponibilità contemporaneamente][]. Quindi, aggiungere le nuove macchine virtuali al set.
- [Opzione 2: Aggiungere una macchina virtuale esistente a un set di disponibilità][].

>[AZURE.NOTE] Nel modello classico, le macchine virtuali che si desidera inserire nello stesso set di disponibilità devono appartenere allo stesso servizio cloud.

## <a id="createset"> </a>Opzione 1: Creare una macchina virtuale e un set di disponibilità contemporaneamente.##

A tale scopo, è possibile usare il portale di Azure o i comandi di Azure PowerShell.

Per usare il Portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com), se questa operazione non è già stata eseguita.

2. Nel menu Hub fare clic su **+ Nuovo**, quindi su **Macchina virtuale**.
    
    ![Testo immagine alt](./media/virtual-machines-common-classic-configure-availability/ChooseVMImage.png)

3. Selezionare l'immagine della macchina virtuale del Marketplace che si desidera usare. È possibile scegliere di creare una macchina virtuale Linux o Windows.

4. Per la macchina virtuale selezionata verificare che il modello di distribuzione sia impostato su **Classico**, quindi fare clic su **Crea**
    
    ![Testo immagine alt](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)

5. Immettere il nome della macchina virtuale, il nome utente e la password (per computer Windows) o la chiave pubblica SSH (per i computer Linux).

6. Scegliere le dimensioni della VM, quindi fare clic su **Seleziona** per continuare.

7. Scegliere **Configurazione facoltativa > Set di disponibilità**, quindi selezionare il set di disponibilità che si vuole aggiungere alla macchina virtuale.
    
    ![Testo immagine alt](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png)

8. Verificare le impostazioni di configurazione. Al termine dell'operazione, scegliere **Crea**.

9. Mentre Azure crea la macchina virtuale, è possibile tenere traccia dello stato di avanzamento nel menu Hub in **Macchine virtuali**.

Per usare i comandi di Azure PowerShell per creare una macchina virtuale di Azure e aggiungerla a un set di disponibilità nuovo o esistente, vedere [Creare macchine virtuali Windows con il modello di distribuzione classica e PowerShell](../articles/virtual-machines/virtual-machines-windows-classic-create-powershell.md)

## <a id="addmachine"></a>Opzione 2: Aggiungere una macchina virtuale esistente a un set di disponibilità.##

Nel Portale di Azure è possibile aggiungere le macchine virtuali classiche a un set di disponibilità esistente oppure creare un set nuovo per le macchine. (si noti che le macchine virtuali presenti nello stesso set di disponibilità devono appartenere allo stesso servizio cloud). La procedura è quasi la stessa: con Azure PowerShell è possibile aggiungere la macchina virtuale a un set di disponibilità esistente.

1. Accedere al [portale di Azure](https://portal.azure.com), se questa operazione non è già stata eseguita.

2. Fare clic su **Macchine virtuali (classico)** nel menu Hub.
    
    ![Testo immagine alt](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)

3. Nell'elenco delle macchine virtuali selezionare il nome della macchina virtuale che si desidera aggiungere al set.

4. Scegliere **Set di disponibilità** in **Impostazioni** della macchina virtuale.
    
    ![Testo immagine alt](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)

5. Selezionare il set di disponibilità che si desidera aggiungere alla macchina virtuale. La macchina virtuale deve appartenere allo stesso servizio cloud del set di disponibilità.
    
    ![Testo immagine alt](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)

6. Fare clic su **Save**.

Per usare i comandi di Azure PowerShell, aprire una sessione di Azure PowerShell con privilegi di amministratore ed attivare il seguente comando. Per i segnaposto, ad esempio &lt;VmCloudServiceName&gt;, sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < e >, con i nomi corretti.

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE] Potrebbe essere necessario riavviare la macchina virtuale per completarne l'aggiunta al set di disponibilità.


<!-- LINKS -->
[Opzione 1: Creare una macchina virtuale e un set di disponibilità contemporaneamente]: #createset
[Opzione 2: Aggiungere una macchina virtuale esistente a un set di disponibilità]: #addmachine

[Bilanciamento del carico per i servizi di infrastruttura di Azure]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Gestione della disponibilità delle macchine virtuali]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

<!---HONumber=AcomDC_0713_2016-->