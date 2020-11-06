---
title: Configurazione delle regole di routing richieste del gateway applicazione Azure
description: Questo articolo descrive come configurare le regole di routing delle richieste del gateway applicazione Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 047ce9b33836e2c23a37b1383942323d7c382485
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397536"
---
# <a name="application-gateway-request-routing-rules"></a>Regole di routing delle richieste del gateway applicazione

Quando si crea un gateway applicazione usando il portale di Azure, si crea una regola predefinita ( *Rule1* ). Questa regola associa il listener predefinito ( *appGatewayHttpListener* ) con il pool back-end predefinito ( *appGatewayBackendPool* ) e le impostazioni http back-end predefinite ( *appGatewayBackendHttpSettings* ). Dopo aver creato il gateway, è possibile modificare le impostazioni della regola predefinita o creare nuove regole.

## <a name="rule-type"></a>Tipo regola

Quando si crea una regola, è possibile scegliere tra [ *base* e *basata sul percorso*](./application-gateway-components.md#request-routing-rules).

- Scegliere di base se si desidera inviare tutte le richieste sul listener associato (ad esempio, *Blog <i></i> . contoso.com/ \* )* a un singolo pool back-end.
- Scegliere basato sul percorso se si desidera instradare le richieste da percorsi URL specifici a pool back-end specifici. Il modello di percorso viene applicato solo al percorso dell'URL, non ai parametri della query.

### <a name="order-of-processing-rules"></a>Ordine delle regole di elaborazione

Per lo SKU V1 e V2, i criteri di ricerca delle richieste in ingresso vengono elaborati nell'ordine in cui i percorsi sono elencati nella mappa del percorso URL della regola basata sul percorso. Se una richiesta corrisponde al modello in due o più percorsi nella mappa di percorso, il percorso elencato per primo viene associato. E la richiesta viene trasmessa al back-end associato a tale percorso.

## <a name="associated-listener"></a>Listener associato

Associare un listener alla regola in modo che la *regola di routing richiesta* associata al listener venga valutata per determinare il pool back-end a cui indirizzare la richiesta.

## <a name="associated-back-end-pool"></a>Pool back-end associato

Associare alla regola il pool back-end che contiene le destinazioni di back-end che gestiscono le richieste ricevute dal listener.

 - Per una regola di base è consentito un solo pool back-end. Tutte le richieste sul listener associato vengono inviate al pool back-end.

 - Per una regola basata sul percorso, aggiungere più pool back-end che corrispondono a ogni percorso URL. Le richieste che corrispondono al percorso URL immesso vengono inviate al pool back-end corrispondente. Aggiungere inoltre un pool back-end predefinito. Le richieste che non corrispondono ad alcun percorso URL nella regola vengono inviate a tale pool.

## <a name="associated-back-end-http-setting"></a>Impostazione HTTP back-end associata

Aggiungere un'impostazione HTTP back-end per ogni regola. Le richieste vengono indirizzate dal gateway applicazione alle destinazioni di back-end usando il numero di porta, il protocollo e altre informazioni specificate in questa impostazione.

Per una regola di base è consentita una sola impostazione HTTP back-end. Tutte le richieste sul listener associato vengono inviate alle destinazioni back-end corrispondenti utilizzando questa impostazione HTTP.

Per una regola basata sul percorso, aggiungere più impostazioni HTTP back-end corrispondenti a ogni percorso URL. Le richieste che corrispondono al percorso URL in questa impostazione vengono inviate alle destinazioni back-end corrispondenti usando le impostazioni HTTP che corrispondono a ogni percorso URL. Aggiungere inoltre un'impostazione HTTP predefinita. Le richieste che non corrispondono ad alcun percorso URL in questa regola vengono inviate al pool back-end predefinito usando l'impostazione HTTP predefinita.

## <a name="redirection-setting"></a>Impostazione del reindirizzamento

Se il reindirizzamento è configurato per una regola di base, tutte le richieste sul listener associato vengono reindirizzate alla destinazione. Si tratta di un reindirizzamento *globale* . Se il reindirizzamento è configurato per una regola basata sul percorso, vengono reindirizzate solo le richieste in un'area specifica del sito. Un esempio è un'area del carrello acquisti indicata da */cart/ \**. Si tratta del reindirizzamento *basato sul percorso* .

Per altre informazioni sui reindirizzamenti, vedere [Panoramica del reindirizzamento del gateway applicazione](redirect-overview.md).

### <a name="redirection-type"></a>Tipo di Reindirizzamento

Scegliere il tipo di reindirizzamento necessario: *permanente (301* ), *temporaneo (307)* , *trovato (302)* o *vedere altro (303)*.

### <a name="redirection-target"></a>Destinazione di Reindirizzamento

Scegliere un altro listener o un sito esterno come destinazione del reindirizzamento.

#### <a name="listener"></a>Listener

Scegliere listener come destinazione di reindirizzamento per reindirizzare il traffico da un listener a un altro sul gateway. Questa impostazione è obbligatoria quando si desidera abilitare il Reindirizzamento da HTTP a HTTPS. Reindirizza il traffico dal listener di origine che controlla le richieste HTTP in ingresso al listener di destinazione che controlla le richieste HTTPS in ingresso. È anche possibile scegliere di includere la stringa di query e il percorso dalla richiesta originale nella richiesta che viene trasmessa alla destinazione di reindirizzamento.

![Finestra di dialogo componenti gateway applicazione](./media/configuration-overview/configure-redirection.png)

Per ulteriori informazioni sul Reindirizzamento da HTTP a HTTPS, vedere:
- [Reindirizzamento da HTTP a HTTPS tramite il portale di Azure](redirect-http-to-https-portal.md)
- [Reindirizzamento da HTTP a HTTPS tramite PowerShell](redirect-http-to-https-powershell.md)
- [Reindirizzamento da HTTP a HTTPS usando l'interfaccia della riga di comando di Azure](redirect-http-to-https-cli.md)

#### <a name="external-site"></a>Sito esterno

Scegliere sito esterno quando si desidera reindirizzare il traffico sul listener associato a questa regola a un sito esterno. È possibile scegliere di includere la stringa di query dalla richiesta originale nella richiesta che viene trasmessa alla destinazione di reindirizzamento. Non è possibile inviare il percorso al sito esterno che era presente nella richiesta originale.

Per ulteriori informazioni sul reindirizzamento, vedere:
- [Reindirizzare il traffico a un sito esterno tramite PowerShell](redirect-external-site-powershell.md)
- [Reindirizzare il traffico a un sito esterno tramite l'interfaccia della riga di comando](redirect-external-site-cli.md)

## <a name="rewrite-http-headers-and-url"></a>Riscrivere l'URL e le intestazioni HTTP

Con le regole di riscrittura è possibile aggiungere, rimuovere o aggiornare le intestazioni di richiesta e risposta HTTP (S), nonché il percorso URL e i parametri della stringa di query, perché i pacchetti di richiesta e risposta passano tra il client e i pool back-end tramite il gateway applicazione.

I parametri Header e URL possono essere impostati su valori statici o ad altre intestazioni e variabili server. Questo consente di utilizzare casi di utilizzo importanti, ad esempio l'estrazione di indirizzi IP del client, la rimozione di informazioni riservate sul back-end, l'aggiunta di ulteriore sicurezza e così via.
Per altre informazioni, vedere:

 - [Riscrivere le intestazioni HTTP e la panoramica degli URL](rewrite-http-headers-url.md)
 - [Configurare la riscrittura dell'intestazione HTTP](rewrite-http-headers-portal.md)
 - [Configurare la riscrittura URL](rewrite-url-portal.md)

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulle impostazioni HTTP](configuration-http-settings.md)