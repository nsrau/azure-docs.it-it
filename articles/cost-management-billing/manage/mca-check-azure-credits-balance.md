---
title: Tenere traccia del saldo dei crediti Azure per un Contratto del cliente Microsoft
description: Informazioni su come controllare il saldo dei crediti Azure per un Contratto del cliente Microsoft.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: banders
ms.openlocfilehash: e65637f20d14b93c212b1af2f20695ff5dfeb350
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199688"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Tenere traccia del saldo dei crediti Azure per un Contratto del cliente Microsoft

È possibile controllare il saldo del credito Azure dell'account di fatturazione per un Contratto del cliente Microsoft nel portale di Azure o tramite le API REST.

Nell'account di fatturazione per un Contratto del cliente Microsoft, i crediti vengono assegnati a un profilo di fatturazione. Ogni profilo di fatturazione dispone di crediti che vengono applicati automaticamente agli addebiti sulla fattura. È necessario disporre di un ruolo di proprietario, collaboratore, lettore o gestione fatture nel profilo di fatturazione o del ruolo di proprietario, collaboratore o lettore nell'account di fatturazione per visualizzare il saldo del credito Azure per un profilo di fatturazione. Per altre informazioni sui ruoli, vedere [Informazioni sui ruoli amministrativi per il Contratto del cliente Microsoft in Azure](understand-mca-roles.md).

Questo articolo di applica a un account di fatturazione per un Contratto del cliente Microsoft. [Verificare di avere accesso a un Contratto del cliente Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Controllare il saldo dei crediti

### <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale](./media/mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. Nella pagina Ambiti di fatturazione selezionare l'account di fatturazione per il quale si vuole tenere traccia del saldo del credito. L'account di fatturazione dovrebbe essere di tipo **Contratto del cliente Microsoft**.

    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale](./media/mca-check-azure-credits-balance/list-of-scopes.png)

    > [!NOTE]
    >
    > Il portale di Azure tiene traccia dell'ultimo ambito di fatturazione a cui si accede e lo visualizza al successivo accesso alla pagina Gestione dei costi e fatturazione. La pagina Ambiti di fatturazione non viene visualizzata se la pagina Gestione dei costi e fatturazione è stata visitata in precedenza. In tal caso, verificare di essere nell'[ambito corretto](#check-access-to-a-microsoft-customer-agreement). In caso contrario, [cambiare ambito](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) per selezionare l'account di fatturazione relativo a un Contratto del cliente Microsoft.

3. Selezionare **Metodi di pagamento** sul lato sinistro e quindi selezionare **Crediti Azure**.

   ![Screenshot del saldo dei crediti per un profilo di fatturazione](./media/mca-check-azure-credits-balance/mca-payment-methods.png)

4. La pagina Crediti Azure contiene le sezioni seguenti:

   #### <a name="balance"></a>Balance

   La sezione del saldo visualizza il riepilogo del saldo dei crediti Azure.

   ![Screenshot del saldo dei crediti per un profilo di fatturazione](./media/mca-check-azure-credits-balance/mca-credit-balance.png)

   | Termine               | Definizione                           |
   |--------------------|--------------------------------------------------------|
   | Saldo stimato  | Importo stimato dei crediti disponibili dopo aver preso in considerazione tutte le transazioni fatturate e in sospeso |
   | Saldo corrente    | Importo dei crediti dall'ultima fattura. Non include le transazioni in sospeso |

   Quando il saldo stimato scende a 0, viene addebitato tutto l'utilizzo, anche quello relativo ai prodotti idonei per i crediti.

   #### <a name="credits-list"></a>Elenco dei crediti

   La sezione Elenco dei crediti visualizza l'elenco dei crediti Azure.

   ![Screenshot dell'elenco dei crediti per un profilo di fatturazione](./media/mca-check-azure-credits-balance/mca-credits-list.png)

   | Termine | Definizione |
   |---|---|
   | Source (Sorgente) | Origine di acquisizione del credito |
   | Data di inizio | Data in cui è stato acquisito il credito |
   | Expiration date | Data di scadenza del credito |
   | Saldo corrente | Saldo dall'ultima fattura |
   | Importo originale | Importo originale del credito |
   | Stato | Stato corrente del credito. Può essere attivo, usato, scaduto o in scadenza |

   #### <a name="transactions"></a>Transazioni

   La sezione Transazioni visualizza tutte le transazioni che hanno influito sul saldo dei crediti.

   ![Screenshot delle transazioni di credito per un profilo di fatturazione](./media/mca-check-azure-credits-balance/mca-credits-transactions.png)

   | Termine | Definizione |
   |---|---|
   | Data della transazione | Data in cui è avvenuta la transazione |
   | Descrizione | Descrizione della transazione |
   | Amount| L'importo della transazione |
   | Balance | Saldo dopo la transazione |

    > [!NOTE]
    >
    > Se i crediti Azure non sono visualizzati nella pagina Metodi di pagamento, significa che non sono disponibili crediti oppure che non è stato selezionato l'ambito corretto. Selezionare l'account di fatturazione che dispone di crediti o uno dei suoi profili di fatturazione. Per informazioni su come cambiare ambito, vedere [Cambiare ambito di fatturazione nel portale di Azure](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).

5. Se i crediti Azure sono visualizzati nell'ambito dell'account di fatturazione e tale account ha più profili di fatturazione, la pagina Crediti Azure conterrà una tabella con un riepilogo dei crediti Azure per ogni profilo di fatturazione. Selezionare un profilo di fatturazione nell'elenco, selezionare i metodi di pagamento e quindi i crediti Azure per visualizzare i dettagli di un profilo di fatturazione.

    ![Screenshot dell'elenco dei crediti per un account di fatturazione](./media/mca-check-azure-credits-balance/mca-account-credit-list.png)

### <a name="rest-api"></a>[REST API](#tab/rest)

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

| Nome dell'elemento  | Descrizione                                                                           |
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
| Nome dell'elemento  | Descrizione                                                                                               |
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
| Nome dell'elemento  | Descrizione                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | Data in cui si è verificata la transazione. |
| `description` | Descrizione della transazione. |
| `adjustments`   | Rettifiche del credito per la transazione.    |
| `creditExpired`      | Importo del credito scaduto. |
| `charges`      |  Addebiti per la transazione.  |
| `closedBalance`  | Saldo dopo la transazione.   |
| `eventType`  | Tipo di transazione.   |
| `invoiceNumber`  | Numero della fattura in cui viene fatturata la transazione. Questo valore sarà vuoto per le transazioni in sospeso.   |

---

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
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sull'account di fatturazione del Contratto del cliente Microsoft](../understand/mca-overview.md)
- [Informazioni sui termini presenti nella fattura del Contratto del cliente Microsoft](../understand/mca-understand-your-invoice.md)
