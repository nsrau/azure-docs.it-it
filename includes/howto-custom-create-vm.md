<properties authors="kathydav" editor="tysonn" manager="jeffreyg" />

#Come creare una macchina virtuale personalizzata

Per macchina virtuale *personalizzata* si intende una macchina virtuale creata con il metodo **Da raccolta**, che consente di configurare un numero maggiore di opzioni rispetto al metodo **Creazione rapida**. Tali opzioni includono:

- Più possibilità di scelta per l'immagine da usare per creare la macchina virtuale
- Connessione della macchina virtuale a una rete virtuale 
- Aggiunta della macchina virtuale a un servizio cloud esistente 
- Aggiunta della macchina virtuale a un set di disponibilità

**Importante**: se si vuole usare una rete virtuale in modo da connettersi direttamente mediante nome host o per configurare connessioni cross-premise, accertarsi di specificare la rete virtuale quando si crea la macchina virtuale. È possibile configurare una macchina virtuale in modo da aggiungerla a una rete virtuale solo quando viene creata. Per altre informazioni sulle reti virtuali, vedere [Panoramica di Rete virtuale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com).

2. Sulla barra dei comandi fare clic su **Nuovo**.

3. Fare clic su **Calcolo**, **Macchina virtuale** e quindi su **Da raccolta**.

4. Scegliere l'immagine da usare, quindi fare clic sulla freccia per continuare.

5. Se sono disponibili più versioni, selezionare quella da usare in **Data di rilascio versione**. 

6. In **Nome macchina virtuale** digitare il nome da usare per la macchina virtuale.

7. Usare **Livello** e **Dimensione** per selezionare la dimensione appropriata per la macchina virtuale. La dimensione selezionata influisce sulla configurazione massima della macchina virtuale, oltre che sul prezzo. Per i dettagli di configurazione, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://go.microsoft.com/fwlink/p/?LinkID=389844).

8. In **Nuovo nome utente** digitare un nome per l'account amministrativo da usare per la gestione del server. 

9. In **Nuova password** immettere una password complessa per l'account amministrativo. In **Conferma password** ridigitare la password.

10. Fare clic sulla freccia per continuare.

11. In **Servizio cloud** eseguire una delle seguenti operazioni:
	
	- Se questa è la prima o l'unica macchina virtuale nel servizio cloud, selezionare **Crea un nuovo servizio cloud**. Quindi, in **Nome DNS del servizio cloud** digitare un nome composto da un minimo di 3 a un massimo di 24 caratteri, tra lettere in minuscolo e numeri. Tale nome sarà incluso nell'URI usato per contattare la macchina virtuale tramite il servizio cloud.
	- Se questa macchina virtuale deve essere aggiunta a un servizio cloud, selezionarla nell'elenco.

	**Nota**: per altre informazioni su come aggiungere le macchine virtuali nello stesso servizio cloud, vedere [Come connettere macchine virtuali in un servizio cloud](http://www.windowsazure.com/it-it/manage/windows/how-to-guides/connect-to-a-cloud-service/).

12. In **Regione/Gruppo di affinità/Rete virtuale** selezionare l'area, il gruppo di affinità o la rete virtuale da usare per la macchina virtuale. Per altre informazioni sui gruppi di affinità, vedere la pagina relativa alle [informazioni sui gruppi di affinità per la rete virtuale](http://msdn.microsoft.com/it-it/library/azure/jj156085.aspx).

13. In **Account di archiviazione** selezionare un account di archiviazione esistente per il file VHD o usarne uno generato automaticamente. Verrà creato automaticamente un solo account di archiviazione per ogni area. Tutte le altre macchine virtuali create con questa impostazione verranno inserite in questo account di archiviazione. È possibile creare un massimo di 20 account di archiviazione.

14. Se si vuole che la macchina virtuale faccia parte di un set di disponibilità, in **Set di disponibilità** selezionare **Crea set di disponibilità** o aggiungerla a un set di disponibilità esistente. 

	**Nota**: le macchine virtuali che fanno parte di un set di disponibilità vengono distribuite in diversi domini di errore. Collocando più macchine virtuali in un set di disponibilità è possibile garantire la disponibilità dell'applicazione in caso di errori della rete, guasti hardware di un disco locale ed eventuali tempi di inattività pianificati.

15.  In **Endpoint**verificare che i nuovi endpoint che verranno creati consentano le connessioni alla macchina virtuale, ad esempio tramite Desktop remoto o un client Secure Shell (SSH). È inoltre possibile aggiungere subito gli endpoint o crearli in un secondo momento. Per istruzioni su come crearli in un secondo momento, vedere [Come configurare gli endpoint a una macchina virtuale](http://azure.microsoft.com/it-it/documentation/articles/virtual-machines-set-up-endpoints/). 

16.  In **Agente di macchine virtuali** scegliere se installare l'agente VM. Tale agente offre l'ambiente per installare le estensioni che consentono di interagire con la macchina virtuale. Per informazioni dettagliate, vedere [Gestire le estensioni](http://go.microsoft.com/FWLink/p/?LinkID=390493).

17. Fare clic sulla freccia per creare la macchina virtuale.


	![Custom virtual machine creation successful](./media/howto-custom-create-vm/VMSuccessWindows.png)

##Passaggi successivi##
Una volta creata, la macchina virtuale viene avviata automaticamente. Quando dal portale risulta che lo stato è in esecuzione, è possibile eseguire l'accesso alla macchina virtuale. Per istruzioni, vedere uno degli articoli seguenti:

- [Come accedere a una macchina virtuale che esegue Linux](../virtual-machines-linux-how-to-log-on)
- [Come accedere a una macchina virtuale che esegue Windows Server](../virtual-machines-log-on-windows-server)


