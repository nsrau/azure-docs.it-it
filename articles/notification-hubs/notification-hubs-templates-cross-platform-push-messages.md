---
title: Modelli
description: In questo argomento vengono illustrati i modelli per Hub di notifica di Azure.
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: 
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 1ca24a4bf08ecdbe1c1e47a931613144309a04a9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="templates"></a>Modelli
## <a name="overview"></a>Panoramica
I modelli consentono alle applicazioni client di specificare il formato esatto delle notifiche da ricevere. Mediante i modelli, le app possono ottenere diversi vantaggi, fra cui i seguenti:

* Un back-end indipendente dalla piattaforma
* Notifiche personalizzate
* Indipendenza dalla versione del client
* Localizzazione semplice

In questa sezione vengono forniti due esempi dettagliati di come utilizzare modelli per inviare notifiche indipendenti dalla piattaforma destinate a i dispositivi di tutte le piattaforme e per personalizzare la notifica di trasmissione per ciascun dispositivo.

## <a name="using-templates-cross-platform"></a>Utilizzo di modelli multipiattaforma
Il metodo standard per inviare notifiche push consiste nell'inviare, per ciascuna notifica, uno specifico payload dei servizi di notifica della piattaforma (WNS, servizio APN). Per inviare un avviso al servizio APN, ad esempio, il payload è un oggetto Json nel formato seguente:

    {"aps": {"alert" : "Hello!" }}

Per inviare un messaggio di avviso popup simile in un'applicazione di Windows Store, il payload XML è il seguente:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">Hello!</text>
        </binding>
      </visual>
    </toast>

È possibile creare payload simili per MPNS (Windows Phone) e piattaforme GCM (Android).

Tale requisito impone al back-end dell'app la produzione di payload diversi per ciascuna piattaforma e di fatto rende il back-end responsabile di parte del livello di presentazione dell'app. Alcuni dei problemi riguardano la localizzazione e i layout grafici (soprattutto per applicazioni di Windows Store che comprendono notifiche per vari tipi di riquadri).

La funzionalità del modello degli Hub di notifica consente a un'app client di creare registrazioni speciali, denominate registrazioni con modello, comprendenti un modello, oltre al set di tag. La funzionalità del modello Hub di notifica consente a un'app client di associare i dispositivi con i modelli, indipendentemente dall'utilizzo di installazioni (scelta consigliata) o di registrazioni. Dati gli esempi di payload precedenti, le sole informazioni indipendenti dalla piattaforma consistono nel messaggio di avviso effettivo (Hello!). Un modello è un set di istruzioni per l'Hub di notifica circa la modalità di formattazione di un messaggio indipendente dalla piattaforma per la registrazione di una determinata app client. Nell'esempio precedente, il messaggio indipendente dalla piattaforma consiste in una singola proprietà: **message = Hello!**.

Nell'immagine seguente viene illustrato il processo indicato in precedenza:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

Il modello per la registrazione dell'app client iOS è il seguente:

    {"aps": {"alert": "$(message)"}}

Il modello corrispondente per l'applicazione client di Windows Store è:

    <toast>
        <visual>
            <binding template=\"ToastText01\">
                <text id=\"1\">$(message)</text>
            </binding>
        </visual>
    </toast>

Si noti che il messaggio effettivo viene sostituito dall'espressione $(message). Tale espressione indica all'Hub di notifica, ogni volta che viene inviato un messaggio a quella particolare registrazione, di creare un messaggio successivo, commutandolo nel valore comune.

Se si utilizza il modello di installazione, la chiave dei "modelli" di installazione contiene un JSON composto da più modelli. Se si utilizza il modello di registrazione, l'applicazione client può creare più registrazioni per poter utilizzare più modelli: ad esempio, un modello per i messaggi di avviso e un modello per gli aggiornamenti dei riquadri. Inoltre, le applicazioni client possono unire registrazioni native (registrazioni senza modello) e registrazioni con modello.

L'Hub di notifica invia una notifica per ciascun modello senza considerare se appartengono alla stessa app client. Questo comportamento può essere utilizzato per convertire notifiche indipendenti dalla piattaforma in più notifiche. Ad esempio, lo stesso messaggio indipendente dalla piattaforma all'Hub di notifica può essere facilmente convertito in un avviso popup e in un aggiornamento del riquadro, senza essere rilevato dal back-end. Si noti che alcune piattaforme (ad esempio, iOS) potrebbero comprimere più notifiche allo stesso dispositivo se queste vengono inviate in un breve periodo di tempo.

## <a name="using-templates-for-personalization"></a>Utilizzo di modelli per la personalizzazione
Un altro vantaggio derivante dall'utilizzo di modelli è la possibilità di utilizzare gli Hub di notifica per eseguire la personalizzazione delle notifiche per registrazione. Si consideri ad esempio un'app meteo che visualizza un riquadro sulle condizioni meteorologiche relative a un luogo specifico. Un utente può scegliere tra gradi Celsius o Fahrenheit e una previsione singola o di cinque giorni. Mediante i modelli, ciascuna installazione dell'app client può registrare il formato richiesto (1 giorno in gradi Celsius 1 giorno in gradi Fahrenheit, 5 giorni in gradi Celsius, 5 giorni in gradi Fahrenheit), e far sì che il back-end invii un unico messaggio contenente tutte le informazioni necessarie per compilare i modelli (ad esempio una previsione di cinque giorni con gradi Celsius e Fahrenheit).

Il modello per la previsione di un giorno in gradi Celsius è il seguente:

    <tile>
      <visual>
        <binding template="TileWideSmallImageAndText04">
          <image id="1" src="$(day1_image)" alt="alt text"/>
          <text id="1">Seattle, WA</text>
          <text id="2">$(day1_tempC)</text>
        </binding>  
      </visual>
    </tile>

Il messaggio inviato all'Hub di notifica contiene tutte le proprietà seguenti:

<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>

Utilizzando questo modello, il back-end invia solo un unico messaggio senza dover memorizzare opzioni di personalizzazione specifiche per gli utenti dell'app. Nell'immagine seguente viene illustrato tale scenario:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Come registrare i modelli
Per la registrazione dei modelli mediante il modello di installazione (scelta consigliata) o il modello di registrazione, vedere [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Linguaggio di espressione dei modelli
I modelli sono limitati ai formati di documento XML o JSON. Inoltre, è possibile inserire solo le espressioni in particolari punti: ad esempio, attributi dei nodi o valori per il formato XML, valori delle proprietà di stringa per il formato JSON.

Nella tabella seguente viene descritto il linguaggio consentito nei modelli:

| Espressione | Descrizione |
| --- | --- |
| $(prop) |Riferimento a una proprietà di evento con il nome specificato. I nomi delle proprietà non distinguono tra maiuscole e minuscole. Questa espressione viene risolta nel valore di testo della proprietà o in una stringa vuota se la proprietà non è presente. |
| $(prop, n) |Come in precedenza, ma il testo viene esplicitamente troncato dopo n caratteri, ad esempio $(title, 20) tronca il contenuto della proprietà del riquadro dopo 20 caratteri. |
| .(prop, n) |Come in precedenza, ma vengono aggiunti tre punti alla fine del testo troncato. La dimensione totale della stringa troncata e del suffisso non supera n caratteri. .(title, 20) con una proprietà di input "Questa è la riga del titolo" restituisce **Questa è la riga...** |
| %(prop) |È simile a $(name), a eccezione del fatto che l'output è codificato in formato URI. |
| #(prop) |Utilizzata nei modelli JSON (ad esempio, per modelli iOS e Android).<br><br>Questa funzione opera esattamente come l'espressione $(prop) specificata in precedenza, salvo quando viene utilizzata nei modelli JSON (ad esempio, nei modelli Apple). In questo caso, se questa funzione non è racchiusa tra "{','}", ad esempio 'myJsonProperty' : '#(name)', e restituisce un numero in formato Javascript, ad esempio regexp: (0&#124;(&#91;1-9&#93;&#91;0-9&#93;*))(\.&#91;0-9&#93;+)?((e&#124;E)(+&#124;-)?&#91;0-9&#93;+)?, l'output JSON sarà un numero.<br><br>Ad esempio, ' badge : '#(name)' diventa 'badge' : 40 (e non '40'). |
| 'text' o "text" |Valore letterale. I valori letterali contengono testo arbitrario racchiuso tra virgolette singole o doppie. |
| expr1 + expr2 |Operatore di concatenazione che unisce due espressioni in una singola stringa. |

Il formato delle espressioni può essere uno dei precedenti.

Quando si utilizza la concatenazione, l'intera espressione deve essere racchiusa tra parentesi graffe {}. Ad esempio, {$(prop) + ' - ' + $(prop2)}. |

Ad esempio, il modello XML seguente non è valido:

    <tile>
      <visual>
        <binding $(property)>
          <text id="1">Seattle, WA</text>
        </binding>  
      </visual>
    </tile>


Come spiegato in precedenza, quando si utilizza la concatenazione, le espressioni devono essere racchiuse tra parentesi graffe. Ad esempio:

    <tile>
      <visual>
        <binding template="ToastText01">
          <text id="1">{'Hi, ' + $(name)}</text>
        </binding>  
      </visual>
    </tile>

