È possibile verificare se la connessione è riuscita usando il comando [az network vpn-connection show](/cli/azure/network/vpn-connection#show). Il valore " --name" nell'esempio fa riferimento al nome della connessione che si vuole testare. Mentre è in corso l'operazione per stabilire la connessione, lo stato della connessione è "Connecting". Dopo che la connessione è stata stabilita, lo stato diventa "Connected".

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

