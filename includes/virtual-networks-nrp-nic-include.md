## <a name="nic"></a>NIC
La scheda di interfaccia di rete (o NIC) è una risorsa che fornisce la connettività di rete a una subnet esistente in una risorsa di rete virtuale. Sebbene sia possibile creare una scheda di interfaccia di rete come oggetto autonomo, è necessario associarla a un altro oggetto per fornire davvero la connettività. Una scheda di interfaccia di rete può essere usata per la connessione di una macchina virtuale a una subnet, a un indirizzo IP pubblico o a un servizio di bilanciamento del carico.  

| Proprietà | DESCRIZIONE | Valori di esempio |
| --- | --- | --- |
| **virtualMachine** |VM alla quale è associata la scheda di interfaccia di rete. |/subscriptions/{guid}/../Microsoft.Compute/virtualMachines/vm1 |
| **macAddress** |Indirizzo MAC della scheda di interfaccia di rete |qualsiasi valore compreso tra 4 e 30. |
| **networkSecurityGroup** |Gruppo di sicurezza di rete associato alla scheda di interfaccia di rete |/subscriptions/{guid}/../Microsoft.Network/networkSecurityGroups/myNSG1 |
| **dnsSettings** |Impostazioni DNS della scheda di interfaccia di rete. |vedere [PIP](#Public-IP-address) |

La scheda di interfaccia di rete, o NIC, rappresenta un'interfaccia di rete che è possibile associare a una macchina virtuale (VM). Una macchina virtuale può avere una o più schede di interfaccia di rete.

![Scheda di interfaccia di rete in una macchina virtuale singola](./media/resource-groups-networking/Figure3.png)

### <a name="ip-configurations"></a>Configurazioni IP
Le schede di interfaccia di rete dispongono di un oggetto figlio denominato **ipConfigurations** contenente le proprietà seguenti:

| Proprietà | DESCRIZIONE | Valori di esempio |
| --- | --- | --- |
| **subnet** |Subnet alla quale è connessa la scheda di interfaccia di rete. |/Subscriptions/{GUID}/../microsoft.Network/virtualNetworks/myvnet1/Subnets/mysub1 |
| **privateIPAddress** |Indirizzo IP della scheda di interfaccia di rete nella subnet |10.0.0.8 |
| **privateIPAllocationMethod** |Metodo di allocazione degli indirizzi IP |Dinamico o statico |
| **enableIPForwarding** |Indica se la scheda di interfaccia di rete può essere usata per il routing |true o false |
| **primary** |Indica se la scheda di interfaccia di rete è la scheda primaria per la macchina virtuale |true o false |
| **publicIPAddress** |Indirizzo PIP associato alla scheda di interfaccia di rete |vedere [Impostazioni DNS](#DNS-settings) |
| **loadBalancerBackendAddressPools** |Pool di indirizzi di back-end associati alla scheda di interfaccia di rete | |
| **loadBalancerInboundNatRules** |Regole NAT del servizio di bilanciamento del carico in ingresso associate alla scheda di interfaccia di rete | |

Indirizzo IP pubblico di esempio in formato JSON:

    {
        "name": "lb-nic1-be",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkInterfaces",
        "location": "eastus",
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "ipConfigurations": [
                {
                    "name": "NIC-config",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/NIC-config",
                    "etag": "W/\"0027f1a2-3ac8-49de-b5d5-fd46550500b1\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "privateIPAddress": "10.0.0.4",
                        "privateIPAllocationMethod": "Dynamic",
                        "subnet": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet"
                        },
                        "loadBalancerBackendAddressPools": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool"
                            }
                        ],
                        "loadBalancerInboundNatRules": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1"
                            }
                        ]
                    }
                }
            ],
            "dnsSettings": { ... },
            "macAddress": "00-0D-3A-10-F1-29",
            "enableIPForwarding": false,
            "primary": true,
            "virtualMachine": {
                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Compute/virtualMachines/web1"
            }
        }
    }

### <a name="additional-resources"></a>Risorse aggiuntive
* Leggere [la documentazione di riferimento API REST](https://msdn.microsoft.com/library/azure/mt163579.aspx) per schede di interfaccia di rete.

