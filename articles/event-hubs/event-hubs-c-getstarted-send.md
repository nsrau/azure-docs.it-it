---
title: Inviare eventi a Hub eventi di Azure usando C | Microsoft Docs
description: Inviare eventi a Hub di eventi di Azure usando C
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 01/30/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: fe331199333d492dbc42c9125c9da96a44066ee1
ms.openlocfilehash: a6d6db0e0e6b6fe8d8ccbd6cbd3858ae0df5cb2d

---

# <a name="send-events-to-azure-event-hubs-using-c"></a>Inviare eventi a Hub di eventi di Azure usando C

## <a name="introduction"></a>Introduzione
Hub eventi è un sistema di inserimento a scalabilità elevata, in grado di accettare milioni di eventi al secondo, che permette a un'applicazione di elaborare e analizzare le elevate quantità di dati prodotti dalle applicazioni e dai dispositivi connessi. Dopo la raccolta nell'hub eventi, i dati possono essere trasformati e archiviati tramite qualsiasi provider di analisi in tempo reale o qualsiasi cluster di archiviazione.

Per altre informazioni, vedere [Panoramica di Hub eventi][Event Hubs overview].

In questa esercitazione si apprenderà come inviare eventi a un Hub eventi usando un'applicazione console in C. Per ricevere gli eventi, fare clic sulla lingua di destinazione appropriata nel sommario a sinistra.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

* Ambiente di sviluppo in C. Per questa esercitazione si presuppone l'uso di uno stack gcc in una VM Linux in Azure con Ubuntu 14.04.
* Microsoft Visual Studio Express per Windows
* Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="send-messages-to-event-hubs"></a>Inviare messaggi all'hub eventi
In questa sezione si scriverà un'app C per inviare eventi all'hub eventi. Si userà la libreria Proton AMQP dal [progetto Apache Qpid](http://qpid.apache.org/). Il procedimento è simile a quello adottato per l'uso da C di code e argomenti del bus di servizio con AMQP, come illustrato [qui](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Per altre informazioni, vedere la [documentazione di Qpid Proton](http://qpid.apache.org/proton/index.html).

1. Dalla [pagina di Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html)fare clic sul collegamento per l' **installazione di Qpid Proton** e seguire le istruzioni a seconda del proprio ambiente.
2. Per compilare la libreria Proton, installare i seguenti pacchetti:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Scaricare la [libreria Qpid Proton](http://qpid.apache.org/proton/index.html) e quindi estrarla, ad esempio:
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Creare una directory di compilazione, compilare e installare:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. Nella directory di lavoro creare un nuovo file denominato **sender.c** con il contenuto seguente. Ricordare di sostituire il valore per il nome del proprio hub eventi e quello per il nome dello spazio dei nomi (quest'ultimo è in genere `{event hub name}-ns`). È anche necessario sostituire una versione codificata con URL della chiave per l'elemento **SendRule** creato in precedenza. È possibile creare la versione codificata con URL [qui](http://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     
      {                                                                          
        if(pn_messenger_errno(messenger))                                        
        {                                                                        
          printf("check\n");                                                     
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); 
        }                                                                        
      }  
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Compilare il file (si presuppone **gcc**):
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > In questo codice viene usata una finestra in uscita pari a 1 per imporre un invio dei messaggi il più rapido possibile. In generale l'applicazione dovrebbe cercare di riunire i messaggi in batch per migliorare la velocità effettiva. Vedere la [pagina di Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html) per altre informazioni sull'uso della libreria Qpid Proton in questo e in altri ambienti e anche nelle piattaforme per le quali sono fornite associazioni (al momento Perl, PHP, Python e Ruby).


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Create an Event Hub](event-hubs-create.md) (Creare un Hub eventi)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Feb17_HO1-->


