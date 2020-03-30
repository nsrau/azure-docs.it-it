---
title: Log non elaborati HTTP della rete CDN di AzureAzure CDN HTTP raw logs
description: Questo articolo descrive i log non elaborati HTTP DELLA rete CDN di Azure.This article describes the Azure CDN HTTP raw logs.
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: c6e8570746ae3dd0051dbec084c89d90580d28b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371641"
---
# <a name="azure-cdn-http-raw-logs"></a>Log non elaborati HTTP della rete CDN di AzureAzure CDN HTTP raw logs
I log non elaborati forniscono informazioni dettagliate sulle operazioni e sugli errori importanti per il controllo e la risoluzione dei problemi. I log non elaborati differiscono dai log attività. I log attività offrono visibilità sulle operazioni eseguite sulle risorse di Azure.Activity logs provide visibility into the operations done on Azure resources. I log non elaborati forniscono un record delle operazioni della risorsa.

> [!IMPORTANT]
> La funzionalità HTTP dei log non elaborati è disponibile per la rete CDN di Azure di Microsoft.The HTTP raw logs feature is available for Azure CDN from Microsoft.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di [https://portal.azure.com](https://portal.azure.com)Azure all'indirizzo .

## <a name="configuration"></a>Configurazione

Per configurare i log non elaborati per la rete CDN di Azure dal profilo Microsoft:To configure Raw logs for your Azure CDN from Microsoft profile: 

1. Nel menu del portale di Azure selezionare **Tutte le risorse** >> **\<>profilo-CDN. **

2. In **Monitoraggio**selezionare **Impostazioni di diagnostica.**

3. Selezionare **: Aggiungi impostazione diagnostica**.

    ![Impostazione diagnostica della rete CDN](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > I log non elaborati sono disponibili solo a livello di profilo, mentre i log del codice di stato http aggregati sono disponibili a livello di endpoint.

4. In **Impostazioni di diagnostica**immettere un nome per l'impostazione di diagnostica in Nome impostazioni di **diagnostica**.

5. Selezionare il **registro** e impostare la conservazione in giorni.

6. Selezionare i **dettagli della destinazione**. Le opzioni di destinazione sono:
    * **Invia a Log Analytics**
        * Selezionare l'area di lavoro **Sottoscrizione** e **Log Analytics**.
    * **Archivia in un account di archiviazione**
        * Selezionare la **sottoscrizione** e l'account **di archiviazione**.
    * **Streaming in un hub eventi**
        * Selezionare **Sottoscrizione**, **Spazio dei nomi hub eventi**, Nome hub eventi **(facoltativo)** e **Nome criterio hub eventi**.

    ![Impostazione diagnostica della rete CDN](./media/cdn-raw-logs/raw-logs-02.png)

7. Selezionare **Salva**.

## <a name="raw-logs-properties"></a>Proprietà dei registri non elaborati

La rete CDN di Azure dal servizio Microsoft fornisce attualmente log non elaborati. I log non elaborati forniscono singole richieste API con ogni voce con lo schema seguente:Raw logs provide individual API requests with each entry having the following schema: 

| Proprietà              | Descrizione                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference (Riferimento tracking)     | Stringa di riferimento univoca che identifica una richiesta servita da Front Door, inviata anche come intestazione X-Azure-Ref al client. Necessario per la ricerca dei dettagli nei log di accesso per una richiesta specifica. |
| HttpMethod            | Metodo HTTP usato dalla richiesta.                                                                                                                                                                     |
| HttpVersion           | Tipo di richiesta o connessione.                                                                                                                                                                   |
| Requesturi            | URI della richiesta ricevuta.                                                                                                                                                                         |
| Byte richiesta          | Dimensione del messaggio di richiesta HTTP in byte, incluse le intestazioni della richiesta e il corpo della richiesta.                                                                                                   |
| ResponseBytes (Byte risposta)         | Byte inviati dal server back-end come risposta.                                                                                                                                                    |
| UserAgent             | Tipo di browser utilizzato dal client.                                                                                                                                                               |
| ClientIp              | Indirizzo IP del client che ha eseguito la richiesta.                                                                                                                                                  |
| TimeTaken             | Periodo di tempo impiegato dall'azione, in millisecondi.                                                                                                                                            |
| Protocollo Di sicurezzaSecurityProtocol      | La versione del protocollo TLS/SSL utilizzata dalla richiesta o null se non viene utilizzata alcuna crittografia.                                                                                                                           |
| Endpoint              | L'host dell'endpoint della rete CDN è configurato nel profilo della rete CDN padre.                                                                                                                                   |
| Nome host back-end     | Nome dell'host back-end o origine in cui vengono inviate le richieste.                                                                                                                                |
| Inviato allo scudo di origine | Se true, significa che la richiesta ha ricevuto risposta dalla cache dello scudo di origine anziché dal pop perimetrale. Scudo di origine è una cache padre utilizzata per migliorare il rapporto riscontri cache.                                       |
| HttpStatusCode        | Codice di stato HTTP restituito dal proxy.                                                                                                                                                        |
| HttpStatusDetailsHttpStatusDetails (Informazioni in questo sito     | Stato risultante nella richiesta. Significato di questo valore stringa è disponibile in una tabella di riferimento di stato.                                                                                              |
| Pop                   | Il pop edge, che ha risposto alla richiesta dell'utente. Le abbreviazioni dei POP sono codici aeroportuali delle rispettive metropolitane.                                                                                   |
| Stato cache          | Indica se l'oggetto è stato restituito dalla cache o proviene dall'origine.                                                                                                             |
> [!IMPORTANT]
> La funzionalità HTTP Raw logs è disponibile automaticamente per tutti i profili creati o aggiornati dopo **il 25 febbraio 2020.** Per i profili CDN creati in precedenza, è necessario aggiornare l'endpoint della rete CDN dopo aver impostato la registrazione. Ad esempio, è possibile passare al filtro geografico negli endpoint della rete CDN e bloccare qualsiasi paese non rilevante per il carico di lavoro e il salvataggio. 

> [!NOTE]
> I log possono essere visualizzati nel profilo di Log Analytics eseguendo una query. Una query di esempio sarebbe simile a AzureDiagnostics . dove Categoria : "AzureCdnAccessLog"

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati abilitati i log HTTP non elaborati per il servizio CDN Microsoft.In this article, you enabled HTTP raw logs for the Microsoft CDN service.

Per altre informazioni sulla rete CDN di Azure e sugli altri servizi di Azure menzionati in questo articolo, vedere:For more information on Azure CDN and the other Azure services mentioned in this article, see:

* [Analizza](cdn-log-analysis.md) Modelli di utilizzo della rete CDN di Azure.Azure CDN usage patterns.

* Altre informazioni su Monitoraggio di Azure .Learn [.Learn more](https://docs.microsoft.com/azure/azure-monitor/overview)about Azure Monitor .

* Configurare [Log Analytics in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
