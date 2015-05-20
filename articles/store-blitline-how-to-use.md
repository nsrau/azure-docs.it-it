<properties 
	pageTitle="Come usare Blitline per l&#39;elaborazione delle immagini - Guida alle funzionalità di Azure" 
	description="Informazioni su come usare il servizio Blitline per elaborare immagini all&#39;interno di un&#39;applicazione Azure." 
	services="" 
	documentationCenter=".net" 
	authors="blitline-dev" 
	manager="jason@blitline.com" 
	editor="jason@blitline.com"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/09/2014" 
	ms.author="support@blitline.com"/>






# Come utilizzare Blitline con Azure e l'archiviazione di Azure

In questa guida verrà descritto come accedere ai servizi Blitline e come inviare i processi a Blitline.

## Sommario

[Informazioni su Blitline][] [Operazioni NON eseguite da Blitline][] [Creazione di un account Blitline][] [Come creare un processo di Blitline][] [Come salvare un'immagine nell'archiviazione di Azure][] [Passaggi successivi][]

## <a id="whatis"></a>Informazioni su Blitline

Blitline è un servizio di elaborazione a livello aziendale delle immagini basate su cloud, dal costo nettamente inferiore a quello che comporterebbe la creazione autonoma delle stesse.

Il fatto è che l'elaborazione delle immagini è un'operazione ripetuta di continuo, di solito creandole da zero per ciascun sito Web. Ce ne rendiamo conto perché noi stessi le abbiamo create un milione di volte. Ad un certo punto abbiamo deciso che era probabilmente arrivato il momento di farlo per tutti. Disponiamo delle opportune competenze, della velocità e dell'efficienza necessaria, che ci consentono di offrire a tutti un enorme risparmio del carico di lavoro.

Per ulteriori informazioni, visitare il sito [http://www.blitline.com](http://www.blitline.com).

## <a id="whatisnot"></a>Operazioni NON eseguite da Blitline

Allo scopo di chiarire l'utilità di Blitline è spesso più semplice identificare le operazioni che Blitline NON esegue prima di procedere oltre.

- Blitline NON dispone di widget HTML per caricare le immagini. Affinché Blitline possa ottenerle, le immagini devono essere disponibili pubblicamente oppure con autorizzazioni limitate.

- Blitline NON esegue l'elaborazione delle immagini attive come Aviary.com

- Blitline NON accetta i caricamenti delle immagini; non è possibile eseguire il push delle immagini direttamente su Blitline. È necessario eseguire il push delle immagini nell'archiviazione di Azure o in altri luoghi supportati da Blitline, quindi informare il software della posizione in cui recuperarle.

- Blitline opera principalmente in parallelo e NON esegue alcuna elaborazione sincrona; vale a dire che l'utente deve comunicare un postback\_url affinché sia possibile avvisarlo del termine dell'elaborazione.

## <a id="createaccount"></a>Creazione di un account Blitline

[AZURE.INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a id="createjob"></a>Come creare un processo di Blitline

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

In questo caso, il codice JSON prende un'immagine "src" \("...boys.jpeg"\) e la ridimensiona a 240x140.

L'ID applicazione è un elemento reperibile nella scheda **CONNECTION INFO** o **MANAGE** su Azure. È l'identificatore segreto che consente di eseguire i processi su Blitline.

Il parametro "save" consente di identificare le informazioni sulla posizione in cui si desidera inserire l'immagine dopo che è stata elaborata. In questo caso, la posizione non è stata definita. Se non viene definita alcuna posizione, Blitline archivierà l'immagine in locale \(temporaneamente\) in una posizione univoca sul cloud. Sarà possibile ottenere tale posizione dal codice JSON restituito da Blitline. Per identificare questa particolare immagine salvata è necessario l'identificatore "image" che viene restituito all'utente.

Ulteriori informazioni sulle *funzioni* supportate sono disponibili qui: <http://www.blitline.com/docs/functions>

È inoltre disponibile la documentazione sulle opzioni dei processi qui: <http://www.blitline.com/docs/api>

Dopo aver ottenuto il codice JSON, basta solo pubblicarlo come **POST** su `http://api.blitline.com/jobs`

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


Il codice informa che Blitline ha ricevuto la richiesta, inserendola in una coda di elaborazione, e che al termine l'immagine sarà disponibile all'indirizzo: **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_APP\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a id="saveazure"></a>Come salvare un'immagine nell'account di archiviazione di Azure

Se si dispone di un account di archiviazione di Azure è possibile eseguire facilmente il push delle immagini elaborate in Blitline nel contenitore di Azure. Aggiungendo il frammento "azure\_destination" è possibile definire la posizione e le autorizzazioni per il push di Blitline.

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


Sostituendo i valori IN MAIUSCOLO con i propri, sarà possibile inviare questo codice JSON a http://api.blitline.com/job. L’immagine "src" verrà elaborata con un filtro sfocatura e quindi sottoposta a push verso la destinazione di Azure.

<h3>Nota bene:</h3>

SAS deve contenere l'intero URL di SAS, incluso il nome file del file di destinazione.

Esempio:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


È anche possibile leggere l'ultima edizione dei documenti sull'archiviazione di Azure di Blitline [qui](http://www.blitline.com/docs/azure_storage).


## <a id="nextsteps"></a>Passaggi successivi

Visitare blitline.com per informazioni su tutte le altre funzionalità:

* Documenti sugli endpoint API di Blitline <http://www.blitline.com/docs/api>
* Funzioni delle API di Blitline <http://www.blitline.com/docs/functions>
* Esempi delle API di Blitline <http://www.blitline.com/docs/examples>
* Libreria NuGet di terze parti <http://nuget.org/packages/Blitline.Net>


[Passaggi successivi]: #nextsteps
[Informazioni su Blitline]: #whatis
[Operazioni NON eseguite da Blitline]: #whatisnot
[Creazione di un account Blitline]: #createaccount
[Come creare un processo di Blitline]: #createjob
[Come salvare un'immagine nell'archiviazione di Azure]: #saveazure


<!--HONumber=52-->
