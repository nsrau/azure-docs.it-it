## <a name="route-tables"></a>Tabelle di route
Le risorse di tabelle di route contengono le route usate per definire il flusso del traffico all'interno dell'infrastruttura di Azure. È possibile usare le route definite dall'utente (UDR) per inviare tutto il traffico da una determinata subnet a un dispositivo virtuale, ad esempio un firewall o un sistema di rilevamento delle intrusioni (IDS). È possibile associare una tabella di route alle subnet. 

Le tabella di route contengono le proprietà seguenti.

| Proprietà | Descrizione | Valori di esempio |
| --- | --- | --- |
| **routes** |Raccolta di route definite dall'utente nella tabella di route |vedere [route definite dall'utente](#User-defined-routes) |
| **subnet** |Raccolta di subnet a cui viene applicata la tabella di route |vedere [subnet](#Subnets) |

### <a name="user-defined-routes"></a>route definite dall'utente
È possibile creare route definite dall'utente per specificare dove inviare il traffico, in base al relativo indirizzo di destinazione. Una route può essere considerata come la definizione del gateway predefinito in base all'indirizzo di destinazione di un pacchetto di rete.

Le route definite dall'utente contengono le proprietà seguenti. 

| Proprietà | Descrizione | Valori di esempio |
| --- | --- | --- |
| **addressPrefix** |Prefisso dell'indirizzo o indirizzo IP completo per la destinazione |192.168.1.0/24, 192.168.1.101 |
| **nextHopType** |Tipo di dispositivo al quale verrà inviato il traffico. |VirtualAppliance, Gateway VPN, Internet |
| **nextHopIpAddress** |Indirizzo IP per l'hop successivo |192.168.1.4 |

Tabella di route di esempio in formato JSON:

    {
        "name": "UDR-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/routeTables",
        "location": "westus",
        "properties": {
            "provisioningState": "Succeeded",
            "routes": [
                {
                    "name": "RouteToFrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd/routes/RouteToFrontEnd",
                    "etag": "W/\"v\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "nextHopType": "VirtualAppliance",
                        "nextHopIpAddress": "192.168.0.4"
                    }
                }
            ],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd"
                }
            ]
        }
    }

### <a name="additional-resources"></a>Risorse aggiuntive
* Altre informazioni sulle [route definite dall'utente](../articles/virtual-network/virtual-networks-udr-overview.md).
* Leggere [la documentazione di riferimento API REST](https://msdn.microsoft.com/library/azure/mt502549.aspx) per le tabelle di route.
* Leggere [la documentazione di riferimento API REST](https://msdn.microsoft.com/library/azure/mt502539.aspx) per le route definite dall'utente.



<!--HONumber=Nov16_HO3-->


