#### Per creare endpoint pubblici nel dispositivo virtuale
1. Accedere al portale di Microsoft Azure classico.

* Fare clic su **Macchine virtuali**, quindi selezionare la macchina virtuale che viene utilizzata come dispositivo virtuale.
* Fare clic su **Endpoint**. Nella pagina **Endpoint** sono elencati tutti gli endpoint disponibili per la macchina virtuale.
* Fare clic su **Aggiungi**. Verrà visualizzata la finestra di dialogo **Add Endpoint**. Fare clic sulla freccia per continuare.
* Nel campo **Nome** digitare il nome seguente per l'endpoint: **WinRMHttps**.
* Per il **protocollo**, specificare **TCP**.
* Per la **porta pubblica**, digitare i numeri di porta che si desidera utilizzare per la connessione.
* Specificare **5986** per la **porta privata**.
* Fare clic sul segno di spunta per creare l'endpoint.

Dopo aver creato l'endpoint, è possibile visualizzarne i dettagli e determinare l'indirizzo IP virtuale pubblico (VIP, Virtual Private Network). Registrare tale indirizzo.

<!---HONumber=AcomDC_0128_2016-->