---
title: I pool back-end e back-end nel servizio di ingresso principale di Azure | Microsoft Docs
description: Questo articolo descrive i pool di back-end e back-end sono in primo piano lo sportello della configurazione.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 543e237a4a8390a8ebf74d0eb2a1f4be41dcd911
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60193714"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Back-end e pool back-end nel servizio Frontdoor di Azure
Questo articolo descrive i concetti su come eseguire il mapping di distribuzione della tua app con il servizio di ingresso principale di Azure. Vengono inoltre illustrati i diversi termini in configurazione di ingresso principale tutto back-end dell'app.

## <a name="backends"></a>Back-end
Un back-end è uguale all'istanza di distribuzione di un'app in un'area. Servizio di ingresso principale supporta sia Azure sia back-end non Azure, in modo che l'area non è limitato solo alle aree di Azure. Inoltre, può essere il tuo Data Center locale o un'istanza dell'app in un altro cloud.

Back-end del servizio di ingresso principale fanno riferimento al nome host o indirizzo IP pubblico dell'app, che possono rispondere alle richieste client. Back-end non deve essere confusa con il livello di database, livello di archiviazione e così via. Back-end deve essere considerato come l'endpoint pubblico dei back-end dell'app. Quando si aggiunge un back-end in un pool di back-end di ingresso principale, è necessario anche aggiungere quanto segue:

- **Tipo di host di back-end**. Il tipo di risorsa da aggiungere. Il servizio di ingresso principale supporta il rilevamento automatico dei back-end di app dal servizio app, servizio cloud o di archiviazione. Se si desidera un'altra risorsa in Azure o anche un back-end non Azure, selezionare **Custom host**.

    >[!IMPORTANT]
    >Durante la configurazione, le API non convalidano se il back-end non è accessibile da ambienti di ingresso principale. Assicurarsi che l'ingresso principale possa raggiungere il back-end.

- **Nome host di back-end e sottoscrizione**. Se non è stato selezionato **Custom host** per tipo di host di back-end, selezionare il back-end, scegliere la sottoscrizione appropriata e il nome host di back-end corrispondente nell'interfaccia utente.

- **Intestazione host di back-end**. Il valore dell'intestazione host inviato al back-end per ogni richiesta. Per altre informazioni, vedere [intestazione host di back-end](#hostheader).

- **Priorità**. Assegnare le priorità per il back-end diverso quando si desidera usare un back-end di servizio primario per tutto il traffico. Specificare anche i backup se il database primario o back-end di backup non sono disponibili. Per altre informazioni, vedere [priorità](front-door-routing-methods.md#priority).

- **Peso**. Assegnare pesi per il back-end diversi per distribuire il traffico in un set di back-end, in modo uniforme o in base ai coefficienti peso. Per altre informazioni, vedere [pesi](front-door-routing-methods.md#weighted).

### <a name = "hostheader"></a>Intestazione host back-end

Le richieste inoltrate da porta principale per un back-end includono un campo di intestazione host che usa il back-end per recuperare la risorsa di destinazione. Il valore per questo campo in genere deriva dall'URI del back-end e contiene l'host e la porta.

Ad esempio, una richiesta effettuata per www\.contoso.com avrà www l'intestazione host\.contoso.com. Se si usa il portale di Azure per configurare il back-end, il valore predefinito per questo campo è il nome host del back-end. Se il back-end è contoso-westus.azurewebsites.net, nel portale di Azure, il valore compilato automaticamente per l'intestazione host di back-end sarà westus.azurewebsites.net di contoso. Tuttavia, se si usa modelli di Azure Resource Manager o un altro metodo senza impostare in modo esplicito questo campo, servizio di ingresso principale invierà il nome host in entrata come valore per l'intestazione host. Se la richiesta è stata effettuata per www\.contoso.com e il back-end è contoso-westus.azurewebsites.net che dispone di un campo di intestazione vuota, il servizio di ingresso principale imposterà l'intestazione host come www\.contoso.com.

La maggior parte dei back-end dell'app (app Web di Azure, archiviazione Blob e i servizi Cloud) richiedono l'intestazione host corrisponda al dominio di back-end. Tuttavia, l'host di front-end che indirizza al back-end utilizzerà un nome host diverso, ad esempio www\.contoso.azurefd.net.

Se il back-end richiede l'intestazione host corrisponda al nome host di back-end, assicurarsi che l'intestazione host di back-end include il nome di host back-end.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Configurazione dell'intestazione host back-end per il back-end

Per configurare il **intestazione host di back-end** field per un back-end nella sezione pool back-end:

1. Aprire la risorsa di ingresso principale e selezionare il pool back-end con il back-end alla configurazione.

2. Aggiungere un back-end se è ancora fatto o modificarne uno esistente.

3. Impostare l'host di back-end di campo di intestazione su un valore personalizzato o lasciarla vuota. Il nome host per la richiesta in ingresso verrà utilizzato come valore dell'intestazione host.

## <a name="backend-pools"></a>Pool back-end
Un pool back-end in primo piano sportello servizio indica il set dei back-end che ricevono traffico simili per le app. In altre parole, è un raggruppamento logico delle istanze delle app in tutto il mondo che ricevono il traffico stesso e rispondere con il comportamento previsto. Questi back-end vengono distribuiti in diverse aree geografiche o nella stessa area. Tutti i back-end può essere in modalità di distribuzione attiva/attiva o in quello definito nella configurazione attivo/passivo.

Un pool back-end definisce come back-end diversi devono essere valutati tramite i probe di integrità. Definisce anche come bilanciamento del carico si verifica tra di essi.

### <a name="health-probes"></a>Probe di integrità
Servizio di ingresso principale invia richieste periodiche di probe HTTP/HTTPS a ciascuno dei back-end configurato. Le richieste di probe determinano la prossimità e l'integrità di ogni back-end per caricare bilanciare le richieste degli utenti finali. Le impostazioni di probe di integrità per un pool back-end definiscono come si esegue il polling di stato di integrità del back-end dell'app. Le impostazioni seguenti sono disponibili per la configurazione di bilanciamento del carico:

- **Percorso**. L'URL usato per le richieste di probe per tutti i back-end nel pool di back-end. Ad esempio, se uno dei back-end è contoso-westus.azurewebsites.net e il percorso è impostato su /probe/test.aspx, quindi gli ambienti del servizio di ingresso principale, presupponendo che il protocollo è impostato su HTTP, invierà le richieste di probe di integrità HTTP\:/ / contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protocollo**. Definisce se si desidera inviare le richieste di probe di integrità dal servizio di ingresso principale per il back-end con il protocollo HTTP o HTTPS.

- **Intervallo (secondi)** . Definisce la frequenza di probe di integrità back-end o gli intervalli in cui ognuno degli ambienti di ingresso principale invia un probe.

    >[!NOTE]
    >Per garantire failover più veloci, impostare l'intervallo su un valore inferiore. Più basso è il valore, maggiore sarà il volume di probe di integrità back-end riceve. Ad esempio, se l'intervallo è impostato su 30 secondi con 90 gli ambienti di ingresso principale o server POP a livello globale, ogni back-end verrà inviato in merito dal 3 al 5 richieste di probe al secondo.

Per altre informazioni, vedere [probe di integrità](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Impostazioni di bilanciamento del carico
Le impostazioni di bilanciamento del carico per il pool back-end definiscono la modalità Valutiamo i probe di integrità. Queste impostazioni determinano se il back-end è integro o meno. È anche possibile verificare come il traffico di bilanciamento del carico tra diversi back-end nel pool di back-end. Le impostazioni seguenti sono disponibili per la configurazione di bilanciamento del carico:

- **Le dimensioni del campione**. Identifica il numero di campioni di probe di integrità è necessario prendere in considerazione per la valutazione dell'integrità back-end.

- **Dimensioni del campione riuscita**. Definisce le dimensioni del campione come indicata in precedenza, il numero di campioni riuscite necessario per chiamare il back-end integro. Si supponga, ad esempio, un intervallo di probe di integrità porta d'ingresso è 30 secondi, dimensioni del campione sono 5 e dimensioni del campione riuscito sono 3. Individua tramite probe ogni volta che valuta l'integrità back-end, prendiamo in esame ultimi cinque campioni più di 150 secondi (5 x 30). Almeno tre probe riusciti necessario dichiarare il back-end integro.

- **Sensibilità di latenza (latenza aggiuntiva)** . Definisce se vuoi l'ingresso principale per inviare la richiesta al back-end all'interno dell'intervallo di sensibilità misurazioni di latenza o inoltrare la richiesta al back-end più vicino.

Per altre informazioni, vedere [metodo di routing basato su latenza minima](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Passaggi successivi

- [Creare un profilo di porta principale](quickstart-create-front-door.md)
- [Funzionamento di ingresso principale](front-door-routing-architecture.md)