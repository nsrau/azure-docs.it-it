---
title: "Panoramica su Integrità dei servizi di Azure | Microsoft Docs"
description: Informazioni personalizzate su come le app di Azure sono interessate dalla manutenzione e dai problemi attuali e futuri dei servizi di Azure.
services: Resource health
documentationcenter: 
author: rboucher
manager: 
editor: 
ms.assetid: 
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/07/2017
ms.author: robb
ms.openlocfilehash: c463479b7eaee5a0548c8891dd3a20ef070dd39b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="azure-service-health"></a>Integrità dei servizi di Azure
Integrità dei servizi di Azure offre informazioni tempestive e personalizzate quando nei servizi di Azure si verificano problemi che influiscono sui servizi.  Consente inoltre di preparare una manutenzione pianificata per il futuro.

## <a name="service-health-events"></a>Eventi di Integrità dei servizi
Integrità dei servizi registra tre tipi di eventi di integrità che possono influire sulle risorse:
1. **Problemi dei servizi**: problemi dei servizi di Azure che influiscono in questo momento. 
2. **Manutenzione pianificata**: manutenzione imminente che può influire sulla disponibilità dei servizi in futuro.  
3. **Avvisi sull'integrità**: modifiche apportate ai servizi di Azure che richiedono attenzione, ad esempio quando le funzionalità di Azure sono deprecate o si supera una quota di utilizzo.

    ![Eventi di Integrità dei servizi](./media/service-health-overview/azure-service-health-overview-7.png)

## <a name="get-started-with-service-health"></a>Introduzione a Integrità dei servizi
Per avviare il dashboard di Integrità dei servizi, selezionare il riquadro Integrità dei servizi nel dashboard del portale. Se in precedenza il riquadro è stato rimosso o si una un dashboard personalizzato, cercare il servizio Integrità dei servizi in "Altri servizi" in basso a sinistra nel dashboard.
![Introduzione a Integrità dei servizi](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Vedere i problemi che attualmente influiscono su servizi
La visualizzazione **Problemi del servizio** indica eventuali problemi presenti nei servizi di Azure che influiscono sulle risorse. È possibile capire quando è iniziato il problema e quali sono le aree e i servizi interessati. È anche possibile leggere l'aggiornamento più recente per comprendere che cosa sta facendo Azure per risolvere il problema. 
![Gestire il problema del servizio](./media/service-health-overview/azure-service-health-overview-2.png)

Scegliere la scheda **Impatto potenziale** per visualizzare l'elenco specifico delle risorse di cui si è proprietari che potrebbero essere interessate dal problema. È possibile scaricare un elenco in formato CSV delle risorse per condividerle con il team.
![Gestire il problema del servizio - Impatto](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Ottenere i collegamenti e spiegazioni scaricabili 
È possibile ottenere un collegamento relativo al problema e usarlo nel sistema di gestione del problema. È possibile scaricare file PDF e in alcuni casi CSV da condividere con gli utenti che non hanno accesso al portale di Azure.   
![Gestire il problema del servizio - Gestione dei problemi](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Ricevere assistenza da Microsoft
Contattare il supporto tecnico se la risorsa rimane in uno stato insoddisfacente anche se il problema è stato risolto.  Usare i collegamenti del supporto tecnico nella parte destra della pagina.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Aggiungere una mappa dell'integrità personalizzata al dashboard
Filtrare Integrità dei servizi in modo da visualizzare le sottoscrizioni, le aree e i tipi di risorse cruciali per l'azienda. Salvare il filtro e aggiungere al dashboard del portale una mappa del mondo personalizzata per l'integrità. 
![Filtro con mappa dell'integrità personalizzata](./media/service-health-overview/azure-service-health-overview-6a.png)
![Aggiungere una mappa dell'integrità personalizzata](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Configurare gli avvisi di Integrità dei servizi
Integrità dei servizi di Azure si integra con Monitoraggio di Azure per l'invio di avvisi via posta elettronica, messaggi di testo e notifiche webhook in caso di impatto sulle risorse aziendali cruciali. Impostare un avviso di log attività per l'evento di Integrità dei servizi appropriato. Indirizzare tale avviso alle persone appropriate nell'organizzazione usando i gruppi di azioni. Per altre informazioni, vedere [Configurare gli avvisi per Integrità dei servizi](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)

 
