---
title: Filtri per la connessione IP del DPS Azure Microsoft Docs
description: Come usare il filtro IP per bloccare le connessioni da indirizzi IP specifici all'istanza di Azure Internet. È possibile bloccare le connessioni da singoli indirizzi IP o da intervalli di indirizzi IP.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79284915"
---
# <a name="use-ip-filters"></a>Usare i filtri IP

La sicurezza è un aspetto importante di qualsiasi soluzione. Talvolta è necessario specificare in modo esplicito gli indirizzi IP da cui possono connettersi i dispositivi come parte della configurazione di sicurezza. La funzionalità *filtro IP* per un servizio Device provisioning in hub Azure Azure consente di configurare regole per rifiutare o accettare il traffico da indirizzi IPv4 specifici.

## <a name="when-to-use"></a>Utilizzo

Esistono due casi d'uso specifici in cui è utile bloccare le connessioni a un endpoint DPS da determinati indirizzi IP:

* Il DPS dovrebbe ricevere traffico solo da un intervallo di indirizzi IP specificato e rifiutare tutto il resto. Ad esempio, si usa il DPS con [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) per creare connessioni private tra un DPS e i dispositivi.

* È necessario rifiutare il traffico da indirizzi IP identificati come sospetti dall'amministratore DPS.

## <a name="how-filter-rules-are-applied"></a>Come vengono applicate le regole di filtro

Le regole del filtro IP vengono applicate a livello di istanza DPS. Le regole del filtro IP vengono quindi applicate a tutte le connessioni provenienti dai dispositivi e dalle app back-end con qualsiasi protocollo supportato.

Qualsiasi tentativo di connessione da un indirizzo IP che corrisponde a una regola IP rifiutata nell'istanza di DPS riceve un codice di stato 401 e una descrizione non autorizzati. Il messaggio di risposta non indica la regola IP.

## <a name="default-setting"></a>Impostazione predefinita

Per impostazione predefinita, la griglia del **filtro IP** nel portale per DPS è vuota. Questa impostazione predefinita indica che il DPS accetta connessioni da qualsiasi indirizzo IP. Questa impostazione predefinita equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0.

![Impostazioni predefinite del filtro IP DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Aggiungere o modificare una regola del filtro IP

Per aggiungere una regola di filtro IP, selezionare **+ Aggiungi regola di filtro IP**.

![Aggiungere una regola di filtro IP a un DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Dopo aver selezionato **Aggiungi regola di filtro IP**, compilare i campi.

![Dopo aver selezionato Aggiungi una regola di filtro IP](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Specificare un **nome** per la regola di Filtro IP. Questo deve essere univoco e costituito da una stringa alfanumerica che non fa distinzione tra maiuscole e minuscole e ha una lunghezza massima di 128 caratteri. Sono ammessi solo caratteri alfanumerici ASCII a 7 bit più `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Specificare un singolo indirizzo IPv4 o un blocco di indirizzi IP in notazione CIDR. In notazione CIDR, ad esempio, 192.168.100.0/22 rappresenta gli indirizzi IPv4 1024 da 192.168.100.0 a 192.168.103.255.

* Selezionare **Consenti** o **Blocca** come **azione** per la regola del Filtro IP.

Dopo aver compilato i campi, selezionare **Salva** per salvare la regola. Viene visualizzato un avviso che informa che l'aggiornamento è in corso.

![Notifica sul salvataggio di una regola di filtro IP](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

L'opzione **Aggiungi** è disabilitata quando si raggiunge il numero massimo di dieci regole del filtro IP.

Per modificare una regola esistente, selezionare i dati che si desidera modificare, apportare le modifiche, quindi selezionare **Salva** per salvare la modifica.

> [!NOTE]
> Il rifiuto degli indirizzi IP può impedire ad altri servizi di Azure di interagire con l'istanza di DPS.

## <a name="delete-an-ip-filter-rule"></a>Eliminare una regola del filtro IP

Per eliminare una regola di filtro IP, selezionare l'icona del cestino sulla riga e quindi selezionare **Salva**. La regola viene rimossa e la modifica viene salvata.

![Eliminare una regola di filtro IP DPS](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Aggiornare le regole del filtro IP nel codice

È possibile recuperare e modificare il filtro IP DPS usando l'endpoint REST del provider di risorse di Azure. Vedere `properties.ipFilterRules` nel [metodo createorupdate](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

L'aggiornamento delle regole del filtro IP DPS non è attualmente supportato con l'interfaccia della riga di comando di Azure o Azure PowerShell ma è possibile usare i modelli Azure Resource Manager. Per informazioni sull'uso di modelli Gestione risorse, vedere [Azure Resource Manager modelli](../azure-resource-manager/templates/overview.md) . Gli esempi di modelli che seguono illustrano come creare, modificare ed eliminare le regole del filtro IP DPS.

### <a name="add-an-ip-filter-rule"></a>Aggiungere una regola di filtro IP

Nell'esempio di modello seguente viene creata una nuova regola di filtro IP denominata "AllowAll" che accetta tutto il traffico.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Aggiornare gli attributi della regola di filtro IP del modello in base ai requisiti.

| Attributo                | Descrizione |
| ------------------------ | ----------- |
| **NomeFiltro**           | Consente di specificare un nome per la regola di filtro IP. Questo deve essere univoco e costituito da una stringa alfanumerica che non fa distinzione tra maiuscole e minuscole e ha una lunghezza massima di 128 caratteri. Solo i caratteri alfanumerici ASCII a 7 bit più {'-',':','/',' \' ,' .',' +', '%',' _',' #',' *','?','!',' (',')',',',' =',' @',';','''} sono accettati. |
| **Azione**               | I valori accettati sono **Accept**   o **Reject**   come azione per la regola di filtro IP. |
| **ipMask**               | Specificare un singolo indirizzo IPv4 o un blocco di indirizzi IP in notazione CIDR. In notazione CIDR, ad esempio, 192.168.100.0/22 rappresenta gli indirizzi IPv4 1024 da 192.168.100.0 a 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Aggiornare una regola di filtro IP

Nell'esempio di modello seguente viene aggiornata la regola di filtro IP denominata "AllowAll", illustrata in precedenza, per rifiutare tutto il traffico.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Eliminare una regola del filtro IP

Nell'esempio di modello seguente vengono eliminate tutte le regole di filtro IP per l'istanza di DPS.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>Valutazione delle regole del filtro IP

Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

Se ad esempio si vogliono accettare gli indirizzi nell'intervallo 192.168.100.0/22 e rifiutare tutti gli altri, la prima regola nella griglia dovrà accettare l'intervallo di indirizzi 192.168.100.0/22. La regola successiva dovrà rifiutare tutti gli indirizzi usando l'intervallo 0.0.0.0/0.

È possibile modificare l'ordine delle regole del filtro IP nella griglia facendo clic sui tre punti verticali all'inizio di una riga e trascinando la selezione.

Per salvare il nuovo ordine delle regole del filtro IP, fare clic su **Salva**.

![Modificare l'ordine delle regole del filtro IP DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Passaggi successivi

Per esplorare ulteriormente la gestione dei DPS, vedere:

* [Informazioni sugli indirizzi IP DPS](iot-dps-understand-ip-address.md)
* [Configurare DPS usando l'interfaccia della riga di comando di Azure](how-to-manage-dps-with-cli.md)
* [Controllare l'accesso a DPS](how-to-control-access.md)
