### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Passaggio 1: Passare al gateway di rete virtuale

1. Nel [portale di Azure](https://portal.azure.com) passare a **Tutte le risorse**. 
2. Per aprire il pannello del gateway di rete virtuale, passare al gateway di rete virtuale da eliminare e farvi clic.

### <a name="step-2-delete-connections"></a>Passaggio 2: Eliminare le connessioni

1. Nel pannello del gateway di rete virtuale fare clic su **Connessioni** per visualizzare tutte le connessioni del gateway.
2. Fare clic su **...** sulla riga del nome della connessione, quindi selezionare **Elimina** nell'elenco a discesa.
3. Fare clic su **Sì** per confermare l'eliminazione della connessione. Se si dispone di più connessioni, eliminarle tutte.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Passaggio 3: Eliminare il gateway di rete virtuale

Si noti che se in aggiunta alla configurazione S2S si ha una configurazione P2S per questa rete virtuale, l'eliminazione del gateway di rete virtuale disconnette automaticamente tutti i client P2S senza alcun avviso.

1. Nel pannello del gateway di rete virtuale fare clic su **Panoramica**.
2. Nel pannello **Informazioni generali**, fare clic su **Elimina**.
