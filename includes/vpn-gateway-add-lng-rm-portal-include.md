1. Nel portale di Azure passare a **Nuovo** **>** **Rete** **>** **Gateway di rete locale**.

	![Creare il gateway di rete locale](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. Nel pannello **Crea un gateway di rete locale** specificare un **Nome** per l'oggetto gateway di rete locale.
 
3. Specificare un **Indirizzo IP** per il gateway. Questo è l'indirizzo IP del dispositivo VPN esterno a cui connettersi. Non può essere protetto da NAT e deve essere raggiungibile da Azure.

4. **Spazio indirizzi** fa riferimento agli intervalli di indirizzi nella rete, solitamente locale. È possibile aggiungere più intervalli di spazi indirizzi. Gli intervalli immessi non possono sovrapporsi gli intervalli di spazi indirizzi usati per una delle reti virtuali che comunicano tramite il gateway. È necessario che siano coordinati con la configurazione locale e con gli spazi indirizzi della rete virtuale di Azure.
 
5. Per **Sottoscrizione** verificare che sia visualizzata la sottoscrizione corretta.

6. Per **Gruppo di risorse** selezionare il gruppo di risorse che si vuole usare. È possibile creare un nuovo gruppo di risorse o selezionarne uno già creato. Per creare un nuovo gruppo di risorse, digitare il nome nella casella. Per selezionare un gruppo di risorse già creato, fare clic su **Gruppo di risorse** per aprire il pannello **Gruppo di risorse** e quindi selezionare il gruppo che si vuole usare.

7. Per **Località**, se si sta creando un nuovo gateway di rete locale, è possibile usare la stessa località del gateway di rete virtuale. Questo passaggio non è obbligatorio. Il gateway di rete locale può essere in una località diversa.

8. Lasciare selezionata la casella "Aggiungi al dashboard" se si vuole trovare facilmente il gateway di rete locale dal dashboard.

9. Fare clic su **Crea** per creare il gateway di rete locale. Nel dashboard verrà visualizzato un messaggio in cui è indicato che "è in corso la distribuzione del gateway di rete locale".

10. Quando è stato creato il gateway di rete locale, verrà aperto nel portale per la visualizzazione.

	
