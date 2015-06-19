1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com). 
Sulla barra dei comandi fare clic su **New**.

2. Fare clic su **Virtual Machine**, quindi su **From Gallery**.

3. In **Choose an Image**, selezionare un'immagine da uno degli elenchi. Le immagini visualizzate possono variare in base alla sottoscrizione in uso. Fare clic sulla freccia per continuare.

4. Se sono disponibili più versioni, selezionare quella da usare in **Version Release Date**.

5. In **Virtual Machine Name** digitare il nome da usare. Per questa macchina virtuale digitare **MyTestVM1**.

6. In **Size** selezionare le dimensioni da usare per la macchina virtuale. Il valore da selezionare dipende dal numero di core necessari per l'applicazione. Per questa macchina virtuale selezionare la dimensione minima disponibile.

7. In **New User Name** digitare il nome dell'account che verrà utilizzato per amministrare la macchina virtuale. Non è possibile usare ROOT per questo nome utente. Per questa macchina virtuale digitare **NewUser1**.

8. In Autenticazione selezionare **Provide a Password**. Fornire quindi le informazioni necessarie e fare clic sulla freccia per continuare.

9. È possibile collocare macchine virtuali insieme nel servizio cloud, ma, ai fini della presente esercitazione, verrà creata una sola macchina virtuale. A tale scopo, selezionare **Create a new cloud service**.

10. In **Cloud Service DNS Name** digitare un nome composto da un minimo di 3 a un massimo di 24 caratteri, tra lettere in minuscolo e numeri. Occorre usare il nome del proprio servizio cloud perché deve essere univoco in Azure. Il nome del servizio cloud viene incluso nell'URI usato per contattare la macchina virtuale tramite il servizio cloud.

11. In **Region/Affinity Group/Virtual Network** selezionare l'area in cui situare la macchina virtuale.

12. È possibile selezionare un account di archiviazione in cui è archiviato il file VHD. Per questa esercitazione accettare l'impostazione predefinita di **Use an Automatically Generated Storage Account**.

13. Per le finalità di questa esercitazione, in **Availability Set** utilizzare l'impostazione predefinita **None**.

14.	In **Endpoints** esaminare l'endpoint creato automaticamente per consentire connessioni Secure Shell (SSH) alla macchina virtuale. Gli endpoint consentono a risorse in Internet o in altre reti virtuali di comunicare con una macchina virtuale. È possibile creare altri endpoint ora oppure aggiungerne in seguito. Per istruzioni su come crearli in un secondo momento, vedere [Come configurare gli endpoint a una macchina virtuale](../articles/virtual-machines/virtual-machines-set-up-endpoints.md).

15.  In **Agente di macchine virtuali**, esaminare le estensioni disponibili. Tali estensioni forniscono varie funzionalità che semplificano l'uso e la gestione di una macchina virtuale. Per informazioni dettagliate, vedere [Estensioni delle macchine virtuali di Azure](http://go.microsoft.com/FWLink/p/?LinkID=390493).


Dopo aver creato la macchina virtuale e il servizio cloud in Azure, questi vengono elencati nel portale di gestione rispettivamente in **Macchine virtuali** e **Servizi cloud**. Sia la macchina virtuale che il servizio cloud vengono avviati automaticamente. 
<!--HONumber=52-->
