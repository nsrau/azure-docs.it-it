## <a name="virtual-network"></a>Rete virtuale
Le reti virtuali e le risorse subnet consentono di definire un limite di sicurezza per i carichi di lavoro in esecuzione in Azure. Una rete virtuale è caratterizzata da una raccolta di spazi di indirizzi, definiti come blocchi CIDR. 

> [!NOTE]
> Gli amministratori di rete hanno familiarità con la notazione CIDR. Se non si ha familiarità con CIDR, è possibile [ottenere maggiori informazioni](http://whatismyipaddress.com/cidr).
> 
> 

![Reti virtuali con più subnet](./media/resource-groups-networking/Figure4.png)

Le reti virtuali contengono le proprietà seguenti.

| Proprietà | Descrizione | Valori di esempio |
| --- | --- | --- |
| **addressSpace** |Raccolta di prefissi di indirizzi che costituiscono la rete virtuale nella notazione CIDR |192.168.0.0/16 |
| **subnet** |Raccolta di subnet che costituiscono la rete virtuale |vedere [subnet](#Subnets) di seguito. |
| **ipAddress** |Indirizzo IP assegnato all'oggetto. La proprietà è di sola lettura. |104.42.233.77 |

### <a name="subnets"></a>Subnet
Una subnet è una risorsa figlio di una rete virtuale e consente di definire i segmenti degli spazi di indirizzi all'interno di un blocco CIDR, usando i prefissi degli indirizzi IP. Le NIC possono essere aggiunte alle subnet e connesse alle macchine virtuali, fornendo connettività per diversi carichi di lavoro.

Le subnet contengono le proprietà seguenti. 

| Proprietà | Descrizione | Valori di esempio |
| --- | --- | --- |
| **addressPrefix** |Singolo prefisso di indirizzo che costituisce la subnet nella notazione CIDR |192.168.1.0/24 |
| **networkSecurityGroup** |NSG applicato alla subnet |vedere [NSG](#Network-Security-Group) |
| **routeTable** |Tabella di route applicata alla subnet |vedere [UDR](#Route-table) |
| **ipConfigurations** |Raccolta di oggetti di configurazione IP usati dalle schede di rete connesse alla subnet |vedere [UDR](#Route-table) |

Rete virtuale di esempio in formato JSON:

    {
        "name": "TestVNet",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/virtualNetworks",
        "location": "westus",
        "tags": {
            "displayName": "VNet"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "addressSpace": {
                "addressPrefixes": [
                    "192.168.0.0/16"
                ]
            },
            "subnets": [
                {
                    "name": "FrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "networkSecurityGroup": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "routeTable": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                        },
                        "ipConfigurations": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                            },
                            ...]
                    }
                },
                ...]
        }
    }

### <a name="additional-resources"></a>Risorse aggiuntive
* Altre informazioni sulle [reti virtuali](../articles/virtual-network/virtual-networks-overview.md).
* Leggere [la documentazione di riferimento API REST](https://msdn.microsoft.com/library/azure/mt163650.aspx) per le reti virtuali.
* Leggere [la documentazione di riferimento API REST](https://msdn.microsoft.com/library/azure/mt163618.aspx) per le subnet.

