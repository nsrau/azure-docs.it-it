---
title: Abilitazione di SSL end-to-end nel gateway applicazione Azure
description: Questo articolo offre un'introduzione al supporto di SSL end-to-end del gateway applicazione.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2018
ms.author: amsriva
ms.openlocfilehash: e7020ef5c1f7411c7226e7a2db489112ee6bf0a4
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945502"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Panoramica di SSL end-to-end con il gateway applicazione

Il gateway applicazione supporta la terminazione SSL nel gateway, dopo la quale il traffico scorre generalmente non crittografato verso i server back-end. Questa funzionalità consente ai server Web di non gestire il costoso carico di crittografia e decrittografia. Tuttavia, per alcuni clienti le comunicazioni non crittografate verso i server back-end non rappresentano un'opzione accettabile. Le comunicazioni non crittografate potrebbero essere causate da requisiti di sicurezza o conformità oppure da un'applicazione che può accettare solo una connessione protetta. Per tali applicazioni, il gateway applicazione supporta ora la crittografia SSL end-to-end.

La crittografia SSL end-to-end consente di trasmettere in modo sicuro dati sensibili crittografati al back-end, usufruendo comunque dei vantaggi delle funzionalità di bilanciamento del carico di livello 7 offerte dal gateway applicazione. Alcune di queste funzionalità sono l'affinità di sessione basata su cookie, il routing basato su URL, il supporto per il routing basato su siti o la possibilità di inserire intestazioni X-Forwarded-*.

Se configurato con la modalità di comunicazione SSL end-to-end, il gateway applicazione termina le sessioni SSL nel gateway ed esegue la decrittografia del traffico utente. Applica quindi le regole configurate per selezionare un'istanza del pool di back-end adeguata su cui instradare il traffico. Il gateway applicazione avvia a questo punto una nuova connessione SSL al server back-end e crittografa nuovamente i dati usando il certificato di chiave pubblica del server back-end prima di trasmettere la richiesta al back-end. Per abilitare SSL end-to-end si imposta la configurazione del protocollo in **BackendHTTPSetting** su HTTPS. Questa impostazione viene quindi applicata a un pool back-end. Per una comunicazione protetta, ogni server back-end nel pool di back-end con SSL end-to-end abilitato deve essere configurato con un certificato.

![Scenario di SSL end-to-end][1]

In questo esempio, le richieste che usano TLS1.2 vengono instradate ai server back-end in Pool1 con SSL end-to-end.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL end-to-end e aggiunta dei certificati all'elenco dei consentiti

Il gateway applicazione comunica solo con istanze back-end note, il cui certificato è incluso nell'elenco dei consentiti del gateway applicazione. Per abilitare l'aggiunta dei certificati all'elenco dei consentiti, è necessario caricare nel gateway applicazione la chiave pubblica dei certificati dei server back-end (non il certificato radice). Sono quindi consentite solo le connessioni a back-end noti e inclusi nell'elenco. Gli altri back-end generano un errore del gateway. I certificati autofirmati vengono usati a scopo di test e non sono consigliati per i carichi di lavoro. Prima dell'uso, questi certificati devono essere aggiunti all'elenco dei consentiti nel gateway applicazione, come descritto nei passaggi precedenti.

> [!NOTE]
> L'installazione di certificati di autenticazione non è necessaria per servizi di Azure attendibili, come ad esempio App Web di Azure.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>SSL end-to-end con SKU v2

I certificati di autenticazione sono stati deprecati e sostituiti da certificati radice attendibili nello SKU v2 gateway applicazione. Funzionano in modo analogo ai certificati di autenticazione con alcune differenze principali:

- I certificati firmati da autorità di certificazione note il cui nome comune corrisponde al nome host nelle impostazioni HTTP di back-end non richiedono alcun passaggio aggiuntivo per il funzionamento di SSL end-to-end. 

   Ad esempio, se i certificati di back-end sono rilasciati da un'autorità di certificazione nota il cui nome comune è contoso.com e anche il campo host delle impostazioni HTTP di back-end è impostato su contoso.com, non sono necessarie altre operazioni. Se si configura il protocollo come HTTPS nell'impostazione HTTP di back-end, sia il probe di integrità che il percorso dei dati saranno abilitati per SSL. Se si usano app Web di Azure o altri servizi Web di Azure come back-end, anche questi sono considerati implicitamente attendibili e non sono necessari altri passaggi per SSL end-to-end.
- Se il certificato è autofirmato o firmato da intermediari sconosciuti, per abilitare SSL end-to-end nello SKU v2 deve essere definito un certificato radice attendibile. Il gateway applicazione comunicherà solo con back-end il cui certificato radice del certificato del server corrisponde a uno dei certificati dell'elenco dei certificati radice attendibili nell'impostazione HTTP di back-end associata al pool.
- Oltre alla corrispondenza del certificato radice, il gateway applicazione verifica anche se l'impostazione Host specificata nell'impostazione HTTP di back-end corrisponde a quella del nome comune presentata dal certificato SSL del server back-end. Quando si tenta di stabilire una connessione SSL al back-end, il gateway applicazione imposta l'estensione Indicazione nome server (SNI) sull'host specificato nell'impostazione HTTP di back-end.
- Se si sceglie **pick hostname from backend address** (selezionare il nome host dall'indirizzo di back-end) anziché il campo Host nell'impostazione HTTP di back-end, l'intestazione SNI è sempre impostata sul nome di dominio completo del pool di back-end e il nome comune sul certificato SSL del server back-end deve corrispondere al nome di dominio completo. I membri del pool di back-end con indirizzi IP non sono supportati in questo scenario.
- Il certificato radice è un certificato radice codificato con codifica base64 dai certificati del server back-end.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso i concetti di SSL end-to-end, passare a [Configurare SSL end-to-end usando un gateway applicazione con PowerShell](application-gateway-end-to-end-ssl-powershell.md) per creare un gateway applicazione usando SSL end-to-end.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
