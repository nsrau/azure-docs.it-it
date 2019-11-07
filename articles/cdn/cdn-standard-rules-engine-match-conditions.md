---
title: Condizioni di corrispondenza del motore della rete CDN di Azure dalle regole standard Microsoft | Microsoft Docs
description: Documentazione di riferimento per la rete per la distribuzione di contenuti di Azure dalle condizioni di corrispondenza del motore regole standard Microsoft.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: a72452d37b152a9463a5aee0e199fd42ea852236
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615962"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-match-conditions"></a>Condizioni di corrispondenza del motore delle regole standard della rete CDN di Azure da Microsoft

Questo articolo elenca le descrizioni dettagliate delle condizioni di corrispondenza disponibili per la rete per la distribuzione di contenuti (CDN) di Azure del [motore delle regole standard](cdn-standard-rules-engine.md)di Microsoft.

La prima parte di una regola è costituita da un set di condizioni di corrispondenza. Ogni regola può avere un massimo di 4 condizioni di corrispondenza. Una condizione di corrispondenza identifica tipi specifici di richieste per le quali verranno eseguite le azioni definite nella regola. Se si usano più condizioni di corrispondenza, queste verranno raggruppate usando la logica AND.

Ad esempio, è possibile usare una condizione di corrispondenza per:

- Filtrare le richieste generate da un particolare indirizzo IP o paese/area geografica.
- Filtrare le richieste in base alle informazioni dell'intestazione.
- Filtrare le richieste da dispositivi mobili o desktop.

## <a name="match-conditions"></a>Condizioni di corrispondenza

Le condizioni di corrispondenza seguenti sono disponibili per l'utilizzo. 

### <a name="device-type"></a>Tipo di dispositivo 

La condizione di corrispondenza del tipo di dispositivo identifica le richieste effettuate da un dispositivo mobile o desktop in base alle relative proprietà.  

**Campi obbligatori**

Operatore | Valore supportato
---------|----------------
Uguale a, non uguale a | Dispositivi mobili, desktop


### <a name="http-version"></a>Versione HTTP

La condizione di corrispondenza della versione HTTP identifica le richieste in base alla versione HTTP con cui arriva la richiesta.

**Campi obbligatori**

Operatore | Valore supportato
---------|----------------
Uguale a, non uguale a | 2,0, 1,1, 1,0, 0,9, tutti


### <a name="request-cookies"></a>Cookie di richiesta

La condizione di corrispondenza dei cookie di richiesta identifica le richieste in base alle informazioni sui cookie nella richiesta in ingresso.

**Campi obbligatori**

Nome del cookie | Operatore | Cookie Value (Valore cookie) | Trasformazione case
------------|----------|--------------|---------------
String | [Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

Informazioni chiave
- I valori jolly, inclusi gli asterischi (*), non sono supportati quando si specifica un nome di cookie. solo le corrispondenze esatte dei nomi di cookie sono idonee per il confronto.
- È possibile specificare un solo nome di cookie per istanza della condizione di corrispondenza.
- I confronti tra nomi di cookie non fanno distinzione tra maiuscole e minuscole.
- È possibile specificare più valori cookie delimitandoli con uno spazio singolo. 
- I valori dei cookie possono sfruttare i valori jolly.
- Se non è stato specificato un valore con caratteri jolly, solo una corrispondenza esatta soddisferà questa condizione di corrispondenza. Se ad esempio si specifica "value", il valore corrisponderà a "value", ma non a "value1". 

### <a name="post-argument"></a>Argomento post

**Campi obbligatori**

Nome argomento | Operatore | Valore argomento | Trasformazione case
--------------|----------|----------------|---------------
String | [Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

### <a name="query-string"></a>Stringa di query

Le condizioni di corrispondenza della stringa di query identificano le richieste contenenti un parametro della stringa di query specificato. Questo parametro è impostato su un valore che corrisponde a un modello specificato. I parametri della stringa di query, ad esempio parametro=valore, nell'URL della richiesta determinano se questa condizione viene soddisfatta. Questa condizione di corrispondenza identifica un parametro della stringa di query in base al nome e accetta uno o più valori per il valore del parametro.

**Campi obbligatori**

Operatore | Stringa di query | Trasformazione case
---------|--------------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

### <a name="remote-address"></a>Indirizzo remoto

La condizione di corrispondenza indirizzo remoto identifica le richieste in base alla posizione o all'indirizzo IP del richiedente.

**Campi obbligatori**

Operatore | Valori supportati
---------|-----------------
Qualsiasi | N/D
Corrispondenza geografica | Codici paese
Corrispondenza IP | Indirizzi IP (spazio separato)
Nessun | N/D
Non corrispondenza geografica | Codici paese
Non corrispondenza IP | Indirizzi IP (spazio separato)

Informazioni chiave:

- Usare la notazione CIDR.
- Per specificare più indirizzi IP e/o blocchi di indirizzi IP, delimitarli con uno spazio singolo. Ad esempio:
  - **Esempio di indirizzo IPv4**: 1.2.3.4 10.20.30.40 corrisponde alle richieste provenienti dall'indirizzo 1.2.3.4 o 10.20.30.40.
  - **Esempio di indirizzo IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 corrisponde alle richieste provenienti dall'indirizzo 1:2:3:4:5:6:7:8 o 10:20:30:40:50:60:70:80.
- La sintassi per un blocco di indirizzi IP è l'indirizzo IP di base seguito da una barra e dalle dimensioni del prefisso. Ad esempio:
  - **Esempio di indirizzo IPv4**: 5.5.5.64/26 corrisponde alle richieste provenienti dagli indirizzi compresi tra 5.5.5.64 e 5.5.5.127.
  - **Esempio di indirizzo IPv6**: 1:2:3:/48 corrisponde alle richieste provenienti dagli indirizzi compresi tra 1:2:3:0:0:0:0:0 e 1:2:3:ffff:ffff:ffff:ffff:ffff.

### <a name="request-body"></a>Request Body

**Campi obbligatori**

Operatore | Request Body | Trasformazione case
---------|--------------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

### <a name="request-header"></a>Intestazione di richiesta

**Campi obbligatori**
Nome intestazione | Operatore | Valore intestazione | Trasformazione case
------------|----------|--------------|---------------
String | [Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

### <a name="request-method"></a>Metodo richiesta

**Campi obbligatori**

Operatore | Valore supportato
---------|----------------
Uguale a, non uguale a | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

Informazioni chiave:

- Solo il metodo di richiesta GET può generare contenuto memorizzato nella cache nella rete CDN. Tutti gli altri metodi di richiesta vengono elaborati tramite la rete. 

### <a name="request-protocol"></a>Request Protocol (Protocollo richiesta)

**Campi obbligatori**

Operatore | Valore supportato
---------|----------------
Uguale a, non uguale a | HTTP, HTTPS

### <a name="request-url"></a>URL richiesta

**Campi obbligatori**

Operatore | URL richiesta | Trasformazione case
---------|-------------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

Informazioni chiave:

- Quando si immette l'URL della richiesta, assicurarsi di includere le informazioni sul protocollo. Ad esempio "https://www. [dominio]. com "

### <a name="url-file-extension"></a>Estensione di file URL

**Campi obbligatori**

Operatore | Estensione | Trasformazione case
---------|-----------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

Informazioni chiave:

- Per l'estensione, non includere un punto principale; usare, ad esempio, HTML anziché HTML.

### <a name="url-file-name"></a>Nome file URL

**Campi obbligatori**

Operatore | Nome file | Trasformazione case
---------|-----------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

Informazioni chiave:

- Per specificare più nomi di file, separare ogni nome file con uno spazio singolo. 

### <a name="url-path"></a>Percorso URL

**Campi obbligatori**

Operatore | Valore | Trasformazione case
---------|-------|---------------
[Elenco operatori standard](#standard-operator-list) | Stringa, int | Nessuna trasformazione, maiuscola e minuscola

Informazioni chiave:

- Un valore di nome file può trarre vantaggio dai valori jolly. Ad esempio, ogni modello di nome file può contenere uno o più asterischi (*), in cui ogni asterisco corrisponde a una sequenza di uno o più caratteri.

## <a name="reference-for-rules-engine-match-conditions"></a>Riferimento per le condizioni di corrispondenza del motore regole

### <a name="standard-operator-list"></a>Elenco operatori standard

Per le regole che contengono l'elenco di operatori standard, gli operatori seguenti sono validi:

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

Per gli operatori numerici, ad esempio "minore di" o "maggiore o uguale a", il confronto utilizzato sarà basato sulla lunghezza. In questo caso, il valore della condizione di corrispondenza deve essere un numero intero, uguale alla lunghezza che si desidera confrontare. 

---

[Torna all'inizio](#match-conditions)

</br>

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della rete per la distribuzione di contenuti di Azure](cdn-overview.md)
- [Informazioni di riferimento sul motore regole](cdn-standard-rules-engine-reference.md)
- [Azioni del motore regole](cdn-standard-rules-engine-actions.md)
- [Applicare HTTPS usando il motore regole standard](cdn-standard-rules-engine.md)
