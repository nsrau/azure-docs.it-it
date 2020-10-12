---
title: Condizioni di corrispondenza nel motore delle regole standard per la rete CDN di Azure | Microsoft Docs
description: Documentazione di riferimento per le condizioni di corrispondenza nel motore delle regole standard per la rete per la distribuzione di contenuti di Azure (rete CDN di Azure).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b8050b973027ac91ede0ba98f4d1c76831da9828
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81259928"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Condizioni di corrispondenza nel motore delle regole standard per la rete CDN di Azure

Nel [motore delle regole standard](cdn-standard-rules-engine.md) per la rete per la distribuzione di contenuti di Azure (rete CDN di Azure), una regola è costituita da una o più condizioni di corrispondenza e da un'azione. Questo articolo fornisce descrizioni dettagliate delle condizioni di corrispondenza che è possibile usare nel motore delle regole standard per la rete CDN di Azure.

La prima parte di una regola è costituita da una o più condizioni di corrispondenza. Nel motore delle regole standard per la rete CDN di Azure ogni regola può avere fino a quattro condizioni di corrispondenza. Una condizione di corrispondenza identifica specifici tipi di richieste per cui verranno eseguite le azioni definite. Se si usano più condizioni di corrispondenza, verranno raggruppate insieme tramite la logica AND.

Ad esempio, è possibile usare una condizione di corrispondenza per:

- Filtrare le richieste in base a un indirizzo IP, un paese o un'area specifica.
- Filtrare le richieste in base alle informazioni dell'intestazione.
- Filtrare le richieste provenienti da dispositivi mobili o desktop.

## <a name="match-conditions"></a>Condizioni di corrispondenza

Le condizioni di corrispondenza seguenti sono disponibili per l'uso nel motore delle regole standard per la rete CDN di Azure. 

### <a name="device-type"></a>Tipo di dispositivo 

Identifica le richieste effettuate da un dispositivo mobile o desktop.  

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|----------------
Uguale a, diverso da | Mobile, Desktop

### <a name="http-version"></a>Versione HTTP

Identifica le richieste in base alla versione HTTP della richiesta.

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|----------------
Uguale a, diverso da | 2,0, 1,1, 1,0, 0,9, tutti

### <a name="request-cookies"></a>Cookie della richiesta

Identifica le richieste in base alle informazioni sui cookie nella richiesta in ingresso.

#### <a name="required-fields"></a>Required fields

Nome del cookie | Operatore | Valore cookie | Trasformazione maiuscole/minuscole
------------|----------|--------------|---------------
string | [Elenco di operatori standard](#standard-operator-list) | String, Int | Nessuna trasformazione, maiuscola e minuscola

#### <a name="key-information"></a>Informazioni chiave

- Quando si specifica un nome di cookie, non è possibile usare i valori jolly (inclusi gli asterischi) \* . è necessario usare un nome esatto del cookie.
- È possibile specificare solo un nome di cookie singolo per ogni istanza di questa condizione di corrispondenza.
- I confronti tra nomi di cookie non fanno distinzione tra maiuscole e minuscole.
- Per specificare più valori dei cookie, usare uno spazio singolo tra ogni valore di cookie. 
- I valori dei cookie possono sfruttare i valori jolly.
- Se non è stato specificato un valore con caratteri jolly, solo una corrispondenza esatta soddisfa questa condizione di corrispondenza. Ad esempio, "value" corrisponderà a "value" ma non a "value1". 

### <a name="post-argument"></a>Argomento richiesta POST

Identifica le richieste in base agli argomenti definiti per il metodo di richiesta POST usato. 

#### <a name="required-fields"></a>Required fields

Nome dell'argomento | Operatore | Valore dell'argomento | Trasformazione maiuscole/minuscole
--------------|----------|----------------|---------------
string | [Elenco di operatori standard](#standard-operator-list) | String, Int | Nessuna trasformazione, maiuscola e minuscola

### <a name="query-string"></a>Stringa di query

Identifica le richieste che contengono un parametro di stringa di query specificato. Questo parametro è impostato su un valore che corrisponde a un modello specifico. I parametri di stringa di query, ad esempio **parametro=valore**, nell'URL della richiesta determinano se questa condizione viene soddisfatta. Questa condizione di corrispondenza identifica un parametro della stringa di query in base al nome e accetta uno o più valori per il valore del parametro.

#### <a name="required-fields"></a>Required fields

Operatore | Stringa di query | Trasformazione maiuscole/minuscole
---------|--------------|---------------
[Elenco di operatori standard](#standard-operator-list) | String, Int | Nessuna trasformazione, maiuscola e minuscola

### <a name="remote-address"></a>Indirizzo remoto

Identifica le richieste in base alla posizione o all'indirizzo IP del richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|-----------------
Qualsiasi | N/D
Corrispondenza geografica | Codice paese
Corrispondenza IP | Indirizzo IP (delimitato da spazi)
Nessun | N/D
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

### <a name="request-body"></a>Corpo della richiesta

Identifica le richieste in base a testo specifico visualizzato nel relativo corpo.

#### <a name="required-fields"></a>Required fields

Operatore | Corpo della richiesta | Trasformazione maiuscole/minuscole
---------|--------------|---------------
[Elenco di operatori standard](#standard-operator-list) | String, Int | Nessuna trasformazione, maiuscola e minuscola

### <a name="request-header"></a>Intestazione della richiesta

Identifica le richieste che usano un'intestazione specifica.

#### <a name="required-fields"></a>Required fields

Nome intestazione | Operatore | Valore intestazione | Trasformazione maiuscole/minuscole
------------|----------|--------------|---------------
string | [Elenco di operatori standard](#standard-operator-list) | String, Int | Nessuna trasformazione, maiuscola e minuscola

### <a name="request-method"></a>Metodo richiesta

Identifica le richieste che usano il metodo di richiesta specificato.

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|----------------
Uguale a, diverso da | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Informazioni chiave

- Solo il metodo di richiesta GET può generare contenuto memorizzato nella cache nella rete CDN di Azure. Tutti gli altri metodi di richiesta vengono elaborati tramite la rete. 

### <a name="request-protocol"></a>Protocollo richiesta

Identifica le richieste che usano il protocollo specificato.

#### <a name="required-fields"></a>Required fields

Operatore | Valori supportati
---------|----------------
Uguale a, diverso da | HTTP, HTTPS

### <a name="request-url"></a>URL richiesta

Identifica le richieste che corrispondono all'URL specificato.

#### <a name="required-fields"></a>Required fields

Operatore | URL richiesta | Trasformazione maiuscole/minuscole
---------|-------------|---------------
[Elenco di operatori standard](#standard-operator-list) | String, Int | Nessuna trasformazione, maiuscola e minuscola

#### <a name="key-information"></a>Informazioni chiave

- Quando si usa questa condizione della regola, assicurarsi di includere le informazioni sul protocollo. Ad esempio: *https://www.\<yourdomain\>.com*.

### <a name="url-file-extension"></a>Estensione di file URL

Identifica le richieste che includono l'estensione del nome file specificata nell'URL richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | Estensione | Trasformazione maiuscole/minuscole
---------|-----------|---------------
[Elenco di operatori standard](#standard-operator-list) | String, Int | Nessuna trasformazione, maiuscola e minuscola

#### <a name="key-information"></a>Informazioni chiave

- Per l'estensione, non includere un punto iniziale. Ad esempio, usare *html* invece di *.html*.

### <a name="url-file-name"></a>Nome file URL

Identifica le richieste che includono il nome file specificato nell'URL richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | Nome file | Trasformazione maiuscole/minuscole
---------|-----------|---------------
[Elenco di operatori standard](#standard-operator-list) | String, Int | Nessuna trasformazione, maiuscola e minuscola

#### <a name="key-information"></a>Informazioni chiave

- Per specificare più nomi di file, separare ogni nome file con uno spazio singolo. 

### <a name="url-path"></a>Percorso URL

Identifica le richieste che includono il percorso specificato nell'URL richiedente.

#### <a name="required-fields"></a>Required fields

Operatore | valore | Trasformazione maiuscole/minuscole
---------|-------|---------------
[Elenco di operatori standard](#standard-operator-list) | String, Int | Nessuna trasformazione, maiuscola e minuscola

#### <a name="key-information"></a>Informazioni chiave

- Un nome file può usare caratteri jolly. Ad esempio, ogni modello di nome file può contenere uno o più asterischi (*), in cui ogni asterisco corrisponde a una sequenza di uno o più caratteri.

## <a name="reference-for-rules-engine-match-conditions"></a>Riferimento per le condizioni di corrispondenza del motore regole

### <a name="standard-operator-list"></a>Elenco di operatori standard

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

- [Panoramica della rete CDN di Azure](cdn-overview.md)
- [Informazioni di riferimento sul motore regole standard](cdn-standard-rules-engine-reference.md)
- [Azioni del motore regole standard](cdn-standard-rules-engine-actions.md)
- [Applicare HTTPS usando il motore regole standard](cdn-standard-rules-engine.md)
