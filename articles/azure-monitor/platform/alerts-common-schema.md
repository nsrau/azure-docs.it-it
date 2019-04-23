---
title: Schema comune di avviso per gli avvisi di monitoraggio di Azure
description: La comprensione di schema comune di avviso, il motivo per cui è consigliabile usare il e sulla relativa abilitazione
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: c18227a491478d0d8010761440a54fd088344b39
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149377"
---
# <a name="common-alert-schema"></a>Schema di avviso comune

Questo articolo descrive che cos'è lo schema comune degli avvisi, i vantaggi dell'uso e sulla relativa abilitazione.

## <a name="what-is-the-common-alert-schema"></a>Che cos'è lo schema comune degli avvisi?

Lo schema comune degli avvisi consente di standardizzare l'esperienza di utilizzo per le notifiche di avviso in Azure oggi stesso. In passato, i tre tipi di avviso in Azure oggi stesso (metriche, log e log attività) hanno avuto i propri modelli di posta elettronica, gli schemi di webhook e così via. Con lo schema di avviso comune, è ora possibile ricevere notifiche di avviso con uno schema coerente.

Qualsiasi istanza di avviso descrive **la risorsa che è stata interessata** e **la causa dell'avviso**, e queste istanze sono descritti nello schema comune nelle sezioni seguenti:
* **Essentials**: Un set di **standardizzato campi**comune a tutti i tipi di avviso, che descrivono **quali risorse** l'avviso è sui insieme a ulteriori i metadati di avviso comuni (ad esempio, la gravità o descrizione). 
* **Contesto dell'avviso**: Un set di campi che descrivono il **causa dell'avviso**, con i campi che variano **basato sul tipo di avviso**. Ad esempio, un avviso di metrica avrebbe campi come il nome della metrica e il valore della metrica nel contesto degli avvisi, mentre un avviso del log attività dovrà avere informazioni sull'evento che ha generato l'avviso. 

Gli scenari di integrazione tipici che dei clienti prevedono il routing dell'istanza di avviso al team interessato basato su alcuni pivot (ad esempio, gruppo di risorse), dopo il quale il team responsabile inizia a lavorare su di esso. Con lo schema di avviso comune, può aver standardizzato la logica di routing tra i tipi di avviso, sfruttando i campi essenziali, lasciando i campi di contesto come per i team in questione per indagare più a.

Ciò significa che è possibile avere meno integrazioni, rendendo così il processo di gestione e manutenzione una _gran parte_ attività più semplici. Inoltre, miglioramenti futuri payload avviso (ad esempio, la personalizzazione, arricchimento di diagnostica, e così via) segnalare solo backup nello schema comune.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Quali miglioramenti per trasferire lo schema comune degli avvisi?

Lo schema comune degli avvisi si manifesta principalmente le notifiche di avviso. Sono elencati i miglioramenti che verrà visualizzato di seguito:

| Azione | Miglioramenti|
|:---|:---|
| sms | Un modello coerente SMS per tutti i tipi di avviso. |
| Email | Un modello di posta elettronica coerente e dettagliate, consentendo così di diagnosticare facilmente problemi in modo immediato. I collegamenti avanzato incorporati per l'istanza di avviso nel portale e la risorsa interessata assicurano che è possibile passare rapidamente al processo di monitoraggio e aggiornamento. |
| Webhook/logica o App di Azure (funzione) o dell'automazione Runbook | Coerente con struttura JSON per tutti i tipi di avviso, che consente di compilare facilmente le integrazioni tra i diversi tipi di avviso. |

Il nuovo schema abiliterà inoltre un'esperienza più completa di utilizzo degli avvisi tra il portale di Azure e l'app per dispositivi mobili di Azure nell'immediato futuro. 

[Altre informazioni sulle definizioni dello schema dei Webhook/logica o app di Azure funzioni/automazione runbook.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> Le azioni seguenti non supportano lo schema di avviso comune: Connettore ITSM.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Come si abilita lo schema comune degli avvisi?

È possibile acconsentire o rifiutare esplicitamente allo schema comune di avviso tramite gruppi di azioni, in entrambi il portale e tramite l'API REST. L'elemento toggle per passare al nuovo schema esistente a livello di azione. Ad esempio, è necessario acconsentire separatamente per un'azione di posta elettronica e un'azione webhook.

> [!NOTE]
> 1. Per impostazione predefinita, i tipi di avviso seguenti supportano lo schema comune (nessun opt in richiesto):
>     * Avvisi del rilevamento intelligente
> 1. I seguenti tipi di avviso attualmente non supportano lo schema comune:
>     * Gli avvisi generati da [monitoraggio di Azure per le macchine virtuali](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * Gli avvisi generati da [gestione costi di Azure](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>Tramite il portale di Azure

![Acconsentire esplicitamente uno schema comune di avviso](media/alerts-common-schema/portal-opt-in.png)

1. Aprire qualsiasi esistente o una nuova azione in un gruppo di azioni. 
1. Selezionare 'Sì' per l'elemento toggle abilitare lo schema di avviso comune, come illustrato.

### <a name="through-the-action-groups-rest-api"></a>Tramite l'API REST di gruppi di azioni

È anche possibile usare la [API dei gruppi di azioni](https://docs.microsoft.com/rest/api/monitor/actiongroups) per fornire il consenso esplicito allo schema comune di avviso. Rendendo il [crea o aggiorna](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) chiamata all'API REST, è possibile impostare il flag "useCommonAlertSchema" su 'true' (per fornire il consenso esplicito) o "false" (per rifiutare esplicitamente) per le seguenti azioni - runbook di automazione di funzione o di posta elettronica/webhook/logica di Azure o app.

Ad esempio, la richiesta seguente corpo tentato il [crea o aggiorna](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) API REST eseguirà le operazioni seguenti:

* Abilitare lo schema comune degli avvisi per l'azione di posta elettronica "Messaggio di posta elettronica John Doe"
* Disabilitare lo schema comune degli avvisi per l'azione di posta elettronica "Messaggio di posta elettronica Jane Smith"
* Abilitare lo schema comune degli avvisi per l'azione di webhook "Webhook di esempio"

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

- [Definizioni di avviso dello schema comuni per i runbook di funzioni o dell'automazione di Webhook/logica o app di Azure.](https://aka.ms/commonAlertSchemaDefinitions)



