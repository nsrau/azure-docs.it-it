---
title: Frontdoor di Azure
description: Questo articolo fornisce un elenco delle condizioni di corrispondenza disponibili con il motore regole del servizio Frontdoor di Azure.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: d42b6b56f0cdd1f6ef2ea45b21a027f1b4c56b1c
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85322000"
---
# <a name="azure-front-door-rules-engine-match-conditions"></a>Condizioni di corrispondenza del motore regole di Frontdoor di Azure

Nel [motore regole di Frontdoor di Azure](front-door-rules-engine.md) una regola è costituita da zero o più condizioni di corrispondenza e da un'azione. Questo articolo fornisce descrizioni dettagliate delle condizioni di corrispondenza che è possibile usare nel motore regole di Frontdoor di Azure.

La prima parte di una regola è costituita da una o più condizioni di corrispondenza. Una regola può contenere fino a 10 condizioni di corrispondenza. Una condizione di corrispondenza identifica specifici tipi di richieste per cui verranno eseguite le azioni definite. Se si usano più condizioni di corrispondenza, verranno raggruppate insieme tramite la logica AND. Per tutte le condizioni di corrispondenza che supportano più valori (come indicato di seguito come "delimitati da spazi"), viene usato l'operatore "OR".

Ad esempio, è possibile usare una condizione di corrispondenza per:

- Filtrare le richieste in base a un indirizzo IP, un paese o un'area specifica.
- Filtrare le richieste in base alle informazioni dell'intestazione.
- Filtrare le richieste provenienti da dispositivi mobili o desktop.

Le condizioni di corrispondenza seguenti sono disponibili per l'uso nel motore regole di Frontdoor di Azure.  

## <a name="device-type"></a>Tipo di dispositivo 

Identifica le richieste effettuate da un dispositivo mobile o desktop.  

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|----------------
Uguale a, diverso da | Mobile, Desktop

## <a name="post-argument"></a>Argomento richiesta POST

Identifica le richieste in base agli argomenti definiti per il metodo di richiesta POST usato. 

#### <a name="required-fields"></a>Required fields

Nome dell'argomento | Operatore | Valore dell'argomento | Trasformazione maiuscole/minuscole
--------------|----------|----------------|---------------
string | [Elenco di operatori standard](#standard-operator-list) | String, Int | Minuscole, Maiuscole, Taglia, Rimuovi spazi vuoti, Codifica URL, Decodifica URL

## <a name="query-string"></a>Stringa di query

Identifica le richieste che contengono un parametro di stringa di query specificato. Questo parametro è impostato su un valore che corrisponde a un modello specifico. I parametri di stringa di query, ad esempio **parametro=valore**, nell'URL della richiesta determinano se questa condizione viene soddisfatta. Questa condizione di corrispondenza identifica un parametro della stringa di query in base al nome e accetta uno o più valori per il valore del parametro.

#### <a name="required-fields"></a>Required fields

Operatore | Stringa di query | Trasformazione maiuscole/minuscole
---------|--------------|---------------
[Elenco di operatori standard](#standard-operator-list) | String, Int | Minuscole, Maiuscole, Taglia, Rimuovi spazi vuoti, Codifica URL, Decodifica URL

## <a name="remote-address"></a>Indirizzo remoto

Identifica le richieste in base alla posizione o all'indirizzo IP del richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|-----------------
Corrispondenza geografica | Codice paese
Corrispondenza IP | Indirizzo IP (delimitato da spazi)
Non corrispondenza geografica | Codice paese
Non corrispondenza IP | Indirizzo IP (delimitato da spazi)

#### <a name="key-information"></a>Informazioni chiave

- Usare la notazione CIDR.
- Per specificare più indirizzi IP e blocchi di indirizzi IP, usare un singolo spazio tra i valori:
  - **Esempio di indirizzo IPv4**: *1.2.3.4 10.20.30.40* corrisponde alle richieste provenienti dall'indirizzo 1.2.3.4 o 10.20.30.40.
  - **Esempio di indirizzo IPv6**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* corrisponde alle richieste provenienti dall'indirizzo 1:2:3:4:5:6:7:8 o 10:20:30:40:50:60:70:80.
- La sintassi per un blocco di indirizzi IP è l'indirizzo IP di base seguito da una barra e dalle dimensioni del prefisso. Ad esempio:
  - **Esempio di indirizzo IPv4**: *5.5.5.64/26* corrisponde alle richieste provenienti dagli indirizzi compresi tra 5.5.5.64 e 5.5.5.127.
  - **Esempio di indirizzo IPv6**: *1:2:3:/48* corrisponde alle richieste provenienti dagli indirizzi compresi tra 1:2:3:0:0:0:0:0 e 1:2:3:ffff:ffff:ffff:ffff:ffff.

## <a name="request-body"></a>Corpo della richiesta

Identifica le richieste in base a testo specifico visualizzato nel relativo corpo.

#### <a name="required-fields"></a>Required fields

Operatore | Corpo della richiesta | Trasformazione maiuscole/minuscole
---------|--------------|---------------
[Elenco di operatori standard](#standard-operator-list) | String, Int | Minuscole, Maiuscole, Taglia, Rimuovi spazi vuoti, Codifica URL, Decodifica URL

## <a name="request-header"></a>Intestazione della richiesta

Identifica le richieste che usano un'intestazione specifica.

#### <a name="required-fields"></a>Required fields

Nome intestazione | Operatore | Valore intestazione | Trasformazione maiuscole/minuscole
------------|----------|--------------|---------------
string | [Elenco di operatori standard](#standard-operator-list) | String, Int | Minuscole, Maiuscole, Taglia, Rimuovi spazi vuoti, Codifica URL, Decodifica URL

## <a name="request-method"></a>Metodo richiesta

Identifica le richieste che usano il metodo di richiesta specificato.

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|----------------
Uguale a, diverso da | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Informazioni chiave

- Solo il metodo di richiesta GET può generare contenuto memorizzato nella cache in Frontdoor di Azure. Tutti gli altri metodi di richiesta vengono elaborati tramite la rete. 

## <a name="request-protocol"></a>Protocollo richiesta

Identifica le richieste che usano il protocollo specificato.

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|----------------
Uguale a, diverso da | HTTP, HTTPS

## <a name="request-url"></a>URL richiesta

Identifica le richieste che corrispondono all'URL specificato.

#### <a name="required-fields"></a>Required fields

Operatore | URL richiesta | Trasformazione maiuscole/minuscole
---------|-------------|---------------
[Elenco di operatori standard](#standard-operator-list) | String, Int | Minuscole, Maiuscole, Taglia, Rimuovi spazi vuoti, Codifica URL, Decodifica URL

#### <a name="key-information"></a>Informazioni chiave

- Quando si usa questa condizione della regola, assicurarsi di includere le informazioni sul protocollo. Ad esempio: *https://www.\<yourdomain\>.com*.

## <a name="request-file-extension"></a>Estensione file della richiesta

Identifica le richieste che includono l'estensione del nome file specificata nell'URL richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | Estensione | Trasformazione maiuscole/minuscole
---------|-----------|---------------
[Elenco di operatori standard](#standard-operator-list) | String, Int | Minuscole, Maiuscole, Taglia, Rimuovi spazi vuoti, Codifica URL, Decodifica URL

#### <a name="key-information"></a>Informazioni chiave

- Per l'estensione, non includere un punto iniziale. Ad esempio, usare *html* invece di *.html*.

## <a name="request-file-name"></a>Nome file della richiesta

Identifica le richieste che includono il nome file specificato nell'URL richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | Nome file | Trasformazione maiuscole/minuscole
---------|-----------|---------------
[Elenco di operatori standard](#standard-operator-list) | String, Int | Minuscole, Maiuscole, Taglia, Rimuovi spazi vuoti, Codifica URL, Decodifica URL

#### <a name="key-information"></a>Informazioni chiave

- Per specificare più nomi di file, separarli premendo INVIO. 

## <a name="request-path"></a>Percorso della richiesta

Identifica le richieste che includono il percorso specificato nell'URL richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | valore | Trasformazione maiuscole/minuscole
---------|-------|---------------
[Elenco di operatori standard](#standard-operator-list) | String, Int | Minuscole, Maiuscole, Taglia, Rimuovi spazi vuoti, Codifica URL, Decodifica URL

## <a name="standard-operator-list"></a>Elenco di operatori standard

Per le regole che accettano valori dall'elenco di operatori standard, gli operatori seguenti sono validi:

- Qualsiasi
- Uguale a 
- Contiene 
- Inizia con 
- Finisce con 
- Minore di
- Minore o uguale a
- Maggiore di
- Maggiore o uguale a
- Nessuno
- Non contiene
- Non inizia con 
- Non termina con 
- Non è minore di
- Non è minore o uguale a
- Non è maggiore di
- Non è maggiore o uguale a

Per gli operatori numerici, ad esempio *minore di* e *maggiore o uguale a*, il confronto si basa sulla lunghezza. In questo caso, il valore della condizione di corrispondenza deve essere un numero intero uguale alla lunghezza da confrontare. 


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come configurare la prima [configurazione del motore regole](front-door-tutorial-rules-engine.md). 
- Altre informazioni sulle [azioni del motore regole](front-door-rules-engine-actions.md)
- Altre informazioni sul [motore regole di Frontdoor di Azure](front-door-rules-engine.md)
