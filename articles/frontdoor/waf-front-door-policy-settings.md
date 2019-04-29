---
title: Impostazioni dei criteri per il firewall applicazione web con l'ingresso principale di Azure
description: Informazioni su firewall applicazione web (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 4c2f070e9b3c972f063008df8880b196ddb069cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459369"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>Impostazioni dei criteri per il firewall applicazione web con l'ingresso principale di Azure

Un criterio di Web Application Firewall (WAF) consente di controllare l'accesso alle applicazioni web da un set di regole personalizzate e gestite. Il nome del criterio WAF deve essere univoco. Se si prova a usare un nome esistente, si riceverà un errore di convalida. Sono presenti più impostazioni a livello di criteri che si applicano a tutte le regole specificate per il criterio, come descritto in questo articolo.

## <a name="waf-state"></a>Stato di Web Application firewall

Un criterio di Web Application firewall per l'ingresso principale può trovarsi in uno dei due stati seguenti:
- **Abilitato:** Quando un criterio è abilitato, Web Application Firewall sta verificando attivamente le richieste in ingresso e intraprende le azioni corrispondenti in base alle definizioni delle regole
- **Disabilitata:** : quando un criterio è disabilitato, ispezione di Web Application firewall è in pausa. Le richieste in ingresso possono ignorare WAF e vengono inviate al back-end in base al routing di ingresso principale.

## <a name="waf-mode"></a>Modalità WAF

Criteri di Web Application firewall possono essere configurati per l'esecuzione in due modalità seguenti:

- **Modalità di rilevamento** quando eseguito in modalità di rilevamento, Web Application firewall non intraprendere le azioni diverse da monitorare e registrare la richiesta e la regola WAF corrispondente per i log WAF. Attivare la registrazione diagnostica per la porta di ingresso (quando si usa portale, questo scopo, passare al **diagnostica** sezione nel portale di Azure).

- **Modalità di prevenzione** quando configurato per l'esecuzione in modalità di prevenzione, WAF accetta l'azione specificata se la richiesta soddisfa una regola. Tutte le richieste corrispondenti vengono inoltre registrate nei registri del WAF.

## <a name="waf-response-for-blocked-requests"></a>Risposta di Web Application firewall per le richieste bloccate

Per impostazione predefinita, quando WAF blocca una richiesta a causa di una regola corrispondente, restituisce un codice di 403 stato con - **la richiesta è bloccata** messaggio. Viene inoltre restituita una stringa di riferimento per la registrazione.

È possibile definire un codice di stato risposta personalizzata e un messaggio di risposta quando una richiesta è bloccata da Web Application firewall. Sono supportati i seguenti codici di stato personalizzato:

- 200    OK
- 403    Forbidden
- 405 metodo non consentito
- 406-pagina non accettabile
- 429 numero eccessivo numero di richieste

Messaggio di codice e risposta di stato risposta personalizzata è un'impostazione a livello di criteri. Dopo averlo configurato, tutte le richieste bloccate ottenere la stessa dello stato di risposta personalizzata e un messaggio di risposta.

## <a name="uri-for-redirect-action"></a>URI per l'operazione di reindirizzamento

Viene richiesto di definire un URI per il reindirizzamento delle richieste se la **REINDIRIZZARE** per le quali le regole contenute in un criterio di Web Application firewall è selezionata l'azione. Questo reindirizzamento URI deve essere un sito HTTP (S) valido e una volta configurato, tutte le richieste di regole di corrispondenza con un'azione di "Reindirizzamento" verranno reindirizzate al sito specificato.


## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come definire WAF [risposte personalizzate](waf-front-door-configure-custom-response-code.md)
