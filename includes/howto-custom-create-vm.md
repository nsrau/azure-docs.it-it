<properties  authors="kathydav" editor="tysonn" manager="jeffreyg" />

# Come creare una macchina virtuale personalizzata

La creazione di una macchina virtuale personalizzata consente di scegliere opzioni che non sono disponibili utilizzando il metodo **Quick Create**. In tali opzioni è inclusa la connessione a una rete virtuale, a un servizio cloud esistente e a un set di disponibilità.

Ogni macchina virtuale è associata a un servizio cloud, da sola o insieme ad altre macchine virtuali nello stesso servizio cloud. Uno dei motivi comuni per i quali si collocano più macchine virtuali nello stesso servizio cloud è fornire il bilanciamento del carico per l'applicazione. Se l'applicazione richiede una sola macchina virtuale o se si sta creando la prima macchina virtuale, il servizio cloud verrà creato mentre si crea la macchina virtuale. In caso contrario, la nuova macchina virtuale verrà aggiunta a un servizio cloud esistente.

**Importante**: se si desidera utilizzare una rete virtuale in modo da connettersi direttamente mediante nome host o per configurare connessioni cross-premise, accertarsi di specificare la rete virtuale quando si crea la macchina virtuale. È possibile configurare una macchina virtuale in modo da aggiungerla a una rete virtuale solo quando viene creata. Per ulteriori informazioni sulle reti virtuali, vedere
[Informazioni generali su Rete virtuale di Azure][1].

1.  Accedere al [portale di gestione di Azure][2].

2.  Sulla barra dei comandi fare clic su **New**.

3.  Fare clic su **Compute**, quindi su **Virtual Machine** e infine su **From Gallery**.

4.  In **Platform Images** selezionare l'immagine della piattaforma da utilizzare e quindi fare clic sulla freccia per continuare.

5.  Se sono disponibili più versioni, selezionare quella da utilizzare in **Version Release Date**.

6.  In **Virtual Machine Name** digitare il nome da utilizzare per la macchina virtuale.

7.  In **Size** selezionare le dimensioni da utilizzare per la macchina virtuale. Il valore da selezionare dipende dal numero di core necessari per l'applicazione.

8.  In **New User Name** digitare un nome per l'account amministrativo da utilizzare per la gestione del server.

9.  In **New Password** digitare una password complessa per l'account amministrativo della macchina virtuale. In **Cloud Service** ridigitare la password.

10. Fare clic sulla freccia per continuare.

11. In **Cloud Service** eseguire una delle operazioni seguenti:

* Se questa è la prima o l'unica macchina virtuale nel servizio cloud, selezionare **Create a New Cloud Service**. Quindi, in **Cloud Service DNS Name** digitare un nome composto da un minimo di 3 a un massimo di
  24 caratteri, tra lettere in minuscolo e numeri. Tale nome sarà incluso nell'URI utilizzato per contattare la macchina virtuale tramite il servizio cloud.
* Se questa macchina virtuale deve essere aggiunta a un servizio cloud, selezionarla nell'elenco.
  
  	**Nota**: per ulteriori informazioni su come aggiungere le macchine virtuali nello stesso servizio cloud, vedere [Come connettere macchine virtuali in un servizio cloud][3].

1.  In **Region/Affinity Group/Virtual Network** selezionare l'area, il gruppo di affinità o la rete virtuale da utilizzare per la macchina virtuale. Per ulteriori informazioni sui gruppi di affinità, vedere [Informazioni sui gruppi di affinità per Rete virtuale][4].

2.  In **Storage Account** selezionare un account di archiviazione esistente per il file VHD o utilizzarne uno generato automaticamente. Verrà creato automaticamente un solo account di archiviazione per ogni area. Tutte le altre macchine virtuali create con questa impostazione verranno inserite in questo account di archiviazione. È possibile creare un massimo di 20 account di archiviazione.

3.  Se si desidera che la macchina virtuale faccia parte di un set di disponibilità, in **Availability Set** selezionare **Create availability set** o aggiungerla a un set di disponibilità esistente.
    
    **Nota**: le macchine virtuali che fanno parte di un set di disponibilità vengono distribuite in diversi domini di errore. Collocando più macchine virtuali in un set di disponibilità è possibile garantire la disponibilità dell'applicazione in caso di errori della rete, guasti hardware di un disco locale ed eventuali tempi di inattività pianificati.

4.  In **VM Agent** scegliere se installare l'agente VM. Tale agente offre l'ambiente per installare le estensioni che consentono di interagire con la macchina virtuale. Per informazioni dettagliate, vedere l'articolo sull'[utilizzo delle estensioni][5].

5.  In **Endpoints** verificare che i nuovi endpoint che verranno creati consentano le connessioni alla macchina virtuale, ad esempio tramite Desktop remoto o un client Secure Shell (SSH). È inoltre possibile aggiungere subito gli endpoint o crearli in un secondo momento. Per istruzioni su come crearli in un secondo momento, vedere [Come configurare le comunicazioni con una macchina virtuale][6].

6.  Fare clic sulla freccia per creare la macchina virtuale.
    
    ![Creazione della macchina virtuale personalizzata
    completata](./media/howto-custom-create-vm/VMSuccessWindows.png)



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://manage.windowsazure.com
[3]: http://www.windowsazure.com/en-us/manage/windows/how-to-guides/connect-to-a-cloud-service/
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/
[5]: http://go.microsoft.com/FWLink/p/?LinkID=390493
[6]: http://www.windowsazure.com/en-us/manage/linux/how-to-guides/setup-endpoints/