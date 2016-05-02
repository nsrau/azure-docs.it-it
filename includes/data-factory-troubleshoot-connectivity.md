## Risoluzione dei problemi di connettività
Usare la scheda **Diagnostica** di **Gestione configurazione di Gateway di gestione dati** per risolvere i problemi di connessione.

1. Avviare **Gestione configurazione di Gateway di gestione dati**. È possibile eseguire direttamente "C:\\Programmi\\Microsoft Data Management Gateway\\1.0\\Shared\\ConfigManager.exe" o eseguire una ricerca di **Gateway** per trovare un collegamento all'applicazione **Gateway di gestione dati di Microsoft** come indicato nell'immagine seguente. 

	![Ricerca nel gateway](./media/data-factory-troubleshoot-connectivity/search-gateway.png)
2. Passare alla scheda **Diagnostica**.

	![Diagnostica del gateway](./media/data-factory-troubleshoot-connectivity/data-factory-gateway-diagnostics.png) 
3. Selezionare il **tipo** di dati archiviati (servizio collegato). 
4. Specificare l'**autenticazione** e immettere le **credenziali** o immettere la **stringa di connessione** per la connessione all'archivio dati. 
5. Fare clic su **Test connessione** per testare la connessione all'archivio dati. 

<!---HONumber=AcomDC_0420_2016-->