---
title: Filtri di connessioni IP del servizio Device Provisioning in hub IoT di Azure | Microsoft Docs
description: Come usare i filtri IP per bloccare le connessioni da indirizzi IP specifici all'istanza del servizio Device Provisioning in hub IoT di Azure. È possibile bloccare le connessioni da singoli indirizzi IP o da intervalli di indirizzi IP.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 580c378df5fc3912aa540b5d85adf99bc42605e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86511943"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Usare i filtri di connessioni IP del servizio Device Provisioning in hub IoT di Azure

La sicurezza rappresenta un aspetto importante di ogni soluzione IoT. Talvolta è necessario specificare in modo esplicito gli indirizzi IP da cui possono connettersi i dispositivi come parte della configurazione di sicurezza. La funzionalità *Filtro IP* per il servizio Device Provisioning in hub IoT di Azure consente di configurare regole per rifiutare o accettare il traffico proveniente da specifici indirizzi IPv4.

## <a name="when-to-use"></a>Utilizzo

Esistono due casi d'uso specifici in cui è utile bloccare le connessioni a un endpoint del servizio Device Provisioning da specifici indirizzi IP:

* Il servizio Device Provisioning deve ricevere il traffico solo da un intervallo di indirizzi IP specificato e rifiutare tutto il resto. Il servizio Device Provisioning viene ad esempio usato con [Azure ExpressRoute](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) per creare connessioni private tra il servizio e i dispositivi.

* È necessario rifiutare il traffico proveniente da indirizzi IP identificati come sospetti dall'amministratore del servizio Device Provisioning.

## <a name="how-filter-rules-are-applied"></a>Come vengono applicate le regole di filtro

Le regole del filtro IP vengono applicate a livello di istanza del servizio Device Provisioning. Le regole del filtro IP vengono quindi applicate a tutte le connessioni provenienti dai dispositivi e dalle app back-end con qualsiasi protocollo supportato.

Qualsiasi tentativo di connessione da un indirizzo IP corrispondente a una regola di rifiuto nell'istanza del servizio Device Provisioning riceve un codice di stato 401 e la descrizione. Il messaggio di risposta non indica la regola IP.

## <a name="default-setting"></a>Impostazione predefinita

Per impostazione predefinita, la griglia **Filtro IP** nel portale per il servizio Device Provisioning è vuota. Questa impostazione predefinita indica che il servizio Device Provisioning accetta connessioni da qualsiasi indirizzo IP. Questa impostazione predefinita equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0.

![Impostazioni predefinite del filtro IP per il servizio Device Provisioning in hub IoT](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Aggiungere o modificare una regola del filtro IP

Per aggiungere una regola di filtro IP, selezionare **+ Aggiungi regola di filtro IP**.

![Aggiungere una regola di filtro IP al servizio Device Provisioning in hub IoT](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

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
> Il rifiuto di indirizzi IP può impedire l'interazione di altri servizi di Azure con l'istanza del servizio Device Provisioning.

## <a name="delete-an-ip-filter-rule"></a>Eliminare una regola del filtro IP

Per eliminare una regola di filtro IP, selezionare l'icona del cestino sulla riga e quindi selezionare **Salva**. La regola viene rimossa e la modifica viene salvata.

![Eliminare una regola di filtro IP del servizio Device Provisioning](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Aggiornare le regole di filtro IP nel codice

È possibile recuperare e modificare il filtro IP del servizio Device Provisioning usando l'endpoint REST del provider di risorse di Azure. Vedere `properties.ipFilterRules` nel [metodo createorupdate](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

L'aggiornamento delle regole del filtro IP del servizio Device Provisioning non è attualmente supportato con l'interfaccia della riga di comando di Azure o con Azure PowerShell, ma è possibile usare i modelli di Azure Resource Manager. Per informazioni sull'uso dei modelli di Azure Resource Manager, vedere [Modelli di Azure Resource Manager](../azure-resource-manager/templates/overview.md). Gli esempi di modelli che seguono illustrano come creare, modificare ed eliminare le regole del filtro IP del servizio Device Provisioning.

### <a name="add-an-ip-filter-rule"></a>Aggiungere una regola di filtro IP

L'esempio di modello seguente crea una nuova regola di filtro denominata "AllowAll" che accetta tutto il traffico.

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
| **FilterName**           | Specificare un nome per la regola di filtro IP. Questo deve essere univoco e costituito da una stringa alfanumerica che non fa distinzione tra maiuscole e minuscole e ha una lunghezza massima di 128 caratteri. Sono ammessi solo i caratteri alfanumerici ASCII a 7 bit, oltre a {'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''} . |
| **Azione**               | I valori ammessi sono **Accetta** o **Rifiuta** come azione per la regola di filtro IP. |
| **ipMask**               | Specificare un singolo indirizzo IPv4 o un blocco di indirizzi IP in notazione CIDR. In notazione CIDR, ad esempio, 192.168.100.0/22 rappresenta gli indirizzi IPv4 1024 da 192.168.100.0 a 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Aggiornare una regola del filtro IP

L'esempio di modello seguente aggiorna la regola di filtro IP denominata "AllowAll", illustrata in precedenza, per rifiutare tutto il traffico.

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

L'esempio di modello seguente elimina tutte le regole di filtro IP per l'istanza del servizio Device Provisioning.

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

![Cambiare l'ordine delle regole di filtro IP del servizio Device Provisioning](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Passaggi successivi

Per esplorare ulteriormente la gestione del servizio Device Provisioning, vedere:

* [Informazioni sugli indirizzi IP del servizio Device Provisioning](iot-dps-understand-ip-address.md)
* [Configurare il servizio Device Provisioning con l'interfaccia della riga di comando di Azure](how-to-manage-dps-with-cli.md)
* [Controllare l'accesso al servizio Device Provisioning](how-to-control-access.md)
