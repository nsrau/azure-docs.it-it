---
title: Modelli di Azure Resource Manager - aggiornare una risorsa | Documentazione Microsoft
description: "Viene descritto come estendere la funzionalità dei Modelli di Azure Resource Manager per aggiornare una risorsa"
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/02/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 7807b3f6135ede80cd7e7e6486364482cde72e29
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---updating-a-resource"></a>Schemi per estendere le funzionalità dei modelli di Azure Resource Manager - aggiornamento di una risorsa

Esistono alcuni scenari in cui è necessario aggiornare una risorsa durante una distribuzione. Questo scenario si verifica quando non è possibile specificare tutte le proprietà per una risorsa finché non vengono create altre risorse dipendenti. Ad esempio, se si crea un pool di back-end per un bilanciamento del carico, si potrebbero aggiornare le interfacce di rete (NIC) nelle macchine virtuali (VM) per includerle nel pool di back-end. Resource Manager supporta l'aggiornamento delle risorse durante la distribuzione, ma è necessario progettare il modello in modo corretto per evitare errori e verificare che la distribuzione venga gestita come un aggiornamento.

## <a name="understand-the-pattern"></a>Informazioni sul modello

In primo luogo, è necessario fare riferimento alla risorsa una volta nel modello per la sua creazione, ma poi è necessario fare riferimento alla risorsa con lo stesso nome per aggiornarla in un secondo momento. Tuttavia, se due risorse hanno lo stesso nome in un modello, Resource Manager genera un'eccezione. Per evitare questo errore, specificare la risorsa aggiornata in un secondo modello che è collegato o incluso come sotto-modello usando il tipo di risorsa `Microsoft.Resources/deployments`.

Successivamente, nel secondo modello è necessario specificare il nome della proprietà esistente da modificare o un nuovo nome per una proprietà da aggiungere. È quindi necessario specificare anche le proprietà originali e i relativi valori originali. Se non si riescono a fornire le proprietà e i valori originali, Resource Manager presuppone che si desideri creare una nuova risorsa ed elimina la risorsa originale. Sostituisce la risorsa originale con una nuova risorsa che include solo le nuove proprietà che sono state specificate.

Infine, è necessario rendere la risorsa dipendente da tutte le risorse correlate che si desiderano distribuire. Questa dipendenza assicura che le risorse vengano create nell'ordine corretto. L'ordine è:

1. Risorsa creata
2. Risorse dipendenti create
3. Risorsa (dal passaggio 1) aggiornata con i valori dalle risorse dipendenti (passaggio 2)

## <a name="example-template"></a>Modello di esempio

Il modello di esempio seguente illustra questo schema. Distribuisce una rete virtuale (VNet) denominata `firstVNet` che ha una subnet denominata `firstSubnet`. Distribuisce quindi un'interfaccia di rete virtuale (NIC) denominata `nic1` e la associa alla subnet. Quindi, una risorsa di distribuzione denominata `updateVNet` include un modello annidato che fa riferimento al nome della risorsa `firstVNet`. 

Esaminare la proprietà `addressSpace` e la proprietà `subnets` in questa risorsa. Si noti che il valore `addressSpace` è impostato sullo stesso valore della proprietà sull'oggetto di distribuzione di risorse `firstVNet`. Nella matrice `subnets` il valore per `firstSubnet` è impostato in modo analogo. Poiché tutte le proprietà originali `firstVNet` sono state specificate, Resource Manager aggiorna la risorsa in Azure. In questo caso l'aggiornamento è l'aggiunta di una nuova subnet denominata `secondSubnet`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
      {
      "apiVersion": "2016-03-30",
      "name": "firstVNet",
      "location":"[resourceGroup().location]",
      "type": "Microsoft.Network/virtualNetworks",
      "properties": {
          "addressSpace":{"addressPrefixes": [
              "10.0.0.0/22"
          ]},
          "subnets":[              
              {
                  "name":"firstSubnet",
                  "properties":{
                    "addressPrefix":"10.0.0.0/24"
                  }
              }
            ]
      }
    },
    {
        "apiVersion": "2015-06-15",
        "type":"Microsoft.Network/networkInterfaces",
        "name":"nic1",
        "location":"[resourceGroup().location]",
        "dependsOn": [
            "firstVNet"
        ],
        "properties": {
            "ipConfigurations":[
                {
                    "name":"ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod":"Dynamic",
                        "subnet": {
                            "id": "[concat(resourceId('Microsoft.Network/virtualNetworks','firstVNet'),'/subnets/firstSubnet')]"
                        }
                    }
                }
            ]
        }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "updateVNet",
      "dependsOn": [
          "nic1"
      ],
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
              {
                  "apiVersion": "2016-03-30",
                  "name": "firstVNet",
                  "location":"[resourceGroup().location]",
                  "type": "Microsoft.Network/virtualNetworks",
                  "properties": {
                      "addressSpace": "[reference('firstVNet').addressSpace]",
                      "subnets":[
                          {
                              "name":"[reference('firstVNet').subnets[0].name]",
                              "properties":{
                                  "addressPrefix":"[reference('firstVNet').subnets[0].properties.addressPrefix]"
                                  }
                          },
                          {
                              "name":"secondSubnet",
                              "properties":{
                                  "addressPrefix":"10.0.1.0/24"
                                  }
                          }
                     ]
                  }
              }
          ],
          "outputs": {}
          }
        }
    }
  ],
  "outputs": {}
}
```

## <a name="try-the-template"></a>Provare il modello

Se si vuole sperimentare con questo modello, seguire questi passaggi:

1.    Accedere al portale di Azure, selezionare l'icona "+" e cercare il tipo di risorsa "distribuzione del modello". Dopo averla individuata nei risultati della ricerca, selezionarla.
2.    Quando si arriva alla pagina "distribuzione del modello", selezionare il pulsante **Crea**, che apre il pannello "distribuzione personalizzata".
3.    Selezionare l'icona **Modifica**.
4.    Eliminare il modello vuoto.
5.    Copiare e incollare il modello di esempio precedente nel riquadro destro.
6.    Fare clic sul pulsante **Salva**.
7.    Si torna al riquadro "distribuzione personalizzata", ma questa volta sono presenti alcune caselle di riepilogo a discesa. Selezionare la propria sottoscrizione, creare un nuovo gruppo di risorse o usarne uno esistente e selezionare un percorso. Esaminare i termini e condizioni e quindi selezionare il pulsante **Accetto**.
8.    Fare clic sul pulsante **Acquista**.

Al termine della distribuzione, aprire il gruppo di risorse specificato nel portale. Sarà possibile notare una rete virtuale denominata `firstVNet` e una scheda di rete denominata `nic1`. Fare clic su `firstVNet`, quindi fare clic su `subnets`. Viene visualizzato il `firstSubnet` che è stato originariamente creato e viene visualizzato il `secondSubnet` che è stato aggiunto nella risorsa `updateVNet`. 

![Subnet originale e subnet aggiornata](./media/resource-manager-update/firstVNet-subnets.png)

Quindi, tornare al gruppo di risorse, fare clic su `nic1` e quindi fare clic su `IP configurations`. Nella sezione `IP configurations` il `Subnet` è impostato su `firstSubnet (10.0.0.0/24)`. 

![impostazioni di configurazioni IP NIC1](./media/resource-manager-update/nic1-ipconfigurations.png)

L'originale `firstVNet` è stato aggiornato anziché ricreato. Se `firstVNet` era stato ricreato, `nic1` potrebbe non essere associato a `firstVNet`.

## <a name="next-steps"></a>Passaggi successivi

È possibile usare questo modello nei modelli per specificare nuovamente le proprietà originali della risorsa che si desidera aggiornare. Specificare la risorsa di aggiornamento in un modello collegato o nidificato usando il tipo di risorsa `Microsoft.Resources/deployments`.

* Per informazioni introduttive sulla funzione `reference()`, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
* Questo schema è implementato anche nel [progetto dei blocchi predefiniti del modello](https://github.com/mspnp/template-building-blocks) e nelle [architetture di riferimento di Azure](/azure/architecture/reference-architectures/).
