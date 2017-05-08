È possibile verificare se la connessione è riuscita usando il comando dell'interfaccia della riga di comando seguente. Configurare i valori in modo che corrispondano ai propri. Nell'esempio,  -n fa riferimento al nome della connessione creata che si vuole testare.

```azurecli
az network vpn-connection show -n VNet1toSite2 -g TestRG1
```

Quando la connessione non è ancora stata stabilita, il relativo stato di connessione visualizza "Connessione". Dopo aver stabilito la connessione, lo stato cambia in "Connesso" ed è possibile visualizzare i byte in ingresso e in uscita.