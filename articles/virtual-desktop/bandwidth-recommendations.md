---
title: Suggerimenti per la larghezza di banda per le sessioni remote-Azure
description: Suggerimenti per la larghezza di banda disponibili per le sessioni remote.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: helohr
ms.openlocfilehash: 1a88cfb2fcd5f0b607e7a2b43ae833789a584e34
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695312"
---
# <a name="bandwidth-recommendations-for-remote-sessions"></a>Suggerimenti per la larghezza di banda per le sessioni remote

Quando si usa una sessione remota di Windows, la larghezza di banda disponibile per la rete influisce significativamente sulla qualità dell'esperienza utente. Diverse applicazioni e risoluzioni di visualizzazione richiedono configurazioni di rete diverse, quindi è importante assicurarsi che la rete sia configurata in base alle esigenze. Questo articolo descrive le larghezze di banda consigliate per ogni carico di lavoro.

>[!NOTE]
>Le indicazioni seguenti si applicano alle reti con una perdita inferiore al 0,1%.

## <a name="applications"></a>Applicazioni

Nella tabella seguente sono elencati i requisiti minimi per un'esperienza utente uniforme. 

|Carico di lavoro        |Applicazioni di esempio                                                                                           |Larghezza di banda consigliata|
|----------------|--------------------------------------------------------------------------------------------------------------|---------------------|
|Task Worker     |Microsoft Word, Outlook, Excel, Adobe Reader                                                                  |1.5 @ no__t-0Mbps             |
|Ruolo di lavoro di Office   |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Visualizzatore foto                                        |3 @ no__t-0Mbps               |
|Knowledge Worker|Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Visualizzatore foto, Java                                  |5 @ no__t-0Mbps               |
|Power Worker    |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Visualizzatore foto, Java, CAD/CAM, illustrazione/pubblicazione|15 @ no__t-0Mbps              |

Tenere presente che lo stress messo sulla rete dipende sia dalla frequenza dei fotogrammi di output del carico di lavoro dell'app che dalla risoluzione dello schermo. Se aumenta la frequenza dei fotogrammi o la risoluzione dello schermo, aumenterà anche il requisito di larghezza di banda. Un carico di lavoro leggero con schermo ad alta risoluzione, ad esempio, richiede una larghezza di banda maggiore disponibile rispetto a un carico di lavoro leggero con risoluzione normale o bassa.

Altri scenari possono modificare i requisiti di larghezza di banda a seconda del modo in cui vengono usati, ad esempio:

- Conferenza vocale o video
- Comunicazione in tempo reale
- Video di streaming 4K

Assicurarsi di eseguire il test di carico di questi scenari nella distribuzione usando strumenti di simulazione, ad esempio Login VSI. Modificare le dimensioni del carico, eseguire test di stress e testare gli scenari utente comuni nelle sessioni remote per comprendere meglio i requisiti della rete. 

## <a name="display-resolutions"></a>Risoluzioni di visualizzazione

Diverse risoluzioni di visualizzazione richiedono larghezze di banda disponibili diverse. La tabella seguente elenca le larghezze di banda consigliate per un'esperienza utente uniforme in risoluzioni di visualizzazione tipiche con una frequenza di fotogrammi pari a 30 fotogrammi al secondo (fps). Tenere presente che gli scenari che comportano una frequenza di fotogrammi inferiore a 30 fps, ad esempio la lettura di testo statico, richiedono una larghezza di banda inferiore disponibile. 

|Risoluzioni di visualizzazione tipiche a 30 fps    |Larghezza di banda consigliata|
|-----------------------------------------|---------------------|
|Circa 1024 × 768 px                      |1,5 Mbps             |
|Circa 1280 × 720 px                      |3 Mbps               |
|Circa 1920 × 1080 px                     |5 Mbps               |
|Circa 3840 × 2160 px (4K)                |15 Mbps              |
