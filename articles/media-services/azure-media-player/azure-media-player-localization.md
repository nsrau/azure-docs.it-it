---
title: Azure Media Player Localization
description: Supporto in più lingue per utenti di impostazioni locali diverse dall'inglese.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 34805c4eaae5d969fc2338c24f9f92404e065d15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727177"
---
# <a name="localization"></a>Localizzazione #

Il supporto in più lingue consente agli utenti di impostazioni locali diverse dall'inglese di interagire in modo nativo con il lettore. Azure Media Player creerà un'istanza con un dizionario globale di lingue, che localizzerà i messaggi di errore in base alla lingua della pagina. Uno sviluppatore può anche creare un'istanza del lettore con una lingua impostata forzata. La lingua predefinita è l'inglese (en).

> [!NOTE]
> Questa funzione è ancora in fase di test e come tale è soggetta a bug.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player supporta attualmente le lingue seguenti con i codici lingua corrispondenti:Azure Media Player currently supports the following languages with their corresponding language codes:

| Linguaggio            | Codice | Linguaggio                | Codice   | Linguaggio                | Codice         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Inglese : predefinito   | en   | Croato                | hr     | Rumeno                | ro           |
| Arabo              | ar   | Ungherese               | hu     | Slovacco                  | sk           |
| Bulgaro           | bg   | Indonesiano              | id     | Sloveno                 | sl           |
| Catalano             | Ca   | Islandese               | is     | Serbo - Alfabeto cirillico      | sr-cyrl-cs   |
| Ceco               | cs   | Italiano                 | it     | Serbo - Alfabeto latino         | sr-latn-rs   |
| Danese              | da   | Giapponese                | ja     | Russo                 | ru           |
| Tedesco              | de   | Kazako                  | Kk     | Svedese                 | sv           |
| Greco               | el   | Coreano                  | ko     | Thai                    | th           |
| Spagnolo             | es   | Lituano              | lt     | Tagalog                 | Tl           |
| Estone            | et   | Lettone                 | lv     | Turco                 | tr           |
| Basco              | Ue   | Malese               | ms     | Ucraino               | uk           |
| Farsi               | fa   | Norvegese - Bokmàl     | nb     | Urdu                    | La tua           |
| Finlandese             | fi   | Olandese                   | nl     | Vietnamita              | vi           |
| Francese              | fr   | Norvegese - Nynorsk     | nn     | Cinese - semplificato    | zh-hans      |
| Galiziano            | gl   | Polacco                  | pl     | Cinese - tradizionale   | zh-hant      |
| Ebraico              | he   | Portoghese (Brasile)     | pt-br  |                         |              |
| Hindi               | hu   | Portoghese (Portogallo)   | pt-pt  |                         |              |


> [!NOTE]
> Se non si desidera che si verifichi alcuna localizzazione è necessario forzare la lingua all'inglese

## <a name="next-steps"></a>Passaggi successivi ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)