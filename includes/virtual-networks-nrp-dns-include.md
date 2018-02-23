## <a name="azure-dns"></a>DNS di Azure
DNS di Azure è un servizio di hosting per i domini DNS, che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure.

| Proprietà | DESCRIZIONE | Valore di esempio |
| --- | --- | --- |
| **DNSzones** |Informazioni sulla zona del dominio per ospitare i record DNS di un particolare dominio. |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com" |

### <a name="dns-record-sets"></a>Set di record DNS
Le zone DNS dispongono di un oggetto figlio denominato set di record. I set di record sono una raccolta di record host in base al tipo per una zona DNS. I tipi record sono A, AAAA, CNAME, MX, NS, SOA, SRV e TXT.

| Proprietà | DESCRIZIONE | Valore di esempio |
| --- | --- | --- |
| Una  |Tipo di record IPv4 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/A/www |
| AAAA |Tipo di record IPv6 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/AAAA/hostrecord |
| CNAME |tipo di record di nome canonical <sup>1</sup> |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/CNAME/www |
| MX |Tipo di record di posta elettronica |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/MX/mail |
| NS |tipo di record di nome del server |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/NS/ |
| SOA |Tipo di record di "Start of Authority" <sup>2</sup> |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SOA |
| SRV |tipo di record di servizio |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SRV |

<sup>1</sup> consente solo un valore per ogni set di record.

<sup>2</sup> consente solo un tipo di record SOA per ogni zona DNS. 

Esempio di zona DNS nel formato Json:

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "newZoneName": {
          "type": "String",
          "metadata": {
              "description": "The name of the DNS zone to be created."
          }
        },
        "newRecordName": {
          "type": "String",
          "defaultValue": "www",
          "metadata": {
              "description": "The name of the DNS record to be created.  The name is relative to the zone, not the FQDN."
          }
        }
      },
      "resources": 
      [
        {
          "type": "microsoft.network/dnszones",
          "name": "[parameters('newZoneName')]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": {
          }
        },
        {
          "type": "microsoft.network/dnszones/a",
          "name": "[concat(parameters('newZoneName'), concat('/', parameters('newRecordName')))]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": 
          {
            "TTL": 3600,
            "ARecords": 
            [
                {
                    "ipv4Address": "1.2.3.4"
                },
                {
                    "ipv4Address": "1.2.3.5"
                }
            ]
          },
          "dependsOn": [
            "[concat('Microsoft.Network/dnszones/', parameters('newZoneName'))]"
          ]
        }
          ]
    }

## <a name="additional-resources"></a>Risorse aggiuntive
Per ulteriori informazioni, leggere la [documentazione dell'API REST per le zone DNS ](https://msdn.microsoft.com/library/azure/mt130626.aspx) .

Leggere la [documentazione dell'API REST per i set di record DNS](https://msdn.microsoft.com/library/azure/mt130627.aspx) per ulteriori informazioni.

