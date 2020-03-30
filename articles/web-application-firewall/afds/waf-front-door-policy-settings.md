---
title: Impostazioni dei criteri per il firewall dell'applicazione Web con lo sportello anteriore di Azure
description: Imparare Web Application Firewall (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932607"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Impostazioni dei criteri per il firewall dell'applicazione Web in Azure Front Door

Un criterio Web Application Firewall (WAF) consente di controllare l'accesso alle applicazioni Web tramite un set di regole personalizzate e gestite. Il nome del criterio WAF deve essere univoco. Se si tenta di utilizzare un nome esistente, verrà visualizzato un errore di convalida. Esistono più impostazioni a livello di criteri che si applicano a tutte le regole specificate per tale criterio, come descritto in questo articolo.

## <a name="waf-state"></a>Stato WAF

Una politica WAF per Front Door può trovarsi in uno dei due stati seguenti:
- **Abilitato:** Quando un criterio è abilitato, WAF sta ispezionando attivamente le richieste in arrivo ed esegue le azioni corrispondenti in base alle definizioni delle regole
- **Disabilitato:** - Quando un criterio è disabilitato, l'ispezione WAF viene sospesa. Le richieste in arrivo bypasseranno WAF e vengono inviate ai back-end in base al routing della porta anteriore.

## <a name="waf-mode"></a>Modalità WAF

I criteri di WAF possono essere configurati per l'esecuzione nelle due modalità seguenti:

- **Modalità di rilevamento** Quando viene eseguito in modalità di rilevamento, WAF non esegue alcuna azione diversa dal monitoraggio e dal log della richiesta e dalla regola WAF corrispondente nei registri WAF. Attivare la diagnostica di registrazione per Front Door (quando si usa il portale, è possibile eseguire questa operazione passando alla sezione **Diagnostica** nel portale di Azure).

- **Modalità Prevenzione** Se configurato per l'esecuzione in modalità di prevenzione, WAF esegue l'azione specificata se una richiesta corrisponde a una regola. Tutte le richieste corrispondenti vengono anche registrate nei log di WAF.

## <a name="waf-response-for-blocked-requests"></a>Risposta WAF per le richieste bloccate

Per impostazione predefinita, quando WAF blocca una richiesta a causa di una regola corrispondente, restituisce un codice di stato 403 con - **La richiesta è bloccata.** Viene inoltre restituita una stringa di riferimento per la registrazione.

È possibile definire un codice di stato della risposta personalizzato e un messaggio di risposta quando una richiesta viene bloccata da WAF. Sono supportati i seguenti codici di stato personalizzati:

- 200 OK
- 403 Vietato
- 405 Metodo non consentito
- 406 Non accettabile
- 429 Troppe richieste

Il codice di stato della risposta personalizzata e il messaggio di risposta sono un'impostazione a livello di criteri. Una volta configurato, tutte le richieste bloccate ottengono lo stesso stato di risposta personalizzata e lo stesso messaggio di risposta.

## <a name="uri-for-redirect-action"></a>URI per l'azione di reindirizzamento

È necessario definire un URI a cui reindirizzare le richieste se l'azione **REDIRECT** è selezionata per una delle regole contenute in un criterio WAF. Questo URI di reindirizzamento deve essere un sito HTTP(S) valido e, una volta configurato, tutte le richieste corrispondenti alle regole con un'azione "REDIRECT" verranno reindirizzate al sito specificato.


## <a name="next-steps"></a>Passaggi successivi
- Scopri come definire [le risposte personalizzate](waf-front-door-configure-custom-response-code.md) WAF
