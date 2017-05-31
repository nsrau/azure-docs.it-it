In questo passaggio si creerà una regola del firewall per aprire la porta probe per l'endpoint con carico bilanciato (59999, come specificato in precedenza) e un'altra regola per aprire la porta del listener del gruppo disponibilità. Poiché è stato creato l'endpoint con carico bilanciato nelle VM contenenti repliche del gruppo di disponibilità, è necessario aprire la porta probe e la porta del listener sulle rispettive macchine virtuali.

1. Nelle macchine virtuali che ospitano le repliche, avviare **Windows Firewall con sicurezza avanzata**.

2. Fare clic con il pulsante destro del mouse su **Regole connessioni in entrata** e quindi scegliere **Nuova regola**.

3. Nella pagina **Tipo di regola** selezionare **Porta** e quindi fare clic su **Avanti**.

4. Nella pagina **Protocollo e porte** selezionare **TCP**, digitare **59999** nella casella **Porte locali specifiche** e quindi fare clic su **Avanti**.

5. Nella pagina **Azione** mantenere selezionata l'opzione **Consenti la connessione** e quindi fare clic su **Avanti**.

6. Nella pagina **Profilo** accettare le impostazioni predefinite e quindi fare clic su **Avanti**.

7. Nella pagina **Nome** specificare nella casella di testo **Nome** un nome per la regola, ad esempio **Porta probe del listener AlwaysOn** e quindi fare clic su **Fine**.

8. Ripetere i passaggi precedenti per la porta del listener del gruppo di disponibilità (come specificato in precedenza nel parametro $EndpointPort dello script) e specificare un nome di regola appropriato, ad esempio **Porta del Listener AlwaysOn**.

