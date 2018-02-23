---
title: Inviare eventi a Hub eventi di Azure usando C | Microsoft Docs
description: Inviare eventi a Hub di eventi di Azure usando C
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 12/4/2017
ms.author: sethm
ms.openlocfilehash: 2b714c5de96a8fb7ed66a30c62daaa38b84fdc5b
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>Inviare eventi a Hub di eventi di Azure usando C

## <a name="introduction"></a>Introduzione
Hub eventi è un sistema di inserimento a scalabilità elevata, in grado di inserire milioni di eventi al secondo, che permette a un'applicazione di elaborare e analizzare le elevate quantità di dati prodotti dalle applicazioni e dai dispositivi connessi. Dopo la raccolta in un hub eventi, è possibile trasformare e archiviare i dati usando qualsiasi provider di analisi in tempo reale o un cluster di archiviazione.

Per altre informazioni, vedere [Panoramica di Hub eventi][Panoramica di Hub eventi].

Questa esercitazione illustra come inviare eventi a un hub eventi usando un'applicazione console in C. Per informazioni su come ricevere gli eventi, fare clic sul linguaggio di ricezione appropriato nel sommario a sinistra.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Ambiente di sviluppo in C. Questa esercitazione presuppone l'uso di uno stack gcc in una VM Linux di Azure con Ubuntu 14.04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* Un account Azure attivo. Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="send-messages-to-event-hubs"></a>Inviare messaggi all'hub eventi
In questa sezione viene illustrato come scrivere un'app C per inviare eventi all'hub eventi. Il codice usa la libreria Proton AMQP dal [progetto Apache Qpid](http://qpid.apache.org/). Il procedimento è simile a quello adottato per l'uso di code e argomenti del bus di servizio con AMQP da C, come illustrato [in questo esempio](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Per altre informazioni, vedere la [documentazione di Qpid Proton](http://qpid.apache.org/proton/index.html).

1. Dalla [pagina di Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html) seguire le istruzioni per l'installazione di Qpid Proton a seconda dell'ambiente corrente.
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
5. Nella directory di lavoro creare un nuovo file denominato **sender.c** con il codice seguente. Ricordare di sostituire i valori per la chiave o il nome della firma di accesso condiviso, il nome dell'hub eventi e lo spazio dei nomi. È anche necessario sostituire una versione codificata con URL della chiave per l'elemento **SendRule** creato in precedenza. È possibile creare la versione codificata con URL [qui](http://www.w3schools.com/tags/ref_urlencode.asp).
   
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
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
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
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
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
    > Questo codice usa una finestra in uscita pari a 1 per imporre un invio dei messaggi il più rapido possibile. È opportuno che l'applicazione provi a inviare i messaggi in batch per aumentare la velocità effettiva. Vedere la [pagina di Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html) per informazioni sull'uso della libreria Qpid Proton in questo e in altri ambienti e anche nelle piattaforme per le quali sono disponibili associazioni, al momento Perl, PHP, Python e Ruby.


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
