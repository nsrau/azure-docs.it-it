---
title: Limiti di dimensioni di richiesta di Web application firewall ed elenchi di esclusione nel gateway applicazione Azure - Portale di Azure
description: Questo articolo fornisce informazioni su limiti di dimensioni di richiesta di Web application firewall e sulla configurazione di elenchi di esclusione nel gateway applicazione con il portale di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.workload: infrastructure-services
ms.date: 10/11/2018
ms.author: victorh
ms.openlocfilehash: 9306280d00ec901633585aba2f23ed06b25b4e1e
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115455"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists-public-preview"></a>Limiti delle dimensioni di richiesta di Web application firewall ed elenchi di esclusione (Anteprima pubblica)

Il Web application firewall del gateway applicazione di Azure (WAF) fornisce la protezione per le Applicazioni Web. Questo articolo descrive i limiti di dimensioni di richiesta di WAF e la configurazione di elenchi di esclusione.

> [!IMPORTANT]
> La configurazione dei limiti di dimensioni di richiesta di Web application firewall e degli elenchi di esclusione è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="waf-request-size-limits"></a>Limiti di dimensioni di richiesta WAF
Web application firewall consente agli utenti di configurare i limiti di dimensioni di richiesta entro i limiti inferiori e superiori. Sono disponibili le seguenti due configurazioni di limiti di dimensioni:

- Il campo della dimensione massima del corpo di richiesta è specificato in KB e controlla il limite di dimensioni di richiesta complessivo, escluso qualsiasi caricamento di file. Questo campo può variare da un minimo di 1 kB a un valore massimo di 128 kB. Il valore predefinito per le dimensioni del corpo della richiesta è 128 kB.
- Il campo limite di caricamento file è specificato in MB e determina le dimensioni massime consentite per il caricamento file. Questo campo può avere un valore minimo di 1 MB e un massimo di 500 MB. Il valore predefinito per il limite di caricamento file è 100 MB.

Web application firewall offre anche una funzione configurabile per attivare o disattivare l'ispezione del corpo della richiesta. Per impostazione predefinita, viene abilitata l'ispezione del corpo della richiesta. Se l'ispezione del corpo della richiesta è stata disattivata, Web application firewall non valuta il contenuto del corpo del messaggio HTTP. In questi casi, WAF continua applicare le regole WAF su intestazioni, cookie e URI. Se l'ispezione del corpo della richiesta è stata disattivata, il campo dimensioni massime del corpo di richiesta non è applicabile e non può essere impostato. La disattivazione dell'ispezione del corpo della richiesta consente l'invio a WAF di messaggi superiori a 128 kB. Tuttavia, il corpo del messaggio non viene controllato per le vulnerabilità.

## <a name="waf-exclusion-lists"></a>Elenchi di esclusione di WAF

Gli elenchi di esclusione di Web Application firewall consentono agli utenti di omettere determinati attributi di richiesta da una valutazione di WAF. Un esempio comune è rappresentato dai token inseriti in Active Directory che vengono usati per l'autenticazione o per i campi password. Tali attributi sono soggetti a contenere caratteri speciali che possono attivare un falso positivo dalle regole di WAF. Una volta che un attributo viene aggiunto all'elenco di esclusione WAF, non viene preso in considerazione da nessuna regola WAF configurata e attiva. Gli elenchi di esclusione hanno ambito globale.
È possibile aggiungere intestazioni di richiesta, il corpo della richiesta, i cookie di richiesta o gli argomenti di stringa di query di richiesta per gli elenchi di esclusione di WAF. Se il corpo ha dati del modulo o XML/JSON (coppie chiave-valore), è possibile usare il tipo di esclusione dell'attributo della richiesta.

È possibile specificare un'esatta intestazione di richiesta, un corpo, un cookie o una corrispondenza dell'attributo stringa della query o, facoltativamente, è possibile specificare corrispondenze parziali.

Di seguito sono riportati gli operatori di criteri di corrispondenza supportati:

- **Uguale a**: questo operatore viene usato per una corrispondenza esatta. Ad esempio, per selezionare l'intestazione denominata **bearerToken** usare operatore uguale a con impostato come selettore **bearerToken**.
- **Inizia con**: questo operatore corrisponde a tutti i campi che iniziano con un valore del selettore specificato. 
- **Termina con**: questo operatore corrisponde a tutti i campi di richiesta che terminano con un valore del selettore specificato. 
- **Contiene**: questo operatore corrisponde a tutti i campi di richiesta che contengono un valore del selettore specificato.

In tutti i casi, la corrispondenza non distingue le maiuscole e le espressioni regolari non sono consentite come selettori.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le impostazioni di WAF, viene descritto come visualizzare i log WAF. Per altre informazioni, vedere [Diagnostica del gateway applicazione](application-gateway-diagnostics.md#diagnostic-logging).