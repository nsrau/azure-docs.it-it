---
title: Suggerimenti per la larghezza di banda per le sessioni remote-Azure
description: Suggerimenti per la larghezza di banda disponibili per le sessioni remote.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: helohr
ms.openlocfilehash: 8352858b9fd6059257b5ba7637822f85c56eb070
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802621"
---
# <a name="bandwidth-recommendations-for-remote-sessions"></a>Raccomandazioni relative alla larghezza di banda per le sessioni remote

Quando si usa una sessione remota di Windows, la larghezza di banda disponibile per la rete influisce significativamente sulla qualità dell'esperienza utente. Diverse applicazioni e risoluzioni di visualizzazione richiedono configurazioni di rete diverse, quindi è importante assicurarsi che la rete sia configurata in base alle esigenze.

>[!NOTE]
>Le indicazioni seguenti si applicano alle reti con una perdita inferiore al 0,1%.

## <a name="applications"></a>Applicazioni

La tabella seguente elenca le larghezze di banda minime consigliate per un'esperienza utente uniforme. 

|Carico di lavoro        |Applicazioni di esempio                                                                                           |Larghezza di banda consigliata|
|----------------|--------------------------------------------------------------------------------------------------------------|---------------------|
|Task Worker     |Microsoft Word, Outlook, Excel, Adobe Reader                                                                  |1.5 @ no__t-0Mbps        |
|Ruolo di lavoro di Office   |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Visualizzatore foto                                        |3 @ no__t-0Mbps          |
|Knowledge Worker|Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Visualizzatore foto, Java                                  |5 @ no__t-0Mbps          |
|Power Worker    |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Visualizzatore foto, Java, CAD/CAM, illustrazione/pubblicazione|15 @ no__t-0Mbps         |

>[!NOTE]
>Queste indicazioni si applicano indipendentemente dal numero di utenti presenti nella sessione.

Tenere presente che lo stress messo sulla rete dipende sia dalla frequenza dei fotogrammi di output del carico di lavoro dell'app che dalla risoluzione dello schermo. Se aumenta la frequenza dei fotogrammi o la risoluzione dello schermo, aumenterà anche il requisito di larghezza di banda. Un carico di lavoro leggero con schermo ad alta risoluzione, ad esempio, richiede una larghezza di banda maggiore disponibile rispetto a un carico di lavoro leggero con risoluzione normale o bassa.

Altri scenari possono modificare i requisiti di larghezza di banda a seconda del modo in cui vengono usati, ad esempio:

- Conferenza vocale o video
- Comunicazione in tempo reale
- Video di streaming 4K

Assicurarsi di eseguire il test di carico di questi scenari nella distribuzione usando strumenti di simulazione, ad esempio Login VSI. Modificare le dimensioni del carico, eseguire test di stress e testare gli scenari utente comuni nelle sessioni remote per comprendere meglio i requisiti della rete. 

## <a name="display-resolutions"></a>Risoluzioni di visualizzazione

Diverse risoluzioni di visualizzazione richiedono larghezze di banda disponibili diverse. La tabella seguente elenca le larghezze di banda consigliate per un'esperienza utente uniforme in risoluzioni di visualizzazione tipiche con una frequenza di fotogrammi pari a 30 fotogrammi al secondo (fps). Questi consigli si applicano a scenari utente singoli e multipli. Tenere presente che gli scenari che comportano una frequenza di fotogrammi inferiore a 30 fps, ad esempio la lettura di testo statico, richiedono una larghezza di banda inferiore disponibile. 

|Risoluzioni di visualizzazione tipiche a 30 fps    |Larghezza di banda consigliata|
|-----------------------------------------|---------------------|
|Circa 1024 × 768 px                      |1,5 Mbps             |
|Circa 1280 × 720 px                      |3 Mbps               |
|Circa 1920 × 1080 px                     |5 Mbps               |
|Circa 3840 × 2160 px (4K)                |15 Mbps              |

>[!NOTE]
>Queste indicazioni si applicano indipendentemente dal numero di utenti presenti nella sessione.

## <a name="additional-resources"></a>Risorse aggiuntive

L'area di Azure in cui ci si trova può influire negativamente sull'esperienza utente, come le condizioni della rete. Per altre informazioni, vedere la pagina relativa allo strumento di [stima dell'esperienza desktop virtuale di Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/) .
