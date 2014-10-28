<properties authors="kathydav" editor="tysonn" manager="jeffreyg" />

# Come creare una macchina virtuale personalizzata

Per macchina virtuale *personalizzata* si intende una macchina virtuale creata con il metodo **Da raccolta**, che consente di configurare un numero maggiore di opzioni rispetto al metodo **Creazione rapida**. Tali opzioni includono:

-   Più possibilità di scelta per l'immagine da usare per creare la macchina virtuale
-   Connessione della macchina virtuale a una rete virtuale
-   Aggiunta della macchina virtuale a un servizio cloud esistente
-   Aggiunta della macchina virtuale a un set di disponibilità

**Importante**: se si desidera utilizzare una rete virtuale in modo da connettersi direttamente mediante nome host o per configurare connessioni cross-premise, accertarsi di specificare la rete virtuale quando si crea la macchina virtuale. È possibile configurare una macchina virtuale in modo da aggiungerla a una rete virtuale solo quando viene creata. Per altre informazioni sulle reti virtuali, vedere [Informazioni generali su Rete virtuale di Azure][Informazioni generali su Rete virtuale di Azure].

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Sulla barra dei comandi fare clic su **New**.

3.  Fare clic su **Compute**, quindi su **Virtual Machine** e infine su **From Gallery**.

4.  Scegliere l'immagine che si desidera usare, quindi fare clic sulla freccia per continuare.

5.  Se sono disponibili più versioni, selezionare quella da utilizzare in **Version Release Date**.

6.  In **Virtual Machine Name** digitare il nome da utilizzare per la macchina virtuale.

7.  Usare **Livello** e **Dimensione** per selezionare la dimensione appropriata per la macchina virtuale. La dimensione selezionata influisce sulla configurazione massima della macchina virtuale, oltre che sul prezzo. Per i dettagli di configurazione, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure][Dimensioni delle macchine virtuali e dei servizi cloud per Azure].

8.  In **New User Name** digitare un nome per l'account amministrativo da utilizzare per la gestione del server.

9.  In **New Password** immettere una password complessa per l'account amministrativo. In **Cloud Service** ridigitare la password.

10. Fare clic sulla freccia per continuare.

11. In **Cloud Service** eseguire una delle operazioni seguenti:

    -   Se questa è la prima o l'unica macchina virtuale nel servizio cloud, selezionare **Create a New Cloud Service**. Quindi, in **Cloud Service DNS Name** digitare un nome composto da un minimo di 3 a un massimo di 24 caratteri, tra lettere in minuscolo e numeri. Tale nome sarà incluso nell'URI utilizzato per contattare la macchina virtuale tramite il servizio cloud.
    -   Se questa macchina virtuale deve essere aggiunta a un servizio cloud, selezionarla nell'elenco.

    **Nota**: per ulteriori informazioni su come aggiungere le macchine virtuali nello stesso servizio cloud, vedere [Come connettere macchine virtuali in un servizio cloud][Come connettere macchine virtuali in un servizio cloud].

12. In **Region/Affinity Group/Virtual Network** selezionare l'area, il gruppo di affinità o la rete virtuale da utilizzare per la macchina virtuale. Per ulteriori informazioni sui gruppi di affinità, vedere [Informazioni sui gruppi di affinità per Rete virtuale][Informazioni sui gruppi di affinità per Rete virtuale].

13. In **Storage Account** selezionare un account di archiviazione esistente per il file VHD o utilizzarne uno generato automaticamente. Verrà creato automaticamente un solo account di archiviazione per ogni area. Tutte le altre macchine virtuali create con questa impostazione verranno inserite in questo account di archiviazione. È possibile creare un massimo di 20 account di archiviazione.

14. Se si desidera che la macchina virtuale faccia parte di un set di disponibilità, in **Availability Set** selezionare **Create availability set** o aggiungerla a un set di disponibilità esistente.

    **Nota**: le macchine virtuali che fanno parte di un set di disponibilità vengono distribuite in diversi domini di errore. Collocando più macchine virtuali in un set di disponibilità è possibile garantire la disponibilità dell'applicazione in caso di errori della rete, guasti hardware di un disco locale ed eventuali tempi di inattività pianificati.

15. In **Endpoints** verificare che i nuovi endpoint che verranno creati consentano le connessioni alla macchina virtuale, ad esempio tramite Desktop remoto o un client Secure Shell (SSH). È inoltre possibile aggiungere subito gli endpoint o crearli in un secondo momento. Per istruzioni su come crearli in un secondo momento, vedere [Come configurare gli endpoint a una macchina virtuale][Come configurare gli endpoint a una macchina virtuale].

16. In **VM Agent** scegliere se installare l'agente VM. Tale agente offre l'ambiente per installare le estensioni che consentono di interagire con la macchina virtuale. Per informazioni dettagliate, vedere [Gestire le estensioni][Gestire le estensioni].

17. Fare clic sulla freccia per creare la macchina virtuale.

    ![Creazione della macchina virtuale personalizzata completata][Creazione della macchina virtuale personalizzata completata]

## Passaggi successivi

Una volta creata, la macchina virtuale viene avviata automaticamente. Quando dal portale risulta che lo stato è in esecuzione, è possibile eseguire l'accesso alla macchina virtuale. Per istruzioni, vedere gli articoli seguenti:

-   [Come accedere a una macchina virtuale che esegue Linux][Come accedere a una macchina virtuale che esegue Linux]
-   [Come accedere a una macchina virtuale che esegue Windows Server][Come accedere a una macchina virtuale che esegue Windows Server]

  [Informazioni generali su Rete virtuale di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Dimensioni delle macchine virtuali e dei servizi cloud per Azure]: http://go.microsoft.com/fwlink/p/?LinkID=389844
  [Come connettere macchine virtuali in un servizio cloud]: http://www.windowsazure.com/it-it/manage/windows/how-to-guides/connect-to-a-cloud-service/
  [Informazioni sui gruppi di affinità per Rete virtuale]: http://msdn.microsoft.com/it-it/library/azure/jj156085.aspx
  [Come configurare gli endpoint a una macchina virtuale]: http://azure.microsoft.com/it-it/documentation/articles/virtual-machines-set-up-endpoints/
  [Gestire le estensioni]: http://go.microsoft.com/FWLink/p/?LinkID=390493
  [Creazione della macchina virtuale personalizzata completata]: ./media/howto-custom-create-vm/VMSuccessWindows.png
  [Come accedere a una macchina virtuale che esegue Linux]: ../virtual-machines-linux-how-to-log-on
  [Come accedere a una macchina virtuale che esegue Windows Server]: ../virtual-machines-log-on-windows-server
