---
title: "Come usare Blitline per l'elaborazione delle immagini - Guida alle funzionalità di Azure"
description: Informazioni su come usare il servizio Blitline per elaborare immagini all'interno di un'applicazione Azure.
services: 
documentationcenter: .net
author: blitline-dev
manager: jason@blitline.com
editor: jason@blitline.com
ms.assetid: 6c711248-0e52-4895-ba9e-8395628de924
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2014
ms.author: support@blitline.com
ms.openlocfilehash: 1d90599e028b3407a513b04b878e3aefc39928a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blitline-with-azure-and-azure-storage"></a>Come utilizzare Blitline con Azure e l'archiviazione di Azure
In questa guida verrà descritto come accedere ai servizi Blitline e come inviare i processi a Blitline.

## <a name="what-is-blitline"></a>Informazioni su Blitline
Blitline è un servizio di elaborazione a livello aziendale delle immagini basate su cloud, dal costo nettamente inferiore a quello che comporterebbe la creazione autonoma delle stesse.

Il fatto è che l'elaborazione delle immagini è un'operazione ripetuta di continuo, di solito creandole da zero per ciascun sito Web. Ce ne rendiamo conto perché noi stessi le abbiamo create un milione di volte. Ad un certo punto abbiamo deciso che era probabilmente arrivato il momento di farlo per tutti. Disponiamo delle opportune competenze, della velocità e dell'efficienza necessaria, che ci consentono di offrire a tutti un enorme risparmio del carico di lavoro.

Per ulteriori informazioni, visitare il sito [http://www.blitline.com](http://www.blitline.com).

## <a name="what-blitline-is-not"></a>Operazioni NON eseguite da Blitline
Allo scopo di chiarire l'utilità di Blitline è spesso più semplice identificare le operazioni che Blitline NON esegue prima di procedere oltre.

* Blitline NON dispone di widget HTML per caricare le immagini. Affinché Blitline possa ottenerle, le immagini devono essere disponibili pubblicamente oppure con autorizzazioni limitate.
* Blitline NON esegue l'elaborazione delle immagini attive come Aviary.com
* Blitline NON accetta i caricamenti delle immagini; non è possibile eseguire il push delle immagini direttamente su Blitline. È necessario eseguire il push delle immagini nell'archiviazione di Azure o in altri luoghi supportati da Blitline, quindi informare il software della posizione in cui recuperarle.
* Blitline opera principalmente in parallelo e NON esegue alcuna elaborazione sincrona; vale a dire che l'utente deve comunicare un postback_url affinché sia possibile avvisarlo del termine dell'elaborazione.

## <a name="create-a-blitline-account"></a>Creare un account Blitline
[!INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a name="how-to-create-a-blitline-job"></a>Come creare un processo di Blitline
Blitline utilizza JSON per definire le azioni da intraprendere riguardo un'immagine. Il codice JSON è composto da alcuni semplici campi:

L'esempio più semplice è riportato di seguito:

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

In questo caso, il codice JSON prende un'immagine "src" ("...boys.jpeg") e la ridimensiona a 240x140.

L'ID applicazione è un elemento reperibile nella scheda **INFORMAZIONI DI CONNESSIONE** o **GESTISCI** in Azure. È l'identificatore segreto che consente di eseguire i processi su Blitline.

Il parametro "save" consente di identificare le informazioni sulla posizione in cui si desidera inserire l'immagine dopo che è stata elaborata. In questo caso, la posizione non è stata definita. Se non viene definita alcuna posizione, Blitline archivierà l'immagine in locale (temporaneamente) in una posizione univoca sul cloud. Sarà possibile ottenere tale posizione dal codice JSON restituito da Blitline. Per identificare questa particolare immagine salvata è necessario l'identificatore "image" che viene restituito all'utente.

Altre informazioni sulle *funzioni* supportate sono disponibili qui: <http://www.blitline.com/docs/functions>

Inoltre la documentazione sulle opzioni dei processi è disponibile qui: <http://www.blitline.com/docs/api>

Dopo aver ottenuto il codice JSON, basta solo pubblicarlo come **POST** su `http://api.blitline.com/job`

Si otterrà un codice JSON che avrà più o meno l'aspetto seguente:

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


Il codice informa che Blitline ha ricevuto la richiesta, l'ha inserita in una coda di elaborazione e al termine dell'operazione l'immagine sarà disponibile all'indirizzo: **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_APP\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a name="how-to-save-an-image-to-your-azure-storage-account"></a>Come salvare un'immagine nell'account di archiviazione di Azure
Se si dispone di un account di archiviazione di Azure è possibile eseguire facilmente il push delle immagini elaborate in Blitline nel contenitore di Azure. Aggiungendo il frammento "azure_destination" è possibile definire la posizione e le autorizzazioni per il push di Blitline.

Di seguito è fornito un esempio:

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


Sostituendo i valori IN MAIUSCOLO con i propri, sarà possibile inviare questo codice JSON a http://api.blitline.com/job; l'immagine "src" verrà elaborata con un filtro sfocatura, quindi sottoposta a push verso la destinazione di Azure.

### <a name="please-note"></a>Nota bene:
SAS deve contenere l'intero URL di SAS, incluso il nome file del file di destinazione.

Esempio:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


È anche possibile leggere l'ultima edizione dei documenti sull'archiviazione di Azure di Blitline [qui](http://www.blitline.com/docs/azure_storage).

## <a name="next-steps"></a>Passaggi successivi
Visitare blitline.com per informazioni su tutte le altre funzionalità:

* Documenti sugli endpoint API di Blitline <http://www.blitline.com/docs/api>
* Funzioni delle API di Blitline <http://www.blitline.com/docs/functions>
* Esempi delle API di Blitline <http://www.blitline.com/docs/examples>
* Libreria NuGet di terze parti <http://nuget.org/packages/Blitline.Net>

