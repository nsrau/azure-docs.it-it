## Creare un hub eventi

1. Accedere al [portale di Azure][] e fare clic su **Nuovo** nella parte superiore sinistra della schermata.

2. Fare clic su **Dati e analisi** e quindi su **Hub eventi**.

	![][9]  

3. Nel pannello **Crea spazio dei nomi** immettere un nome per lo spazio dei nomi. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.

	![](./media/event-hubs-create-event-hub/create-event-hub1.png)  

4. Dopo aver verificato che il nome dello spazio dei nomi sia disponibile, scegliere il piano tariffario (Basic o Standard). Scegliere anche una sottoscrizione, un gruppo di risorse e una località di Azure in cui creare la risorsa.

2. Fare clic su **Crea** per creare lo spazio dei nomi.

6. Nell'elenco degli spazi dei nomi di Hub eventi fare clic sullo spazio dei nomi appena creato.

	![][2]  

7. Nel pannello dello spazio dei nomi fare clic su **Hub eventi**.

	![][3]  

8. Nella parte superiore del pannello fare clic su **Aggiungi hub eventi**.

	![][4]  

3. Digitare un nome per l'hub eventi e quindi fare clic su **Crea**.

	![][5]  

4. Nell'elenco degli hub eventi fare clic sul nome dell'hub eventi appena creato.

	![][6]  

5. Nel pannello dello spazio dei nomi (non in quello dello specifico hub eventi) fare clic su **Criteri di accesso condiviso** e quindi su **RootManageSharedAccessKey**.

	![](./media/event-hubs-create-event-hub/create-event-hub7.png)  

5. Fare clic sul pulsante di copia per copiare la stringa di connessione **RootManageSharedAccessKey** negli Appunti. Salvare questa stringa di connessione per usarla più avanti nell'esercitazione.

	![][8]

L'hub eventi è stato creato e sono disponibili le stringhe di connessione necessarie per inviare e ricevere eventi.

[2]: ./media/event-hubs-create-event-hub/create-event-hub2.png
[3]: ./media/event-hubs-create-event-hub/create-event-hub3.png
[4]: ./media/event-hubs-create-event-hub/create-event-hub4.png
[5]: ./media/event-hubs-create-event-hub/create-event-hub5.png
[6]: ./media/event-hubs-create-event-hub/create-event-hub6.png
[8]: ./media/event-hubs-create-event-hub/create-event-hub8.png
[9]: ./media/event-hubs-create-event-hub/create-event-hub9.png

[portale di Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0914_2016-->