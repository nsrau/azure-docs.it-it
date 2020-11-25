---
title: Risolvere i problemi di sicurezza e controllo di accesso
description: Informazioni su come risolvere i problemi di sicurezza e controllo di accesso in Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 21a1523016502bd7b0a8682461f1fc16acda2ebb
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008727"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Risolvere i problemi di Azure Data Factory sicurezza e controllo di accesso

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i metodi comuni per la risoluzione dei problemi di sicurezza e controllo di accesso in Azure Data Factory.

## <a name="common-errors-and-messages"></a>Errori comuni e messaggi

### <a name="invalid-or-empty-authentication-key-issue-after-disabling-public-network-access"></a>Problema di chiave di autenticazione non valido o vuoto dopo la disabilitazione dell'accesso alla rete pubblica

#### <a name="symptoms"></a>Sintomi

Il runtime di integrazione self-hosted genera l'errore "la chiave di autenticazione non è valida o è vuota" dopo avere disabilitato l'accesso alla rete pubblica per Data Factory.

#### <a name="cause"></a>Causa

Il problema è probabilmente causato da un problema di risoluzione DNS, perché la disabilitazione della connettività pubblica e la definizione di un endpoint privato non sono utili per la riconnessione.

#### <a name="resolution"></a>Risoluzione

1. È stato eseguito un PsPing per il nome di dominio completo di ADF ed è stato rilevato che il buffer stava per accedere a un endpoint pubblico di ADF, anche dopo che è stato disabilitato.

1. Modificare il file host nella macchina virtuale per eseguire il mapping dell'indirizzo IP privato a FQDN ed eseguire di nuovo PsPing. Il buffer sarà in grado di accedere all'indirizzo IP privato corretto di ADF.

1. Ripetere la registrazione del runtime di integrazione self-hosted, che sarà disponibile e in esecuzione.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Non è possibile registrare la chiave di autenticazione IR nelle macchine virtuali indipendenti a causa del collegamento privato

#### <a name="symptoms"></a>Sintomi

Non è possibile registrare la chiave di autenticazione IR nella macchina virtuale indipendente a causa del collegamento privato abilitato.

Le informazioni sull'errore sono indicate di seguito:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Causa

Il problema potrebbe essere causato dalla macchina virtuale in cui è in corso il tentativo di installazione di. L'accesso alla rete pubblica deve essere abilitato per la connessione al cloud.

#### <a name="resolution"></a>Risoluzione

 **Soluzione 1:** Per risolvere il problema, è possibile attenersi alla procedura seguente:

1. Passare al collegamento seguente: 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. Fare clic sull'opzione **prova** e compilare tutti i dettagli necessari. Incollare anche sotto la proprietà nel **corpo** :

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Fare clic su **Esegui** alla fine della pagina per eseguire la funzione. Assicurarsi di ottenere il codice di risposta 200. La proprietà incollata verrà mostrata anche nella definizione JSON.

1. È quindi possibile provare ad aggiungere di nuovo la chiave di autenticazione IR in Integration Runtime.


**Soluzione 2:** È possibile fare riferimento all'articolo seguente per la soluzione:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Provare ad abilitare l'accesso alla rete pubblica con l'interfaccia utente.

![Abilitare l'accesso alla rete pubblica](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Collegamento privato per Data Factory](data-factory-private-link.md)
*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Pagina delle domande di Domande e risposte Microsoft](/answers/topics/azure-data-factory.html)
*  [Forum di stack overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)