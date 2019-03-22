---
title: Servizio Frontdoor di Azure - Back-end e pool back-end | Microsoft Docs
description: Questo articolo fornisce informazioni sui back-end e i pool back-end per la configurazione di Frontdoor.
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
ms.openlocfilehash: 228ed5c54a382db7b47d19adacf9e5db398c53ae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123692"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Back-end e pool back-end nel servizio Frontdoor di Azure
Questo articolo illustra i diversi concetti relativi alla modalità di mapping della distribuzione delle applicazioni con Frontdoor. Verrà anche illustrato i diversi termini in primo piano configurazione sportello tutto back-end dell'applicazione significato.

## <a name="backend-pool"></a>Pool back-end
Un pool back-end in Frontdoor indica il set dei back-end equivalenti che possono ricevere lo stesso tipo di traffico per la propria applicazione. In altre parole, è un raggruppamento logico delle istanze dell'applicazione in tutto il mondo che possono ricevere lo stesso traffico e rispondere con il comportamento previsto. In genere, questi back-end vengono distribuiti in diverse aree geografiche o nella stessa area. Inoltre, tutti questi back-end possono essere nella modalità di distribuzione attivo/attivo oppure è possibile definire una configurazione attivo/passivo.

Il pool back-end definisce anche come deve essere valutata l'integrità dei diversi back-end tramite i probe di integrità e di conseguenza come deve avvenire il bilanciamento del carico tra i back-end.

### <a name="health-probes"></a>Probe di integrità
Frontdoor invia periodicamente richieste di probe HTTP/HTTPS a ciascuno dei back-end configurati per determinare la prossimità e l'integrità di ogni back-end per il bilanciamento del carico delle richieste degli utenti finali. Le impostazioni dei probe di integrità per un pool back-end definiscono la modalità con cui viene eseguito il polling dello stato di integrità per i back-end dell'applicazione. Sono disponibili le impostazioni seguenti per la configurazione per il bilanciamento del carico:

1. **Percorso**: Percorso URL cui verranno inviate le richieste di probe a per tutti i back-end nel pool di back-end. Ad esempio, se uno dei back-end è `contoso-westus.azurewebsites.net` e il percorso è impostato su `/probe/test.aspx`, gli ambienti Frontdoor, presupponendo che il protocollo sia impostato su HTTP, invieranno le richieste di probe di integrità a http://contoso-westus.azurewebsites.net/probe/test.aspx. 
2. **Protocollo**: Definisce se le richieste di probe di integrità da porta principale al back-end verranno inviate tramite il protocollo HTTP o HTTPS.
3. **Intervallo (secondi)**: Questo campo definisce la frequenza di probe di integrità per il back-end, vale a dire, gli intervalli in cui ognuno degli ambienti di ingresso principale invierà un probe. Nota: per rendere i failover più veloci, impostare questo campo su un valore inferiore. Tuttavia, più basso è il valore, maggiore sarà il volume dei probe di integrità che riceverà il back-end. Per avere un'idea del volume dei probe generati da Frontdoor nei back-end, esaminiamo un esempio. Supponiamo che l'intervallo sia impostato su 30 secondi e di avere circa 90 ambienti o POP Frontdoor a livello globale. Di conseguenza, ognuno dei back-end riceverà circa 3-5 richieste di probe al secondo.

Per informazioni dettagliate, vedere [Probe di integrità](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Impostazioni di bilanciamento del carico
Le impostazioni di bilanciamento del carico per il pool back-end definiscono come vengono valutati i probe di integrità per determinare se un back-end è integro o meno e anche come occorre bilanciare il traffico tra i diversi back-end nel pool back-end. Sono disponibili le impostazioni seguenti per la configurazione per il bilanciamento del carico:

1. **Le dimensioni del campione**: Questa proprietà identifica il numero di campioni di probe di integrità è necessario prendere in considerazione per la valutazione dell'integrità back-end.
2. **Dimensioni del campione riuscita**: Questa proprietà definisce che delle dimensioni del campione' ' come descritto in precedenza, il numero di campioni è necessario verificare la presenza di successo chiamare il back-end integro. 
</br>Si supponga ad esempio di aver impostato in Frontdoor l'*intervallo* del probe di integrità su 30 secondi, le *dimensioni del campione* su "5" e le *dimensioni del campione con esito positivo* su "3". In base a questa configurazione, ogni volta che valutiamo i probe di integrità per il back-end, esamineremo gli ultimi cinque campioni, che possono riguardare gli ultimi 150 secondi (5*30 secondi), e, a meno che tre o più di questi probe non abbiano avuto esito positivo, considereremo il back-end non integro. Se sono presenti solo due probe con esito positivo, il back-end verrà contrassegnato come non integro. Se alla successiva valutazione vengono rilevati tre probe con esito positivo tra gli ultimi cinque, il back-end verrà nuovamente contrassegnato come integro.
3. **Sensibilità di latenza (latenza aggiuntiva)**: Nel campo sensibilità di latenza definisce se vuoi l'ingresso principale per inviare la richiesta al back-end che siano compresi nell'intervallo tra maiuscole e minuscole in termini di misurazioni di latenza o inoltrare le richieste al back-end più vicino. Per altre informazioni, vedere il [metodo di routing basato sulla latenza minima](front-door-routing-methods.md#latency) per Frontdoor.

## <a name="backend"></a>Back-end
Un back-end è equivalente a un'istanza di distribuzione di un'applicazione in un'area. Poiché Frontdoor supporta sia i back-end di Azure che quelli non di Azure, in questo caso l'area non si limita solo alle aree di Azure, ma può anche essere il data center locale o un'istanza dell'applicazione in un altro cloud.

Per back-end, nel contesto di Frontdoor, si intende il nome host o l'indirizzo IP pubblico dell'applicazione, che può rispondere alle richieste client. Di conseguenza, il back-end non deve essere confuso con il livello database o il livello di archiviazione e così via, ma deve essere piuttosto considerato l'endpoint pubblico del back-end dell'applicazione.

Quando si aggiunge un back-end in un pool back-end di Frontdoor, è necessario specificare i dettagli seguenti:

1. **Tipo di host di back-end**: Il tipo di risorsa da aggiungere. Frontdoor supporta l'individuazione automatica dei back-end dell'applicazione dal servizio app, dal servizio cloud o dall'archiviazione. Per usare un'altra risorsa in Azure o un back-end non Azure, selezionare "Host personalizzato". Nota: durante la configurazione, le API non convalidano se il back-end è accessibile dagli ambienti Frontdoor. È quindi necessario verificare che il back-end sia raggiungibile da Frontdoor. 
2. **Nome host di back-end e sottoscrizione**: Se non è stato selezionato 'Host personalizzato' per l'host di back-end digitare, quindi è necessario definire l'ambito verso il basso e selezionare il back-end, scegliere la sottoscrizione appropriata e il nome host di back-end corrispondente nell'interfaccia utente.
3. **Intestazione host di back-end**: Il valore dell'intestazione Host inviato al back-end per ogni richiesta. Per informazioni dettagliate, vedere [Intestazione host back-end](#hostheader).
4. **Priorità**: è possibile assegnare priorità ai diversi back-end quando si vuole usare un back-end di servizio primario per tutto il traffico e prevedere backup nel caso in cui il back-end primario o i back-end di backup non siano disponibili. Per altre informazioni, vedere [Priorità](front-door-routing-methods.md#priority).
5. **Peso**: è possibile assegnare un peso ai diversi back-end quando si vuole distribuire il traffico in un set di back-end, in modo uniforme o in base ai coefficienti di peso. Per altre informazioni, vedere [Pesi](front-door-routing-methods.md#weighted).


### <a name = "hostheader"></a>Intestazione host back-end

Le richieste inoltrate da Frontdoor a un back-end includono un campo Intestazione host usato dal back-end per recuperare la risorsa di destinazione. Il valore per questo campo in genere deriva dall'URI del back-end e contiene l'host e la porta. Ad esempio, una richiesta effettuata a `www.contoso.com` avrà l'intestazione host `www.contoso.com`. Se si sta configurando il back-end tramite il portale di Azure, il valore predefinito inserito in questo campo è il nome host del back-end. Ad esempio, se è il back-end è `contoso-westus.azurewebsites.net`, nel portale di Azure il valore popolato automaticamente per l'intestazione host del back-end sarà `contoso-westus.azurewebsites.net`. 
</br>Se tuttavia si usano i modelli di Resource Manager o altri meccanismi e non si imposta questo campo in modo esplicito, Frontdoor invia il nome host in entrata come valore dell'intestazione host. Se ad esempio la richiesta è stata effettuata a `www.contoso.com` e il back-end è `contoso-westus.azurewebsites.net` con un campo vuoto per l'intestazione host del back-end, Frontdoor imposterà l'intestazione host su `www.contoso.com`.

Per la maggior parte dei back-end dell'applicazione (ad esempio, App Web, Archiviazione BLOB e Servizi cloud), è necessario che l'intestazione host corrisponda al dominio del back-end. Tuttavia, l'host di front-end che indirizza al back-end avrà un nome host diverso, ad esempio www\.contoso.azurefd.net. Se il back-end che si sta impostando richiede che l'intestazione host corrisponda al nome host del back-end, è necessario assicurarsi che anche in "Intestazione host back-end" sia specificato il nome host del back-end.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Configurazione dell'intestazione host back-end per il back-end
Il campo "Intestazione host back-end" può essere configurato per un back-end nella sezione dei pool back-end.

1. Aprire la risorsa di Frontdoor e fare clic sul pool back-end di cui è necessario configurare il back-end.

2. Aggiungere un back-end, se non ne è stato ancora aggiunto nessuno, o modificarne uno esistente. Il campo "Intestazione host back-end" può essere impostato su un valore personalizzato o lasciato vuoto. In questo caso, come valore dell'intestazione host verrà usato il nome host per la richiesta in ingresso.



## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).