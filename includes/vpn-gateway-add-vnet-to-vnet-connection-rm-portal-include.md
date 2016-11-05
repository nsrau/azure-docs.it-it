Quando si aggiunge una connessione tra reti virtuali, verificare che entrambe le reti virtuali dispongano di un gateway di rete virtuale e che non contengano intervalli di indirizzi sovrapposti.

1. Nel pannello **Reti virtuali** trovare la propria rete virtuale e fare clic su di essa per aprire il pannello corrispondente. Il gateway verrà visualizzato nell'elenco *Dispositivi connessi*. In alternativa, è possibile configurare le impostazioni direttamente dal gateway di rete virtuale senza prima espandere la rete virtuale.
2. Nella finestra delle impostazioni del gateway di rete virtuale fare clic su **Connessioni** e quindi su **Aggiungi**.
3. Nel campo **Nome** assegnare un nome alla connessione. 
4. Per **Tipo di connessione** selezionare **Tra reti virtuali**
5. Il valore del campo **Gateway di rete virtuale** è fisso perché la connessione viene eseguita da questo gateway.
6. Per **Secondo gateway di rete virtuale** selezionare il gateway con cui si vuole stabilire una connessione dal gateway in uso.
7. I valori restanti, relativi ai campi **Sottoscrizione**, **Gruppo di risorse** e **Percorso**, sono fissi.
8. Fare clic su **OK** per creare la connessione. Sullo schermo lampeggerà il messaggio *Creazione della connessione*.
9. Dopo essere stata completata, la connessione verrà visualizzata nel pannello **Connessioni** relativo al gateway.

<!---HONumber=AcomDC_0107_2016-->