---
title: Informazioni su Azure FarmBeats
description: Questo articolo contiene una panoramica di Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12bb0b0098b5108bf780b88fc42b86861ea6fcdc
ms.sourcegitcommit: 14bf4129a73de2b51a575c3a0a7a3b9c86387b2c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87439556"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Panoramica di Azure FarmBeats (anteprima)

Azure FarmBeats è un'offerta business-to-business disponibile in Azure Marketplace. Consente l'aggregazione di set di dati di agricoltura tra i provider. Azure FarmBeats consente di creare modelli di intelligenza artificiale (AI) o di Machine Learning (ML) basati su set di dati combinati. Con Azure FarmBeats, le aziende agricole possono concentrarsi sul valore aggiunto principale, anziché sulle attività più complesse indifferenziate dell'ingegneria dei dati.

> [!NOTE]
> Azure FarmBeats è attualmente disponibile in anteprima pubblica. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Azure FarmBeats è disponibile senza un contratto di servizio. Per assistenza, usare il [forum di supporto su Azure FarmBeats](https://aka.ms/farmbeatssupport).

![Progetto FarmBeats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Con l'anteprima di Azure FarmBeats è possibile:

- Valutare lo stato dell'azienda agricola usando l'indice di vegetazione e l'indice idrico basato sulle immagini satellitari.
- Ottenere consigli sul numero di sensori di umidità del suolo da usare e sul percorso in cui posizionarli.
- Tenere traccia della condizione dell'azienda agricola visualizzando i dati del terreno raccolti dai sensori di fornitori diversi.
- Ottenere la mappa dell'umidità del suolo basata sulla fusione dei dati del sensore e del satellite.
- Ottenere informazioni dettagliate di utilità pratica creando modelli di intelligenza artificiale/apprendimento automatico sulla base di set di dati aggregati.
- Creare o potenziare la soluzione di agricoltura digitale grazie ad avvisi sullo stato dell'azienda agricola.

## <a name="datahub"></a>Datahub

Azure FarmBeats Datahub è un livello API, che consente l'aggregazione, la normalizzazione e la contestualizzazione di diversi set di dati di agricoltura tra i provider. È possibile usare Azure FarmBeats per ottenere:
- **Dati dei sensori** di tre provider di sensori [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), [Pessl Instruments](https://metos.at/)
- **Immagini satellitari** della missione satellitare [Sentinel-2](https://sentinel.esa.int/web/sentinel/home) dell'Agenzia spaziale europea
- **Immagini di droni** di tre provider di immagini di droni [senseFly](https://www.sensefly.com/), [SlantRange](https://slantrange.com/), [DJI](https://dji.com/)

Datahub è progettato come piattaforma API estendibile. Collaboriamo con molti più provider per l'integrazione con Azure FarmBeats, in modo da aumentare le possibilità di scelta durante la creazione della soluzione.

## <a name="accelerator"></a>Acceleratore

Azure FarmBeats Accelerator è un'applicazione Web di esempio basata su Datahub. Accelerator avvia rapidamente l'interfaccia utente e lo sviluppo del modello. Azure FarmBeats Accelerator usa le API di Azure FarmBeats. Visualizza i dati dei sensori inseriti come grafici e gli output del modello come mappe. È possibile ad esempio usare l'acceleratore per creare rapidamente un'azienda agricola e ottenere con facilità una mappa dell'indice di vegetazione o una mappa di posizionamento dei sensori.

## <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo

Un amministratore può definire le regole di accesso per Azure FarmBeats usando uno dei ruoli predefiniti. I ruoli determinano le aree dell'applicazione a cui un utente può accedere e le azioni che può eseguire. Esistono due tipi di ruoli in Azure FarmBeats, per gli utenti e per i partner.

### <a name="user-roles"></a>Ruoli utente

Un [amministratore può aggiungere e gestire gli utenti](manage-users-in-azure-farmbeats.md) e definire i loro livelli di accesso in base a due ruoli utente: Amministratore e Sola lettura.

### <a name="partner-roles"></a>Ruoli partner

Un amministratore può aggiungere più partner come provider di dati ad Azure FarmBeats. Di seguito vengono riepilogati i ruoli partner disponibili in FarmBeats e le relative autorizzazioni:

| Tipo di partner    |   Azioni  | Scope |
| ---- | -------- | -------- |
| Partner per i sensori  |   Creazione, lettura, aggiornamento <br/> <br/> Lettura, aggiornamento | DeviceModel, Device, SensorModel, Sensor <br/> <br/> ExtendedType |
| Partner per le immagini  |   Creazione, lettura, aggiornamento <br/> <br/> Lettura, aggiornamento <br/> <br/> Lettura | Scene, SceneFile <br/> <br/> ExtendedType <br/> <br/> Farm |
| Partner per il meteo* <br/> <br/>  (* prossimamente) |   Creazione, lettura, aggiornamento <br/> <br/> Lettura, aggiornamento <br/> <br/> Lettura | WeatherDataModel, WeatherDataLocation, JobType <br/> <br/> ExtendedType <br/> <br/> Farm |

## <a name="resources"></a>Risorse

Azure FarmBeats è disponibile senza costi aggiuntivi e si paga solo per le risorse di Azure usate. Per altre informazioni sull'offerta, è possibile usare le risorse seguenti:

- Per informazioni sulle ultime novità su Azure FarmBeats, visitare il [blog di Azure FarmBeats](https://aka.ms/farmbeatsblog).
- Per richiedere assistenza, pubblicare una domanda nel [forum di supporto di Azure FarmBeats](https://aka.ms/farmbeatssupport).
- Inviare commenti e suggerimenti pubblicando un'idea su una funzionalità nel [forum dei commenti di Azure FarmBeats](https://aka.ms/farmbeatsfeedback) o votando per un'idea già pubblicata.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Installare Azure FarmBeats](install-azure-farmbeats.md)
