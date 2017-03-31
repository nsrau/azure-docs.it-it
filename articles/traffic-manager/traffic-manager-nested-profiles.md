---
title: Profili annidati di Gestione Traffico | Microsoft Docs
description: "Questo articolo descrive la funzionalità &quot;Profili nidificati&quot; di Gestione traffico di Azure"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f1b112c4-a3b1-496e-90eb-41e235a49609
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: e274d10b59c6f198962974fda0a804f4d993c324
ms.lasthandoff: 03/22/2017

---

# <a name="nested-traffic-manager-profiles"></a>Profili nidificati di Gestione traffico

Gestione traffico include diversi metodi di routing del traffico, che consentono di controllare la modalità con cui Gestione traffico sceglie quale endpoint deve ricevere traffico da ogni utente finale. Per altre informazioni, vedere [Metodi di routing del traffico di Gestione traffico](traffic-manager-routing-methods.md).

Ciascun profilo di Gestione traffico specifica un solo metodo di routing del traffico. Esistono scenari che tuttavia richiedono un sistema di routing del traffico più avanzato anziché il routing fornito da un singolo profilo di Gestione traffico. È possibile annidare i profili di Gestione traffico per combinare i vantaggi offerti da più metodi di routing del traffico. I profili annidati consentono di ignorare il comportamento predefinito di Gestione traffico per supportare distribuzioni di applicazioni di dimensioni maggiori e più complesse.

Gli esempi seguenti illustrano come usare i profili di Gestione traffico annidati in vari scenari.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Esempio 1: Combinazione di metodi di routing del traffico "Prestazioni" e "Ponderato"

Si supponga che l'applicazione sia stata distribuita in più aree di Azure: Stati Uniti occidentali, Europa occidentale e Asia orientale. Si userà il metodo di routing del traffico "Prestazioni" di Gestione traffico per distribuire il traffico nell'area più vicina all'utente.

![Profilo singolo di Gestione traffico][4]

Si supponga ora di voler valutare un aggiornamento del servizio prima di distribuirlo in modo più ampio. A tale scopo si vuole usare il metodo di routing del traffico "Ponderato", che può indirizzare una piccola percentuale di traffico alla distribuzione di prova. Impostare la distribuzione di prova insieme alla distribuzione di produzione in Europa occidentale.

Con un singolo profilo non è possibile combinare i metodi di routing del traffico "Ponderato" e "Prestazioni". Per supportare questo scenario, creare un profilo di Gestione traffico usando i due endpoint dell'Europa occidentale e il metodo di routing del traffico "Ponderato". Aggiungere quindi questo profilo figlio come endpoint del profilo padre, che continua a usare il metodo di routing del traffico "Prestazioni" e contiene le altre distribuzioni globali come endpoint.

Il diagramma seguente illustra questo esempio:

![Profili nidificati di Gestione traffico][2]

In questa configurazione, il traffico indirizzato tramite il profilo padre verrà distribuito in più aree, come di consueto. In Europa occidentale il traffico verrà distribuito dal profilo annidato alla produzione e alle distribuzioni di prova in base ai pesi assegnati.

Quando il profilo padre usa il metodo di routing del traffico "Prestazioni", a ogni endpoint deve essere assegnata una posizione. Questa posizione deve essere assegnata al momento della configurazione dell'endpoint. Scegliere l'area di Azure più vicina alla distribuzione specifica. Le opzioni disponibili sono le aree di Azure, ovvero i valori delle posizioni supportati dalla tabella della latenza di Internet. Per altre informazioni, vedere [Metodo di routing del traffico Prestazioni](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Esempio 2: Monitoraggio degli endpoint nei profili nidificati

Gestione traffico esegue continuamente il monitoraggio dell'integrità di ogni endpoint di servizio. Se un endpoint non è integro, Gestione traffico indirizza gli utenti verso endpoint alternativi, mantenendo così la disponibilità del servizio. Il comportamento di monitoraggio e failover degli endpoint si applica a tutti i metodi di routing del traffico. Per altre informazioni, vedere [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md). Il monitoraggio degli endpoint è differente nei profili annidati. Ad esempio, nei profili annidati il profilo padre non esegue i controlli di integrità direttamente sul profilo figlio. L'integrità degli endpoint del profilo figlio viene usata invece per calcolare l'integrità complessiva del profilo figlio e queste informazioni vengono propagate alla gerarchia dei profili annidati. Il profilo padre usa quindi lo stato aggregato per determinare se indirizzare il traffico al profilo figlio. Vedere le [domande frequenti](traffic-manager-FAQs.md#traffic-manager-nested-profiles) per informazioni dettagliate sul monitoraggio dello stato di profili annidati.

Tornando all'esempio precedente, si supponga che la distribuzione di produzione in Europa occidentale abbia esito negativo. Per impostazione predefinita il profilo figlio indirizza tutto il traffico alla distribuzione di prova. Se anche questa operazione ha esito negativo, il profilo padre stabilirà che, poiché tutti gli endpoint figlio sono danneggiati, il profilo figlio non deve ricevere traffico, e provvederà quindi a distribuire il traffico ad altre aree.

![Failover dei profili nidificati (comportamento predefinito)][3]

È possibile che questa soluzione risulti soddisfacente, o che si tema che tutto il traffico dell'Europa occidentale venga indirizzato alla distribuzione di prova invece che a un sottoinsieme limitato. Indipendentemente dall'integrità della distribuzione di prova, si preferisce eseguire il failover ad altre aree se la distribuzione di produzione in Europa occidentale ha esito negativo. Per abilitare questo failover, durante la configurazione del profilo figlio come endpoint nel profilo padre è possibile specificare il parametro 'MinChildEndpoints', che determina il numero minimo di endpoint che devono essere disponibili nel profilo figlio, il cui valore predefinito è 1. In questo scenario, impostare il valore MinChildEndpoints su 2. Al di sotto di questa soglia, il profilo padre considererà l'intero profilo figlio non disponibile e indirizzerà il traffico agli altri endpoint.

L'immagine seguente illustra questa configurazione:

![Failover dei profili annidati con 'MinChildEndpoints' = 2][4]

> [!NOTE]
> Il metodo di routing del traffico "Priorità" distribuisce tutto il traffico a un unico endpoint. In questo caso è quindi inutile impostare MinChildEndpoints su un valore diverso da 1 per il profilo figlio.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Esempio 3: Aree di failover con priorità con metodo di routing del traffico "Prestazioni"

Il comportamento predefinito per il metodo di routing del traffico "Prestazioni" è progettato per evitare il sovraccarico dell'endpoint successivo più vicino e una conseguente serie di errori a catena. Se un endpoint ha esito negativo, tutto il traffico normalmente indirizzato a tale endpoint viene invece distribuito in modo uniforme tra gli altri endpoint in tutte le aree.

![Routing del traffico "Prestazioni" con failover predefinito][5]

Si supponga tuttavia di preferire che il failover del traffico dell'Europa occidentale venga eseguito negli Stati Uniti occidentali e che il traffico venga indirizzato in altre aree solo se entrambi gli endpoint non sono disponibili. A tale scopo,creare un profilo figlio che usa il metodo di routing del traffico "Priorità".

![Routing del traffico "Prestazioni" con failover preferenziale][6]

Poiché l'endpoint dell'Europa occidentale ha priorità più elevata rispetto all'endpoint degli Stati Uniti occidentali, tutto il traffico viene inviato all'endpoint dell'Europa occidentale quando entrambi gli endpoint sono online. Se l'Europa occidentale ha esito negativo, il relativo traffico viene indirizzato agli Stati Uniti occidentali. Con il profilo annidato, il traffico viene indirizzato all'Asia orientale solo quando sia l'Europa occidentale sia gli Stati Uniti occidentali non sono disponibili.

È possibile ripetere questo modello per tutte le aree, sostituendo i tre endpoint nel profilo padre con tre profili figlio, ognuno con una sequenza di failover con priorità.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Esempio 4: Controllo del metodo di routing del traffico "Prestazioni" tra più endpoint nella stessa area

Si supponga che il metodo di routing del traffico "Prestazioni" venga usato in un profilo con più di un endpoint in una determinata area. Per impostazione predefinita il traffico indirizzato a tale area viene distribuito in modo uniforme tra tutti gli endpoint disponibili nell'area.

![Routing del traffico "Prestazioni" con distribuzione del traffico nell'area (comportamento predefinito)][7]

Anziché aggiungere più endpoint in Europa occidentale, gli endpoint possono essere inclusi in un profilo figlio separato e il profilo figlio può essere aggiunto all'elemento padre come unico endpoint in Europa occidentale. Le impostazioni del profilo figlio possono quindi essere usate per controllare la distribuzione del traffico in Europa occidentale, abilitando il routing del traffico basato sulla priorità o sul peso all'interno di tale area.

![Routing del traffico "Prestazioni" con distribuzione personalizzata del traffico nell'area][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Esempio 5: Impostazioni di monitoraggio per ogni endpoint

Si supponga di usare Gestione traffico per facilitare la migrazione di traffico da un sito Web legacy locale verso una nuova versione basata su cloud ospitata in Azure. Per il sito legacy si vuole usare l'URI della homepage per monitorare l'integrità del sito, ma per la nuova versione basata su cloud si vuole implementare una pagina di monitoraggio personalizzata (percorso "/monitor.aspx") che include controlli aggiuntivi.

![Monitoraggio degli endpoint di Gestione traffico (comportamento predefinito)][9]

Le impostazioni di monitoraggio in un profilo di Gestione traffico si applicano a tutti gli endpoint all'interno del profilo. Per definire impostazioni di monitoraggio diverse nei profili annidati si usa invece un profilo figlio diverso per ogni sito.

![Monitoraggio degli endpoint di Gestione traffico con impostazioni per ogni endpoint][10]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [profili di Gestione traffico](traffic-manager-overview.md)

Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png

