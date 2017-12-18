Verificare che sia già stato creato uno spazio dei nomi del bus di servizio, come illustrato [qui][namespace-how-to].

1. Accedere al [portale di Azure][azure-portal].
2. Nel riquadro di spostamento a sinistra del portale, fare clic su **Bus di servizio** (se non viene visualizzato **Bus di servizio**, fare clic su **Altri servizi**).
3. Fare clic sullo spazio dei nomi in cui si vuole creare la coda. In questo caso è **sbnstest1**.
   
    ![Creare una coda][createqueue1]
4. Nella finestra dello spazio dei nomi fare clic su **Code** e quindi nella finestra **Code** fare clic su **+ Coda**.
   
    ![Selezionare Code][createqueue2]
5. Immettere il nome della coda in **Nome** e lasciare le impostazioni predefinite per gli altri valori.
   
    ![Selezionare Nuovo][createqueue3]
6. Nella parte inferiore della finestra fare clic su **Crea**.

[createqueue1]: ./media/service-bus-create-queue-portal/create-queue1.png
[createqueue2]: ./media/service-bus-create-queue-portal/create-queue2.png
[createqueue3]: ./media/service-bus-create-queue-portal/create-queue3.png

[namespace-how-to]: ../articles/service-bus-messaging/service-bus-create-namespace-portal.md
[azure-portal]: https://portal.azure.com
