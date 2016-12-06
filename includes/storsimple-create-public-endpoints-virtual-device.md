#### <a name="to-create-public-endpoints-on-the-virtual-device"></a>Per creare endpoint pubblici nel dispositivo virtuale

1. Accedere al portale di Microsoft Azure classico.
2. Fare clic su **Macchine virtuali**, quindi selezionare la macchina virtuale che viene utilizzata come dispositivo virtuale.
3. Fare clic su **Endpoint**. Nella pagina **Endpoint** sono elencati tutti gli endpoint disponibili per la macchina virtuale.
4. Fare clic su **Aggiungi**. Verrà visualizzata la finestra di dialogo **Add Endpoint** . Fare clic sulla freccia per continuare.
5. In **Nome** digitare il nome seguente per l'endpoint: **WinRMHttps**.
6. In **Protocollo** specificare **TCP**.
7. Per la **porta pubblica**, digitare i numeri di porta che si desidera utilizzare per la connessione.
8. In **Porta privata** digitare **5986**.
9. Fare clic sul segno di spunta per creare l'endpoint.

Dopo aver creato l'endpoint, è possibile visualizzarne i dettagli e determinare l'indirizzo IP virtuale pubblico (VIP, Virtual Private Network). Registrare tale indirizzo.



<!--HONumber=Nov16_HO3-->


