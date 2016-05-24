## Creare un hub eventi

1. Accedere al [portale di Azure classico][] e fare clic su **NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, **Bus di servizio**, **Hub eventi** e infine **Creazione rapida**.

	![][1]

3. Digitare un nome per l'hub eventi, selezionare l'area desiderata, quindi fare clic su **Crea un nuovo hub eventi**.

	![][2]

4. Se in una determinata area non è stato selezionato in modo esplicito uno spazio dei nomi esistente, il portale ne crea uno, in genere nel formato ***nome hub eventi*-ns**. Fare clic su tale spazio dei nomi, in questo esempio **eventhub-ns**.

	![][3]

5. Nella parte inferiore della pagina, fare clic su **Informazioni di connessione**. Fare clic sul pulsante di copia (illustrato nella figura seguente) per copiare la stringa di connessione **RootManageSharedAccessKey** negli Appunti. Salvare questa stringa di connessione per usarla più avanti nell'esercitazione.

	![][4]

L'hub eventi è stato creato e sono disponibili le stringhe di connessione necessarie per inviare e ricevere eventi.

[1]: ./media/event-hubs-create-event-hub/create-event-hub1.png
[2]: ./media/event-hubs-create-event-hub/create-event-hub2.png
[3]: ./media/event-hubs-create-event-hub/create-event-hub3.png
[4]: ./media/event-hubs-create-event-hub/create-conn-str1.png