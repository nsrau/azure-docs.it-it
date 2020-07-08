---
title: Log non elaborati HTTP della rete CDN di Azure
description: Questo articolo descrive i log non elaborati HTTP della rete CDN di Azure.
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: a2522eba17574246ab99a0d47a42f128d5f61ace
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84888646"
---
# <a name="azure-cdn-http-raw-logs"></a>Log non elaborati HTTP della rete CDN di Azure
I log non elaborati offrono informazioni dettagliate sulle operazioni e sugli errori importanti per il controllo e la risoluzione dei problemi. I log non elaborati differiscono dai log attività. I log attività offrono visibilità sulle operazioni eseguite sulle risorse di Azure. I log non elaborati forniscono un record delle operazioni della risorsa.

> [!IMPORTANT]
> La funzionalità log non elaborati HTTP è disponibile per la rete CDN di Azure da Microsoft.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration"></a>Configurazione

Per configurare i log non elaborati per la rete CDN di Azure dal Microsoft: 

1. Scegliere **tutte le risorse**dal menu portale di Azure  >>  **\<your-CDN-profile>** .

2. In **Monitoraggio** selezionare **Impostazioni di diagnostica**.

3. Fare clic su **+ Aggiungi impostazione di diagnostica**.

    ![Impostazione di diagnostica della rete CDN](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > I log non elaborati sono disponibili solo a livello di profilo mentre i log del codice di stato HTTP aggregati sono disponibili a livello di endpoint.

4. In **Impostazioni di diagnostica** immettere un nome per l'impostazione di diagnostica in **Diagnostic settings name** (Nome impostazioni di diagnostica).

5. Selezionare il **log** e impostare i giorni di conservazione.

6. Selezionare i **Dettagli destinazione**. Le opzioni di destinazione sono:
    * **Invia a Log Analytics**
        * Selezionare la **sottoscrizione** e l'**area di lavoro Log Analytics**.
    * **Archivia in un account di archiviazione**
        * Selezionare la **sottoscrizione** e l'**account di archiviazione**.
    * **Streaming in un hub eventi**
        * Selezionare la **sottoscrizione**, lo **spazio dei nomi dell'hub eventi**, il **nome dell'hub eventi (facoltativo)** e il **nome criterio hub eventi**.

    ![Impostazione di diagnostica della rete CDN](./media/cdn-raw-logs/raw-logs-02.png)

7. Selezionare **Salva**.

## <a name="raw-logs-properties"></a>Proprietà dei log non elaborati

La rete CDN di Azure di Servizi Microsoft attualmente fornisce log non elaborati. I log non elaborati forniscono richieste API singole le cui voci hanno lo schema seguente: 

| Proprietà              | Descrizione                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | Stringa di riferimento univoca che identifica una richiesta fornita da Frontdoor, inviata anche come intestazione X-Azure-Ref al client. Obbligatoria per la ricerca di dettagli nei log di accesso per una richiesta specifica. |
| HttpMethod            | Metodo HTTP usato dalla richiesta.                                                                                                                                                                     |
| HttpVersion           | Tipo della richiesta o della connessione.                                                                                                                                                                   |
| RequestUri            | URI della richiesta ricevuta.                                                                                                                                                                         |
| RequestBytes          | Dimensioni del messaggio di richiesta HTTP in byte, inclusi intestazioni e corpo della richiesta.                                                                                                   |
| ResponseBytes         | Byte inviati dal server back-end come risposta.                                                                                                                                                    |
| UserAgent             | Tipo di browser usato dal client.                                                                                                                                                               |
| ClientIp              | Indirizzo IP del client che ha eseguito la richiesta.                                                                                                                                                  |
| TimeTaken             | Durata dell'azione, in millisecondi.                                                                                                                                            |
| SecurityProtocol      | Versione del protocollo TLS/SSL usata dalla richiesta o Null se non è stato usato alcun tipo di crittografia.                                                                                                                           |
| Endpoint              | Endpoint della rete CDN configurato dall'host nel profilo della rete CDN padre.                                                                                                                                   |
| Nome host back-end     | Nome dell'host o dell'origine back-end in cui vengono inviate le richieste.                                                                                                                                |
| Sent to origin shield (Inviato a shield di origine) | Se true, la risposta alla richiesta proviene dalla cache dello shield di origine anziché dal POP perimetrale. Lo shield di origine è una cache padre usata per migliorare la percentuale di riscontri nella cache.                                       |
| HttpStatusCode        | Codice di stato HTTP restituito dal proxy.                                                                                                                                                        |
| HttpStatusDetails     | Stato risultante nella richiesta. Il significato di questo valore stringa è disponibile in una tabella di riferimento sullo stato.                                                                                              |
| POP                   | POP perimetrale che ha risposto alla richiesta utente. Le abbreviazioni dei POP sono codici aeroportuali delle rispettive Metro.                                                                                   |
| Stato della cache          | Indica se l'oggetto è stato restituito dalla cache o proviene dall'origine.                                                                                                             |
> [!IMPORTANT]
> La funzionalità dei log non elaborati HTTP è disponibile automaticamente per tutti i profili creati o aggiornati dopo il **25 febbraio 2020**. Per i profili della rete CDN creati in precedenza, è necessario aggiornare l'endpoint della rete CDN dopo aver configurato la registrazione. È ad esempio possibile passare al filtro geografico negli endpoint della rete CDN e bloccare i paesi/le aree geografiche non pertinenti per il carico di lavoro e quindi fare clic su Salva. 

> [!NOTE]
> I log possono essere visualizzati nel profilo di Log Analytics tramite una query. Una query di esempio può essere              AzureDiagnostics | where Category == "AzureCdnAccessLog"

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati abilitati i log non elaborati HTTP per il servizio rete CDN Microsoft.

Per altre informazioni sulla rete CDN di Azure e sugli altri servizi di Azure menzionati in questo articolo, vedere:

* [Analizzare](cdn-log-analysis.md) i modelli di utilizzo della rete CDN di Azure.

* Altre informazioni su [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview).

* Configurare [Log Analytics in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
