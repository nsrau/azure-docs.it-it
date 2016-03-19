1. Nel portale, passare a **Nuovo**, quindi **Sfoglia**. Selezionare i **gateway di rete virtuale** dall'elenco.
2. Fare clic su **Aggiungi**.
3. Assegnare un nome al gateway. Questa operazione non è come quella utilizzata per assegnare un nome alla subnet del gateway. Questo è il nome dell'oggetto del gateway. 
4. In **Rete virtuale**, selezionare la rete virtuale che si desidera connettere a questo gateway.
5. Nelle impostazioni per la rete virtuale, per il valore dell'**Indirizzo IP pubblico**, creare un nome di indirizzo IP pubblico. Notare che non si tratta di richiedere un indirizzo IP. L'indirizzo IP viene assegnato in modo dinamico. Al contrario, questo è il nome dell'oggetto indirizzo IP a cui l'indirizzo viene assegnato. 
6. Per **Tipo VPN**, le opzioni disponibili sono basate su criteri e route. Assicurarsi di selezionare il tipo di gateway VPN supportato sia dallo scenario di configurazione che, se richiesto dalla configurazione, dal dispositivo gateway VPN che si intende usare.
7. Per **Gruppo di risorse**, scegliere **Seleziona esistente** e il gruppo di risorse in cui si trova la rete virtuale, salvo diversamente richiesto dalla configurazione.
8. Per **Percorso**, assicurarsi che sia visualizzato il percorso in cui si trovano sia il gruppo di risorse che la rete virtuale.
9. Fare clic su **Create**. Verrà visualizzato il riquadro relativo alla *distribuzione del gateway di rete virtuale* nel dashboard. La creazione di un gateway richiede tempo. Le attività in background sono numerose. Prevedere un tempo minimo di 15 minuti. Potrebbe essere necessario aggiornare la pagina del portale per visualizzare lo stato di completamento.
10. Dopo la creazione del gateway, è possibile visualizzare l'indirizzo IP assegnato esaminando la rete virtuale nel portale. Il gateway viene visualizzato come un dispositivo connesso. È possibile visualizzare il nome e l'indirizzo IP assegnato al gateway.

<!---HONumber=AcomDC_0114_2016-->