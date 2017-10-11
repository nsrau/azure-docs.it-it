---
title: Panoramica approfondita dei piani di servizio app di Azure | Documentazione Microsoft
description: Informazioni sui piani di servizio app per Azure App Service e sui vantaggi offerti all'esperienza di gestione.
keywords: servizio app, servizio app di azure, scala, scalabile, piano di servizio app, costo del servizio app
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: byvinyal
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 688f57de662fec6a04227c35d6578097c795c6da
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="azure-app-service-plans-in-depth-overview"></a>Panoramica approfondita dei piani di servizio app di Azure

I piani di servizio app rappresentano la raccolta di risorse fisiche usate per ospitare le app.

I piani di servizio app definiscono:

- Area (Stati Uniti occidentali, Stati Uniti orientali e così via)
- Numero di scala (una, due, tre istanze e così via)
- Dimensione dell'istanza (Small, Medium, Large)
- SKU: Free, Shared, Basic, Standard, Premium, PremiumV2, Isolated.

Nel [Servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) le app Web, le app per dispositivi mobili, le app per le API e le app per le funzioni (o Funzioni) vengono tutte eseguite in un piano di servizio app.  Le app appartenenti alla stessa sottoscrizione e area possono condividere lo stesso piano di servizio app. 

Tutte le applicazioni assegnate a un **piano di servizio app**  condividono le risorse definite dal piano, in modo da consentire un risparmio sui costi quando si ospitano più app in un unico piano di servizio app.

Il **piano di servizio app** può passare dai livelli **Free** e **Shared** ai livelli **Basic**, **Standard**,  **Premium**, e **Isolated**. Ogni livello superiore consente di accedere a più risorse e funzionalità.

Se il piano di servizio app è impostato sul livello **Basic** o superiore, è possibile controllare le **dimensioni** e il numero di scala delle VM.

Se il piano è configurato per usare due istanze "piccole" nel livello **Standard**, ad esempio, tutte le app del piano vengono eseguite in entrambe le istanze. Le app hanno anche accesso alle funzionalità previste dal livello **Standard**. Le istanze del piano in cui vengono eseguite le app sono completamente gestite e a disponibilità elevata.

> [!IMPORTANT]
> Il piano tariffario del piano di servizio app determina il costo e non il numero di app ospitate nel piano.

L'articolo illustra le caratteristiche principali di un piano di servizio app, come il piano tariffario e la scalabilità, e il modo in cui contribuiscono alla gestione delle app.

## <a name="new-pricing-tier-premiumv2"></a>Nuovo piano tariffario: PremiumV2

Il nuovo piano tariffario **PremiumV2** offre [VM della serie Dv2](../virtual-machines/windows/sizes-general.md#dv2-series) con processori più veloci, archiviazione SSD e un rapporto doppio tra memoria e core rispetto al livello **Standard**. **PremiumV2** supporta anche una scalabilità superiore tramite un numero di istanze incrementato, offrendo al tempo stesso tutte le funzionalità avanzate disponibili nel piano Standard. Tutte le funzionalità disponibili nel livello **Premium** esistente sono incluse in **PremiumV2**.

Analogamente ad altri livelli dedicati, per questo livello sono disponibili tre dimensioni di macchina virtuale:

- Small (1 core CPU, 3,5 GiB di memoria) 
- Medium (2 core di CPU, 7 GiB di memoria) 
- Large (4 core CPU, 14 GiB di memoria)  

Per informazioni sui prezzi di [PremiumV2](/pricing/details/app-service/), vedere **Prezzi del servizio app**.

Per un'introduzione al nuovo piano tariffario **PremiumV2**, vedere [Configurare il livello PremiumV2](app-service-configure-premium-tier.md).

## <a name="apps-and-app-service-plans"></a>App e piani di servizio app

Un'app in un servizio app può essere associata a un solo piano per volta.

App e piani sono inclusi in un **gruppo di risorse**. Un gruppo di risorse funge da confine del ciclo di vita per ogni risorsa al suo interno. È possibile usare i gruppi di risorse per gestire contemporaneamente tutte le parti di un'applicazione.

Dato che un unico gruppo di risorse può includere più piani di servizio app, è possibile allocare app diverse a risorse fisiche diverse.

Si possono ad esempio separare le risorse tra ambienti di sviluppo, test e produzione. L'uso di ambienti separati per la produzione e lo sviluppo e test consente di isolare le risorse. In questo modo, l'esecuzione di un test di carico su una nuova versione delle app non usa le stesse risorse delle app di produzione, che devono rispondere alle richieste dei clienti reali.

Quando in un unico gruppo di risorse sono inclusi più piani, è anche possibile definire un'applicazione che copre più aree geografiche.

Ad esempio, un'app a disponibilità elevata in esecuzione in due aree include almeno due piani, uno per ogni area, e un'app associata a ogni piano. In una situazione di questo tipo, tutte le copie dell'app sono contenute in un unico gruppo di risorse. La disponibilità di un gruppo di risorse con più piani e più app semplifica la gestione, il controllo e la visualizzazione dell'integrità dell'applicazione.

## <a name="create-an-app-service-plan-or-use-existing-one"></a>Creare un piano di servizio app o usare quello esistente

Quando si crea una nuova app Web nel servizio app, è possibile condividere le risorse di hosting inserendo l'app in un piano di servizio app esistente. Per determinare se la nuova app avrà le risorse adeguate, è necessario valutare la capacità del piano di servizio app esistente e il carico previsto per la nuova app. L'allocazione eccessiva di risorse può potenzialmente causare tempi di inattività per le app nuove ed esistenti.

Si consiglia di isolare l'app in un nuovo piano di servizio app nei casi seguenti:

- L'app usa molte risorse.
- L'app ha fattori di scala diversi dalle altre app ospitate in un piano esistente.
- L'app necessita di risorse in un'area geografica diversa.

In questo modo è possibile allocare un nuovo set di risorse per l'app e ottenere un maggiore controllo delle app.

## <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

> [!TIP]
> Se è disponibile l'opzione Ambiente del servizio app di Azure, vedere [Creare un'app Web in un ambiente del servizio app](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

È possibile creare un piano di servizio app vuoto o come parte della creazione di app.

Nel [portale di Azure](https://portal.azure.com) fare clic su **Nuovo** > **Web e dispositivi mobili** e quindi selezionare **App Web** o un altro tipo di app del servizio app.

![Creare un'app nel portale di Azure.][createWebApp]

È quindi possibile selezionare o creare il piano di servizio app per la nuova app.

 ![Creare un piano di servizio app.][createASP]

Per creare un piano di servizio app, fare clic su **[+] Crea nuovo**, digitare il nome in **Piano di servizio app** e quindi selezionare una **Località** appropriata. Fare clic su **Piano tariffario**e quindi selezionare un piano tariffario appropriato per il servizio. Selezionare **Visualizza tutto** per visualizzare altre opzioni sui prezzi, ad esempio **Gratuito** e **Condiviso**. Dopo aver scelto il piano tariffario, fare clic sul pulsante **Seleziona** .

## <a name="move-an-app-to-a-different-app-service-plan"></a>Spostare un'app in un piano di servizio app diverso

È possibile spostare un'app in un piano di servizio app diverso nel [portale di Azure](https://portal.azure.com). Le app possono essere spostate tra i piani solo se i piani si trovano _nello stesso gruppo di risorse e nella stessa area geografica_.

Per spostare un'applicazione in un altro piano:

- Passare all'app che si desidera spostare.
- Nel **Menu** cercare la sezione **Piano di servizio app**.
- Selezionare **Cambia il piano di servizio app** per avviare il processo.

**Cambia il piano di servizio app** apre il selettore **Piano di servizio app**. A questo punto, è possibile scegliere un piano esistente in cui spostare l'app. Vengono visualizzati solo i piani nello stesso gruppo di risorse e nella stessa area.

![Pannello di selezione Piano di servizio app.][change]

È previsto un piano tariffario diverso per ogni piano. Quando si sposta un sito dal livello gratuito al livello Standard, ad esempio, tutte le app assegnate possono usare le funzionalità e le risorse del livello Standard.

## <a name="clone-an-app-to-a-different-app-service-plan"></a>Clonare un'app in un piano di servizio app diverso

Se si vuole spostare l'app in un'area diversa, in alternativa consiste nel clonarla. Con la clonazione si crea una copia dell'app in un piano di servizio app nuovo o esistente di qualsiasi area.

**Clona app** è disponibile nella sezione **Strumenti di sviluppo** del menu.

> [!IMPORTANT]
> La clonazione presenta alcune limitazioni, illustrate nell'articolo [Clonare un'app](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Scalare un piano di servizio app

Sono disponibili tre modi per ridimensionare un piano:

- **Modificare il piano tariffario del piano**. Un piano del livello Basic può essere convertito in Standard e tutte le app assegnate potranno usare le funzionalità del livello Standard.
- **Modificare le dimensioni delle istanze del piano**. Un piano con livello Basic che usa istanze piccole, ad esempio, può essere modificato per usare istanze grandi. Tutte le app associate al piano ora possono usare le risorse di memoria e CPU aggiuntive offerte dalle maggiori dimensioni delle istanze.
- **Modificare il numero di istanze del piano**. Un piano Standard con tre istanze, ad esempio, può essere aumentato fino a 10 istanze. Un piano Premium può essere aumentato fino a 20 istanze (a seconda della disponibilità). Tutte le app associate al piano ora possono usare le risorse di memoria e CPU aggiuntive offerte dal maggior numero di istanze.

È possibile modificare il piano tariffario e le dimensioni delle istanze facendo clic sulla voce **Aumenta prestazioni** nelle impostazioni dell'app o del piano di servizio app. Le modifiche vengono applicate al piano di servizio app e interessano tutte le app ospitate.

 ![Impostare i valori per aumentare le prestazioni di un'app.][pricingtier]

## <a name="app-service-plan-cleanup"></a>Pulizia del piano di servizio app

> [!IMPORTANT]
> Anche i **piani di servizio app** a cui non sono associate app vengono addebitati, in quanto continuano a riservare la capacità di calcolo.

Per evitare costi imprevisti, dopo aver eliminato l'ultima app ospitata in un piano di servizio app, per impostazione predefinita viene eliminato anche il piano di servizio app vuoto risultante.

## <a name="summary"></a>Riepilogo

I piani di servizio app rappresentano un set di funzionalità e capacità che è possibile condividere tra le app. I piani di servizio app garantiscono la possibilità di allocare app specifiche a un set di risorse e ottimizzare ulteriormente l'utilizzo delle risorse di Azure. In questo modo, se si vuole risparmiare denaro sull'ambiente di test è possibile condividere un piano tra più app. È anche possibile ottimizzare la velocità effettiva nell'ambiente di produzione scalandolo in più aree e piani.

## <a name="whats-changed"></a>Modifiche apportate

- Per una guida relativa al passaggio da Siti Web al servizio App, vedere [Servizio app di Azure e i servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png

