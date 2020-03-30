---
title: Filtri di connessione IP di Azure IoT DPS Documenti Microsoft
description: Come usare il filtro IP per bloccare le connessioni da indirizzi IP specifici all'istanza DPS di Azure IoT.How to use IP filtering to block connections from specific IP addresses to your Azure IoT DPS instance. È possibile bloccare le connessioni da singoli indirizzi IP o da intervalli di indirizzi IP.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284915"
---
# <a name="use-ip-filters"></a>Usare i filtri IP

La sicurezza è un aspetto importante di qualsiasi soluzione IoT.Security is an important aspect of any IoT solution. Talvolta è necessario specificare in modo esplicito gli indirizzi IP da cui possono connettersi i dispositivi come parte della configurazione di sicurezza. La funzionalità *di filtro IP* per un servizio DPS (Hub Device Provisioning) di Azure IoT consente di configurare regole per rifiutare o accettare il traffico da indirizzi IPv4 specifici.

## <a name="when-to-use"></a>Utilizzo

Esistono due casi d'uso specifici in cui è utile bloccare le connessioni a un endpoint DPS da determinati indirizzi IP:There are two specific use-cases where it is useful to block connections to a DPS endpoint from certain IP addresses:

* Il DPS deve ricevere traffico solo da un intervallo specificato di indirizzi IP e rifiutare tutto il resto. Ad esempio, si usa DPS con [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) per creare connessioni private tra un DPS e i dispositivi.

* È necessario rifiutare il traffico proveniente da indirizzi IP identificati come sospetti dall'amministratore DPS.

## <a name="how-filter-rules-are-applied"></a>Come vengono applicate le regole di filtro

Le regole di filtro IP vengono applicate a livello di istanza DPS. Le regole del filtro IP vengono quindi applicate a tutte le connessioni provenienti dai dispositivi e dalle app back-end con qualsiasi protocollo supportato.

Qualsiasi tentativo di connessione da un indirizzo IP che corrisponde a una regola IP di rifiuto nell'istanza DPS riceve un codice di stato 401 non autorizzato e una descrizione. Il messaggio di risposta non indica la regola IP.

## <a name="default-setting"></a>Impostazione predefinita

Per impostazione predefinita, la griglia **Filtro IP** nel portale per DPS è vuota. Questa impostazione predefinita indica che il DPS accetta connessioni da qualsiasi indirizzo IP. Questa impostazione predefinita equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0.

![Impostazioni predefinite filtro IP IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Aggiungere o modificare una regola del filtro IP

Per aggiungere una regola di filtro IP, selezionare **Aggiungi regola filtro IP**.

![Aggiungere una regola di filtro IP a un DPS IoT](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Dopo aver selezionato **Aggiungi regola filtro IP**, compilare i campi.

![Dopo aver selezionato Aggiungi una regola di filtro IP](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Specificare un **nome** per la regola di filtro IP. Deve essere una stringa alfanumerica univoca, senza distinzione tra maiuscole e minuscole, composta da un massimo di 128 caratteri. Sono ammessi solo caratteri alfanumerici ASCII a 7 bit più `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Specificare un singolo indirizzo IPv4 o un blocco di indirizzi IP in notazione CIDR. In notazione CIDR, ad esempio, 192.168.100.0/22 rappresenta gli indirizzi IPv4 1024 da 192.168.100.0 a 192.168.103.255.

* Selezionare **Consenti** o **Blocca** come **azione** per la regola di filtro IP.

Dopo aver compilato i campi, selezionare **Salva** per salvare la regola. Viene visualizzato un avviso che informa che l'aggiornamento è in corso.

![Notifica sul salvataggio di una regola di filtro IP](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

L'opzione **Aggiungi** è disabilitata quando si raggiunge il numero massimo di dieci regole del filtro IP.

Per modificare una regola esistente, seleziona i dati da modificare, apporta la modifica, quindi seleziona **Salva** per salvare la modifica.

> [!NOTE]
> Il rifiuto di indirizzi IP può impedire ad altri servizi di Azure di interagire con l'istanza DPS.

## <a name="delete-an-ip-filter-rule"></a>Eliminare una regola del filtro IP

Per eliminare una regola di filtro IP, selezionare l'icona del cestino in tale riga e quindi selezionare **Salva**. La regola viene rimossa e la modifica viene salvata.

![Eliminare una regola di filtro IP IoT DPSDelete an IoT DPS IP filter rule](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Aggiornare le regole di filtro IP nel codiceUpdate IP filter rules in code

È possibile recuperare e modificare il filtro IP DPS usando l'endpoint REST del provider di risorse di Azure.You may retrieve and modify your DPS IP filter using Azure resource Provider's REST endpoint. Vedere `properties.ipFilterRules` nel [metodo createorupdate](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

L'aggiornamento delle regole di filtro IP DPS non è attualmente supportato con l'interfaccia della riga di comando di Azure o Azure PowerShell, ma può essere eseguita con i modelli di Azure Resource Manager.Updating DPS IP filter rules is not currently supported with Azure CLI or Azure PowerShell but, can be accomplished with Azure Resource Manager templates. Per istruzioni sull'uso dei modelli di Resource Manager, vedere Modelli di Azure Resource Manager.See, [Azure Resource Manager templates](../azure-resource-manager/templates/overview.md) for guidance on using Resource Manager templates. Negli esempi di modello seguenti viene illustrato come creare, modificare ed eliminare le regole di filtro IP DPS.

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

Aggiornare gli attributi della regola di filtro IP del modello in base alle proprie esigenze.

| Attributo                | Descrizione |
| ------------------------ | ----------- |
| **NomeFiltro**           | Specificare un nome per la regola di filtro IP. Deve essere una stringa alfanumerica univoca, senza distinzione tra maiuscole e minuscole, composta da un massimo di 128 caratteri. Sono accettati solo i caratteri ASCII ASCII a 7 bit alfanumerici più ''-', ':', '', ',',\'''', '%', ''', ''', ''', ''', ''', ''', '''', ''','''', '''', '''', ''''' sono accettati. |
| **Azione**               | I valori accettati sono **Accetta** o **Rifiuta** come azione per la regola di filtro IP. |
| **Maschera ipMask**               | Specificare un singolo indirizzo IPv4 o un blocco di indirizzi IP in notazione CIDR. In notazione CIDR, ad esempio, 192.168.100.0/22 rappresenta gli indirizzi IPv4 1024 da 192.168.100.0 a 192.168.103.255. |


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

Nell'esempio di modello seguente vengono eliminate tutte le regole di filtro IP per l'istanza DPS.

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

![Modificare l'ordine delle regole di filtro IP DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Passaggi successivi

Per esplorare ulteriormente la gestione dei DPS, vedere:

* [Informazioni sugli indirizzi IP IoT DPS](iot-dps-understand-ip-address.md)
* [Configurare DPS usando l'interfaccia della riga di comando di AzureConfigure DPS using the Azure CLI](how-to-manage-dps-with-cli.md)
* [Controllare l'accesso a DPS](how-to-control-access.md)
