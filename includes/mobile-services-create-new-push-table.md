
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/), fare clic su **Servizi mobili** e quindi sull'app.

	![](./media/mobile-services-create-new-push-table/mobile-services-selection.png)

2. Fare clic sulla scheda **Data** e quindi su **Create**.

	![](./media/mobile-services-create-new-push-table/mobile-create-table.png)

	Verrà visualizzata la finestra di dialogo **Create new table**.

3. Mantenere l'impostazione predefinita di **Anybody with the application key** per tutte le autorizzazioni, digitare _Registrations_ in **Table name**, quindi fare clic sul segno di spunta.

	![](./media/mobile-services-create-new-push-table/mobile-create-registrations-table.png)

  Verrà creata la tabella **Registrations**, in cui sono archiviati gli URI di canale utilizzati per l'invio di notifiche push.

In seguito, sarà necessario modificare l'app per abilitare le notifiche push.

<!---HONumber=AcomDC_1203_2015-->