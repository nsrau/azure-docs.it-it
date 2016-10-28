In questo passaggio si creerà una regola del firewall per aprire la porta probe per l'endpoint con carico bilanciato (59999 come specificato in precedenza) e un'altra regola per aprire la porta del listener del gruppo disponibilità. Poiché è stato creato l'endpoint con carico bilanciato nelle VM di Azure contenenti repliche del gruppo di disponibilità, è necessario aprire la porta probe e la porta del listener sulle rispettive macchine virtuali di Azure.

1. Nelle macchine virtuali che ospitano le repliche, avviare**Windows Firewall con sicurezza avanzata**.

1. Fare clic con il pulsante destro del mouse su **Regole connessioni in entrata**, quindi su **Nuova regola**.

1. Nella pagina **Tipo di regola** selezionare **Porta** e quindi fare clic su **Avanti**.

1. Nella pagina**Protocollo e porte**selezionare**TCP**e digitare **59999**nella casella **Porte locali specifiche**. Quindi fare clic su **Avanti**.

1. Nella pagina**Azione**, mantenere selezionato**Consenti solo connessioni** e fare clic su**Avanti**.

1. Nella pagina **Profilo**, accettare le impostazioni predefinite e fare clic su**Avanti**.

1. Nella pagina**Nome**specificare un nome di regola, ad esempio**Porta Probe del Listener AlwaysOn**nella casella di testo**Nome**e fare clic su**Fine**.

1. Ripetere i passaggi precedenti per la porta del listener del gruppo di disponibilità (come specificato in precedenza nel parametro $EndpointPort dello script) e specificare un nome di regola appropriata, ad esempio**Porta del Listener AlwaysOn**.

<!---HONumber=Oct15_HO3-->