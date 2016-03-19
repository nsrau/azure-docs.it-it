Quando si aggiunge una connessione da sito a sito al gateway della rete virtuale, è necessario innanzitutto creare un gateway di rete locale a cui fare riferimento dalla configurazione in uso. Verificare quindi di avere un gateway di rete locale configurato. È possibile cercare i gateway di rete locale facendo clic sul pulsante **Sfoglia** e applicando il filtro relativo ai **gateway di rete locale**.

1. Nel pannello **Reti virtuali** trovare la propria rete virtuale e fare clic su di essa per aprire il pannello corrispondente. Il gateway verrà visualizzato nell'elenco *Dispositivi connessi*.
2. Fare clic sul ***nome del gateway di rete virtuale*** -> **Gateway di rete virtuale** -> **Impostazioni** -> **Connessioni** e quindi su **Aggiungi**.
3. Nel campo **Nome** assegnare un nome alla connessione. In questo esempio si userà *GW1S2S*
4. Per **Tipo di connessione** selezionare **Da sito a sito(IPSec)**
5. Il valore del campo **Gateway di rete virtuale** è fisso perché la connessione viene eseguita da questo gateway.
6. Per **Gateway di rete locale** fare clic su **Scegli un gateway di rete locale** e selezionare il gateway di rete locale da usare. In questo esempio si userà il gateway *GW1LocalNet*.
7. I valori del campo **Chiave condivisa** devono corrispondere a quelli relativi al dispositivo VPN locale. Se il dispositivo VPN nella rete locale non fornisce una chiave condivisa, è possibile crearne una e immetterla sia in questo campo sia nel dispositivo locale. La cosa importante è che coincidano.
8. I valori restanti, relativi ai campi **Sottoscrizione**, **Gruppo di risorse** e **Percorso**, sono fissi.
9. Fare clic su **OK** per creare la connessione. Sullo schermo lampeggerà il messaggio *Creazione della connessione*.
10. Dopo essere stata completata, la connessione verrà visualizzata nel pannello **Connessioni** relativo al gateway.

<!---HONumber=AcomDC_0107_2016-->