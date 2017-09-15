### <a name="to-view-local-network-gateways"></a>Per visualizzare i gateway di rete locali

Per visualizzare un elenco di gateway di rete locali, usare il comando [az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_list).

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>Per verificare i valori della chiave condivisa

Verificare che il valore della chiave condivisa sia lo stesso usato per la configurazione del dispositivo VPN. In caso contrario, eseguire di nuovo la connessione usando il valore del dispositivo o aggiornare il dispositivo con il valore restituito. I valori devono corrispondere. Per visualizzare la chiave condivisa, usare il comando [az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection#az_network_vpn_connection_list).

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>Per visualizzare l'indirizzo IP pubblico del gateway VPN

Per trovare l'indirizzo IP pubblico del gateway di rete virtuale, usare il comando [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_list). Per facilitare la lettura, la formattazione dell'output di questo esempio visualizza l'elenco di IP pubblici in formato tabella.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
