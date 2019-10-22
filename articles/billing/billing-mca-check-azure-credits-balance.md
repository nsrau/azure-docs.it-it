---
title: Tenere traccia del saldo dei crediti Azure per un Contratto del cliente Microsoft
description: Informazioni su come controllare il saldo dei crediti Azure per un Contratto del cliente Microsoft.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 4eae7299ab696b01c57a27fd46cbf903c9395152
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375566"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Tenere traccia del saldo dei crediti Azure per un Contratto del cliente Microsoft

È possibile controllare il saldo del credito Azure dell'account di fatturazione per un Contratto del cliente Microsoft nel portale di Azure. 

I crediti vengono usati per il pagamento degli addebiti idonei. Vengono addebitati costi quando si usano prodotti non idonei per l'utilizzo dei crediti o quando l'utilizzo supera il saldo del credito. Per altre informazioni, vedere [Prodotti non coperti dai crediti Azure](#products-that-arent-covered-by-azure-credits).

Nell'account di fatturazione per un Contratto del cliente Microsoft, i crediti vengono assegnati a un profilo di fatturazione. Ogni profilo di fatturazione ha i propri crediti. È necessario disporre di un ruolo di proprietario, collaboratore, lettore o gestione fatture nel profilo di fatturazione o del ruolo di proprietario, collaboratore o lettore nell'account di fatturazione per visualizzare il saldo del credito Azure per un profilo di fatturazione. Per altre informazioni sui ruoli, vedere [Informazioni sui ruoli amministrativi per il Contratto del cliente Microsoft in Azure](billing-understand-mca-roles.md).

Questo articolo di applica a un account di fatturazione per un Contratto del cliente Microsoft. [Verificare di avere accesso a un Contratto del cliente Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance-in-the-azure-portal"></a>Controllare il saldo del credito nel portale di Azure

1. Accedere al [portale di Azure]( https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Selezionare **Crediti Azure** sul lato sinistro. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione o un profilo di fatturazione e quindi **Crediti Azure**.

4. Nella pagina Crediti Azure vengono visualizzate le informazioni seguenti:

   ![Screenshot del saldo del credito e delle transazioni per un profilo di fatturazione](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Termine               | Definizione                           |
   |--------------------|--------------------------------------------------------|
   | Saldo stimato  | Importo stimato dei crediti disponibili dopo aver preso in considerazione tutte le transazioni fatturate e in sospeso |
   | Saldo corrente    | Importo dei crediti dall'ultima fattura. Non include le transazioni in sospeso |
   | Transazioni       | Tutte le transazioni di fatturazione che hanno influito sul saldo del credito Azure |

   Quando il saldo stimato scende a 0, viene addebitato tutto l'utilizzo, anche quello relativo ai prodotti idonei per i crediti.

6. Selezionare **Elenco dei crediti** per visualizzare l'elenco dei crediti per il profilo di fatturazione. L'elenco dei crediti offre le informazioni seguenti:

   ![Screenshot dell'elenco dei crediti per un profilo di fatturazione](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Termine | Definizione |
   |---|---|
   | Source (Sorgente) | Origine di acquisizione del credito |
   | Data di inizio | Data in cui è stato acquisito il credito |
   | Expiration date | Data di scadenza del credito |
   | Saldo corrente | Saldo dall'ultima fattura |
   | Importo originale | Importo originale del credito |
   | Stato | Stato corrente del credito. Può essere attivo, usato, scaduto o in scadenza |

## <a name="check-your-credit-balance-programmatically"></a>Verificare il saldo del credito a livello di codice

Per ottenere il saldo del credito per l'account di fatturazione a livello di codice, è possibile usare le API di [fatturazione ](https://docs.microsoft.com/rest/api/billing/) e di [utilizzo di Azure](https://docs.microsoft.com/rest/api/consumption/).

Gli esempi illustrati di seguito usano le API REST. Attualmente, PowerShell e l'interfaccia della riga di comando di Azure non sono supportati.

### <a name="find-billing-profiles-you-have-access-to"></a>Trovare i profili di fatturazione a cui si ha accesso

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
La risposta API restituisce un elenco di account di fatturazione e i relativi profili di fatturazione.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Usare la proprietà `displayName` del profilo di fatturazione per identificare il profilo di fatturazione per il quale si vuole controllare il saldo del credito. Copiare l'`id` del profilo di fatturazione. Se ad esempio si vuole controllare il saldo del credito per il profilo di fatturazione **Sviluppo**, copiare ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Incollare questo valore da qualche parte per poterlo usare nel passaggio successivo.

### <a name="get-azure-credit-balance"></a>Ottenere il saldo del credito Azure 

Eseguire la richiesta seguente, sostituendo `<billingProfileId>` con l'`id` copiato nel primo passaggio (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

La risposta API restituisce il saldo stimato e corrente per il profilo di fatturazione.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| Nome dell'elemento  | DESCRIZIONE                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | Importo stimato dei crediti disponibili dopo aver preso in considerazione tutte le transazioni fatturate e in sospeso. |
| `currentBalance`   | Importo dei crediti alla data dell'ultima fattura. Non include le transazioni in sospeso.    |
| `pendingCreditAdjustments`      | Rettifiche, ad esempio i rimborsi, che non sono ancora state fatturate.  |
| `expiredCredit`      |  Credito scaduto dall'ultima fattura.  |
| `pendingEligibleCharges`  | Addebiti idonei per il credito non ancora fatturati.   |

### <a name="get-list-of-credits"></a>Ottenere l'elenco dei crediti

Eseguire la richiesta seguente, sostituendo `<billingProfileId>` con l'`id` copiato nel primo passaggio (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
La risposta API restituisce gli elenchi dei crediti Azure per un profilo di fatturazione.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| Nome dell'elemento  | DESCRIZIONE                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | Importo originale del credito. |
| `closedBalance`   | Saldo alla data dell'ultima fattura.    |
| `source`      | Origine che definisce chi ha acquisito il credito. |
| `startDate`      |  Data di attivazione del credito.  |
| `expirationDate`  | Data di scadenza del credito.   |
| `poNumber`  | Numero dell'ordine di acquisto della fattura su cui è stato fatturato il credito.   |

### <a name="get-transactions-that-affected-credit-balance"></a>Ottenere le transazioni che hanno influito sul saldo del credito

Eseguire la richiesta seguente, sostituendo `<billingProfileId>` con l'`id` copiato nel primo passaggio (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). È necessario passare un oggetto **startDate**  e un oggetto **endDate**  per ottenere le transazioni per la durata richiesta.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
La risposta API restituisce tutte le transazioni che hanno influito sul saldo del credito per il profilo di fatturazione.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| Nome dell'elemento  | DESCRIZIONE                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | Data in cui si è verificata la transazione. |
| `description` | Descrizione della transazione. |
| `adjustments`   | Rettifiche del credito per la transazione.    |
| `creditExpired`      | Importo del credito scaduto. |
| `charges`      |  Addebiti per la transazione.  |
| `closedBalance`  | Saldo dopo la transazione.   |
| `eventType`  | Tipo di transazione.   |
| `invoiceNumber`  | Numero della fattura in cui viene fatturata la transazione. Questo valore sarà vuoto per le transazioni in sospeso.   |

## <a name="how-credits-are-used"></a>Modalità d'uso dei crediti

In un account di fatturazione per un Contratto del cliente Microsoft, per gestire le fatture e i metodi di pagamento si usano profili di fatturazione. Per ogni profilo di fatturazione viene generata una fattura mensile, per il cui pagamento si usano i metodi di pagamento.

I crediti acquisiti vengono assegnati a un profilo di fatturazione. Quando viene generata una fattura per il profilo di fatturazione, i crediti vengono applicati automaticamente agli addebiti totali per calcolare l'importo da pagare. L'importo rimanente viene pagato con uno dei metodi di pagamento definiti, ad esempio assegno/bonifico o carta di credito.

## <a name="products-that-arent-covered-by-azure-credits"></a>Prodotti non coperti dai crediti Azure

 I prodotti seguenti non sono coperti dai crediti Azure e il relativo uso viene addebitato indipendentemente dal saldo dei crediti:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Utente registrato
- Openlogic
- Utente registrato XenApp Essentials con Diritti di accesso remoto
- Ubuntu Advantage
- Visual Studio Enterprise (mensile)
- Visual Studio Enterprise (annuale)
- Visual Studio Professional (mensile)
- Visual Studio Professional (annuale)
- Prodotti di Azure Marketplace
- Piani di supporto di Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un Contratto del cliente Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sull'account di fatturazione del Contratto del cliente Microsoft](billing-mca-overview.md)
- [Informazioni sui termini presenti nella fattura del Contratto del cliente Microsoft](billing-mca-understand-your-invoice.md)
