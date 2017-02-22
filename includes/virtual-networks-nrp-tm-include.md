## <a name="traffic-manager-profile"></a>Profilo di Gestione traffico
Gestione traffico e la relativa risorsa endpoint figlio consentono il routing DNS agli endpoint all'interno e all'esterno di Azure. Questa distribuzione del traffico è regolata dai metodi dei criteri di routing. Gestione traffico consente anche il monitoraggio dell'integrità dell'endpoint e la corretta deviazione del traffico in base all'integrità di un endpoint. 

| Proprietà | Descrizione |
| --- | --- |
| **trafficRoutingMethod** |i valori possibili sono *Prestazioni*, *Valore ponderato* e *Priorità* |
| **dnsConfig** |FQDN per il profilo |
| **Protocollo** |protocollo di monitoraggio, i valori possibili sono *HTTP* e *HTTPS* |
| **Porta** |porta di monitoraggio |
| **Percorso** |percorso di monitoraggio |
| **Endpoint** |contenitore per le risorse endpoint |

### <a name="endpoint"></a>Endpoint
Un endpoint è una risorsa figlio di un profilo di Gestione traffico. Rappresenta un endpoint di servizio o Web al quale viene distribuito il traffico in base ai criteri configurati nella risorsa del profilo di Gestione traffico. 

| Proprietà | Descrizione |
| --- | --- |
| **Tipo** |il tipo di endpoint. I valori possibili sono *Endpoint di Azure*, *Endpoint esterno* e *Endpoint annidato*. |
| **targetResourceId** |indirizzo IP pubblico di un endpoint di servizio o Web. Può trattarsi di un endpoint di Azure o esterno. |
| **Peso** |peso dell'endpoint usato nella gestione del traffico. |
| **Priorità** |priorità dell'endpoint, usata per definire un'azione di failover |

Esempio di Gestione traffico in formato Json: 

        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }


## <a name="additional-resources"></a>Risorse aggiuntive
Per ulteriori informazioni, leggere la [documentazione dell'API REST per Gestione traffico](https://msdn.microsoft.com/library/azure/mt163664.aspx) .



<!--HONumber=Nov16_HO3-->


