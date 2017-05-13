È possibile verificare se la connessione è riuscita usando il comando [az network vpn-connection show](/cli/azure/network/vpn-connection#show). Configurare i valori in modo che corrispondano ai propri. Il valore  --name refers nell'esempio fa riferimento al nome della connessione creata che si vuole testare.

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

Quando la connessione non è ancora stata stabilita, il relativo stato di connessione visualizza "Connessione". Dopo aver stabilito la connessione, lo stato cambia in "Connesso" ed è possibile visualizzare i byte in ingresso e in uscita.