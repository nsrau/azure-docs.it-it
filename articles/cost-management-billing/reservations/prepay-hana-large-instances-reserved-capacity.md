---
title: Risparmiare su SAP HANA in istanze Large con una prenotazione di Azure
description: Informazioni sui concetti fondamentali da conoscere prima dell'acquisto di una prenotazione di un'Istanza Large di SAP HANA e sulla procedura di acquisto.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 44f7ce657ea9341779e15f6e4817e8fae1515e47
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685970"
---
# <a name="save-on-sap-hana-large-instances-with-an-azure-reservation"></a>Risparmiare su SAP HANA in istanze Large con una prenotazione di Azure

È possibile risparmiare sui costi di SAP HANA in istanze Large acquistando in anticipo prenotazioni di Azure per uno o tre anni. Lo sconto per la prenotazione viene applicato allo SKU di Istanza Large di HANA con provisioning corrispondente all'istanza riservata acquistata. Questo articolo fornisce informazioni sui concetti fondamentali da conoscere prima dell'acquisto di una prenotazione e sulla procedura di acquisto.

Se si acquista una prenotazione, ci si impegna a usare l'Istanza Large di HANA per uno o tre anni. L'acquisto di capacità riservata dell'Istanza Large di HANA copre le risorse di calcolo e di archiviazione NFS incluse nello SKU. La prenotazione non include i costi per le licenze software, ad esempio per il sistema operativo e SAP, o i costi di archiviazione aggiuntivi. Lo sconto per la prenotazione si applica automaticamente all'Istanza Large di SAP HANA con provisioning. Al termine della durata della prenotazione, alla risorsa con provisioning vengono applicate le tariffe con pagamento in base al consumo.

## <a name="purchase-considerations"></a>Considerazioni relative all'acquisto

È necessario effettuare il provisioning di uno SKU dell'Istanza Large di HANA prima di procedere all'acquisto di capacità riservata. La prenotazione viene pagata in anticipo o con pagamenti mensili. Alla capacità riservata dell'Istanza Large di HANA si applicano le restrizioni seguenti:

- Gli sconti per la prenotazione si applicano solo a sottoscrizioni con Contratto Enterprise e Contratto del cliente Microsoft. Le altre sottoscrizioni non sono supportate.
- La flessibilità delle dimensioni istanza non è supportata per la capacità riservata dell'Istanza Large di HANA. Una prenotazione è applicabile solo allo SKU e all'area per cui viene acquistata.
- L'annullamento self-service e lo scambio non sono supportati.
- L'ambito della capacità riservata è un ambito singolo, quindi è applicabile a una sottoscrizione singola e a un singolo gruppo di risorse. La capacità acquistata non può essere aggiornata per l'uso da parte di un'altra sottoscrizione.
- Non è possibile avere un ambito di prenotazione condiviso per la capacità riservata di HANA. Non è possibile dividere, unire o aggiornare l'ambito di prenotazione.
- È possibile acquistare una singola Istanza Large di HANA alla volta usando chiamate API per la capacità riservata. È possibile effettuare chiamate API aggiuntive per acquistare quantità aggiuntive.

È possibile acquistare la capacità riservata nel portale di Azure o mediante l'[API REST](https://docs.microsoft.com/rest/api/reserved-vm-instances/reservationorder/purchase).

## <a name="buy-a-hana-large-instance-reservation"></a>Acquistare una prenotazione di Istanza Large di HANA

Usare le informazioni seguenti per acquistare una prenotazione di Istanza Large di HANA con le [API REST Ordine di prenotazione](https://docs.microsoft.com/rest/api/reserved-vm-instances/reservationorder/purchase).

### <a name="get-the-reservation-order-and-price"></a>Ottenere l'ordine e il prezzo della prenotazione

È prima di tutto necessario ottenere l'ordine e il prezzo della prenotazione per lo SKU dell'Istanza Large di HANA con provisioning usando l'API [Calcola prezzo](https://docs.microsoft.com/rest/api/reserved-vm-instances/reservationorder/calculate).

L'esempio seguente usa [armclient](https://github.com/projectkudu/ARMClient) per effettuare chiamate API REST con PowerShell. L'ordine di prenotazione, la richiesta e il corpo della richiesta dell'API Calcola prezzo devono avere l'aspetto seguente:

```azurepowershell-interactive
armclient post /providers/Microsoft.Capacity/calculatePrice?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': 'testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported'
    }
}"
```

Per altre informazioni sui campi dati e sulle rispettive descrizioni, vedere [Campi della prenotazione dell'Istanza Large di HANA](#hli-reservation-fields).

L'esempio di risposta seguente è analogo alla risposta che viene restituita. Annotare il valore restituito per `quoteId`.

```
{
  "properties": {
    "currencyCode": "USD",
    "netTotal": 313219.0,
    "taxTotal": 0.0,
    "isTaxIncluded": false,
    "grandTotal": 313219.0,
    "purchaseRequest": {
      "sku": {
        "name": "SAP_HANA_On_Azure_S224om"
      },
      "location": "eastus",
      "properties": {
        "billingScopeId": "/subscriptions/11111111-1111-1111-111111111111",
        "term": "P1Y",
        "billingPlan": "Upfront",
        "quantity": 1,
        "displayName": "testreservation_S224om",
        "appliedScopes": [
          "/subscriptions/11111111-1111-1111-111111111111"
        ],
        "appliedScopeType": "Single",
        "reservedResourceType": "SapHana",
        "instanceFlexibility": "NotSupported"
      }
    },
    "quoteId": "d0fd3a890795",
    "isBillingPartnerManaged": true,
    "reservationOrderId": "22222222-2222-2222-2222-222222222222",
    "skuTitle": "SAP HANA on Azure Large Instances - S224om - US East",
    "skuDescription": "SAP HANA on Azure Large Instances, S224om",
    "pricingCurrencyTotal": {
      "currencyCode": "USD",
      "amount": 313219.0
    }
  }
}
```

### <a name="make-your-purchase"></a>Effettuare l'acquisto

Effettuare l'acquisto usando il valore `quoteId` restituito e il valore `reservationOrderId` ottenuto dalla sezione [Ottenere l'ordine e il prezzo della prenotazione](#get-the-reservation-order-and-price) precedente.

Ecco una richiesta di esempio:

```azurepowershell-interactive
armclient put /providers/Microsoft.Capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': ' testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported',
       'renew': true,
       'quoteId': 'd0fd3a890795'
    }
}"
```

Ecco una risposta di esempio. Se l'ordine viene completato correttamente, il valore `provisioningState` dovrebbe essere `creating`.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222",
  "etag": 1,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "term": "P1Y",
    "provisioningState": "Creating",
    "reservations": [
      {
        "sku": {
          "name": "SAP_HANA_On_Azure_S224om"
        },
        "id": "/providers/microsoft.capacity/reservationOrders22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333",
        "type": "Microsoft.Capacity/reservationOrders/reservations",
        "name": "22222222-2222-2222-2222-222222222222/33333333-3333-3333-3333-3333333333333",
        "etag": 1,
        "location": "eastus”
        "properties": {
          "appliedScopes": [
            "/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123"
          ],
          "appliedScopeType": "Single",
          "quantity": 1,
          "provisioningState": "Creating",
          "displayName": " testreservation_S224om",
          "effectiveDateTime": "2020-07-14T05:42:34.3528353Z",
          "lastUpdatedDateTime": "2020-07-14T05:42:34.3528353Z",
          "reservedResourceType": "SapHana",
          "instanceFlexibility": "NotSupported",
          "skuDescription": "SAP HANA on Azure Large Instances – S224om - US East",
          "renew": true
        }
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

### <a name="verify-purchase-status-success"></a>Verificare l'esito positivo dell'acquisto

Eseguire la richiesta GET Ordine di prenotazione per visualizzare lo stato dell'ordine di acquisto. `provisioningState` dovrebbe essere `Succeeded`.

```azurepowershell-interactive
armclient get /providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01
```

La risposta dovrebbe essere analoga all'esempio seguente.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/44444444-4444-4444-4444-444444444444",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222 ",
  "etag": 8,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "createdDateTime": "2020-07-14T05:44:47.157579Z",
    "expiryDate": "2021-07-14",
    "term": "P1Y",
    "provisioningState": "Succeeded",
    "reservations": [
      {
        "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333"
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

## <a name="hli-reservation-fields"></a>Campi della prenotazione dell'Istanza Large di HANA

Le informazioni seguenti spiegano il significato dei diversi campi della prenotazione.

  **SKU** Nome dello SKU dell'Istanza Large di HANA. Ha un aspetto analogo a `SAP_HANA_On_Azure_<SKUname>`.

  **Posizione** Aree disponibili per l'Istanza Large di HANA. Per informazioni sulle aree disponibili, vedere [SKU per SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus). Per ottenere il formato della stringa per la posizione, usare la [chiamata API Ottieni località](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations#locationlistresult).

  **Reserved Resource type** (Tipo di risorsa riservata) `SapHana`

  **Sottoscrizione** Sottoscrizione usata per pagare la prenotazione. I costi per la prenotazione vengono addebitati al metodo di pagamento specificato nella sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri offerta: MS-AZR-0017P or MS-AZR-0148P) o un Contratto del cliente Microsoft. Gli addebiti vengono dedotti dal saldo dell'impegno monetario, se disponibile, o vengono addebitati come eccedenza.

  **Ambito** L'ambito della prenotazione deve essere un ambito singolo.

  **Periodo** Un anno o tre anni. È analogo a `P1Y` o `P3Y`.

  **Quantità** Numero di istanze acquistate per la prenotazione. La quantità da acquistare corrisponde a una singola Istanza Large di HANA alla volta. Per prenotazioni aggiuntive, ripetere la chiamata API con i campi corrispondenti.

## <a name="troubleshoot-errors"></a>Risolvere gli errori

È possibile ricevere un errore analogo all'esempio seguente quando si acquista una prenotazione. L'errore potrebbe essere stato causato dal mancato provisioning dell'Istanza Large di HANA per l'acquisto. In tale caso, è necessario contattare il team dell'account Microsoft per ottenere un'Istanza Large di HANA con provisioning prima di provare ad acquistare una prenotazione.

```
{
  "error": {
    "code": "BadRequest",
    "message": "Capacity check or quota check failed. Please select a different subscription or 
location. You can also go to https://aka.ms/corequotaincrease to learn about quota increase."
  }
} 
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Come chiamare API REST di Azure con Postman e cURL](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman).
- Per un elenco degli SKU disponibili e informazioni sulle aree, vedere [SKU per SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus).