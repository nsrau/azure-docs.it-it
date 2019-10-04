---
title: Schema di avviso comune per gli avvisi di monitoraggio di Azure
description: Informazioni sullo schema di avviso comune, perché è consigliabile usarlo e come abilitarlo
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 9b142e00543d425b73c4102914bba2dd92c75b8b
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702900"
---
# <a name="common-alert-schema"></a>Schema di avviso comune

Questo articolo descrive lo schema di avviso comune, i vantaggi derivanti dall'uso e come abilitarlo.

## <a name="what-is-the-common-alert-schema"></a>Che cos'è lo schema di avviso comune?

Lo schema di avviso comune standardizza l'esperienza di utilizzo per le notifiche di avviso attualmente in Azure. Storicamente, i tre tipi di avviso attualmente disponibili in Azure (metrica, log e log attività) hanno modelli di posta elettronica, schemi webhook e così via. Con lo schema di avviso comune è ora possibile ricevere notifiche di avviso con uno schema coerente.

Tutte le istanze di avviso descrivono **la risorsa interessata** e **la relativa origine**, che vengono descritte nello schema comune nelle seguenti sezioni:
* **Elementi**di base: Set di **campi standardizzati**, comune in tutti i tipi di avviso, che descrivono la **risorsa** in cui si trova l'avviso insieme a metadati di avviso comuni aggiuntivi, ad esempio gravità o descrizione. 
* **Contesto avviso**: Set di campi che descrivono la **motivo dell'avviso**, con campi che variano **in base al tipo di avviso**. Ad esempio, un avviso di metrica avrebbe campi come il nome della metrica e il valore della metrica nel contesto dell'avviso, mentre un avviso del log attività avrebbe informazioni sull'evento che ha generato l'avviso. 

Gli scenari di integrazione tipici dei clienti implicano il routing dell'istanza di avviso al team interessato in base ad alcuni pivot, ad esempio il gruppo di risorse, dopo il quale il team responsabile inizia a lavorare su di esso. Con lo schema di avviso comune è possibile avere una logica di routing standardizzata tra i tipi di avviso sfruttando i campi essenziali, lasciando i campi di contesto così come sono i team interessati a esaminare ulteriormente.

Ciò significa che è possibile avere un minor numero di integrazioni, rendendo il processo di gestione e manutenzione di un'attività _molto_ più semplice. Inoltre, gli arricchimenti futuri del payload degli avvisi (ad esempio, personalizzazione, arricchimento di diagnostica e così via) vengono visualizzati solo nello schema comune.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Quali sono i miglioramenti apportati dallo schema di avviso comune?

Lo schema di avviso comune si manifesterà principalmente nelle notifiche di avviso. I miglioramenti che verranno visualizzati sono elencati di seguito:

| Azione | Miglioramenti|
|:---|:---|
| sms | Un modello SMS coerente per tutti i tipi di avviso. |
| Email | Un modello di posta elettronica coerente e dettagliato, che consente di diagnosticare facilmente i problemi a colpo d'occhio. I collegamenti profondi incorporati all'istanza di avviso nel portale e la risorsa interessata assicurano che sia possibile passare rapidamente al processo di correzione. |
| Webhook/app per la logica/funzione di Azure/Runbook di automazione | Struttura JSON coerente per tutti i tipi di avviso, che consente di creare facilmente integrazioni tra i diversi tipi di avviso. |

Il nuovo schema consentirà inoltre un'esperienza di utilizzo più approfondita degli avvisi sia nel portale di Azure che nel app per dispositivi mobili di Azure nel futuro immediato. 

[Altre informazioni sulle definizioni dello schema per webhook/app per la logica/funzioni di Azure/manuali operativi di automazione.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> Le azioni seguenti non supportano lo schema di avviso comune: Connettore ITSM.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Ricerca per categorie abilitare lo schema di avviso comune?

È possibile acconsentire esplicitamente o rifiutare esplicitamente lo schema di avviso comune tramite i gruppi di azioni, sia nel portale che tramite l'API REST. L'interruttore per passare al nuovo schema esiste a livello di azione. Ad esempio, è necessario optare separatamente per un'azione di posta elettronica e un'azione webhook.

> [!NOTE]
> 1. Per impostazione predefinita, i tipi di avviso seguenti supportano lo schema comune (non è richiesto il consenso esplicito):
>     * Avvisi del rilevamento intelligente
> 1. Attualmente i tipi di avviso seguenti non supportano lo schema comune:
>     * Avvisi generati da [monitoraggio di Azure per le macchine virtuali](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * Avvisi generati da [Gestione costi di Azure](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>Tramite il portale di Azure

![Consenso esplicito per lo schema di avviso comune](media/alerts-common-schema/portal-opt-in.png)

1. Aprire qualsiasi azione esistente o nuova in un gruppo di azione. 
1. Selezionare ' Sì' per attivare lo schema di avviso comune come illustrato.

### <a name="through-the-action-groups-rest-api"></a>Tramite l'API REST dei gruppi di azioni

È anche possibile usare l' [API dei gruppi di azioni](https://docs.microsoft.com/rest/api/monitor/actiongroups) per acconsentire esplicitamente allo schema di avviso comune. Quando si [Crea o si aggiorna](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) la chiamata all'API REST, è possibile impostare il flag "useCommonAlertSchema" su "true" (per acconsentire esplicitamente) o "false" (per rifiutare esplicitamente) per una delle azioni seguenti: indirizzo di posta elettronica/webhook/app per la logica/Runbook di automazione e funzioni di Azure.

Il corpo della richiesta seguente, ad esempio, per l'API REST di [creazione o aggiornamento](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) effettuerà le operazioni seguenti:

* Abilitare lo schema di avviso comune per l'azione di posta elettronica "posta elettronica di John Doe"
* Disabilitare lo schema di avviso comune per l'azione di posta elettronica "messaggio di posta elettronica di Jane Smith"
* Abilitare lo schema di avviso comune per l'azione webhook "esempio di Webhook"

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>Passaggi successivi

- [Definizioni dello schema di avviso comuni per webhook/app per la logica/funzioni di Azure/manuali operativi di automazione.](https://aka.ms/commonAlertSchemaDefinitions)
- [Informazioni su come creare un'app per la logica che sfrutta lo schema di avviso comune per gestire tutti gli avvisi.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



