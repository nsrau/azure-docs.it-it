---
title: Gestire i criteri di registrazione-Azure
description: In questo argomento viene illustrato come gestire i criteri di registrazione.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 321e68087bfe2a8b3e1354e49585a89f9d3af295
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260724"
---
# <a name="manage-recording-policy"></a>Gestire i criteri di registrazione

È possibile usare l'analisi video in tempo reale su IoT Edge per la [registrazione video continua](continuous-video-recording-concept.md), in cui è possibile registrare video nel cloud per settimane o mesi. È possibile gestire la lunghezza (in giorni) dell'archivio cloud usando gli strumenti di [gestione del ciclo](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) di vita incorporati in archiviazione di Azure.  

L'account di servizi multimediali è collegato a un account di archiviazione di Azure e, quando si registra un video nel cloud, il contenuto viene scritto in un [Asset](../latest/assets-concept.md)di servizi multimediali. Ogni asset è mappato a un contenitore nell'account di archiviazione. il ciclo di vita di gestione consente di definire un [criterio](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal#policy) per un account di archiviazione, in cui è possibile specificare una [regola](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal#rules) come la seguente.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

Regola precedente:

* Si applica a tutti i BLOB in blocchi nell'account di archiviazione.
* Specifica che quando i BLOB superano i 30 giorni, vengono spostati dal [livello di accesso frequente a](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)sporadico.
* E quando i BLOB superano i 90 giorni, devono essere eliminati.

Dal momento che analisi video live archivia il video in unità di tempo specificate, l'asset conterrà una serie di BLOB, un BLOB per segmento. Quando i criteri di gestione del ciclo di vita avviano ed eliminano i BLOB meno recenti, si continuerà a essere in grado di accedere e riprodurre i BLOB rimanenti tramite le API del servizio multimediale. Per ulteriori informazioni, vedere [riproduzione delle registrazioni](playback-recordings-how-to.md). 

## <a name="limitations"></a>Limitazioni

Di seguito sono elencate alcune limitazioni note relative alla gestione del ciclo di vita:

* È possibile avere al massimo 100 regole all'interno del criterio e ogni regola può specificare fino a 10 contenitori. Quindi, se era necessario disporre di criteri di registrazione diversi (ad esempio, l'archivio di 3 giorni per la fotocamera con il parcheggio, 30 giorni per la fotocamera nel Dock di caricamento e 180 giorni per la fotocamera dietro il contatore di checkout), con un account del servizio multimediale è possibile personalizzare le regole per al massimo 1000 fotocamere.
* Gli aggiornamenti dei criteri di gestione del ciclo di vita non sono immediato. Per ulteriori informazioni, vedere [la sezione Domande frequenti](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal#faq) .
* Se si sceglie di applicare un criterio in cui i BLOB vengono spostati nel livello di accesso sporadico, potrebbe essere interessata la riproduzione della parte dell'archivio. È possibile che vengano visualizzate latenze aggiuntive o errori sporadici. Servizi multimediali non supporta la riproduzione del contenuto nel livello archivio.

## <a name="next-steps"></a>Passaggi successivi

[Riproduzione di registrazioni](playback-recordings-how-to.md)
