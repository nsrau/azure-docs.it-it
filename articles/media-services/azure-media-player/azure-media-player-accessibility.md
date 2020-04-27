---
title: Accessibilità di Azure Media Player
description: Altre informazioni sulle impostazioni di accessibilità di Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727608"
---
# <a name="accessibility"></a>Accessibilità #

Azure Media Player è compatibile con le funzionalità di lettura dello schermo, ad esempio Assistente vocale di Windows e VoiceOver di Apple OSX/iOS. Sono disponibili tag alternativi per i pulsanti dell'interfaccia utente che l'utilità per la lettura dello schermo è in grado di leggere quando vengono selezionati dall'utente. Altre configurazioni possono essere impostate al livello del sistema operativo.

## <a name="captions-and-subtitles"></a>Sottotitoli ##

Attualmente Azure Media Player supporta i sottotitoli solo per eventi on demand in formato WebVTT ed eventi live tramite CEA-708. Il formato TTML non è attualmente supportato. I sottotitoli permettono di raggiungere più destinatari, ad esempio utenti con disabilità uditive o che vogliono leggere il testo in una lingua diversa. I sottotitoli aumentano anche il coinvolgimento nel video, migliorano la comprensione e permettono di guardarlo anche in ambienti come i luoghi di lavoro, dove normalmente un volume elevato non è permesso.  

## <a name="high-contrast-mode"></a>Modalità a contrasto elevato ##

L'interfaccia utente predefinita di Azure Media Player è conforme alla maggior parte delle modalità di visualizzazione a contrasto elevato di dispositivi e browser. Le configurazioni possono essere impostate al livello del sistema operativo.

## <a name="mobility-options"></a>Opzioni per la mobilità ##

### <a name="tabbing-focus"></a>Spostamento dello stato attivo tramite tabulazione ###

Lo spostamento dello stato attivo tramite tabulazione, fornito dagli standard HTML generali, è disponibile in Azure Media Player. Per abilitare questa funzionalità, è necessario aggiungere `tabindex=0` (o un altro valore se si conosce l'effetto dell'ordine di tabulazione in HTML) al codice HTML `<video>` in questo modo: `<video ... tabindex=0>...</video>`. In alcune piattaforme lo stato attivo per i controlli può essere presente solo se i controlli sono visibili e se la piattaforma supporta queste funzionalità.

Una volta abilitata la funzionalità di spostamento dello stato attivo tramite tabulazione, l'utente finale può esplorare e controllare efficacemente il lettore video senza dipendere dal mouse. È possibile passare a ogni menu di scelta rapida o elemento controllabile premendo TAB e selezionarlo tramite INVIO o con la barra spaziatrice. Premendo INVIO o la barra spaziatrice in un menu di scelta rapida, quest'ultimo si espande e l'utente finale può continuare a spostarsi tramite tabulazione fino a selezionare una voce di menu. Una volta raggiunto il contesto della voce da selezionare, è sufficiente premere di nuovo INVIO o la barra spaziatrice per completare la selezione.

### <a name="hotkeys"></a>Tasti di scelta rapida ###

Azure Media Player supporta il controllo tramite tasti di scelta rapida. In un Web browser l'unico modo per controllare l'elemento video sottostante consiste nello spostare lo stato attivo sul lettore. Quando il lettore riceve lo stato attivo, è possibile controllarne le funzionalità con i tasti di scelta rapida.  La tabella seguente descrive i vari tasti di scelta rapida e il relativo comportamento:

| Tasto di scelta rapida              | Comportamento                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | Il lettore entra/esce dalla modalità a schermo intero                                  |
| M/m                  | Il volume del lettore viene disattivato/riattivato                                          |
| Frecce SU e GIÙ    | Il volume del lettore aumenta/diminuisce                                    |
| Frecce DESTRA e SINISTRA | L'avanzamento del video aumenta/diminuisce                                  |
| 0,1,2,3,4,5,6,7,8,9  | L'avanzamento del video può essere impostato su 0%\- 90% a seconda del tasto premuto |
| Fare clic su Azione         | Il video viene riprodotto/sospeso                                                   |

## <a name="next-steps"></a>Passaggi successivi

<!---Some context for the following links goes here--->
- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)