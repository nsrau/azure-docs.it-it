## Azioni rapide
L'articolo presuppone che sia già stata effettuata la connessione alla sottoscrizione nel portale e che sia stata creata una macchina virtuale con le immagini disponibili tramite il modello di distribuzione Resource Manager. Una volta avviata la macchina virtuale, seguire questi passaggi.

1. Visualizzare le impostazioni della macchina virtuale nel portale e fare clic sull'indirizzo IP pubblico.
   
   ![individuare la risorsa ip](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)
2. Notare che il nome DNS per l'indirizzo IP pubblico è vuoto. Fare clic su **Configurazione** nel pannello relativo all'IP pubblico.
   
   ![impostazioni ip](./media/virtual-machines-common-portal-create-fqdn/settingsIP.PNG)
3. Immettere l'etichetta del nome DNS da usare e **salvare** questa configurazione.
   
   ![immettere l'etichetta del nome dns](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)
   
   La risorsa IP pubblico mostra ora la nuova etichetta DNS nel relativo pannello.
4. Chiudere i pannelli dell'IP pubblico e tornare al pannello macchina virtuale nel portale. Verificare che il nome DNS o FQDN sia visualizzato accanto all'indirizzo IP per la risorsa IP pubblico.
   
   ![FQDN creato](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)

<!---HONumber=AcomDC_0831_2016-->