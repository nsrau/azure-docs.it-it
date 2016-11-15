---
title: Gestione endpoint in Gestione traffico di Azure | Documentazione Microsoft
description: "Questo articolo aiuterà ad aggiungere, rimuovere, abilitare e disabilitare gli endpoint da Gestione traffico di Azure."
services: traffic-manager
documentationcenter: 
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: ade2bbc2-35a7-43c5-8001-4698f7254526
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e6af6652d39fad5812c15e19fa29c757595a78b6

---

# <a name="add-disable-enable-or-delete-endpoints"></a>Aggiungere, disabilitare, abilitare o eliminare gli endpoint

La funzionalità App Web nell’Azure App Service offre già funzionalità di routing del traffico failover e round robin per i siti Web che si trovano all'interno di un data center, indipendentemente dalla modalità del sito Web. Gestione traffico di Azure consente di specificare il routing del traffico failover e round robin per i siti Web e i servizi cloud che si trovano in data center diversi. Il primo passaggio necessario a fornire tale funzionalità consiste nell'aggiungere l'endpoint del servizio cloud o del sito Web a Gestione traffico.

> [!NOTE]
> Questo articolo illustra come usare il portale classico. Il portale di Azure classico supporta solo la creazione e l'assegnazione di servizi cloud e app Web come endpoint. L'interfaccia consigliata è il nuovo [portale di Azure](https://portal.azure.com).

È anche possibile disabilitare singoli endpoint che appartengono a un profilo di Gestione traffico. Quando si disabilita un endpoint questo continua a far parte del profilo, sebbene quest'ultimo si comporti come se non fosse incluso. Questa azione è utile per rimuovere temporaneamente un endpoint che si trova in modalità di manutenzione o è in corso di ridistribuzione. Quando l'endpoint è di nuovo attivo e in esecuzione, è possibile abilitarlo.

> [!NOTE]
> La disabilitazione di un endpoint non ha nulla a che vedere con il relativo stato di distribuzione in Azure. Un endpoint integro rimane attivo e in grado di ricevere traffico anche se è disabilitato in Gestione traffico. La disabilitazione di un endpoint in un profilo non influisce sul relativo stato in un altro profilo.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Per aggiungere un servizio cloud o un endpoint del sito Web

1. Nel riquadro di Gestione traffico nel portale di Azure classico trovare il profilo di Gestione traffico contenente le impostazioni degli endpoint da modificare. Per aprire la pagina delle impostazioni, fare clic sulla freccia a destra del nome del profilo.
2. Nella parte superiore della pagina fare clic su **Endpoint** per visualizzare gli endpoint che fanno già parte della configurazione.
3. Nella parte inferiore della pagina fare clic su **Aggiungi** per accedere alla pagina **Aggiungi endpoint del servizio**. Per impostazione predefinita, in **Endpoint di servizio**sono elencati i servizi cloud.
4. Per i servizi cloud, selezionare nell'elenco quelli che si desidera aggiungere come endpoint per il profilo specificato. Se si cancella il nome del servizio cloud, questo viene rimosso dall'elenco degli endpoint.
5. Per i siti Web fare clic sull'elenco a discesa **Tipo di servizio** e selezionare **App Web**.
6. Selezionare i siti Web nell'elenco e aggiungerli come endpoint per il profilo. Se si cancella il nome del sito Web, questo viene rimosso dall'elenco degli endpoint. È possibile selezionare solo un sito Web per ogni data center (noto anche come area) di Azure. Quando si seleziona il primo sito Web, non è più possibile selezionare gli altri siti Web presenti nello stesso data center. Vengono elencati solo i siti Web standard.
7. Dopo aver selezionato gli endpoint per questo profilo, fare clic sul segno di spunta in basso a destra per salvare le modifiche.

> [!NOTE]
> Dopo aver aggiunto o rimosso un endpoint da un profilo tramite il metodo di routing del traffico *Failover*, non è più possibile ordinare l'elenco priorità failover come desiderato. È possibile modificare l'ordine dell'Elenco priorità failover nella pagina di configurazione. Per ulteriori informazioni, vedere [Configurare il routing del traffico failover](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Per disabilitare un endpoint

1. Nel riquadro di Gestione traffico nel portale di Azure classico trovare il profilo di Gestione traffico contenente le impostazioni degli endpoint da modificare. Per aprire la pagina delle impostazioni, fare clic sulla freccia a destra del nome del profilo.
2. Per visualizzare gli endpoint inclusi nella configurazione, fare clic su **Endpoints** nella parte superiore della pagina.
3. Fare clic sull'endpoint da disabilitare, quindi fare clic su **Disabilita** nella parte inferiore della pagina.
4. I client continuano a inviare traffico all'endpoint per la durata (TTL). È possibile modificare la durata (TTL) nella pagina di configurazione del profilo di Gestione traffico.

## <a name="to-enable-an-endpoint"></a>Per abilitare un endpoint

1. Nel riquadro di Gestione traffico nel portale di Azure classico trovare il profilo di Gestione traffico contenente le impostazioni degli endpoint da modificare. Per aprire la pagina delle impostazioni, fare clic sulla freccia a destra del nome del profilo.
2. Per visualizzare gli endpoint inclusi nella configurazione, fare clic su **Endpoints** nella parte superiore della pagina.
3. Fare clic sull'endpoint da abilitare, quindi fare clic su **Abilita** nella parte inferiore della pagina.
4. I client vengono indirizzati all'endpoint abilitato secondo il profilo.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Per eliminare un servizio cloud o un endpoint del sito Web

1. Nel riquadro di Gestione traffico nel portale di Azure classico trovare il profilo di Gestione traffico contenente le impostazioni degli endpoint da modificare. Per aprire la pagina delle impostazioni, fare clic sulla freccia a destra del nome del profilo.
2. Nella parte superiore della pagina fare clic su **Endpoint** per visualizzare gli endpoint che fanno già parte della configurazione.
3. Nella pagina Endpoint fare clic sul nome dell'endpoint che si desidera eliminare dal profilo.
4. Nella parte inferiore della pagina fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire un profilo di Gestione traffico](traffic-manager-manage-profiles.md)
* [Configure routing methods](traffic-manager-configure-routing-method.md) (Configurare metodi di routing)
* [Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md)
* [Considerazioni sulle prestazioni di gestione traffico](traffic-manager-performance-considerations.md)
* [Operazioni per Gestione traffico (informazioni di riferimento API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)




<!--HONumber=Nov16_HO2-->


