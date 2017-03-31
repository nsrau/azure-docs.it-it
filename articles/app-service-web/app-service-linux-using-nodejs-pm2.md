---
title: Uso della configurazione PM2 per Node.js nelle app Web in Linux | Documentazione Microsoft
description: Uso della configurazione PM2 per Node.js nelle app Web in Linux
keywords: Servizio app di Azure, app Web, nodejs, PM2, Linux, OSS
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: fb420f32-6d74-49c7-992f-0ed5616e66e7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: c4af07e79ae066f916c15aa239cb5dfdd3fef2a8
ms.lasthandoff: 02/17/2017


---
# <a name="use-pm2-configuration-for-nodejs-in-web-apps-on-linux"></a>Usare la configurazione PM2 per Node.js nelle app Web in Linux
Se si imposta lo stack di applicazioni su Node.js per le app Web in Linux, è possibile impostare un file di avvio in Node.js, come illustrato nell'immagine seguente:

![Impostare un file di avvio Node.js][1]

È possibile usare questa opzione per una delle attività seguenti:

* Specificare lo script di avvio per l'app Node.js (ad esempio: /bin/server.js).
* Specificare il file di configurazione PM2 da usare per l'app Node.js (ad esempio: /foo/process.json).
  
  > [!NOTE]
  > Per riavviare automaticamente i processi Node.js quando vengono modificati determinati file, usare la configurazione PM2. In caso contrario, l'applicazione non verrà riavviata quando riceve notifiche di modifica, ad esempio quando viene modificato il codice dell'applicazione.
  > 
  > 

È possibile vedere la [documentazione del file di processo](http://pm2.keymetrics.io/docs/usage/application-declaration/) per tutte le opzioni. Il seguente è un esempio di codice che è possibile usare come file process.json:

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

In questa configurazione è importante notare quanto segue:

* La proprietà "script" specifica lo script di avvio dell'applicazione.
* La proprietà "instances" specifica quante istanze del processo del nodo avviare. Se si esegue l'applicazione in VM di dimensioni maggiori con più core, è consigliabile ottimizzare le risorse impostando un valore più elevato qui.
* La matrice "watch" specifica tutti i file per cui si vuole riavviare il processo del nodo quando vengono modificati.
* Per "watch_options", attualmente è necessario impostare "usePolling" su true a causa del modo in cui viene montato il contenuto dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
* [What is App Service on Linux?](app-service-linux-intro.md) (Che cos'è Servizio app in Linux?)
* [Azure App Service Web Apps on Linux FAQ](app-service-linux-faq.md) (App Web del Servizio app di Azure nelle domande frequenti su Linux)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png

