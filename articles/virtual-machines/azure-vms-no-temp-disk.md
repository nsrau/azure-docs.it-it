---
title: Domande frequenti sulle dimensioni delle macchine virtuali di Azure senza disco temporaneo locale
description: Questo articolo fornisce le risposte alle domande frequenti sulle dimensioni Microsoft Azure VM che non dispongono di un disco temporaneo locale.
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.author: brbell
ms.reviewer: mimckitt
ms.date: 06/15/2020
ms.openlocfilehash: 413f53feedc4fee0877694e3f3a3a509c4d38001
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84783540"
---
# <a name="azure-vm-sizes-with-no-local-temporary-disk"></a>Dimensioni delle macchine virtuali di Azure senza disco temporaneo locale 
Questo articolo fornisce le risposte alle domande frequenti sulle dimensioni delle macchine virtuali di Azure che non dispongono di un disco temporaneo locale (ovvero senza una scrivania temporanea locale). Per altre informazioni su queste dimensioni delle macchine virtuali, vedere [specifiche per DV4 e serie Dsv4 (carichi di lavoro per utilizzo generico)](dv4-dsv4-series.md) o [specifiche per Ev4 e serie Esv4 (carichi di lavoro con ottimizzazione per la memoria)](ev4-esv4-series.md).

> [!IMPORTANT]
> Le dimensioni delle VM DV4, Dsv4, Ev4 e Esv4 sono ora disponibili in anteprima pubblica. Per iscriversi all'anteprima pubblica, compilare questo [modulo](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 

## <a name="what-does-no-local-temp-disk-mean"></a>Che cosa significa nessun disco temporaneo locale? 
Tradizionalmente, le dimensioni delle macchine virtuali, ad esempio Standard_D2s_v3, Standard_E48_v3, includono un piccolo disco locale, ad esempio un'unità D:. Ora, con le nuove dimensioni delle macchine virtuali, il disco locale di dimensioni ridotte non esiste più; Tuttavia, è comunque possibile aggiungere HDD Standard, SSD Premium o Ultra SSD.

## <a name="what-if-i-still-want-local-temp-disk"></a>Che cosa succede se si vuole ancora il disco temporaneo locale?
Se il carico di lavoro richiede un disco temporaneo locale, sono disponibili anche nuove dimensioni di VM [Ddv4 e Ddsv4](ddv4-ddsv4-series.md) o [Edv4 e Edsv4](edv4-edsv4-series.md) . Queste dimensioni offrono un disco temporaneo 50% superiore rispetto alle dimensioni V3 precedenti.

> [!NOTE]
> Il disco temporaneo locale non è persistente; per assicurarsi che i dati siano persistenti, usare le opzioni HDD Standard, SSD Premium o Ultra SSD. 

## <a name="what-are-the-differences-between-these-new-vm-sizes-and-the-general-purpose-dv3dsv3-or-the-memory-optimized-ev3esv3-vm-sizes-that-i-am-used-to"></a>Quali sono le differenze tra le nuove dimensioni delle macchine virtuali e le dimensioni per utilizzo generico dv3/Dsv3 o le VM con ottimizzazione per la memoria EV3/Esv3 a cui si è abituati? 
| V3 famiglie VM con disco temporaneo locale   | Nuove famiglie V4 con disco temporaneo locale | Nuove famiglie V4 senza disco temporaneo locale |
|---|---|---|
| Dv3   | Ddv4 | DV4 |
| Dsv3 | Ddsv4  | Dsv4 |
| EV3   | Edv4  | Ev4 |
| Esv3 | Edsv4 |    Esv4 | 

## <a name="can-i-resize-a-vm-size-that-has-a-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>È possibile ridimensionare le dimensioni di una macchina virtuale con un disco temporaneo locale a una macchina virtuale senza disco temporaneo locale?  
No. Le uniche combinazioni consentite per il ridimensionamento sono: 

1. VM (con disco temporaneo locale)-> VM (con disco temporaneo locale); e 
2. VM (senza disco temporaneo locale)-> VM (senza disco temporaneo locale). 

> [!NOTE]
> Se un'immagine dipende dal disco della risorsa o se nel disco temporaneo locale è presente un pagefile o file, le immagini senza dischi non funzioneranno, bensì utilizzeranno l'alternativa con il disco. 

## <a name="do-these-vm-sizes-support-both-linux-and-windows-operating-systems-os"></a>Queste dimensioni di VM supportano sia i sistemi operativi Linux che Windows (OS)?
Sì.

## <a name="will-this-break-my-custom-scripts-custom-images-or-os-images-that-have-scratch-files-or-page-files-on-a-local-temp-disk"></a>Questa operazione interromperà gli script personalizzati, le immagini personalizzate o le immagini del sistema operativo che contengono file o file di paging in un disco temporaneo locale?
Se l'immagine del sistema operativo personalizzata punta al disco temporaneo locale, l'immagine potrebbe non funzionare correttamente con questa dimensione senza disco.

## <a name="have-questions-or-feedback"></a>Altre domande, commenti o suggerimenti?
Compilare il [modulo di feedback]( https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRUMzdCQkw0OVVRTldFUUtXSTlLQVBPUkVHSy4u). 

## <a name="next-steps"></a>Passaggi successivi 
In questo documento sono state illustrate le domande più frequenti relative alle macchine virtuali di Azure con il disco temporaneo locale. Per altre informazioni su queste dimensioni delle macchine virtuali, vedere gli articoli seguenti:

- [Specifiche per DV4 e serie Dsv4 (carico di lavoro per utilizzo generico)](dv4-dsv4-series.md)
- [Specifiche per Ev4 e serie Esv4 (carico di lavoro con ottimizzazione per la memoria)](ev4-esv4-series.md)
