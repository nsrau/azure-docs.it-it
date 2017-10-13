---
title: Considerazioni sulle prestazioni per Gestione traffico di Azure | Documentazione Microsoft
description: Informazioni sulle prestazioni in Gestione traffico e su come testare le prestazioni in un sito Web quando si usa Gestione traffico
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 3ba5dfa1-2922-43f1-9a23-d06969c4a516
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: f686685138625a53971f1fc5fc754fd22c9d67b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="performance-considerations-for-traffic-manager"></a>Considerazioni sulle prestazioni per Gestione traffico

Questa pagina illustrati alcune considerazioni sulle prestazioni di Gestione traffico. Si consideri lo scenario seguente:

Sono presenti istanze del sito Web nelle aree Stati Uniti occidentali e Asia orientale. Una delle istanze non ha superato il controllo di integrità per il probe di Gestione traffico. Il traffico delle applicazioni viene indirizzato all'area integra. Questo failover è previsto, ma si verifica un problema di prestazioni in base alla latenza del traffico che viene ora indirizzato a un'area distante.

## <a name="performance-considerations-for-traffic-manager"></a>Considerazioni sulle prestazioni per Gestione traffico

L'unico impatto sulle prestazioni prodotto da Gestione traffico in un sito Web è la ricerca DNS iniziale. Il server radice DNS Microsoft che ospita la zona trafficmanager.net gestisce una richiesta DNS per il nome del profilo di Gestione traffico. Gestione traffico popola e aggiorna regolarmente i server radice DNS Microsoft in base ai criteri e ai risultati dei probe. Di conseguenza, a Gestione traffico non viene inviata alcuna query DNS nemmeno durante la ricerca DNS iniziale.

Gestione traffico è costituito da diversi componenti: server dei nomi DNS, un servizio API, il livello di archiviazione e un servizio di monitoraggio degli endpoint. Un eventuale errore in uno di questi componenti non ha alcun effetto sul nome DNS associato al profilo di Gestione traffico. I record presenti nel server DNS Microsoft rimangono invariati. Il monitoraggio degli endpoint e l'aggiornamento DNS, tuttavia, non vengono eseguiti. Di conseguenza, in caso di arresto del sito primario, Gestione traffico non è in grado di aggiornare DNS in modo che punti al sito di failover.

La risoluzione dei nomi DNS è rapida e i risultati vengono memorizzati nella cache. La velocità della ricerca DNS iniziale dipende dai server DNS usati dal client per la risoluzione dei nomi. In genere, un client è in grado di eseguire una ricerca DNS in circa 50 ms. I risultati della ricerca vengono memorizzati nella cache per la durata (TTL) del DNS. Per Gestione traffico, la durata (TTL) predefinita è 300 secondi.

Il traffico NON attraversa Gestione traffico. Dopo il completamento della ricerca DNS, il client dispone di un indirizzo IP per un'istanza del sito Web. Il client si connette direttamente a tale indirizzo e non passa attraverso Gestione traffico. I criteri di Gestione traffico scelti non producono alcun effetto sulle prestazioni DNS. Il metodo di routing del traffico Prestazioni, tuttavia, può avere un impatto negativo sull'esperienza dell'applicazione. Se, ad esempio, i criteri reindirizzano il traffico dall'America del Nord a un'istanza ospitata in Asia, la latenza di rete per tali sessioni può essere un problema di prestazioni.

## <a name="measuring-traffic-manager-performance"></a>Misurazione delle prestazioni di Gestione traffico

Sono presenti diversi siti Web che è possibile usare per comprendere le prestazioni e il comportamento di un profilo di Gestione traffico. Molti di questi siti sono gratuiti, ma possono presentare alcune limitazioni. Alcuni siti offrono servizi avanzati di monitoraggio e report a pagamento.

Gli strumenti disponibili in questi siti misurano la latenza DNS e visualizzano gli indirizzi IP risolti per le ubicazioni dei client nelle diverse aree geografiche. La maggior parte di questi strumenti non memorizza i risultati DNS nella cache. Di conseguenza, gli strumenti visualizzano la ricerca DNS completa ogni volta che viene eseguito un test. Quando si esegue un test dal proprio client, nel corso della durata (TTL) la ricerca DNS completa viene eseguita una sola volta.

## <a name="sample-tools-to-measure-dns-performance"></a>Strumenti di esempio per la misurazione delle prestazioni DNS

* [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS offre diversi strumenti per la misurazione delle prestazioni. Lo strumento DNS Comparison visualizza il tempo necessario per la risoluzione del nome DNS e lo confronta con altri provider di servizi DNS.

* [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    Uno degli strumenti più semplici è WebSitePulse. Immettere l'URL per visualizzare statistiche, ad esempio i tempi di risoluzione DNS, i tempi per il primo byte e per l'ultimo byte e altre statistiche sulle prestazioni. È possibile scegliere tra tre posizioni di test. In questo esempio, la prima esecuzione mostra che la ricerca DNS impiega 0,204 secondi.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Poiché i risultati vengono memorizzati nella cache, nel secondo test relativo allo stesso endpoint di Gestione traffico la ricerca DNS impiega 0,002 secondi.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [CA App Synthetic Monitor](https://asm.ca.com/en/checkit.php)

    Precedentemente noto come Watchmouse Check Website, questo sito mostra i tempi di risoluzione DNS da più aree geografiche simultaneamente. Immettere l'URL per visualizzare i tempi di risoluzione DNS, i tempi di connessione e la velocità da diverse aree geografiche. Usare questo test per vedere quale servizio ospitato viene restituito per le diverse aree geografiche.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](http://tools.pingdom.com/)

    Questo strumento offre statistiche sulle prestazioni per ogni elemento di una pagina Web. La scheda Page Analysis (Analisi pagina) mostra la percentuale di tempo impiegato per la ricerca DNS.

* [What's My DNS?](http://www.whatsmydns.net/)

    Questo sito esegue una ricerca DNS da 20 aree geografiche diverse e visualizza i risultati su una mappa.

* [Dig Web Interface](http://www.digwebinterface.com)

    Mostra informazioni più dettagliate sul DNS, inclusi i record A e CNAME. Verificare di selezionare "Colorize output" (Colora output) e "Stats" (Statistiche) in Options (Opzioni) e di selezionare "All" (Tutti) in Nameservers (Server dei nomi).

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui metodi di routing di Gestione traffico](traffic-manager-routing-methods.md)

[Verifica delle impostazioni di Gestione traffico](traffic-manager-testing-settings.md)

[Operazioni per Gestione traffico (informazioni di riferimento API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlet di Gestione traffico di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)

