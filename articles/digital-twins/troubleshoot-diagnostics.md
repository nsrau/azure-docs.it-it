---
title: Configurare la diagnostica
titleSuffix: Azure Digital Twins
description: Vedere come abilitare la registrazione con le impostazioni di diagnostica.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 5091edbf9138cb8ff03df193dcbeed692aaf13e3
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612402"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Risoluzione dei problemi relativi ai dispositivi gemelli digitali di Azure: registrazione diagnostica

I dispositivi gemelli digitali di Azure raccolgono le [metriche](troubleshoot-metrics.md) per l'istanza del servizio che forniscono informazioni sullo stato delle risorse. È possibile usare queste metriche per valutare l'integrità complessiva del servizio dispositivi gemelli digitali di Azure e le risorse connesse. Queste statistiche per l'utente consentono di vedere cosa accade con i dispositivi gemelli digitali di Azure e consentono di eseguire l'analisi della causa radice dei problemi senza dover contattare il supporto tecnico di Azure.

Questo articolo illustra come attivare la **registrazione diagnostica** per i dati di metrica dall'istanza di Azure Digital gemelli. È possibile usare questi log per risolvere i problemi relativi al servizio e configurare le impostazioni di diagnostica per inviare le metriche di dispositivi gemelli digitali di Azure a destinazioni diverse. Per altre informazioni su queste impostazioni, vedere [*creare le impostazioni di diagnostica per inviare metriche e log di piattaforma a destinazioni diverse*](../azure-monitor/platform/diagnostic-settings.md).

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Attivare le impostazioni di diagnostica con la portale di Azure

Ecco come abilitare le impostazioni di diagnostica per l'istanza di Azure Digital Twins:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'istanza di Azure Digital gemelli. È possibile trovarlo digitando il relativo nome nella barra di ricerca del portale. 

2. Selezionare **impostazioni di diagnostica** dal menu e quindi **Aggiungi impostazione di diagnostica**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Screenshot che mostra la pagina delle impostazioni di diagnostica e il pulsante per aggiungere":::

3. Nella pagina seguente compilare i valori seguenti:
     * **Nome dell'impostazione di diagnostica**: assegnare un nome alle impostazioni di diagnostica.
     * **Dettagli categoria**: scegliere le operazioni che si desidera monitorare e selezionare le caselle per abilitare la diagnostica per tali operazioni. Le operazioni per cui le impostazioni di diagnostica possono inviare report sono:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Per ulteriori informazioni su queste opzioni, vedere la sezione [*Dettagli categoria*](#category-details) riportata di seguito.
     * **Dettagli destinazione**: scegliere il percorso in cui si desidera inviare i log. È possibile selezionare qualsiasi combinazione delle tre opzioni:
        - Invia a Log Analytics
        - Archivia in un account di archiviazione
        - Streaming in un hub eventi

        Se necessario per la selezione della destinazione, è possibile che venga richiesto di specificare ulteriori dettagli.  
    
4. Salvare le nuove impostazioni. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Screenshot che mostra la pagina delle impostazioni di diagnostica e il pulsante per aggiungere":::

Le nuove impostazioni diventano effettive in circa 10 minuti. Successivamente, i log vengono visualizzati nella destinazione configurata di nuovo nella pagina **impostazioni di diagnostica** per l'istanza. 

## <a name="category-details"></a>Dettagli categoria

Di seguito sono riportate altre informazioni sulle categorie di log che è possibile selezionare in **Dettagli categoria** quando si configurano le impostazioni di diagnostica.

| Categoria di log | Descrizione |
| --- | --- |
| ADTModelsOperation | Registrare tutte le chiamate API relative ai modelli |
| ADTQueryOperation | Registrare tutte le chiamate API relative alle query |
| ADTEventRoutesOperation | Registrare tutte le chiamate API relative alle route degli eventi e all'uscita degli eventi dai dispositivi gemelli digitali di Azure a un servizio endpoint come griglia di eventi, Hub eventi e bus di servizio |
| ADTDigitalTwinsOperation | Registrare tutte le chiamate API relative ai dispositivi gemelli digitali di Azure |

Ogni categoria di log è costituita da operazioni di scrittura, lettura, eliminazione e azione.  Questi mapping a chiamate API REST sono i seguenti:

| Tipo di evento | Operazioni dell'API REST |
| --- | --- |
| Scrittura | PUT e PATCH |
| Lettura | GET |
| Delete | DELETE |
| Azione | POST |

Ecco un elenco completo delle operazioni e delle [chiamate API REST di Azure Digital gemelle](https://docs.microsoft.com/rest/api/azure-digitaltwins/) corrispondenti registrate in ogni categoria. 

>[!NOTE]
> Ogni categoria di log contiene diverse chiamate API REST e operazioni. Nella tabella riportata di seguito, ogni categoria di log esegue il mapping a tutte le chiamate API REST e delle operazioni sottostanti fino a quando non viene elencata la categoria di log successiva. 

| Categoria di log | Operazione | Chiamate API REST e altri eventi |
| --- | --- | --- |
| ADTModelsOperation | Microsoft. DigitalTwins/Models/Write | API per l'aggiornamento di modelli gemelli digitali |
|  | Microsoft. DigitalTwins/Models/Read | Modelli di dispositivi gemelli digitali Get by ID ed elenco di API |
|  | Microsoft. DigitalTwins/Models/Delete | API per i modelli gemelli digitali Delete |
|  | Microsoft. DigitalTwins/modelli/azione | I modelli di dispositivi gemelli digitali aggiungono API |
| ADTQueryOperation | Microsoft. DigitalTwins/query/Action | API di query gemelle |
| ADTEventRoutesOperation | Microsoft. DigitalTwins/eventroutes/Write | Route di eventi-Aggiungi API |
|  | Microsoft. DigitalTwins/eventroutes/Read | Route di eventi Get by ID ed elenco di API |
|  | Microsoft. DigitalTwins/eventroutes/Delete | API di eliminazione delle route degli eventi |
|  | Microsoft. DigitalTwins/eventroutes/Action | Errore durante il tentativo di pubblicare eventi in un servizio endpoint (non in una chiamata API) |
| ADTDigitalTwinsOperation | Microsoft. DigitalTwins/DigitalTwins/Write | Dispositivi gemelli digitali aggiungere, aggiungere relazioni, aggiornare e aggiornare un componente |
|  | Microsoft. DigitalTwins/DigitalTwins/Read | Dispositivi gemelli digitali Get by ID, Get Component, Get Relationship by ID, List relazioni in ingresso, elenco relazioni |
|  | Microsoft. DigitalTwins/DigitalTwins/Delete | Eliminazione di gemelli digitali, relazione Delete |
|  | Microsoft. DigitalTwins/DigitalTwins/Action | I dispositivi gemelli digitali inviano dati di telemetria del componente, invia dati |

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla configurazione della diagnostica, vedere [*raccogliere e utilizzare i dati di log dalle risorse di Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Per informazioni sulle metriche dei dispositivi gemelli digitali di Azure, vedere [*risoluzione dei problemi: visualizzare le metriche con monitoraggio di Azure*](troubleshoot-metrics.md).
* Per informazioni su come abilitare gli avvisi per le metriche, vedere [*risoluzione dei problemi: configurare gli avvisi*](troubleshoot-alerts.md).
