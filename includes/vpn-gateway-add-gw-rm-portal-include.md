1. Nel portale passare a **Nuovo** > **Rete** > **Gateway di rete virtuale**. Verrà aperto il pannello **Crea gateway di rete virtuale**.

	![Gateway](./media/vpn-gateway-add-gw-rm-portal-include/creategw250.png)  

2. Nel pannello **Crea gateway di rete virtuale** assegnare il nome del gateway nel campo **Nome**. Questa operazione non è come quella utilizzata per assegnare un nome alla subnet del gateway. Questo è il nome dell'oggetto gateway che verrà creato.

3. Modificare il campo **Località** in modo che faccia riferimento alla località in cui si trova la rete virtuale. Se non si esegue questa operazione, la rete virtuale non sarà visualizzata nell'elenco di reti virtuali.
 
4. Scegliere quindi la rete virtuale a cui si vuole aggiungere il gateway. Fare clic su **Rete virtuale** per aprire il pannello **Scegliere una rete virtuale**. Selezionare la rete virtuale. Per visualizzare la rete virtuale nell'elenco, deve avere già una subnet del gateway valida.

5. Definire un indirizzo IP pubblico. Fare clic su **Indirizzo IP pubblico** per aprire il pannello **Scegli indirizzo IP pubblico**. Fare clic su **+Crea nuovo** per aprire il pannello **Crea indirizzo IP pubblico**. Immettere un nome per l'indirizzo IP pubblico. Verrà creato un oggetto indirizzo IP pubblico a cui verrà assegnato in modo dinamico un indirizzo IP pubblico. <br>Fare clic su **OK** per salvare le modifiche.

5. Per **Tipo di gateway** selezionare il tipo di gateway specificato per la configurazione.

6. Per **Tipo VPN** selezionare il tipo di VPN specificato per la configurazione.

7. Per **Sottoscrizione** verificare che sia selezionata la sottoscrizione corretta.

8. Il **Gruppo di risorse** viene determinato dalla rete virtuale selezionata.

9. Non modificare il valore di **Località** dopo avere specificato le impostazioni precedenti.

10. A questo punto il pannello avrà un aspetto analogo a quello dell'immagine nel Passaggio 1. Verificare che le impostazioni corrispondano a quelle della configurazione specifica. È possibile selezionare **Aggiungi al dashboard** nella parte inferiore del pannello se si vuole che il gateway venga visualizzato nel dashboard.

11. Fare clic su **Crea** per iniziare a creare il gateway. Le impostazioni verranno convalidate e nel dashboard verrà visualizzato il riquadro relativo alla distribuzione del gateway di rete virtuale. La creazione di un gateway può richiedere fino a 45 minuti. Potrebbe essere necessario aggiornare la pagina del portale per visualizzare lo stato di completamento.

	![Gateway](./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png)  

11. Dopo la creazione del gateway, è possibile visualizzare l'indirizzo IP assegnato esaminando la rete virtuale nel portale. Il gateway viene visualizzato come un dispositivo connesso. È possibile fare clic sul dispositivo connesso ovvero il gateway di rete virtuale, per visualizzare altre informazioni.

<!---HONumber=AcomDC_0810_2016-->