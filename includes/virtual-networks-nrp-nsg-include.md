## <a name="network-security-group"></a>Gruppo di sicurezza di rete
Una risorsa del gruppo di sicurezza di rete consente di creare un limite di sicurezza per i carichi di lavoro, implementando le regole di accesso consentito e negato. Tali regole possono essere applicate a una macchina virtuale, una scheda di rete o una subnet.

| Proprietà | DESCRIZIONE | Valori di esempio |
| --- | --- | --- |
| **subnet** |Elenco di ID subnet a cui è collegato il gruppo di sicurezza di rete. |/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd |
| **securityRules** |Elenco di regole di sicurezza che costituiscono il gruppo di sicurezza di rete |Vedere [Regola di sicurezza](#Security-rule) di seguito |
| **defaultSecurityRules** |Elenco delle regole di sicurezza predefinite presenti in ogni gruppo di sicurezza di rete |Vedere [Regole di sicurezza predefinite](#Default-security-rules) di seguito |

* **Regola di sicurezza** : in un gruppo di sicurezza di rete possono essere definite più regole di sicurezza. Ogni regola può consentire o negare diversi tipi di traffico.

### <a name="security-rule"></a>Regola di sicurezza
Una regola di sicurezza è una risorsa figlio di un gruppo di sicurezza di rete contenente le proprietà seguenti.

| Proprietà | DESCRIZIONE | Valori di esempio |
| --- | --- | --- |
| **description** |Descrizione della regola |Consentire il traffico in ingresso per tutte le macchine virtuali nella subnet X |
| **protocol** |Protocollo per la regola |TCP, UDP o * |
| **sourcePortRange** |Intervallo di porte di origine per la regola |80, 100-200, * |
| **destinationPortRange** |Intervallo di porte di destinazione per la regola |80, 100-200, * |
| **sourceAddressPrefix** |Prefisso dell'indirizzo di origine per la regola |10.10.10.1, 10.10.10.0/24, VirtualNetwork |
| **destinationAddressPrefix** |Prefisso dell'indirizzo di destinazione per la regola |10.10.10.1, 10.10.10.0/24, VirtualNetwork |
| **direction** |Direzione del traffico per la regola |in ingresso o in uscita |
| **priority** |Priorità per la regola. Le regole vengono controllate nell'ordine di priorità. Una volta che viene applicata una regola, non viene verificata la corrispondenza di altre regole. |10, 100, 65000 |
| **access** |Tipo di accesso da applicare se la regola corrisponde |consentire o negare |

Gruppo di sicurezza di rete di esempio in formato JSON:

    {
        "name": "NSG-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkSecurityGroups",
        "location": "westus",
        "tags": {
            "displayName": "NSG - Front End"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "securityRules": [
                {
                    "name": "rdp-rule",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/rdp-rule",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "description": "Allow RDP",
                        "protocol": "Tcp",
                        "sourcePortRange": "*",
                        "destinationPortRange": "3389",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "access": "Allow",
                        "priority": 100,
                        "direction": "Inbound"
                    }
                }
            ],
            "defaultSecurityRules": [
                { [...],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                }
            ]
        }
    }

### <a name="default-security-rules"></a>Regole di sicurezza predefinite

Le regole di sicurezza predefinite hanno le stesse proprietà disponibili nelle regole di sicurezza. Esistono per fornire la connettività di base tra le risorse a cui sono applicati gruppi di sicurezza di rete. Assicurarsi di conoscere le [regole di sicurezza predefinite](../articles/virtual-network/virtual-networks-nsg.md#default-rules) esistenti.

### <a name="additional-resources"></a>Risorse aggiuntive
* Altre informazioni sui [gruppi di sicurezza di rete](../articles/virtual-network/virtual-networks-nsg.md).
* Leggere [la documentazione di riferimento API REST](https://msdn.microsoft.com/library/azure/mt163615.aspx) per i gruppi di sicurezza di rete.
* Leggere [la documentazione di riferimento API REST](https://msdn.microsoft.com/library/azure/mt163580.aspx) per le regole di sicurezza.
