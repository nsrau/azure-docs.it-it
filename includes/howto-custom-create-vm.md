# <a name="how-to-create-a-custom-virtual-machine"></a>Come creare una macchina virtuale personalizzata
Per macchina virtuale *personalizzata* si intende una macchina virtuale creata con il metodo **Da raccolta**, che consente di configurare un numero maggiore di opzioni rispetto al metodo **Creazione rapida**. Tali opzioni includono:

* Più possibilità di scelta per l'immagine da usare per creare la macchina virtuale
* Connessione della macchina virtuale a una rete virtuale
* Aggiunta della macchina virtuale a un servizio cloud esistente
* Aggiunta della macchina virtuale a un set di disponibilità

> [!IMPORTANT]
> se si vuole usare una rete virtuale in modo da connettersi direttamente mediante nome host o per configurare connessioni cross-premise, accertarsi di specificare la rete virtuale quando si crea la macchina virtuale. È possibile configurare una macchina virtuale in modo da aggiungerla a una rete virtuale solo quando viene creata. Per ulteriori informazioni sulle reti virtuali, vedere [Informazioni generali su Rete virtuale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

1. Accedere al [portale di Azure](http://manage.windowsazure.com).
2. Sulla barra dei comandi fare clic su **Nuovo**.
3. Fare clic su **Calcolo**, quindi su **Macchina virtuale** e infine su **Da raccolta**.
4. Scegliere l'immagine da usare, quindi fare clic sulla freccia per continuare.
5. Se sono disponibili più versioni, selezionare quella da usare in **Data di rilascio versione**.
6. In **Nome macchina virtuale**digitare il nome da usare per la macchina virtuale.
7. Usare **Livello** e **Dimensione** per selezionare la dimensione appropriata per la macchina virtuale. La dimensione selezionata influisce sulla configurazione massima della macchina virtuale, oltre che sul prezzo. Per i dettagli di configurazione, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://go.microsoft.com/fwlink/p/?LinkID=389844).
8. In **Nuovo nome utente**digitare un nome per l'account amministrativo da usare per la gestione del server.
9. In **Nuova password**immettere una password complessa per l'account amministrativo. In **Servizio cloud**ridigitare la password.
10. Fare clic sulla freccia per continuare.
11. In **Servizio cloud**eseguire una delle operazioni seguenti:
    
    * Se questa è la prima o l'unica macchina virtuale nel servizio cloud, selezionare **Crea un nuovo servizio cloud**. Quindi, in **Nome DNS del servizio cloud**digitare un nome composto da un minimo di 3 a un massimo di 24 caratteri, tra lettere in minuscolo e numeri. Tale nome sarà incluso nell'URI utilizzato per contattare la macchina virtuale tramite il servizio cloud.
    * Se questa macchina virtuale deve essere aggiunta a un servizio cloud, selezionarla nell'elenco.
    
    > [!NOTE]
    > per ulteriori informazioni su come aggiungere le macchine virtuali nello stesso servizio cloud, vedere [Come connettere macchine virtuali in un servizio cloud](https://azure.microsoft.com/manage/windows/how-to-guides/connect-to-a-cloud-service/).
    > 
    > 
12. In **Area/Gruppo di affinità/Rete virtuale**selezionare l'area, il gruppo di affinità o la rete virtuale da usare per la macchina virtuale. Per altre informazioni sui gruppi di affinità, vedere [Informazioni sui gruppi di affinità per Rete virtuale](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md).
13. In **Account di archiviazione**selezionare un account di archiviazione esistente per il file VHD o usarne uno generato automaticamente. Verrà creato automaticamente un solo account di archiviazione per ogni area. Tutte le altre macchine virtuali create con questa impostazione verranno inserite in questo account di archiviazione. È possibile creare un massimo di 20 account di archiviazione.
14. Se si vuole che la macchina virtuale faccia parte di un set di disponibilità, in **Set di disponibilità** selezionare **Crea set di disponibilità** o aggiungere la macchina a un set di disponibilità esistente.
    
    **Nota**: le macchine virtuali in un set di disponibilità vengono distribuite in diversi domini di errore. Collocando più macchine virtuali in un set di disponibilità è possibile garantire la disponibilità dell'applicazione in caso di errori della rete, guasti hardware di un disco locale ed eventuali tempi di inattività pianificati.
15. In **Endpoint**verificare che i nuovi endpoint che verranno creati consentano le connessioni alla macchina virtuale, ad esempio tramite Desktop remoto o un client Secure Shell (SSH). È inoltre possibile aggiungere subito gli endpoint o crearli in un secondo momento. Per istruzioni su come crearli in un secondo momento, vedere [Come configurare gli endpoint in una macchina virtuale](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
16. In **Agente di macchine virtuali**scegliere se installare l'agente VM. Tale agente offre l'ambiente per installare le estensioni che consentono di interagire con la macchina virtuale. Per informazioni dettagliate, vedere [Gestire le estensioni](http://go.microsoft.com/FWLink/p/?LinkID=390493).
17. Fare clic sulla freccia per creare la macchina virtuale.
    
    ![Creazione della macchina virtuale personalizzata completata](./media/howto-custom-create-vm/VMSuccessWindows.png)

## <a name="next-steps"></a>Passaggi successivi
Una volta creata, la macchina virtuale viene avviata automaticamente. Quando dal portale risulta che lo stato è in esecuzione, è possibile eseguire l'accesso alla macchina virtuale. Per istruzioni, vedere gli articoli seguenti:

* [Come accedere a una macchina virtuale che esegue Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Come accedere a una macchina virtuale che esegue Windows Server](../articles/virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

