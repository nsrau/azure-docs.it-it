1. Nel portale fare clic su **+Aggiungi** in **Tutte le risorse**. Nella casella di ricerca del pannello **Tutto** digitare **Gateway di rete locale** e quindi fare clic per avviare la ricerca. Viene restituito un elenco. Fare clic su **Gateway di rete locale** per aprire il pannello e quindi su **Crea** per aprire il pannello **Crea un gateway di rete locale**.
   
    ![Creare il gateway di rete locale](./media/vpn-gateway-add-lng-rm-portal-include/newlng.png)

2. Nel pannello **Crea un gateway di rete locale** specificare un **nome** per l'oggetto gateway di rete locale.
3. Specificare un **indirizzo IP** pubblico valido il dispositivo VPN o il gateway di rete virtuale a cui ci si vuole connettere.<br>Se la rete locale rappresenta una posizione locale, questo è l'indirizzo IP pubblico del dispositivo VPN a cui ci si vuole connettere. Non può essere protetto da NAT e deve essere raggiungibile da Azure.<br>Se la rete locale rappresenta un'altra rete virtuale, sarà necessario specificare l'indirizzo IP pubblico assegnato al gateway di rete virtuale per tale rete virtuale.<br>
4. **Spazio di indirizzi** fa riferimento agli intervalli di indirizzi per la rete rappresentata da questa rete locale. È possibile aggiungere più intervalli di spazi indirizzi. Assicurarsi che gli intervalli specificati non si sovrappongano con gli intervalli di altre reti a cui ci si vuole connettere.
5. Per **Sottoscrizione**verificare che sia visualizzata la sottoscrizione corretta.
6. Per **Gruppo di risorse**selezionare il gruppo di risorse che si vuole usare. È possibile creare un nuovo gruppo di risorse o selezionarne uno già creato.
7. Per **Località**selezionare la località in cui verrà creato questo oggetto. È possibile, ma non necessario, selezionare la stessa località in cui risiede la rete virtuale.
8. Fare clic su **Crea** per creare il gateway di rete locale.



<!--HONumber=Jan17_HO3-->


