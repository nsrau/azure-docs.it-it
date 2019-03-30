---
title: Aggiornamento del Debugger di Snapshot di Application Insights di Azure per le app .NET | Microsoft Docs
description: Come eseguire l'aggiornamento di Snapshot Debugger per la versione più recente in servizi App di Azure o tramite pacchetti Nuget
services: application-insights
author: MarioHewardt
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: Mario.Hewardt
ms.reviewer: mbullwin
ms.openlocfilehash: 54b79897ee378cda52106fe704e25c50a4325f38
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632374"
---
# <a name="upgrading-the-snapshot-debugger"></a>L'aggiornamento di Snapshot Debugger

Per garantire la migliore sicurezza possibile per i tuoi dati, Microsoft è in movimento lontano da TLS 1.0 e TLS 1.1, che hanno dimostrato di essere esposti a utenti malintenzionati determinati. Se si usa una versione precedente dell'estensione del sito, richiesto un aggiornamento per continuare a lavorare. Questo documento descrive i passaggi necessari per aggiornare il debugger di Snapshot per la versione più recente. Esistono due percorsi di aggiornamento primari a seconda se è stato abilitato il Debugger di Snapshot con un'estensione del sito o se è stato usato un SDK/Nuget aggiunti all'applicazione. Entrambi i percorsi di aggiornamento sono descritte di seguito. 

## <a name="upgrading-the-site-extension"></a>Aggiornamento dell'estensione di sito

Se è abilitato il debugger di Snapshot usando l'estensione del sito, è possibile aggiornare facilmente mediante la procedura seguente:

1. Accedere al portale di Azure.
2. Passare alla risorsa con Application Insights e Snapshot debugger abilitato. Ad esempio, per un'App Web, passare alla risorsa del servizio App:

   ![Screenshot della singola risorsa di servizio App denominato DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Dopo essere passati alla risorsa, fare clic su Application Insights nel pannello Panoramica:

   ![Screenshot dei tre pulsanti. Pulsante Centra con nome Application Insights è selezionato](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Verrà visualizzato un nuovo pannello con le impostazioni correnti. A meno che non vuoi sfruttare l'opportunità di modificare le impostazioni, è possibile lasciarli inalterati. Il **applica** pulsante nella parte inferiore del pannello non è abilitato per impostazione predefinita e sarà necessario attivare o disattivare un'impostazione per attivare il pulsante. Non è necessario modificare alcuna impostazione effettive, ma è possibile modificare l'impostazione e quindi immediatamente tale modifica. Si consiglia di attivare e disattivare il Profiler di impostazione e quindi selezionando **applica**.

   ![Pagina di schermata dell'App del servizio configurazione di Application Insights con il pulsante Applica evidenziato in rosso](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. Dopo aver fatto clic **applica**, verrà richiesto di confermare le modifiche.

    > [!NOTE]
    > Il sito verrà riavviato come parte del processo di aggiornamento.

   ![Schermata del servizio App si applicano monitoraggio prompt dei comandi. Casella di testo verrà visualizzato: "Abbiamo verrà ora applicare le modifiche alle impostazioni dell'app e installazione degli strumenti per la risorsa di Application Insights collegata all'app web. Il sito verrà riavviato. Continuare?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Fare clic su **Sì** per applicare le modifiche. Durante il processo verrà visualizzata una notifica che mostra che le modifiche vengano applicate:

   ![Schermata di applicazione delle modifiche - aggiornamento di messaggio di estensioni che viene visualizzato in alto a destra](./media/snapshot-debugger-upgrade/updating-extensions.png)

Una volta completata, una **"Vengono applicate modifiche"** verrà visualizzata la notifica.

   ![Screenshot del messaggio che indica che le modifiche vengono applicate](./media/snapshot-debugger-upgrade/changes-are-applied.png)

Il sito è stato aggiornato ed è pronto per l'uso.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>L'aggiornamento di Snapshot Debugger usando Nuget o SDK

Se l'applicazione usa una versione di `Microsoft.ApplicationInsights.SnapshotCollector` precedente versione 1.3.1, dovranno essere aggiornati a una [versione più recente](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) continuerà a lavorare.
