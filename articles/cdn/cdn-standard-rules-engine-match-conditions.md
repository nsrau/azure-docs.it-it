---
title: Condizioni di corrispondenza nel motore delle regole standard per la rete CDN di Azure | Microsoft Docs
description: Documentazione di riferimento per le condizioni di corrispondenza nel motore delle regole standard per la rete per la distribuzione di contenuti di Azure (rete CDN di Azure).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: c4c2b1f334e37691655b18d2c629fbd8edc95382
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171599"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Condizioni di corrispondenza nel motore delle regole standard per la rete CDN di Azure

Nel [motore delle regole standard](cdn-standard-rules-engine.md) per la rete per la distribuzione di contenuti di Azure (rete CDN di Azure), una regola è costituita da una o più condizioni di corrispondenza e da un'azione. Questo articolo fornisce descrizioni dettagliate delle condizioni di corrispondenza che è possibile usare nel motore delle regole standard per la rete CDN di Azure.

La prima parte di una regola è una condizione di corrispondenza o un set di condizioni di corrispondenza. Nel motore delle regole standard per la rete CDN di Azure ogni regola può avere fino a quattro condizioni di corrispondenza. Una condizione di corrispondenza identifica tipi specifici di richieste per le quali vengono eseguite le azioni definite. Se si utilizzano più condizioni di corrispondenza, le condizioni di corrispondenza vengono raggruppate utilizzando la logica e.

Ad esempio, è possibile usare una condizione di corrispondenza per:

- Filtrare le richieste in base a un indirizzo IP specifico, un paese o un'area geografica.
- Filtrare le richieste in base alle informazioni dell'intestazione.
- Filtrare le richieste da dispositivi mobili o desktop.

## <a name="match-conditions"></a>Condizioni di corrispondenza

Le condizioni di corrispondenza seguenti sono disponibili per l'uso nel motore delle regole standard per la rete CDN di Azure. 

### <a name="device-type"></a>Tipo di dispositivo 

Identifica le richieste effettuate da un dispositivo mobile o desktop.  

#### <a name="required-fields"></a>Campi obbligatori

Operatore | Valori supportati
---------|----------------
Uguale a, non uguale a | Dispositivi mobili, desktop

### <a name="http-version"></a>Versione HTTP

Identifica le richieste in base alla versione HTTP della richiesta.

#### <a name="required-fields"></a>Campi obbligatori

Operatore | Valori supportati
---------|----------------
Uguale a, non uguale a | 2,0, 1,1, 1,0, 0,9, tutti

### <a name="request-cookies"></a>Cookie di richiesta

Identifica le richieste in base alle informazioni sui cookie nella richiesta in ingresso.

#### <a name="required-fields"></a>Campi obbligatori

Nome cookie | Operatore | Valore cookie | Trasformazione case
------------|----------|--------------|---------------
String | [Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

#### <a name="key-information"></a>Informazioni chiave

- Quando si specifica un nome di cookie, non è possibile usare i valori jolly (inclusi gli asterischi (\*)). si usa un nome esatto del cookie.
- È possibile specificare solo un nome di cookie singolo per ogni istanza di questa condizione di corrispondenza.
- I confronti tra nomi di cookie non fanno distinzione tra maiuscole e minuscole.
- Per specificare più valori dei cookie, usare uno spazio singolo tra ogni valore di cookie. 
- I valori dei cookie possono sfruttare i valori jolly.
- Se non è stato specificato un valore con caratteri jolly, solo una corrispondenza esatta soddisfa questa condizione di corrispondenza. Ad esempio, "value" corrisponderà a "value" ma non a "value1". 

### <a name="post-argument"></a>Argomento post

Identifica le richieste in base agli argomenti definiti per il metodo di richiesta POST usato nella richiesta. 

#### <a name="required-fields"></a>Campi obbligatori

Nome dell'argomento | Operatore | Valore dell'argomento | Trasformazione case
--------------|----------|----------------|---------------
String | [Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

### <a name="query-string"></a>Stringa di query

Identifica le richieste che contengono un parametro della stringa di query specifico. Questo parametro è impostato su un valore che corrisponde a un modello specifico. I parametri della stringa di query, ad esempio **parameter = value**, nell'URL della richiesta determinano se questa condizione è soddisfatta. Questa condizione di corrispondenza identifica un parametro della stringa di query in base al nome e accetta uno o più valori per il valore del parametro.

#### <a name="required-fields"></a>Campi obbligatori

Operatore | Stringa di query | Trasformazione case
---------|--------------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

### <a name="remote-address"></a>Indirizzo remoto

Identifica le richieste in base alla posizione o all'indirizzo IP del richiedente.

#### <a name="required-fields"></a>Campi obbligatori

Operatore | Valori supportati
---------|-----------------
Qualsiasi | N/D
Corrispondenza geografica | Codice paese
Corrispondenza IP | Indirizzo IP (separato da spazi)
Nessun | N/D
Non corrispondenza geografica | Codice paese
Non corrispondenza IP | Indirizzo IP (separato da spazi)

#### <a name="key-information"></a>Informazioni chiave

- Usare la notazione CIDR.
- Per specificare più indirizzi IP e blocchi di indirizzi IP, usare uno spazio singolo tra i valori:
  - **Esempio IPv4**: *1.2.3.4 10.20.30.40* corrisponde alle richieste provenienti dall'indirizzo 1.2.3.4 o 10.20.30.40.
  - **Esempio IPv6**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:8*0 corrisponde alle richieste provenienti dall'indirizzo 1:2:3:4:5:6:7:8 o 10:20:30:40:50:60:70:80.
- La sintassi per un blocco di indirizzi IP è l'indirizzo IP di base seguito da una barra e dalle dimensioni del prefisso. Ad esempio:
  - **Esempio IPv4**: *5.5.5.64/26* corrisponde alle richieste provenienti dagli indirizzi 5.5.5.64 tramite 5.5.5.127.
  - **Esempio IPv6**: *1:2:3:/48* corrisponde a tutte le richieste che arrivano dagli indirizzi da 1:2:3:0:0:0:0:0 a 1:2: 3: ffff: ffff: ffff: ffff: FFFF.

### <a name="request-body"></a>Corpo della richiesta

Identifica le richieste in base a testo specifico visualizzato nel corpo della richiesta.

#### <a name="required-fields"></a>Campi obbligatori

Operatore | Corpo della richiesta | Trasformazione case
---------|--------------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

### <a name="request-header"></a>Intestazione della richiesta

Identifica le richieste che usano un'intestazione specifica nella richiesta.

#### <a name="required-fields"></a>Campi obbligatori

Nome intestazione | Operatore | Valore intestazione | Trasformazione case
------------|----------|--------------|---------------
String | [Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

### <a name="request-method"></a>Metodo richiesta

Identifica le richieste che usano il metodo di richiesta specificato.

#### <a name="required-fields"></a>Campi obbligatori

Operatore | Valori supportati
---------|----------------
Uguale a, non uguale a | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Informazioni chiave

- Solo il metodo di richiesta GET può generare contenuto memorizzato nella cache nella rete CDN di Azure. Tutti gli altri metodi di richiesta vengono elaborati tramite la rete. 

### <a name="request-protocol"></a>Protocollo di richiesta

Identifica le richieste che utilizzano il protocollo specificato utilizzato.

#### <a name="required-fields"></a>Campi obbligatori

Operatore | Valori supportati
---------|----------------
Uguale a, non uguale a | HTTP, HTTPS

### <a name="request-url"></a>URL richiesta

Identifica le richieste che corrispondono all'URL specificato.

#### <a name="required-fields"></a>Campi obbligatori

Operatore | URL richiesta | Trasformazione case
---------|-------------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

#### <a name="key-information"></a>Informazioni chiave

- Quando si usa questa condizione della regola, assicurarsi di includere le informazioni sul protocollo. Ad esempio: *https://www.\<yourdomain\>.com* .

### <a name="url-file-extension"></a>Estensione di file URL

Identifica le richieste che includono l'estensione di file specificata nel nome file nell'URL richiedente.

#### <a name="required-fields"></a>Campi obbligatori

Operatore | Estensione | Trasformazione case
---------|-----------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

#### <a name="key-information"></a>Informazioni chiave

- Per l'estensione, non includere un punto principale; usare, ad esempio, *HTML* anziché *HTML.*

### <a name="url-file-name"></a>Nome file URL

Identifica le richieste che includono il nome file specificato nell'URL richiedente.

#### <a name="required-fields"></a>Campi obbligatori

Operatore | Nome file | Trasformazione case
---------|-----------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

#### <a name="key-information"></a>Informazioni chiave

- Per specificare più nomi di file, separare ogni nome file con uno spazio singolo. 

### <a name="url-path"></a>Percorso URL

Identifica le richieste che includono il percorso specificato nell'URL richiedente.

#### <a name="required-fields"></a>Campi obbligatori

Operatore | Valore | Trasformazione case
---------|-------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

#### <a name="key-information"></a>Informazioni chiave

- Un valore di nome file può trarre vantaggio dai valori jolly. Ad esempio, ogni modello di nome file può contenere uno o più asterischi (*), in cui ogni asterisco corrisponde a una sequenza di uno o più caratteri.

## <a name="reference-for-rules-engine-match-conditions"></a>Riferimento per le condizioni di corrispondenza del motore regole

### <a name="standard-operator-list"></a>Elenco operatori standard

Per le regole che accettano valori dall'elenco di operatori standard, gli operatori seguenti sono validi:

- Qualsiasi
- Uguale a 
- Contiene 
- Inizia con 
- Ends with (Finisce con) 
- Minore di
- Minore o uguale a
- Maggiore di
- Maggiore o uguale a
- Nessun
- Non contiene
- Non inizia con 
- Non termina con 
- Non minore di
- Non minore o uguale a
- Non maggiore di
- Non maggiore o uguale a

Per gli operatori numerici come *minore di* e *maggiore o uguale*a, il confronto utilizzato è basato sulla lunghezza. In questo caso, il valore della condizione di corrispondenza deve essere un numero intero uguale alla lunghezza che si desidera confrontare. 

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della rete CDN di Azure](cdn-overview.md)
- [Riferimento al motore regole standard](cdn-standard-rules-engine-reference.md)
- [Azioni nel motore regole standard](cdn-standard-rules-engine-actions.md)
- [Applicare HTTPS usando il motore regole standard](cdn-standard-rules-engine.md)
