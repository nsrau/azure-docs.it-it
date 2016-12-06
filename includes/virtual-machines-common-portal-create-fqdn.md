## <a name="quick-steps"></a>Azioni rapide
L'articolo presuppone che sia già stata effettuata la connessione alla sottoscrizione nel portale e che sia stata creata una macchina virtuale con le immagini disponibili tramite il modello di distribuzione Resource Manager. Una volta avviata la macchina virtuale, seguire questi passaggi.

1. Selezionare la macchina virtuale nel portale. Il nome DNS è vuoto. Fare clic su **Indirizzo IP pubblico**:
   
   ![Fare clic sulla risorsa IP pubblico nel portale](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)

2. Immettere l'etichetta del nome DNS e fare clic su **Salva**.
   
   ![Immettere l'etichetta del nome DNS per la risorsa IP pubblica](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)
   
   La risorsa IP pubblico mostra ora la nuova etichetta DNS nel relativo pannello.

3. Chiudere i pannelli dell'IP pubblico e tornare al pannello della panoramica della macchina virtuale nel portale. Dopo alcuni secondi, il portale aggiornerà le impostazioni. Verificare che il nome DNS o FQDN sia visualizzato accanto all'indirizzo IP per la risorsa **Indirizzo IP pubblico**.
   
   ![Verificare che sia impostata la nuova etichetta DNS](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)



<!--HONumber=Nov16_HO3-->


