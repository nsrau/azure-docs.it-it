Per creare un gateway di rete locale, seguire questa procedura:

1. Usare **Esplora** e filtrare per trovare il pannello **Gateway di rete locali**, quindi fare clic su **Aggiungi**.
2. Nel pannello **Crea un gateway di rete locale** assegnare un **Nome** all'oggetto gateway di rete locale. Per questo esempio, il gateway di rete locale sarà denominato *GW1LocalNet*.
3. Configurare un **Indirizzo IP** per il gateway. Questo è l'indirizzo IP del dispositivo VPN esterno a cui connettersi. Non può essere protetto da NAT e deve essere raggiungibile da Azure. Questo è l'indirizzo IP del dispositivo a cui si connetterà il gateway di Azure.
4. **Spazio indirizzi** fa riferimento agli intervalli di indirizzi nella rete solitamente locale. È possibile aggiungere più intervalli di spazi indirizzi. Gli intervalli immessi non possono sovrapporsi gli intervalli di spazi indirizzi usati per una delle reti virtuali che comunicano tramite il gateway. È necessario che siano coordinati con la configurazione locale e con gli spazi indirizzi della rete virtuale di Azure. 
5. Per **Sottoscrizione** verificare che sia visualizzata la sottoscrizione corretta.
6. Per **Gruppo di risorse** selezionare il gruppo di risorse che si vuole usare. È possibile creare un nuovo gruppo di risorse o selezionarne uno già creato. Per creare un nuovo gruppo di risorse, digitare il nome nella casella. Per selezionare un gruppo di risorse già creato, fare clic su **Gruppo di risorse** per aprire il pannello **Gruppo di risorse** e quindi selezionare il gruppo che si vuole usare.
7. Per **Località** verificare che il percorso corrisponda al quello del gateway di rete virtuale da associare.
8. Lasciare selezionata la casella "Aggiungi al dashboard" se si vuole trovare facilmente il gateway di rete locale dal dashboard.
9. Fare clic su **Crea** per creare il gateway di rete locale. Nel dashboard verrà visualizzato un messaggio in cui è indicato che "è in corso la distribuzione del gateway di rete locale". L'operazione dovrebbe richiedere poco tempo.

<!---HONumber=AcomDC_0107_2016-->