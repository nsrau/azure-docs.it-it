---
title: Backend e pool back-end in Azure Front Door Documenti Microsoft
description: In questo articolo vengono descritti i back-end e i pool back-end nella configurazione Front Door.
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
ms.openlocfilehash: 18b165d83bfa154348842542bd8323a40330aa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293459"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Backend e pool back-end in Azure Front Door
Questo articolo descrive i concetti relativi al mapping della distribuzione dell'app con Azure Front Door.This article describes concepts about how to map your app deployment with Azure Front Door. Vengono inoltre illustrati i diversi termini della configurazione di Front Door relativi ai backend delle app.

## <a name="backends"></a>Back-end
Un back-end è uguale all'istanza di distribuzione di un'app in un'area. Front Door supporta sia i back-end di Azure che quelli non Azure, pertanto l'area non è limitata solo alle aree di Azure.Front Door supports both Azure and non-Azure backends, so the region isn't only restricted to Azure regions. Inoltre, può essere il data center locale o un'istanza dell'app in un altro cloud.

I back-end della porta anteriore fanno riferimento al nome host o all'indirizzo IP pubblico dell'app, che può soddisfare le richieste dei client. I back-end non devono essere confusi con il livello di database, il livello di archiviazione e così via. I back-end devono essere visualizzati come endpoint pubblico del back-end dell'app. Quando si aggiunge un back-end in un pool back-end Front Door, è necessario aggiungere anche quanto segue:

- **Tipo di host back-end**. Tipo di risorsa che si desidera aggiungere. Front Door supporta l'individuazione automatica dei back-end delle app dal servizio app, dal servizio cloud o dall'archiviazione. Se si vuole una risorsa diversa in Azure o anche un back-end non Azure, selezionare **Host personalizzato**.

    >[!IMPORTANT]
    >Durante la configurazione, le API non vengono convalidate se il back-end non è accessibile dagli ambienti Front Door. Assicurati che Front Door possa raggiungere il tuo back-end.

- **Sottoscrizione e nome host back-end**. Se non è stato selezionato **Host personalizzato** per il tipo di host back-end, selezionare il back-end scegliendo la sottoscrizione appropriata e il nome host back-end corrispondente nell'interfaccia utente.

- **Intestazione host back-end**. Valore dell'intestazione host inviato al back-end per ogni richiesta. Per ulteriori informazioni, vedere [Intestazione host back-end](#hostheader).

- **Priorità**. Assegnare priorità ai diversi back-end quando si desidera utilizzare un back-end del servizio primario per tutto il traffico. Inoltre, fornire i backup se il back-end primario o il backup non sono disponibili. Per ulteriori informazioni, vedere [Priorità](front-door-routing-methods.md#priority).

- **Peso**. Assegna pesi ai diversi back-end per distribuire il traffico attraverso un set di backend, in modo uniforme o in base ai coefficienti di peso. Per ulteriori informazioni, vedere [Pesi](front-door-routing-methods.md#weighted).

### <a name="backend-host-header"></a><a name = "hostheader"></a>Intestazione host back-end

Le richieste inoltrate da Front Door a un back-end includono un campo di intestazione host utilizzato dal back-end per recuperare la risorsa di destinazione. Il valore per questo campo in genere deriva dall'URI del back-end e contiene l'host e la porta.

Ad esempio, una `www.contoso.com` richiesta effettuata per avrà l'intestazione host www.contoso.com. Se si usa il portale di Azure per configurare il back-end, il valore predefinito per questo campo è il nome host del back-end. Se il back-end è contoso-westus.azurewebsites.net, nel portale di Azure il valore popolato automaticamente per l'intestazione host back-end verrà contoso-westus.azurewebsites.net. Tuttavia, se si usano modelli di Azure Resource Manager o un altro metodo senza impostare in modo esplicito questo campo, Front Door invierà il nome host in ingresso come valore per l'intestazione host. Se la richiesta è\.stata effettuata per www contoso.com e il back-end è contoso-westus.azurewebsites.net\.con un campo di intestazione vuoto, Front Door imposterà l'intestazione host come www contoso.com.

La maggior parte dei back-end delle app (App Web di Azure, archiviazione BLOB e servizi cloud) richiedono che l'intestazione host corrisponda al dominio del back-end. Tuttavia, l'host front-end che esegue il trasporto al back-end utilizzerà un nome host diverso, ad esempio www.contoso.net.

Se il back-end richiede che l'intestazione host corrisponda al nome host del back-end, assicurarsi che l'intestazione dell'host back-end includa il nome host.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Configurazione dell'intestazione host back-end per il back-end

Per configurare il campo **dell'intestazione host back-end** per un back-end nella sezione del pool back-end:

1. Aprire la risorsa Front Door e selezionare il pool back-end con il back-end da configurare.

2. Aggiungere un back-end se non è stato fatto o modificarne uno esistente.

3. Impostare il campo di intestazione host back-end su un valore personalizzato o lasciarlo vuoto. Il nome host per la richiesta in ingresso verrà utilizzato come valore dell'intestazione host.

## <a name="backend-pools"></a>Pool back-end
Un pool back-end in Front Door si riferisce al set di back-end che ricevono traffico simile per la propria app. In altre parole, si tratta di un raggruppamento logico delle istanze dell'app in tutto il mondo che ricevono lo stesso traffico e rispondono con il comportamento previsto. Questi back-end vengono distribuiti in aree diverse o all'interno della stessa area. Tutti i back-end possono essere in modalità di distribuzione Attivo/Attivo o la configurazione di Active/Passive.

Un pool back-end definisce come i diversi back-end devono essere valutati tramite probe di integrità. Definisce inoltre la modalità di bilanciamento del carico tra di essi.

### <a name="health-probes"></a>Probe di integrità
Front Door invia richieste di probe HTTP/HTTPS periodiche a ciascuno dei back-end configurati. Le richieste probe determinano la prossimità e l'integrità di ogni back-end per bilanciare il carico delle richieste dell'utente finale. Le impostazioni del probe di integrità per un pool back-end definiscono la modalità di polling dello stato di integrità dei back-end dell'app. Per la configurazione di bilanciamento del carico sono disponibili le impostazioni seguenti:The following settings are available for load-balancing configuration:

- **Percorso:** l'URL utilizzato per le richieste probe per tutti i back-end nel pool back-end. Ad esempio, se uno dei back-end è contoso-westus.azurewebsites.net e il percorso è impostato su /probe/test.aspx, gli ambienti Front\:Door, presupponendo che il protocollo sia impostato su HTTP, invieranno richieste di probe di integrità a http /contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protocollo**: Definisce se inviare le richieste del probe di integrità da Front Door ai back-end con protocollo HTTP o HTTPS.

- **Metodo**: il metodo HTTP da utilizzare per l'invio di probe di integrità. Le opzioni includono GET o HEAD (impostazione predefinita).
    > [!NOTE]
    > Per ridurre il carico e i costi sui back-end, Front Door consiglia di utilizzare le richieste HEAD per le sonde di integrità.

- **Intervallo (secondi)**: Definisce la frequenza dei probe di integrità ai back-end o gli intervalli in cui ciascuno degli ambienti della porta frontale invia un probe.

    >[!NOTE]
    >Per failover più veloci, impostare l'intervallo su un valore inferiore. Minore è il valore, maggiore è il volume del probe di integrità ricevuto dai back-end. Ad esempio, se l'intervallo è impostato su 30 secondi con dire, 100 POP Front Door a livello globale, ogni back-end riceverà circa 200 richieste probe al minuto.

Per ulteriori informazioni, vedere [Probe di integrità](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Impostazioni di bilanciamento del carico
Le impostazioni di bilanciamento del carico per il pool back-end definiscono la modalità di valutazione dei probe di integrità. Queste impostazioni determinano se il back-end è integro o non integro. Controllano inoltre come bilanciare il carico del traffico tra back-end diversi nel pool back-end. Per la configurazione di bilanciamento del carico sono disponibili le impostazioni seguenti:The following settings are available for load-balancing configuration:

- **Dimensioni del campione**. Identifica il numero di campioni di probe di integrità da considerare per la valutazione dell'integrità back-end.

- **Dimensioni del campione riuscite**. Definisce la dimensione del campione come indicato in precedenza, il numero di campioni riusciti necessari per chiamare integro il back-end. Si supponga, ad esempio, che l'intervallo di probe di integrità della porta anteriore sia 30 secondi, che la dimensione del campione sia 5 e che la dimensione del campione sia 3. Ogni volta che valutiamo le sonde di integrità per il tuo back-end, osserviamo gli ultimi cinque campioni in 150 secondi (5 x 30). Sono necessari almeno tre probe riusciti per dichiarare il back-end come integro.

- **Sensibilità della latenza (latenza aggiuntiva)**. Definisce se si desidera che Front Door invii la richiesta ai back-end all'interno dell'intervallo di sensibilità di misurazione della latenza o inoltri la richiesta al back-end più vicino.

Per ulteriori informazioni, vedere Metodo di [routing basato sulla latenza](front-door-routing-methods.md#latency)minima .

## <a name="next-steps"></a>Passaggi successivi

- [Creare un profilo Frontdoor](quickstart-create-front-door.md)
- [Come funziona Porta anteriore](front-door-routing-architecture.md)