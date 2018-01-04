---
title: Soddisfano le condizioni per il motore regole di rete per la distribuzione del contenuto | Documenti Microsoft
description: "Documentazione di riferimento per la rete CDN di Azure regole di funzionalità e le condizioni di corrispondenza di motore."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: 944675f4b055c3d0c2559db53e4807a5c00bda64
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="match-conditions-for-the-content-delivery-network-rules-engine"></a>Condizioni di corrispondenza per il motore regole di rete per la distribuzione del contenuto
In questo articolo elenca le descrizioni dettagliate delle condizioni di corrispondenza disponibili per la rete CDN Azure [motore regole di business](cdn-rules-engine.md).

La seconda parte di una regola è la condizione di corrispondenza. Una condizione di corrispondenza identifica specifici tipi di richieste per cui verrà eseguito un set di funzionalità.

Ad esempio, è possibile utilizzare una condizione di corrispondenza per:
- Filtro richieste per il contenuto in una determinata posizione.
- Richieste di filtro generate da un paese o l'indirizzo IP specifico.
- Filtro richieste dalle informazioni di intestazione.

## <a name="always-match-condition"></a>Sempre corrispondono alla condizione

La condizione di corrispondenza sempre, un set predefinito di funzionalità si applica a tutte le richieste.

NOME | Scopo
-----|--------
[Sempre](#always) | Si applica un set predefinito di funzionalità a tutte le richieste.

## <a name="device-match-condition"></a>Condizione di corrispondenza di dispositivo

La condizione di corrispondenza Dispositivo identifica le richieste effettuate da un dispositivo mobile in base alle relative proprietà.  

NOME | Scopo
-----|--------
[Dispositivo](#device) | Identifica le richieste effettuate da un dispositivo mobile in base alle relative proprietà.

## <a name="location-match-conditions"></a>Condizioni di corrispondenza di percorso

Le condizioni di corrispondenza della posizione di identificare le richieste in base alla posizione del richiedente.

NOME | Scopo
-----|--------
[Sotto forma di numero](#as-number) | Identifica le richieste che hanno origine da una determinata rete.
[Paese](#country) | Identifica le richieste che hanno origine dai paesi specificati.

## <a name="origin-match-conditions"></a>Condizioni di corrispondenza di origine

Le condizioni di corrispondenza origine identificano le richieste di archiviazione di rete per la distribuzione del contenuto o un server di origine cliente.

NOME | Scopo
-----|--------
[Origine della rete CDN](#cdn-origin) | Identifica le richieste di contenuto archiviato in archiviazione di rete per la distribuzione del contenuto.
[Origine cliente](#customer-origin) | Identifica le richieste di contenuto in uno specifico server di origine del cliente.

## <a name="request-match-conditions"></a>Condizioni di corrispondenza di richiesta

Le condizioni di corrispondenza di richiesta di identificano le richieste in base alle relative proprietà.

NOME | Scopo
-----|--------
[Indirizzo IP del client](#client-ip-address) | Identifica le richieste che hanno origine da un determinato indirizzo IP.
[Parametro cookie](#cookie-parameter) | Controlla il valore specificato nei cookie associati a ogni richiesta.
[Cookie parametro Regex](#cookie-parameter-regex) | Controlla l'espressione regolare specificata nei cookie associati a ogni richiesta.
[Bordo Cname](#edge-cname) | Identifica le richieste che fanno riferimento a un CNAME periferico specifico.
[Dominio di riferimento](#referring-domain) | Identifica le richieste che sono state definite dai nomi host specificato.
[Valore letterale di intestazione di richiesta](#request-header-literal) | Identifica le richieste che contengono l'intestazione specificata impostata su un valore specificato.
[Regex di intestazione di richiesta](#request-header-regex) | Identifica le richieste che contengono l'intestazione specificata impostata su un valore che corrisponde all'espressione regolare specificata.
[Carattere jolly di intestazione di richiesta](#request-header-wildcard) | Identifica le richieste che contengono l'intestazione specificata impostata su un valore che corrisponde al modello specificato.
[Metodo di richiesta](#request-method) | Identifica le richieste in base al relativo metodo HTTP.
[Schema di richiesta](#request-scheme) | Identifica le richieste in base al relativo protocollo HTTP.

## <a name="url-match-conditions"></a>Condizioni di corrispondenza URL

Le condizioni di corrispondenza URL identificano le richieste in base i relativi URL.

NOME | Scopo
-----|--------
Directory percorso URL | Identifica le richieste in base al percorso relativo.
Estensione percorso URL | Identifica le richieste tramite l'estensione del nome file.
Nome file percorso URL | Identifica le richieste in base al nome di file.
Valore letterale percorso URL | Confronta il percorso relativo di una richiesta con il valore specificato.
Espressione regolare percorso URL | Confronta il percorso relativo di una richiesta con l'espressione regolare specificata.
Carattere jolly percorso URL | Confronta il percorso relativo di una richiesta con il modello specificato.
Valore letterale query URL | Confronta la stringa di query di una richiesta con il valore specificato.
Parametro query URL | Identifica le richieste che contengono il parametro della stringa di query specificato impostato su un valore che corrisponde a un modello specificato.
Espressione regolare query URL | Identifica le richieste che contengono il parametro della stringa di query specificato impostato su un valore che corrisponde a un'espressione regolare specificata.
Carattere jolly query URL | Confronta il valore specificato in una stringa di query della richiesta.


## <a name="reference-for-rules-engine-match-conditions"></a>Riferimento per le condizioni di corrispondenza del motore regole

---
### <a name="always"></a>Sempre

La condizione di corrispondenza sempre, un set predefinito di funzionalità si applica a tutte le richieste.

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="as-number"></a>Numero AS 
Numero AS è definita dal relativo numero sistema autonomo (ASN). Viene fornita un'opzione per indicare se questa condizione verrà soddisfatta quando un client di rete "Corrisponde a" o "Does non corrisponde a" ASN specificato.

Informazioni chiave:
- Specificare più ASN delimitando ognuna con uno spazio singolo. Ad esempio, 64514 64515 corrisponde a richieste che arrivano da 64514 o 64515.
- Alcune richieste potrebbero non restituire un ASN valido. Un punto interrogativo (?) corrisponda alle richieste per cui non è stato possibile determinare un ASN valido.
- È necessario specificare il numero ASN intero per la rete desiderata. I valori parziali non verranno trovati.
- A causa del modo in cui cache vengono rilevate le impostazioni, questa condizione di corrispondenza non è compatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="cdn-origin"></a>Origine CDN
La condizione di corrispondenza di origine della rete CDN viene soddisfatta quando vengono soddisfatte entrambe le condizioni seguenti:
- Il contenuto dall'archiviazione di rete per la distribuzione del contenuto è stato richiesto.
- La richiesta URI utilizza il punto di accesso al contenuto (ad esempio, /000001) è definito in questa condizione di corrispondenza.
  - URL di rete di recapito del contenuto: L'URI della richiesta deve contenere il punto di accesso al contenuto selezionato.
  - URL di CNAME Edge: La configurazione di CNAME corrispondente edge deve puntare al punto di accesso al contenuto selezionato.
  
Informazioni chiave:
 - Il punto di accesso al contenuto identifica il servizio che deve essere utilizzati per il contenuto richiesto.
 - Non utilizzare un'istruzione IF e per combinare condizioni di corrispondenza. Ad esempio, la combinazione di una condizione di corrispondenza di origine della rete CDN con una condizione di corrispondenza di origine cliente creerà un modello di corrispondenza che non può essere associato. Per questo motivo, non è possibile combinare due condizioni di corrispondenza di origine della rete CDN tramite un'istruzione IF e.

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="client-ip-address"></a>Indirizzo IP client
Viene fornita un'opzione per indicare se la condizione di indirizzo IP del Client sarà soddisfatto quando un client l'indirizzo IP "Corrispondenze" o "Does non corrisponde a" gli indirizzi IP specificato.

Informazioni chiave:
- Assicurarsi di usare la notazione CIDR.
- Specificare più indirizzi IP e/o blocchi di indirizzi IP delimitando ognuna con uno spazio singolo.
  - **Esempio IPv4**: 1.2.3.4 10.20.30.40 corrisponde a tutte le richieste che arrivano da 1.2.3.4 o 10.20.30.40.
  - **Esempio di IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 corrisponde a tutte le richieste che arrivano da 1:2:3:4:5:6:7:8 o 10:20:30:40:50:60:70:80.
- La sintassi per un blocco di indirizzi IP è seguito da una barra e dalla dimensione del prefisso di indirizzo IP di base.
  - **Esempio IPv4**: 5.5.5.64/26 corrisponde a tutte le richieste che arrivano da 5.5.5.64 tramite 5.5.5.127.
  - **Esempio di IPv6**: 1:2:3: / 48 corrisponde a tutte le richieste che arrivano da 1:2:3:0:0:0:0:0 tramite 1:2:3:ffff:ffff:ffff:ffff:ffff.
- A causa del modo in cui cache vengono rilevate le impostazioni, questa condizione di corrispondenza non è compatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="cookie-parameter"></a>Parametro cookie
Il **corrispondenze**/**non corrisponde a** opzione determina le condizioni in cui il parametro Cookie corrispondono condizione venga soddisfatta.
- **Corrisponde a**: richiede una richiesta per il cookie specificato con un valore che corrisponde ad almeno uno dei valori definiti in questa condizione di corrispondenza.
- **Non corrisponde**: richiede che la richiesta di soddisfare uno dei seguenti criteri:
  - Non contiene il cookie specificato.
  - Contiene il cookie specificato, ma il relativo valore non corrisponde a uno dei valori definiti in questa condizione di corrispondenza.
  
Informazioni chiave:
- Nome del cookie: 
  - I caratteri speciali, tra cui un asterisco, non sono supportati quando si specifica un nome di cookie. Ciò significa che solo corrispondenze di nome esatto di cookie sono idonee per il confronto.
  - Per ogni istanza di questa condizione di corrispondenza, è possibile specificare solo un nome di cookie singolo.
  - Confronti tra nomi di cookie è tra maiuscole e minuscole.
- Valore del cookie: 
  - Specificare più valori dei cookie delimitando ognuna con uno spazio singolo.
  - Un valore di cookie può ora usufruire dei caratteri speciali. 
  - Se non è stato specificato un carattere jolly, solo una corrispondenza esatta soddisferà la condizione di corrispondenza. Ad esempio, se si specifica "Valore" corrisponderà a "Value", ma non "Value1" o "Value2".
  - Il **Ignora maiuscole** opzione determina se un confronto tra maiuscole e minuscole verrà effettuato valore del cookie della richiesta.
- A causa del modo in cui cache vengono rilevate le impostazioni, questa condizione di corrispondenza non è compatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="cookie-parameter-regex"></a>Espressione regolare parametro cookie
La condizione di corrispondenza Regex parametro Cookie definisce un nome di cookie e un valore. È possibile utilizzare espressioni regolari per definire il valore di cookie desiderato. 

Il **corrispondenze**/**non corrisponde a** opzione determina le condizioni in cui corrispondono alla condizione venga soddisfatta.
- **Corrisponde a**: richiede una richiesta per il cookie specificato con un valore che corrisponde all'espressione regolare specificata.
- **Non corrisponde**: richiede che la richiesta di soddisfare uno dei seguenti criteri:
  - Non contiene il cookie specificato.
  - Contiene il cookie specificato, ma il relativo valore non corrisponde all'espressione regolare specificata.
  
Informazioni chiave:
- Nome del cookie: 
  - Espressioni regolari e i caratteri speciali, tra cui un asterisco, non sono supportati quando si specifica un nome di cookie. Ciò significa che solo corrispondenze di nome esatto di cookie sono idonee per il confronto.
  - Per ogni istanza di questa condizione di corrispondenza, è possibile specificare solo un nome di cookie singolo.
  - Confronti tra nomi di cookie è tra maiuscole e minuscole.
- Valore del cookie: 
  - Un valore di cookie può ora usufruire delle espressioni regolari.
  - Il **Ignora maiuscole** opzione determina se un confronto tra maiuscole e minuscole verrà effettuato valore del cookie della richiesta.
- A causa del modo in cui cache vengono rilevate le impostazioni, questa condizione di corrispondenza non è compatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

--- 
### <a name="country"></a>Paese
È possibile specificare un paese all'interno del codice paese. Viene fornita un'opzione per indicare se questa condizione verrà soddisfatta quando il paese da cui ha origine la richiesta "Corrispondenze" o "Does non corrisponde a" i valori specificati.

Informazioni chiave:
- Specificare più codici paese delimitando ognuna con uno spazio singolo.
- I caratteri jolly non sono supportati quando si specifica un codice paese.
- I codici paese "EU" e "PA" include tutti gli indirizzi IP in queste aree.
- Alcune richieste potrebbero non restituire un codice di paese valido. Un punto interrogativo (?) corrisponda alle richieste per cui non è stato possibile determinare un codice di paese valido.
- I codici paese tra maiuscole e minuscole.
- A causa del modo in cui cache vengono rilevate le impostazioni, questa condizione di corrispondenza non è compatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="customer-origin"></a>Origine cliente

Informazioni chiave: 
- Venga soddisfatta la condizione di corrispondenza di origine cliente indipendentemente dal fatto che venga richiesto il contenuto tramite un URL di rete di recapito di contenuto o un bordo URL CNAME che punta all'origine del cliente selezionato.
- Una configurazione di origine cliente a cui fa riferimento da una regola non può essere eliminata dalla pagina di origine cliente. Prima di tentare di eliminare una configurazione di origine del cliente, assicurarsi che le seguenti configurazioni non farvi riferimento:
  - Una condizione di corrispondenza di origine cliente
  - Una configurazione di CNAME edge
- Non utilizzare un'istruzione IF e per combinare condizioni di corrispondenza. Ad esempio, la combinazione di una condizione di corrispondenza di origine di clienti con una condizione di corrispondenza di origine della rete CDN creerà un modello di corrispondenza che non può essere associato. Per questo motivo, non è possibile combinare due condizioni di corrispondenza di origine cliente tramite un'istruzione IF e.

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="device"></a>Dispositivo

La condizione di corrispondenza Dispositivo identifica le richieste effettuate da un dispositivo mobile in base alle relative proprietà. Il rilevamento del dispositivo mobile viene eseguito tramite [WURFL](http://wurfl.sourceforge.net/). La tabella seguente elenca le funzionalità WURFL e le relative variabili per il motore regole di rete per la distribuzione del contenuto.
<br>
> [!NOTE] 
> Le variabili seguenti sono supportate nel **modificare intestazione di richiesta Client** e **modifica intestazione risposta Client** funzionalità.

Funzionalità | Variabile | DESCRIZIONE | Valori di esempio
-----------|----------|-------------|----------------
Brand Name (Nome marchio) | %{wurfl_cap_brand_name} | Stringa che indica il nome del marchio del dispositivo. | Samsung
Device OS (Sistema operativo dispositivo) | %{wurfl_cap_device_os} | Stringa che indica il sistema operativo installato nel dispositivo. | IOS
Device OS Version (Versione sistema operativo dispositivo) | %{wurfl_cap_device_os_version} | Stringa che indica il numero di versione del sistema operativo installato nel dispositivo. | 1.0.1
Dual Orientation (Orientamento doppio) | %{wurfl_cap_dual_orientation} | Valore booleano che indica se il dispositivo supporta l'orientamento doppio. | true
HTML Preferred DTD (DTD preferito HTML) | %{wurfl_cap_html_preferred_dtd} | Stringa che indica la definizione DTD (Document Type Definition) preferita del dispositivo mobile per il contenuto HTML. | Nessuno<br/>xhtml_basic<br/>html5
Image Inlining (Incorporamento immagini) | %{wurfl_cap_image_inlining} | Valore booleano che indica se il dispositivo supporta le immagini con codifica in Base64. | false
Is Android (È Android) | %{wurfl_vcap_is_android} | Valore booleano che indica se il dispositivo usa il sistema operativo Android. | true
Is IOS (È IOS) | %{wurfl_vcap_is_ios} | Valore booleano che indica se il dispositivo usa il sistema operativo iOS. | false
Is Smart TV (È una smart TV) | %{wurfl_cap_is_smarttv} | Valore booleano che indica se il dispositivo è una smart TV. | false
Is Smartphone (È uno smartphone) | %{wurfl_vcap_is_smartphone} | Valore booleano che indica se il dispositivo è uno smartphone. | true
Is Tablet (È un tablet) | %{wurfl_cap_is_tablet} | Valore booleano che indica se il dispositivo è un tablet. Questa descrizione è indipendente dal sistema operativo. | true
Is Wireless Device (È un dispositivo wireless) | %{wurfl_cap_is_wireless_device} | Valore booleano che indica se il dispositivo è considerato un dispositivo wireless. | true
Marketing Name (Nome marketing) | %{wurfl_cap_marketing_name} | Stringa che indica il nome marketing del dispositivo. | BlackBerry 8100 Pearl
Mobile Browser (Browser per dispositivi mobili) | %{wurfl_cap_mobile_browser} | Stringa che indica il browser che viene usato per richiedere il contenuto dal dispositivo. | Chrome
Mobile Browser Version (Versione browser per dispositivi mobili) | %{wurfl_cap_mobile_browser_version} | Stringa che indica la versione del browser che viene usato per richiedere il contenuto dal dispositivo. | 31
Model Name (Nome modello) | %{wurfl_cap_model_name} | Stringa che indica il nome del modello del dispositivo. | s3
Download progressivo | %{wurfl_cap_progressive_download} | Valore booleano che indica se il dispositivo supporta la riproduzione di audio e video, mentre è ancora corso il download. | true
Data di rilascio | %{wurfl_cap_release_date} | Stringa che indica il mese e l'anno in cui il dispositivo è stato aggiunto al database WURFL.<br/><br/>Formato: `yyyy_mm` | 2013_december
Resolution Height (Altezza risoluzione) | %{wurfl_cap_resolution_height} | Numero intero che indica l'altezza del dispositivo in pixel. | 768
Resolution Width (Larghezza risoluzione) | %{wurfl_cap_resolution_width} | Numero intero che indica la larghezza del dispositivo in pixel. | 1024

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="edge-cname"></a>CNAME periferico
Informazioni chiave: 
- L'elenco del bordo disponibile CNAME è limitato a quelli che sono state configurate nella pagina di record CNAME Edge che corrisponde alla piattaforma in cui viene configurato il motore regole di HTTP.
- Prima di tentare di eliminare una configurazione di CNAME edge, assicurarsi che una condizione di corrispondenza del bordo Cname non farvi riferimento. Impossibile eliminare le configurazioni di CNAME Edge che sono state definite in una regola dalla pagina di record CNAME di bordo. 
- Non utilizzare un'istruzione IF e per combinare condizioni di corrispondenza. Ad esempio, la combinazione di una condizione di corrispondenza Cname bordo con una condizione di corrispondenza di origine cliente creerà un modello di corrispondenza che non può essere associato. Per questo motivo, non è possibile combinare le condizioni di corrispondenza di due Cname Edge tramite un'istruzione IF e.
- A causa del modo in cui cache vengono rilevate le impostazioni, questa condizione di corrispondenza non è compatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="referring-domain"></a>Dominio di riferimento
Il nome host è associato il referente tramite cui è stata richiesta contenuto determina se viene soddisfatta la condizione di dominio che fa riferimento. Viene fornita un'opzione per indicare se questa condizione verrà soddisfatta quando il riferimento nome host "Corrisponde a" o "Does non corrisponde a" i valori specificati.

Informazioni chiave:
- Specificare più nomi host delimitando ognuna con uno spazio singolo.
- Questa condizione di corrispondenza supporta i caratteri speciali.
- Se il valore specificato non contiene un asterisco, deve essere una corrispondenza esatta per nome host del referrer. Ad esempio, specificando "mydomain.com" non restituirebbero alcuna corrispondenza "www.mydomain.com".
- Il **Ignora maiuscole** opzione determina se un confronto tra maiuscole e minuscole deve essere eseguito.
- A causa del modo in cui cache vengono rilevate le impostazioni, questa condizione di corrispondenza non è compatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

---  
### <a name="request-header-literal"></a>Valore letterale intestazione richiesta
Il **corrispondenze**/**non corrisponde a** opzione determina le condizioni in cui corrispondono alla condizione venga soddisfatta.
- **Corrispondenze**: richiede l'uso per contenere l'intestazione specificata. Il valore deve corrispondere a quello definito in questa condizione di corrispondenza.
- **Non corrisponde**: richiede che la richiesta di soddisfare uno dei seguenti criteri:
  - Non contiene l'intestazione specificata.
  - Contiene l'intestazione specificata, ma il relativo valore corrisponde a quello definito in questa condizione di corrispondenza.
  
Informazioni chiave:
- Confronti tra nomi di intestazione è sempre tra maiuscole e minuscole. Il **Ignora maiuscole** opzione determina la distinzione tra maiuscole e di confronto di valori di intestazione.
- A causa del modo in cui cache vengono rilevate le impostazioni, questa condizione di corrispondenza non è compatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

---  
### <a name="request-header-regex"></a>Espressione regolare intestazione richiesta
Il **corrispondenze**/**non corrisponde a** opzione determina le condizioni in cui l'espressione regolare intestazione richiesta corrispondono alla condizione venga soddisfatta.
- **Corrispondenze**: richiede l'uso per contenere l'intestazione specificata. Il valore deve corrispondere al modello definito nell'espressione regolare specificata.
- **Non corrisponde**: richiede che la richiesta di soddisfare uno dei seguenti criteri:
  - Non contiene l'intestazione specificata.
  - Contiene l'intestazione specificata, ma il relativo valore non corrisponde all'espressione regolare specificata.

Informazioni chiave:
- Nome di intestazione: 
  - Confronti tra nomi di intestazione è tra maiuscole e minuscole.
  - Gli spazi nel nome dell'intestazione devono essere sostituiti con "% 20". 
- Valore dell'intestazione: 
  - Un valore di intestazione può ora usufruire delle espressioni regolari.
  - Il **Ignora maiuscole** opzione determina la distinzione tra maiuscole e di confronto di valori di intestazione.
  - Solo corrispondenze valore esatto dell'intestazione per almeno uno dei modelli specificati soddisferà la condizione.
- A causa del modo in cui cache vengono rilevate le impostazioni, questa condizione di corrispondenza non è compatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno 

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="request-header-wildcard"></a>Carattere jolly intestazione richiesta
Il **corrispondenze**/**non corrisponde a** opzione determina le condizioni in cui il carattere jolly intestazione richiesta corrispondono condizione venga soddisfatta.
- **Corrispondenze**: richiede l'uso per contenere l'intestazione specificata. Il valore deve corrispondere ad almeno uno dei valori definiti in questa condizione di corrispondenza.
- **Non corrisponde**: richiede che la richiesta di soddisfare uno dei seguenti criteri:
  - Non contiene l'intestazione specificata.
  - Contiene l'intestazione specificata, ma il relativo valore non corrisponde a nessuno dei valori specificati.
  
Informazioni chiave:
- Nome di intestazione: 
  - Confronti tra nomi di intestazione è tra maiuscole e minuscole.
  - Gli spazi nel nome dell'intestazione devono essere sostituiti con "% 20". È inoltre possibile utilizzare questo valore per specificare gli spazi in un valore di intestazione.
- Valore dell'intestazione: 
  - Un valore di intestazione può sfruttare i caratteri speciali.
  - Il **Ignora maiuscole** opzione determina la distinzione tra maiuscole e di confronto di valori di intestazione.
  - Solo corrispondenze valore esatto dell'intestazione per almeno uno dei modelli specificati soddisferà la condizione.
  - Specificare più valori delimitando ognuna con uno spazio singolo.
- A causa del modo in cui cache vengono rilevate le impostazioni, questa condizione di corrispondenza non è compatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="request-method"></a>Metodo richiesta
La condizione di metodo di richiesta verranno soddisfatta solo le risorse che vengono richiesti tramite il metodo di richiesta selezionata. I metodi di richiesta disponibili sono:
- GET
- HEAD 
- POST 
- OPTIONS 
- PUT 
- DELETE 
- TRACCIA 
- LA CONNESSIONE 

Informazioni chiave:
- Per impostazione predefinita, solo il metodo di richiesta GET può generare il contenuto memorizzato nella cache nella rete. Tutti gli altri metodi di richiesta sono elaborate tramite la rete.
- A causa del modo in cui cache vengono rilevate le impostazioni, questa condizione di corrispondenza non è compatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="request-scheme"></a>Schema richiesta
Solo le risorse che vengono richiesti tramite il protocollo selezionato soddisferà la condizione di schema di richiesta. I protocolli disponibili sono HTTP e HTTPS.

Informazioni chiave:
- A causa del modo in cui cache vengono rilevate le impostazioni, questa condizione di corrispondenza non è compatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#azure-cdn-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Passaggi successivi
* [Cenni preliminari sulla rete CDN di Azure](cdn-overview.md)
* [Riferimento del motore regole](cdn-rules-engine-reference.md)
* [Espressioni condizionali del motore regole](cdn-rules-engine-reference-conditional-expressions.md)
* [Funzionalità del motore regole](cdn-rules-engine-reference-features.md)
* [Override del comportamento HTTP predefinito mediante il motore regole](cdn-rules-engine.md)

