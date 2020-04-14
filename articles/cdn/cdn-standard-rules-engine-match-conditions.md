---
title: Corrispondenza delle condizioni nel motore delle regole Standard per la rete CDN di Azure Documenti Microsoft
description: Documentazione di riferimento per le condizioni di corrispondenza nel motore di regole standard per la rete per la distribuzione di contenuti di Azure (rete CDN di Azure).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b8050b973027ac91ede0ba98f4d1c76831da9828
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259928"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Condizioni di corrispondenza nel motore regole Standard per la rete CDN di AzureMatch conditions in the Standard rules engine for Azure CDN

Nel [motore regole standard](cdn-standard-rules-engine.md) per la rete per la distribuzione di contenuti di Azure una regola è costituita da una o più condizioni di corrispondenza e da un'azione. Questo articolo fornisce descrizioni dettagliate delle condizioni di corrispondenza che è possibile usare nel motore regole Standard per la rete CDN di Azure.This article provides detailed descriptions of the match conditions you can use in the Standard rules engine for Azure CDN.

La prima parte di una regola è una condizione di corrispondenza o un set di condizioni di corrispondenza. Nel motore regole Standard per la rete CDN di Azure ogni regola può avere fino a quattro condizioni di corrispondenza. Una condizione di corrispondenza identifica tipi specifici di richieste per le quali vengono eseguite azioni definite. Se si utilizzano più condizioni di corrispondenza, le condizioni di corrispondenza vengono raggruppate utilizzando la logica AND.

Ad esempio, è possibile usare una condizione di corrispondenza per:

- Filtrare le richieste in base a un indirizzo IP, un paese o un'area geografica specifici.
- Filtrare le richieste in base alle informazioni dell'intestazione.
- Filtrare le richieste da dispositivi mobili o desktop.

## <a name="match-conditions"></a>Condizioni di corrispondenza

The following match conditions are available to use in the Standard rules engine for Azure CDN. 

### <a name="device-type"></a>Tipo di dispositivo 

Identifica le richieste effettuate da un dispositivo mobile o desktop.  

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|----------------
Uguale a, Non uguale a | Dispositivi mobili, desktop

### <a name="http-version"></a>Versione HTTP

Identifica le richieste in base alla versione HTTP della richiesta.

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|----------------
Uguale a, Non uguale a | 2.0, 1.1, 1.0, 0.9, Tutti

### <a name="request-cookies"></a>Cookie della richiesta

Identifica le richieste in base alle informazioni sui cookie nella richiesta in arrivo.

#### <a name="required-fields"></a>Required fields

Nome del cookie | Operatore | Valore cookie | Trasformazione del caso
------------|----------|--------------|---------------
string | [Elenco operatori standard](#standard-operator-list) | Stringa, Int | Nessuna trasformazione, in maiuscolo, in minuscolo

#### <a name="key-information"></a>Informazioni chiave

- Non è possibile utilizzare valori jolly\*(inclusi gli asterischi ( )) quando si specifica il nome di un cookie; è necessario utilizzare un nome di cookie esatto.
- È possibile specificare un solo nome di cookie per ogni istanza di questa condizione di corrispondenza.
- I confronti tra nomi di cookie non fanno distinzione tra maiuscole e minuscole.
- Per specificare più valori di cookie, utilizzare un singolo spazio tra ogni valore del cookie. 
- I valori dei cookie possono sfruttare i valori jolly.
- Se non è stato specificato un valore jolly, solo una corrispondenza esatta soddisfa questa condizione di corrispondenza. Ad esempio, "Valore" corrisponderà a "Valore" ma non a "Valore1". 

### <a name="post-argument"></a>Argomento post

Identifica le richieste in base ad argomenti definiti per il metodo di richiesta POST utilizzato nella richiesta. 

#### <a name="required-fields"></a>Required fields

Nome dell'argomento | Operatore | Valore dell'argomento | Trasformazione del caso
--------------|----------|----------------|---------------
string | [Elenco operatori standard](#standard-operator-list) | Stringa, Int | Nessuna trasformazione, in maiuscolo, in minuscolo

### <a name="query-string"></a>Stringa di query

Identifica le richieste che contengono un parametro di stringa di query specifico. Questo parametro è impostato su un valore che corrisponde a un modello specifico. I parametri della stringa di query (ad esempio, **parametro-valore**) nell'URL della richiesta determinano se questa condizione viene soddisfatta. Questa condizione di corrispondenza identifica un parametro della stringa di query in base al nome e accetta uno o più valori per il valore del parametro.

#### <a name="required-fields"></a>Required fields

Operatore | Stringa di query | Trasformazione del caso
---------|--------------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, Int | Nessuna trasformazione, in maiuscolo, in minuscolo

### <a name="remote-address"></a>Indirizzo remoto

Identifica le richieste in base alla posizione o all'indirizzo IP del richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|-----------------
Qualsiasi | N/D
Corrispondenza geografica | Codice paese
Corrispondenza IP | Indirizzo IP (separato da spazi)
Non Any | N/D
Non Geo Match | Codice paese
Non IP corrispondente | Indirizzo IP (separato da spazi)

#### <a name="key-information"></a>Informazioni chiave

- Usare la notazione CIDR.
- Per specificare più indirizzi IP e blocchi di indirizzi IP, utilizzare un singolo spazio tra i valori:
  - **Esempio IPv4:** *1.2.3.4 10.20.30.40* corrisponde a tutte le richieste che arrivano dall'indirizzo 1.2.3.4 o 10.20.30.40.
  - **Esempio IPv6:** *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* corrisponde a tutte le richieste che arrivano da uno dei due indirizzi 1:2:3:4:5:6:8 o 10:20:30:40:50:60:70:80.
- La sintassi per un blocco di indirizzi IP è l'indirizzo IP di base seguito da una barra e dalle dimensioni del prefisso. Ad esempio:
  - **Esempio IPv4:** *5.5.5.64/26* corrisponde a tutte le richieste provenienti dagli indirizzi da 5.5.5.64 a 5.5.5.127.
  - **Esempio IPv6:** *1:2:3:/48* corrisponde a tutte le richieste che arrivano dagli indirizzi 1:2:3:0:0:0:0 a 1:2:3:ffff:ffff:ffff:ffff:ffffff.

### <a name="request-body"></a>Corpo della richiesta

Identifica le richieste in base al testo specifico visualizzato nel corpo della richiesta.

#### <a name="required-fields"></a>Required fields

Operatore | Corpo della richiesta | Trasformazione del caso
---------|--------------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, Int | Nessuna trasformazione, in maiuscolo, in minuscolo

### <a name="request-header"></a>Intestazione della richiesta

Identifica le richieste che utilizzano un'intestazione specifica nella richiesta.

#### <a name="required-fields"></a>Required fields

Nome intestazione | Operatore | Valore intestazione | Trasformazione del caso
------------|----------|--------------|---------------
string | [Elenco operatori standard](#standard-operator-list) | Stringa, Int | Nessuna trasformazione, in maiuscolo, in minuscolo

### <a name="request-method"></a>Metodo richiesta

Identifica le richieste che utilizzano il metodo di richiesta specificato.

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|----------------
Uguale a, Non uguale a | OTTENERE, POSTARE, METTERE, CANCELLARE, TESTA, OPZIONI, TRACCIA

#### <a name="key-information"></a>Informazioni chiave

- Solo il metodo di richiesta GET può generare contenuto memorizzato nella cache nella rete CDN di Azure.Only the GET request method can generate cached content in Azure CDN. Tutti gli altri metodi di richiesta vengono elaborati tramite la rete. 

### <a name="request-protocol"></a>Protocollo di richiesta

Identifica le richieste che utilizzano il protocollo specificato utilizzato.

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|----------------
Uguale a, Non uguale a | HTTP, HTTPS

### <a name="request-url"></a>URL richiesta

Identifica le richieste che corrispondono all'URL specificato.

#### <a name="required-fields"></a>Required fields

Operatore | URL richiesta | Trasformazione del caso
---------|-------------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, Int | Nessuna trasformazione, in maiuscolo, in minuscolo

#### <a name="key-information"></a>Informazioni chiave

- Quando si utilizza questa condizione della regola, assicurarsi di includere le informazioni sul protocollo. Ad esempio: *https://www.\<yourdomain\>.com*.

### <a name="url-file-extension"></a>Estensione URL

Identifica le richieste che includono l'estensione di file specificata nel nome del file nell'URL richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | Estensione | Trasformazione del caso
---------|-----------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, Int | Nessuna trasformazione, in maiuscolo, in minuscolo

#### <a name="key-information"></a>Informazioni chiave

- Per l'estensione, non includere un periodo iniziale; ad esempio, utilizzare *html* anziché *.html*.

### <a name="url-file-name"></a>Nome file URL

Identifica le richieste che includono il nome file specificato nell'URL richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | Nome file | Trasformazione del caso
---------|-----------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, Int | Nessuna trasformazione, in maiuscolo, in minuscolo

#### <a name="key-information"></a>Informazioni chiave

- Per specificare più nomi di file, separare ogni nome di file con uno spazio singolo. 

### <a name="url-path"></a>Percorso URL

Identifica le richieste che includono il percorso specificato nell'URL richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | valore | Trasformazione del caso
---------|-------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, Int | Nessuna trasformazione, in maiuscolo, in minuscolo

#### <a name="key-information"></a>Informazioni chiave

- Un nome file può usare caratteri jolly. Ad esempio, ogni modello di nome file può contenere uno o più asterischi (*), in cui ogni asterisco corrisponde a una sequenza di uno o più caratteri.

## <a name="reference-for-rules-engine-match-conditions"></a>Riferimento per le condizioni di corrispondenza del motore regole

### <a name="standard-operator-list"></a>Elenco operatori standard

Per le regole che accettano valori dall'elenco di operatori standard, sono validi i seguenti operatori:

- Qualsiasi
- Uguale a 
- Contiene 
- Inizia con 
- Finisce con 
- Minore di
- Minore o uguale a
- Maggiore di
- Maggiore o uguale a
- Non any
- Non contiene
- Non inizia con 
- Non termina con 
- Non minore di
- Non minore o uguale a
- Non maggiore di
- Non maggiore o uguale a

Per gli operatori numerici come *Minore di* e Maggiore di *o uguale a*, il confronto utilizzato è basato sulla lunghezza. In questo caso, il valore nella condizione di corrispondenza deve essere un numero intero uguale alla lunghezza che si desidera confrontare. 

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della rete CDN di Azure](cdn-overview.md)
- [Informazioni di riferimento sul motore regole standard](cdn-standard-rules-engine-reference.md)
- [Azioni nel motore regole StandardActions in the Standard rules engine](cdn-standard-rules-engine-actions.md)
- [Applicare HTTPS usando il motore regole standard](cdn-standard-rules-engine.md)
