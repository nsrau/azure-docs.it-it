---
title: Condizioni di corrispondenza del motore regole della rete CDN di Azure | Documentazione Microsoft
description: Documentazione di riferimento per le condizioni di corrispondenza per il motore regole della rete per la distribuzione di contenuti di Azure.
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
ms.openlocfilehash: e4b7113f27e5e15d69dfdd1efd13e255ef4a8ab7
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Condizioni di corrispondenza del motore regole della rete CDN di Azure 
Questo articolo fornisce descrizioni dettagliate delle condizioni di corrispondenza disponibili per il [motore regole](cdn-rules-engine.md) della rete per la distribuzione di contenuti (rete CDN) di Azure.

La seconda parte di una regola è la condizione di corrispondenza. Una condizione di corrispondenza identifica specifici tipi di richieste per cui verrà eseguito un set di funzionalità.

Ad esempio, è possibile usare una condizione di corrispondenza per:
- Filtrare le richieste per il contenuto in base a una posizione specifica.
- Filtrare le richieste generate da un indirizzo IP o da un paese specifico.
- Filtrare le richieste in base alle informazioni dell'intestazione.

## <a name="always-match-condition"></a>Condizione di corrispondenza Sempre

La condizione di corrispondenza Sempre applica un set predefinito di funzionalità a tutte le richieste.

NOME | Scopo
-----|--------
[Sempre](#always) | Applica un set predefinito di funzionalità a tutte le richieste.

## <a name="device-match-condition"></a>Condizione di corrispondenza Dispositivo

La condizione di corrispondenza Dispositivo identifica le richieste effettuate da un dispositivo mobile in base alle relative proprietà.  

NOME | Scopo
-----|--------
[Dispositivo](#device) | Identifica le richieste effettuate da un dispositivo mobile in base alle relative proprietà.

## <a name="location-match-conditions"></a>Condizioni di corrispondenza Posizione

Le condizioni di corrispondenza Posizione identificano le richieste in base alla posizione del richiedente.

NOME | Scopo
-----|--------
[Numero AS](#as-number) | Identifica le richieste che hanno origine da una determinata rete.
[Paese](#country) | Identifica le richieste che hanno origine dai paesi specificati.

## <a name="origin-match-conditions"></a>Condizioni di corrispondenza Origine

Le condizioni di corrispondenza Origine identificano le richieste che puntano all'archivio della rete CDN o a un server di origine del cliente.

NOME | Scopo
-----|--------
[Origine rete CDN](#cdn-origin) | Identifica le richieste per il contenuto archiviato nell'archivio della rete CDN.
[Origine cliente](#customer-origin) | Identifica le richieste di contenuto in uno specifico server di origine del cliente.

## <a name="request-match-conditions"></a>Condizioni di corrispondenza Richiesta

Le condizioni di corrispondenza Richiesta identificano le richieste in base alle relative proprietà.

NOME | Scopo
-----|--------
[Indirizzo IP client](#client-ip-address) | Identifica le richieste che hanno origine da un determinato indirizzo IP.
[Parametro cookie](#cookie-parameter) | Controlla il valore specificato nei cookie associati a ogni richiesta.
[Espressione regolare parametro cookie](#cookie-parameter-regex) | Controlla l'espressione regolare specificata nei cookie associati a ogni richiesta.
[CNAME perimetrale](#edge-cname) | Identifica le richieste che fanno riferimento a un CNAME periferico specifico.
[Dominio di riferimento](#referring-domain) | Identifica le richieste con riferimenti dai nomi host specificati.
[Valore letterale intestazione richiesta](#request-header-literal) | Identifica le richieste che contengono l'intestazione specificata impostata su un valore specificato.
[Espressione regolare intestazione richiesta](#request-header-regex) | Identifica le richieste che contengono l'intestazione specificata impostata su un valore che corrisponde all'espressione regolare specificata.
[Carattere jolly intestazione richiesta](#request-header-wildcard) | Identifica le richieste che contengono l'intestazione specificata impostata su un valore che corrisponde al modello specificato.
[Metodo richiesta](#request-method) | Identifica le richieste in base al relativo metodo HTTP.
[Schema richiesta](#request-scheme) | Identifica le richieste in base al relativo protocollo HTTP.

## <a name="url-match-conditions"></a>Condizioni di corrispondenza URL

Le condizioni di corrispondenza URL identificano le richieste in base ai relativi URL.

NOME | Scopo
-----|--------
[URL Path Directory](#url-path-directory) (Directory percorso URL) | Identifica le richieste in base al percorso relativo.
[URL Path Extension](#url-path-extension) (Estensione percorso URL) | Identifica le richieste in base all'estensione del nome file.
[URL Path Filename](#url-path-filename) (Nome file percorso URL) | Identifica le richieste in base al nome del file.
[URL Path Literal](#url-path-literal) (Valore letterale percorso URL) | Confronta il percorso relativo di una richiesta con il valore specificato.
[URL Path Regex](#url-path-regex) (Espressione regolare percorso URL) | Confronta il percorso relativo di una richiesta con l'espressione regolare specificata.
[URL Path Wildcard](#url-path-wildcard) (Carattere jolly percorso URL) | Confronta il percorso relativo di una richiesta con il modello specificato.
[URL Query Literal](#url-query-literal) (Valore letterale query URL) | Confronta la stringa di query di una richiesta con il valore specificato.
[URL Query Parameter](#url-query-parameter) (Parametro query URL) | Identifica le richieste che contengono il parametro della stringa di query specificato impostato su un valore che corrisponde a un modello specificato.
[URL Query Regex](#url-query-regex) (Espressione regolare query URL) | Identifica le richieste che contengono il parametro della stringa di query specificato impostato su un valore che corrisponde a un'espressione regolare specificata.
[URL Query Wildcard](#url-query-wildcard) (Carattere jolly query URL) | Confronta uno o più valori specificati con la stringa di query della richiesta.


## <a name="reference-for-rules-engine-match-conditions"></a>Riferimento per le condizioni di corrispondenza del motore regole

---
### <a name="always"></a>Sempre

La condizione di corrispondenza Sempre applica un set predefinito di funzionalità a tutte le richieste.

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>Numero AS 
La rete Numero AS è definita dal relativo numero di sistema autonomo (ASN). 

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza:
- **Matches** (Corrisponde): richiede che l'ASN della rete client corrisponda a uno degli ASN specificati. 
- **Does Not Match** (Non corrisponde): richiede che l'ASN della rete client non corrisponda ad alcuno degli ASN specificati.

Informazioni chiave:
- È possibile specificare più numeri di sistema autonomo delimitandoli con uno spazio singolo. Ad esempio, 64514 64515 corrisponde alle richieste provenienti da 64514 o 64515.
- Determinate richieste potrebbero non restituire un numero di sistema autonomo (ASN) valido. Un punto interrogativo (?) contrassegnerà le richieste per cui non è stato possibile determinare un numero di sistema autonomo (ASN) valido.
- Specificare l'intero ASN per la rete desiderata. Non verrà effettuata una corrispondenza con valori parziali.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cdn-origin"></a>Origine CDN
La condizione di corrispondenza Origine rete CDN viene soddisfatta quando vengono soddisfatte entrambe le condizioni seguenti:
- È stato richiesto contenuto dall'archiviazione della rete CDN.
- L'URI della richiesta usa il tipo di punto di accesso al contenuto, ad esempio /000001, definito nella condizione di corrispondenza:
  - URL della rete CDN: l'URI della richiesta deve contenere il punto di accesso al contenuto selezionato.
  - URL CNAME perimetrale: la configurazione CNAME perimetrale corrispondente deve puntare al punto di accesso al contenuto selezionato.
  
Informazioni chiave:
 - Il punto di accesso al contenuto identifica il servizio che deve gestire il contenuto richiesto.
 - Non usare l'istruzione AND IF per combinare determinate condizioni di corrispondenza. Ad esempio, se si combina una condizione di corrispondenza Origine rete CDN con una condizione di corrispondenza Origine cliente, viene generato un criterio di corrispondenza che non potrà mai restituire risultati. Per questo motivo, due condizioni di corrispondenza Origine rete CDN non possono essere combinate tramite un'istruzione AND IF.

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>Indirizzo IP client
L'opzione **Matches** (Corrisponde)/ **Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza:
- **Matches** (Corrisponde): richiede che l'indirizzo IP del client corrisponda a uno degli indirizzi IP specificati. 
- **Does Not Match** (Non corrisponde): richiede che l'indirizzo IP del client non corrisponda a uno degli indirizzi IP specificati. 

Informazioni chiave:
- Usare la notazione CIDR.
- Per specificare più indirizzi IP e/o blocchi di indirizzi IP, delimitarli con uno spazio singolo. Ad esempio: 
  - **Esempio di indirizzo IPv4**: 1.2.3.4 10.20.30.40 corrisponde alle richieste provenienti dall'indirizzo 1.2.3.4 o 10.20.30.40.
  - **Esempio di indirizzo IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 corrisponde alle richieste provenienti dall'indirizzo 1:2:3:4:5:6:7:8 o 10:20:30:40:50:60:70:80.
- La sintassi per un blocco di indirizzi IP è l'indirizzo IP di base seguito da una barra e dalle dimensioni del prefisso. Ad esempio: 
  - **Esempio di indirizzo IPv4**: 5.5.5.64/26 corrisponde alle richieste provenienti dagli indirizzi compresi tra 5.5.5.64 e 5.5.5.127.
  - **Esempio di indirizzo IPv6**: 1:2:3:/48 corrisponde alle richieste provenienti dagli indirizzi compresi tra 1:2:3:0:0:0:0:0 e 1:2:3:ffff:ffff:ffff:ffff:ffff.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter"></a>Parametro cookie
L'opzione **Matches** (Corrisponde)/**Does Not Matches** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Corrisponde**: prevede che una richiesta contenga il cookie specificato con un valore che corrisponda ad almeno uno dei valori definiti nella condizione di corrispondenza.
- **Does Not Match** (Non corrisponde): specifica che la richiesta soddisfi uno dei criteri seguenti:
  - Non contiene il cookie specificato.
  - Contiene il cookie specificato, ma il relativo valore non corrisponde ad alcun valore definito nella condizione di corrispondenza.
  
Informazioni chiave:
- Nome cookie: 
  - Poiché i valori con caratteri jolly, inclusi gli asterischi (*), non sono supportati per specificare un nome di cookie, solo le corrispondenze esatte dei nomi di cookie sono idonee per il confronto.
  - È possibile specificare un solo nome di cookie per istanza della condizione di corrispondenza.
  - I confronti tra nomi di cookie non fanno distinzione tra maiuscole e minuscole.
- Valore cookie: 
  - È possibile specificare più valori cookie delimitandoli con uno spazio singolo.
  - Un valore di cookie può usare [caratteri jolly](cdn-rules-engine-reference.md#wildcard-values). 
  - Se non è stato specificato un carattere jolly, solo una corrispondenza esatta potrà soddisfare questa condizione di corrispondenza. Se ad esempio si specifica "Valore", verrà trovata una corrispondenza con "Valore", ma non con "Valore1" o "Valore2".
  - Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per determinare se effettuare un controllo rispetto al valore di cookie della richiesta tenendo conto o meno della distinzione tra maiuscole e minuscole.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter-regex"></a>Espressione regolare parametro cookie
La condizione di corrispondenza Espressione regolare parametro cookie definisce un nome e un valore del cookie. Per definire il valore di cookie desiderato, è possibile usare [espressioni regolari](cdn-rules-engine-reference.md#regular-expressions). 

L'opzione **Matches** (Corrisponde)/**Does Not Matches** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Corrisponde**: prevede che la richiesta contenga il cookie specificato con un valore che corrisponde all'espressione regolare specificata.
- **Does Not Match** (Non corrisponde): specifica che la richiesta soddisfi uno dei criteri seguenti:
  - Non contiene il cookie specificato.
  - Contiene il cookie specificato, ma il relativo valore non corrisponde all'espressione regolare specificata.
  
Informazioni chiave:
- Nome cookie: 
  - Poiché le espressioni regolari e i caratteri jolly, inclusi gli asterischi (*), non sono supportati per specificare un nome di cookie, solo le corrispondenze esatte dei nomi di cookie sono idonee per il confronto.
  - È possibile specificare un solo nome di cookie per istanza della condizione di corrispondenza.
  - I confronti tra nomi di cookie non fanno distinzione tra maiuscole e minuscole.
- Valore cookie: 
  - Per i valori cookie è possibile usare le espressioni regolari.
  - Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per determinare se effettuare un controllo rispetto al valore di cookie della richiesta tenendo conto o meno della distinzione tra maiuscole e minuscole.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

--- 
### <a name="country"></a>Paese
È possibile specificare un paese tramite il codice paese corrispondente. 

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza:
- **Matches** (Corrisponde): specifica che la richiesta contenga i valori del codice di paese specificati. 
- **Does Not Match** (Non corrisponde): specifica che la richiesta non contenga i valori del codice di paese specificati.

Informazioni chiave:
- È possibile specificare più codici paese delimitandoli con uno spazio singolo.
- Per specificare i codici paese, i caratteri jolly non sono supportati.
- I codici paese "EU" e "AP" non includono tutti gli indirizzi IP in tali aree.
- Determinate richieste potrebbero non restituire un codice paese valido. Un punto interrogativo (?) contrassegnerà le richieste per cui non è stato possibile determinare un codice paese valido.
- I codici paese fanno distinzione tra maiuscole e minuscole.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementazione del filtro dei paesi tramite il motore regole
Questa condizione di corrispondenza permette di eseguire numerose personalizzazioni in base alla posizione da cui ha origine una richiesta. Ad esempio, il comportamento della funzionalità del filtro dei paesi può essere replicato tramite la configurazione seguente:

- Corrispondenza URL Path Wildcard (Carattere jolly percorso URL): impostare la condizione di corrispondenza [URL Path Wildcard](#url-path-wildcard) (Carattere jolly percorso URL) sulla directory che verrà protetta. 
    Aggiungere un asterisco alla fine del percorso relativo per assicurarsi che l'accesso a tutti gli elementi figlio venga limitato da questa regola.

- Corrispondenza Paese: impostare la condizione di corrispondenza Paese sul set desiderato di paesi.
   - Consenti: impostare la condizione di corrispondenza Paese su **Does Not Match** (Non corrisponde) per consentire solo ai paesi specificati l'accesso al contenuto archiviato nella posizione definita dalla condizione di corrispondenza URL Path Wildcard (Carattere jolly percorso URL).
   - Blocca: impostare la condizione di corrispondenza Paese su **Matches** (Corrisponde) per impedire ai paesi specificati l'accesso al contenuto archiviato nella posizione definita dalla condizione di corrispondenza URL Path Wildcard (Carattere jolly percorso URL).

- Funzionalità Deny Access (403) (Nega accesso - 403): abilitare la funzionalità [Deny Access (403)](cdn-rules-engine-reference-features.md#deny-access-403) (Nega accesso - 403) per replicare la parte Consenti o Blocca della funzionalità del filtro dei paesi.

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>Origine cliente

Informazioni chiave: 
- La condizione di corrispondenza Customer Origin (Origine cliente) viene soddisfatta indipendentemente dal fatto che il contenuto venga richiesto tramite un URL della rete CDN o un URL CNAME perimetrale che punta all'origine cliente selezionata.
- Non è possibile eliminare dalla pagina Origine cliente una configurazione di origine cliente a cui fa riferimento una regola. Prima di provare a eliminare una configurazione di origine cliente, assicurarsi che le configurazioni seguenti non vi facciano riferimento:
  - Una condizione di corrispondenza Origine cliente
  - Una configurazione CNAME perimetrale
- Non usare l'istruzione AND IF per combinare determinate condizioni di corrispondenza. Ad esempio, se si combina una condizione di corrispondenza Origine cliente con una condizione di corrispondenza Origine rete CDN, viene generato un criterio di corrispondenza che non potrà mai restituire risultati. Per questo motivo, due condizioni di corrispondenza Origine cliente non possono essere combinate tramite un'istruzione AND IF.

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>Dispositivo

La condizione di corrispondenza Dispositivo identifica le richieste effettuate da un dispositivo mobile in base alle relative proprietà. Il rilevamento del dispositivo mobile viene eseguito tramite [WURFL](http://wurfl.sourceforge.net/). 

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza:
- **Matches** (Corrisponde): specifica che il dispositivo del richiedente corrisponda al valore specificato. 
- **Does Not Match** (Non corrisponde): specifica che il dispositivo del richiedente non corrisponda al valore specificato.

Informazioni chiave:

- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per specificare se il valore specificato fa distinzione tra maiuscole e minuscole.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

#### <a name="string-type"></a>Tipo di stringa
Una funzionalità WURFL accetta in genere qualsiasi combinazione di numeri, lettere e simboli. A causa della natura flessibile di questa funzionalità, è necessario scegliere il modo in cui interpretare il valore associato alla condizione di corrispondenza. La tabella seguente descrive il set di opzioni disponibile:

type     | DESCRIZIONE
---------|------------
Literal (Valore letterale)  | Selezionare questa opzione per impedire alla maggior parte dei caratteri di assumere un significato speciale usando il rispettivo [valore letterale](cdn-rules-engine-reference.md#literal-values).
Wildcard (Carattere jolly) | Selezionare questa opzione per trarre vantaggio da tutti i [caratteri jolly]([caratteri jolly](cdn-rules-engine-reference.md#wildcard-values).
Regex (Espressione regolare)    | Selezionare questa opzione per usare [espressioni regolari](cdn-rules-engine-reference.md#regular-expressions). Le espressioni regolari sono utili per definire un modello di caratteri.

#### <a name="wurfl-capabilities"></a>Funzionalità WURFL
Una funzionalità WURFL fa riferimento a una categoria che descrive i dispositivi mobili. La funzionalità selezionata determina il tipo di descrizione del dispositivo mobile usata per identificare le richieste.

La tabella seguente elenca le funzionalità WURFL e le rispettive variabili per il motore regole.
<br>
> [!NOTE] 
> Le variabili seguenti sono supportate nelle funzionalità **Modify Client Request Header** (Modifica intestazione richiesta client) e **Modify Client Response Header** (Modifica intestazione risposta client).

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
Mobile Browser (Browser per dispositivi mobili) | %{wurfl_cap_mobile_browser} | Stringa che indica il browser usato per richiedere contenuto dal dispositivo. | Chrome
Mobile Browser Version (Versione browser per dispositivi mobili) | %{wurfl_cap_mobile_browser_version} | Stringa che indica la versione del browser usato per richiedere contenuto dal dispositivo. | 31
Model Name (Nome modello) | %{wurfl_cap_model_name} | Stringa che indica il nome del modello del dispositivo. | s3
Download progressivo | %{wurfl_cap_progressive_download} | Valore booleano che indica se il dispositivo supporta la riproduzione di audio e video mentre il download è ancora in corso. | true
Data di rilascio | %{wurfl_cap_release_date} | Stringa che indica il mese e l'anno in cui il dispositivo è stato aggiunto al database WURFL.<br/><br/>Formato: `yyyy_mm` | 2013_december
Resolution Height (Altezza risoluzione) | %{wurfl_cap_resolution_height} | Numero intero che indica l'altezza del dispositivo in pixel. | 768
Resolution Width (Larghezza risoluzione) | %{wurfl_cap_resolution_width} | Numero intero che indica la larghezza del dispositivo in pixel. | 1024

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="edge-cname"></a>CNAME periferico
Informazioni chiave: 
- L'elenco degli URL CNAME perimetrali disponibili è limitato a quelli che sono stati configurati nella pagina degli URL CNAME perimetrali per la piattaforma in cui viene configurato il motore regole.
- Prima di provare a eliminare una configurazione CNAME perimetrale, assicurarsi che una condizione di corrispondenza CNAME perimetrale non vi faccia riferimento. Le configurazioni CNAME perimetrali definite in una regola non possono essere eliminate dalla pagina CNAME perimetrali. 
- Non usare l'istruzione AND IF per combinare determinate condizioni di corrispondenza. Ad esempio, se si combina una condizione di corrispondenza CNAME perimetrale con una condizione di corrispondenza Origine cliente, viene generato un criterio di corrispondenza che non potrà mai restituire risultati. Per questo motivo, due condizioni di corrispondenza CNAME perimetrale non possono essere combinate tramite un'istruzione AND IF.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="referring-domain"></a>Dominio di riferimento
Il nome host associato al referrer tramite cui il contenuto è stato richiesto determina se viene soddisfatta o meno la condizione Dominio di riferimento. 

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza:
- **Matches** (Corrisponde): richiede che il nome host corrisponda al valore specificato. 
- **Does Not Match** (Non corrisponde): richiede che il nome host non corrisponda al valore specificato.

Informazioni chiave:
- È possibile specificare più nomi host delimitandoli con uno spazio singolo.
- Questa condizione di corrispondenza supporta i [caratteri jolly](cdn-rules-engine-reference.md#wildcard-values).
- Se il valore specificato non contiene un asterisco, deve essere una corrispondenza esatta per il nome host del referrer. Ad esempio, se si specifica "miodominio.com" non verrà trovata una corrispondenza con "www.miodominio.com".
- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per determinare se il controllo deve essere eseguito tenendo conto o meno della distinzione tra maiuscole e minuscole.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-literal"></a>Valore letterale intestazione richiesta
L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Corrisponde**: prevede che la richiesta contenga l'intestazione specificata. Il relativo valore deve corrispondere al valore definito in questa condizione di corrispondenza.
- **Does Not Match** (Non corrisponde): specifica che la richiesta soddisfi uno dei criteri seguenti:
  - Non contiene l'intestazione specificata.
  - Contiene l'intestazione specificata, ma il relativo valore non corrisponde al valore definito in questa condizione di corrispondenza.
  
Informazioni chiave:
- I confronti tra nomi di intestazioni non fanno mai distinzione tra maiuscole e minuscole. Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per controllare la distinzione tra maiuscole e minuscole nei confronti dei valori di intestazione.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-regex"></a>Espressione regolare intestazione richiesta
L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Corrisponde**: prevede che la richiesta contenga l'intestazione specificata. Il valore deve corrispondere al modello definito nell'[espressione regolare](cdn-rules-engine-reference.md#regular-expressions) specificata.
- **Does Not Match** (Non corrisponde): specifica che la richiesta soddisfi uno dei criteri seguenti:
  - Non contiene l'intestazione specificata.
  - Contiene l'intestazione specificata, ma il relativo valore non corrisponde all'espressione regolare specificata.

Informazioni chiave:
- Nome intestazione: 
  - I confronti tra nomi di intestazioni non fanno distinzione tra maiuscole e minuscole.
  - Sostituire gli spazi nel nome dell'intestazione con "%20". 
- Valore dell'intestazione: 
  - Per i valori delle impostazioni è possibile usare le espressioni regolari.
  - Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per controllare la distinzione tra maiuscole e minuscole nei confronti dei valori di intestazione.
  - Questa condizione verrà soddisfatta solo se il valore dell'intestazione corrisponde esattamente ad almeno uno dei modelli specificati.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno 

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-header-wildcard"></a>Carattere jolly intestazione richiesta
L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Corrisponde**: prevede che la richiesta contenga l'intestazione specificata. Il relativo valore deve corrispondere ad almeno uno dei valori definiti in questa condizione di corrispondenza.
- **Does Not Match** (Non corrisponde): specifica che la richiesta soddisfi uno dei criteri seguenti:
  - Non contiene l'intestazione specificata.
  - Contiene l'intestazione specificata, ma il relativo valore non corrisponde ad alcun valore specificato.
  
Informazioni chiave:
- Nome intestazione: 
  - I confronti tra nomi di intestazioni non fanno distinzione tra maiuscole e minuscole.
  - Nel nome dell'intestazione gli spazi devono essere sostituiti con "%20". È anche possibile usare questo valore per specificare gli spazi in un valore dell'intestazione.
- Valore dell'intestazione: 
  - Un valore di intestazione può usare [caratteri jolly](cdn-rules-engine-reference.md#wildcard-values).
  - Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per controllare la distinzione tra maiuscole e minuscole nei confronti dei valori di intestazione.
  - Questa condizione di corrispondenza verrà soddisfatta quando un valore di intestazione corrisponde esattamente ad almeno uno dei modelli specificati.
  - È possibile specificare più valori delimitandoli con uno spazio singolo.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-method"></a>Metodo richiesta
La condizione Metodo di richiesta verrà soddisfatta solo quando gli asset vengono richiesti tramite il metodo di richiesta selezionato. I metodi richiesta disponibili sono:
- GET
- HEAD 
- POST 
- OPTIONS 
- PUT 
- DELETE 
- TRACE 
- CONNECT 

Informazioni chiave:
- Per impostazione predefinita, solo il metodo di richiesta GET può generare contenuto memorizzato nella cache nella rete. Tutti gli altri metodi di richiesta vengono elaborati tramite la rete.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-scheme"></a>Schema richiesta
La condizione Request Scheme (Schema di richiesta) verrà soddisfatta solo quando gli asset vengono richiesti tramite il protocollo selezionato. I protocolli disponibili sono i seguenti: 
- HTTP
- HTTPS

Informazioni chiave:
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-directory"></a>Directory percorso URL
Identifica una richiesta tramite il percorso relativo, che esclude il nome file dell'asset richiesto.

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Matches** (Corrisponde): specifica che la richiesta deve contenere un percorso URL relativo, escluso il nome file, che corrisponda al modello di URL specificato.
- **Does Not Match** (Non corrisponde): specifica che la richiesta deve contenere un percorso URL relativo, escluso il nome file, che non corrisponda al modello di URL specificato.

Informazioni chiave:
- Usare l'opzione **Relative to** (Relativo a) per specificare se il confronto degli URL debba iniziare prima o dopo il punto di accesso al contenuto. Il punto di accesso al contenuto è la parte del percorso visualizzata tra il nome host della rete CDN Verizon e il percorso relativo dell'asset richiesto, ad esempio /800001/CustomerOrigin. Identifica una posizione in base al tipo di server, ad esempio rete CDN o origine del client, e il numero di account del cliente.

   Per l'opzione **Relative to** (Relativo a) sono disponibili i valori seguenti:
   - **Radice**: indica che il punto di confronto degli URL deve iniziare dopo il nome host della rete CDN. 

     Ad esempio: http:\//wpc.0001.&lt;dominio&gt;/**800001/myorigin/cartella**/index.htm

   - **Origine**: indica che il punto di confronto degli URL deve iniziare dopo il punto di accesso al contenuto, ad esempio /000001 o /800001/myorigin. Poiché per impostazione predefinita l'URL CNAME \*.azureedge.net viene creato relativamente alla directory di origine nel nome host della rete CDN Verizon, gli utenti della rete CDN di Azure devono usare il valore **Origine**. 

     Ad esempio: https:\//&lt;endpoint&gt;.azureedge.net/**cartella**/index.htm 

     Questo URL punta a questo nome host della rete CDN Verizon: http:\//wpc.0001.&lt;Dominio&gt;/800001/myorigin/**cartella**/index.htm

- Un URL CNAME perimetrale viene riscritto in un URL della rete CDN prima del confronto degli URL.

    Ad esempio, entrambi gli URL seguenti puntano allo stesso asset e di conseguenza hanno lo stesso percorso URL.
    - URL della rete CDN: http:\//wpc.0001.&lt;Dominio&gt;/800001/CustomerOrigin/path/asset.htm
    
    - URL CNAME perimetrale: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Informazioni aggiuntive:
    - Dominio personalizzato: https:\//my.domain.com/path/asset.htm
    
    - Percorso URL (relativo alla radice): /800001/CustomerOrigin/path/
    
    - Percorso URL (relativo all'origine): /path/

- La parte dell'URL usata per il confronto degli URL termina appena prima del nome file dell'asset richiesto. Una barra finale è l'ultimo carattere in questo tipo di percorso.
    
- Sostituire tutti gli spazi nel modello di percorso URL con "%20".
    
- Ogni modello di percorso URL può contenere uno o più asterischi (*), in cui ogni asterisco corrisponde a una sequenza di uno o più caratteri.
    
- Specificare più percorsi URL nel modello delimitandoli con uno spazio singolo.

    Ad esempio: */sales/ */marketing/

- Per specificare un percorso URL è possibile usare [caratteri jolly](cdn-rules-engine-reference.md#wildcard-values).

- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per determinare se effettuare un controllo tenendo conto o meno della distinzione tra maiuscole e minuscole.

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-extension"></a>Estensione percorso URL
Identifica le richieste in base all'estensione di file dell'asset richiesto.

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Matches** (Corrisponde): specifica che l'URL della richiesta deve contenere un'estensione di file che corrisponda esattamente al modello specificato.

   Ad esempio, se si specifica "htm", gli asset "htm" soddisfano la condizione, ma non gli asset "html".  

- **Does Not Match** (Non corrisponde): specifica che l'URL della richiesta deve contenere un'estensione di file che non corrisponda al modello specificato.

Informazioni chiave:
- Specificare le estensioni per la corrispondenza nella casella **Valore**. Non includere un punto iniziale. Ad esempio, usare htm invece di .htm.

- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per determinare se effettuare un controllo tenendo conto o meno della distinzione tra maiuscole e minuscole.

- Specificare più estensioni delimitandole con uno spazio singolo. 

    Ad esempio: htm html

- Ad esempio, se si specifica "htm", gli asset "htm" soddisfano la condizione, ma non gli asset "html".


#### <a name="sample-scenario"></a>Scenario di esempio

La configurazione di esempio seguente presuppone che questa condizione di corrispondenza venga soddisfatta quando una richiesta corrisponde a una delle estensioni specificate.

Valori specificati: asp aspx php html

Questa condizione di corrispondenza verrà soddisfatta quando vengono trovati URL che terminano con le estensioni seguenti:
- .asp
- aspx
- .php
- .html

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-filename"></a>Nome file percorso URL
Identifica le richieste in base al nome file dell'asset richiesto. Ai fini di questa condizione di corrispondenza, un nome file è costituito dal nome dell'asset richiesto, da un punto e dall'estensione del file, ad esempio index.html.

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Matches** (Corrisponde): specifica che la richiesta deve contenere un nome file nel rispettivo percorso URL che corrisponda al modello specificato.
- **Does Not Match** (Non corrisponde): specifica che la richiesta deve contenere un nome file nel rispettivo percorso URL che non corrisponda al modello specificato.

Informazioni chiave:
- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per determinare se effettuare un controllo tenendo conto o meno della distinzione tra maiuscole e minuscole.

- Per specificare più estensioni, separarle con uno spazio singolo.

    Ad esempio: index.htm index.html

- Sostituire gli spazi nei nomi file con "%20".
    
- Un nome file può usare [caratteri jolly](cdn-rules-engine-reference.md#wildcard-values). Ad esempio, ogni modello di nome file può contenere uno o più asterischi (*), in cui ogni asterisco corrisponde a una sequenza di uno o più caratteri.
    
- Se non vengono specificati caratteri jolly, solo una corrispondenza esatta potrà soddisfare questa condizione di corrispondenza.

    Ad esempio, se si specifica "presentazione.ppt", un asset denominato "presentazione.ppt" soddisferà la condizione, ma non uno denominato "presentazione.pptx".

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-literal"></a>Valore letterale percorso URL
Confronta il percorso URL di una richiesta, incluso il nome file, con il valore specificato.

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Matches** (Corrisponde): specifica che la richiesta deve contenere un percorso URL che corrisponda al modello specificato.
- **Does Not Match** (Non corrisponde): specifica che la richiesta deve contenere un percorso URL che non corrisponda al modello specificato.

Informazioni chiave:
- Usare l'opzione **Relative to** (Relativo a) per specificare se il punto di confronto degli URL debba iniziare prima o dopo il punto di accesso al contenuto. 

    Per l'opzione **Relative to** (Relativo a) sono disponibili i valori seguenti:
     - **Radice**: indica che il punto di confronto degli URL deve iniziare dopo il nome host della rete CDN.

       Ad esempio: http:\//wpc.0001.&lt;Dominio&gt;/**800001/myorigin/cartella/index.htm**

     - **Origine**: indica che il punto di confronto degli URL deve iniziare dopo il punto di accesso al contenuto, ad esempio /000001 o /800001/myorigin. Poiché per impostazione predefinita l'URL CNAME \*.azureedge.net viene creato relativamente alla directory di origine nel nome host della rete CDN Verizon, gli utenti della rete CDN di Azure devono usare il valore **Origine**. 

       Ad esempio: https:\//&lt;endpoint&gt;.azureedge.net/**cartella/index.htm**

     Questo URL punta a questo nome host della rete CDN Verizon: http:\//wpc.0001.&lt;Dominio&gt;/800001/myorigin/**cartella/index.htm**

- Un URL CNAME perimetrale viene riscritto in un URL della rete CDN prima del confronto degli URL.

   Ad esempio, entrambi gli URL seguenti puntano allo stesso asset e di conseguenza hanno lo stesso percorso URL:
    - URL della rete CDN: http:\//wpc.0001.&lt;Dominio&gt;/800001/CustomerOrigin/path/asset.htm
    - URL CNAME perimetrale: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

   Informazioni aggiuntive:
    
    - Percorso URL (relativo alla radice): /800001/CustomerOrigin/path/asset.htm
   
    - Percorso URL (relativo all'origine): /path/asset.htm

- Le stringhe di query nell'URL vengono ignorate.
- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per determinare se effettuare un controllo tenendo conto o meno della distinzione tra maiuscole e minuscole.
- Il valore specificato per questa condizione di corrispondenza verrà confrontato rispetto al percorso relativo della richiesta esatta effettuata dal client.

- Per trovare la corrispondenza di tutte le richieste effettuate in una directory particolare, usare la condizione di corrispondenza [URL Path Directory](#url-path-directory) (Directory percorso URL) o [URL Path Wildcard](#url-path-wildcard) (Carattere jolly percorso URL).

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-regex"></a>Espressione regolare percorso URL
Confronta il percorso URL di una richiesta con l'[espressione regolare](cdn-rules-engine-reference.md#regular-expressions) specificata.

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Matches** (Corrisponde): specifica che la richiesta deve contenere un percorso URL che corrisponda all'espressione regolare specificata.
- **Does Not Match** (Non corrisponde): specifica che la richiesta deve contenere un percorso URL che non corrisponda all'espressione regolare specificata.

Informazioni chiave:
- Un URL CNAME perimetrale viene riscritto in un URL della rete CDN prima del confronto degli URL. 
 
   Ad esempio, entrambi gli URL puntano allo stesso asset e di conseguenza hanno lo stesso percorso URL.

     - URL della rete CDN: http:\//wpc.0001.&lt;Dominio&gt;/800001/CustomerOrigin/path/asset.htm

     - URL CNAME perimetrale: http:\//my.domain.com/path/asset.htm

   Informazioni aggiuntive:
    
     - Percorso URL: /800001/CustomerOrigin/path/asset.htm

- Le stringhe di query nell'URL vengono ignorate.
    
- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per determinare se effettuare un controllo tenendo conto o meno della distinzione tra maiuscole e minuscole.
    
- Gli spazi nel percorso URL devono essere sostituiti con "%20".

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-wildcard"></a>Carattere jolly percorso URL
Confronta il percorso URL relativo di una richiesta con il modello con caratteri jolly specificato.

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Matches** (Corrisponde): specifica che la richiesta deve contenere un percorso URL che corrisponda al modello con caratteri jolly specificato.
- **Does Not Match** (Non corrisponde): specifica che la richiesta deve contenere un percorso URL che non corrisponda al modello con caratteri jolly specificato.

Informazioni chiave:
- Opzione **Relative to** (Relativo a): questa opzione determina se il punto di confronto degli URL deve iniziare prima o dopo il punto di accesso al contenuto.

   L'opzione può avere i valori seguenti:
     - **Radice**: indica che il punto di confronto degli URL deve iniziare dopo il nome host della rete CDN.

       Ad esempio: http:\//wpc.0001.&lt;Dominio&gt;/**800001/myorigin/cartella/index.htm**

     - **Origine**: indica che il punto di confronto degli URL deve iniziare dopo il punto di accesso al contenuto, ad esempio /000001 o /800001/myorigin. Poiché per impostazione predefinita l'URL CNAME \*.azureedge.net viene creato relativamente alla directory di origine nel nome host della rete CDN Verizon, gli utenti della rete CDN di Azure devono usare il valore **Origine**. 

       Ad esempio: https:\//&lt;endpoint&gt;.azureedge.net/**cartella/index.htm**

     Questo URL punta a questo nome host della rete CDN Verizon: http:\//wpc.0001.&lt;Dominio&gt;/800001/myorigin/**cartella/index.htm**

- Un URL CNAME perimetrale viene riscritto in un URL della rete CDN prima del confronto degli URL.

   Ad esempio, entrambi gli URL seguenti puntano allo stesso asset e di conseguenza hanno lo stesso percorso URL:
     - URL della rete CDN: http://wpc.0001.&lt;Dominio&gt;/800001/CustomerOrigin/path/asset.htm
     - URL CNAME perimetrale: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

   Informazioni aggiuntive:
    
     - Percorso URL (relativo alla radice): /800001/CustomerOrigin/path/asset.htm
    
     - Percorso URL (relativo all'origine): /path/asset.htm
    
- Specificare più percorsi URL delimitandoli con uno spazio singolo.

   Ad esempio: /marketing/asset.* /sales/*.htm

- Le stringhe di query nell'URL vengono ignorate.
    
- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per determinare se effettuare un controllo tenendo conto o meno della distinzione tra maiuscole e minuscole.
    
- Sostituire gli spazi nel percorso URL con "%20".
    
- Il valore specificato in un percorso URL può usare [caratteri jolly](cdn-rules-engine-reference.md#wildcard-values). Ogni modello di percorso URL può contenere uno o più asterischi (*), in cui ogni asterisco può corrispondere a una sequenza di uno o più caratteri.

#### <a name="sample-scenarios"></a>Scenari di esempio

Le configurazioni di esempio nella tabella seguente presuppongono che questa condizione di corrispondenza venga soddisfatta quando una richiesta corrisponde al modello URL specificato:

Valore                   | Relative to (Relativo a)    | Risultato 
------------------------|----------------|-------
*/test.html */test.php  | Radice o Origine | Le richieste di asset denominati "test.html" o "test.php" in qualsiasi cartella soddisfano la corrispondenza a questo modello.
/80ABCD/origin/text/*   | Radice           | L'asset richiesto corrisponde a questo modello quando soddisfa i criteri seguenti: <br />- Si trova in un'origine cliente denominata "origin". <br />- Il percorso relativo inizia con una cartella denominata "text". Questo significa che l'asset richiesto può trovarsi nella cartella "text" o in una delle sottocartelle ricorsive.
*/css/* */js/*          | Radice o Origine | Tutti gli URL CNAME perimetrali che contengono una cartella css o js soddisfano la corrispondenza a questo modello.
*.jpg *.gif *.png       | Radice o Origine | Tutti gli URL CNAME perimetrali o di rete CDN che terminano con .jpg, .gif o .png. soddisfano la corrispondenza a questo modello. Un metodo alternativo per specificare questo modello è la condizione di corrispondenza [URL Path Extension](#url-path-extension) (Estensione percorso URL).
/images/* /media/*      | Origine         | Gli URL CNAME perimetrali o di rete CDN il cui percorso relativo inizia con una cartella "images" o "media" soddisfano la corrispondenza a questo modello. <br />- URL della rete CDN: http:\//wpc.0001.&lt;Dominio&gt;/800001/myorigin/images/sales/event1.png<br />- URL CNAME perimetrale di esempio: http:\//cdn.mydomain.com/images/sales/event1.png

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-literal"></a>Valore letterale query URL
Confronta la stringa di query di una richiesta con il valore specificato.

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Matches** (Corrisponde): specifica che la richiesta deve contenere una stringa di query URL che corrisponda alla stringa di query specificata.
- **Does Not Match** (Non corrisponde): specifica che la richiesta deve contenere una stringa di query URL che non corrisponda alla stringa di query specificata.

Informazioni chiave:

- Solo le corrispondenze esatte delle stringhe di query soddisfano questa condizione di corrispondenza.
    
- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per controllare la distinzione tra maiuscole e minuscole nei confronti delle stringhe di query.
    
- Non includere un punto interrogativo iniziale (?) nel testo del valore della stringa di query.
    
- Determinati caratteri richiedono la codifica URL. Usare il simbolo di percentuale per applicare la codifica URL ai caratteri seguenti:

   Character | Codifica URL
   ----------|---------
   Spazio     | %20
   &         | %25

- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
   - Completa riempimento cache
   - Max-Age interno predefinito
   - Forza Max-Age interno
   - Ignora origine No-Cache
   - Max-Stale interno

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-parameter"></a>Parametro query URL
Identifica le richieste che contengono il parametro della stringa di query specificato. Questo parametro è impostato su un valore che corrisponde a un modello specificato. I parametri della stringa di query, ad esempio parametro=valore, nell'URL della richiesta determinano se questa condizione viene soddisfatta. Questa condizione di corrispondenza identifica un parametro della stringa di query in base al nome e accetta uno o più valori per il valore del parametro. 

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Matches** (Corrisponde): specifica che una richiesta deve contenere il parametro specificato con un valore che corrisponda ad almeno uno dei valori definiti nella condizione di corrispondenza.
- **Does Not Match** (Non corrisponde): specifica che la richiesta soddisfi uno dei criteri seguenti:
  - Non contiene il parametro specificato.
  - Contiene il parametro specificato, ma il valore non corrisponde ad alcuno dei valori definiti nella condizione di corrispondenza.

Questa condizione di corrispondenza offre un semplice metodo per specificare le combinazioni di nome/valore del parametro. Per maggiore flessibilità per la corrispondenza di un parametro della stringa di query, provare a usare la condizione di corrispondenza [URL Query Wildcard](#url-query-wildcard) (Carattere jolly query URL).

Informazioni chiave:
- È possibile specificare un solo nome di parametro di query URL per istanza di questa condizione di corrispondenza.
    
- Poiché i caratteri jolly non sono supportati per specificare un nome di parametro, solo le corrispondenze esatte dei nomi di parametro sono idonee per il confronto.
- I valori di parametro possono includere [caratteri jolly](cdn-rules-engine-reference.md#wildcard-values).
   - Ogni modello di valore di parametro può contenere uno o più asterischi (*), in cui ogni asterisco può corrispondere a una sequenza di uno o più caratteri.
   - Determinati caratteri richiedono la codifica URL. Usare il simbolo di percentuale per applicare la codifica URL ai caratteri seguenti:

       Character | Codifica URL
       ----------|---------
       Spazio     | %20
       &         | %25

- Specificare più valori di parametro della stringa di query delimitandoli con uno spazio singolo. Questa condizione di corrispondenza verrà soddisfatta quando una richiesta contiene una delle combinazioni di nome/valore specificate.

   - Esempio 1:

     - Configurazione:

       ValoreA ValoreB

     - Questa configurazione corrisponde ai parametri della stringa di query seguenti:

       Parametro1=ValoreA
    
       Parametro1=ValoreB

   - Esempio 2

     - Configurazione: 

        Valore%20A Valore%20B

     - Questa configurazione corrisponde ai parametri della stringa di query seguenti:

       Parametro1=Valore%20A

       Parametro1=Valore%20B

- Questa condizione di corrispondenza verrà soddisfatta solo quando esiste una corrispondenza esatta con almeno una delle combinazioni di nome/valore della stringa di query specificate.

   Ad esempio, se si usa la configurazione specificata nell'esempio precedente, la combinazione di nome/valore "Parametro1=ValoreAdd" non verrà considerata una corrispondenza. Tuttavia, se si specifica uno dei valori seguenti, la corrispondenza alla combinazione di nome/valore verrà soddisfatta:

   - ValoreA ValoreB ValoreAdd
   - ValoreA* ValoreB

- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per controllare la distinzione tra maiuscole e minuscole nei confronti delle stringhe di query.
    
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
   - Completa riempimento cache
   - Max-Age interno predefinito
   - Forza Max-Age interno
   - Ignora origine No-Cache
   - Max-Stale interno

#### <a name="sample-scenarios"></a>Scenari di esempio
L'esempio seguente mostra il funzionamento di questa opzione in situazioni specifiche:

NOME      | Valore |  Risultato
----------|-------|--------
Utente      | Joe   | La corrispondenza a questo modello viene soddisfatta quando la stringa di query per un URL richiesto è "?user=joe".
Utente      | *     | La corrispondenza a questo modello viene soddisfatta quando la stringa di query per un URL richiesto contiene un parametro User.
Email Joe | *     | La corrispondenza a questo modello viene soddisfatta quando la stringa di query per un URL richiesto contiene un parametro Email che inizia con "Joe".

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-regex"></a>Espressione regolare query URL
Identifica le richieste che contengono il parametro della stringa di query specificato. Questo parametro è impostato su un valore che corrisponde a un'[espressione regolare](cdn-rules-engine-reference.md#regular-expressions) specificata.

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Matches** (Corrisponde): specifica che la richiesta deve contenere una stringa di query URL che corrisponda all'espressione regolare specificata.
- **Does Not Match** (Non corrisponde): specifica che la richiesta deve contenere una stringa di query URL che non corrisponda all'espressione regolare specificata.

Informazioni chiave:
- Solo le corrispondenze esatte all'espressione regolare specificata soddisfano questa condizione di corrispondenza.
    
- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per controllare la distinzione tra maiuscole e minuscole nei confronti delle stringhe di query.
    
- Ai fini di questa opzione, una stringa di query inizia con il primo carattere dopo il delimitatore punto interrogativo (?) per la stringa di query.
    
- Determinati caratteri richiedono la codifica URL. Usare il simbolo di percentuale per applicare la codifica URL ai caratteri seguenti:

   Character | Codifica URL | Valore
   ----------|--------------|------
   Spazio     | %20          | \%20
   &         | %25          | \%25

   I simboli di percentuale devono essere preceduti da un carattere di escape.

- I caratteri speciali nelle espressioni regolari devono essere preceduti da un doppio carattere di escape, ad esempio \^$.+, per includere una barra rovesciata nell'espressione regolare.

   Ad esempio: 

   Valore | Interpretato come 
   ------|---------------
   \\+    | +
   \\\+   | \\+

- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
   - Completa riempimento cache
   - Max-Age interno predefinito
   - Forza Max-Age interno
   - Ignora origine No-Cache
   - Max-Stale interno


[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-wildcard"></a>Carattere jolly query URL
Confronta uno o più valori specificati con la stringa di query della richiesta.

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Matches** (Corrisponde): specifica che la richiesta deve contenere una stringa di query URL che corrisponda al carattere jolly specificato.
- **Does Not Match** (Non corrisponde): specifica che la richiesta deve contenere una stringa di query URL che non corrisponda al carattere jolly specificato.

Informazioni chiave:
- Ai fini di questa opzione, una stringa di query inizia con il primo carattere dopo il delimitatore punto interrogativo (?) per la stringa di query.
- I valori di parametro possono includere [caratteri jolly](cdn-rules-engine-reference.md#wildcard-values):
   - Ogni modello di valore di parametro può contenere uno o più asterischi (*), in cui ogni asterisco può corrispondere a una sequenza di uno o più caratteri.
   - Determinati caratteri richiedono la codifica URL. Usare il simbolo di percentuale per applicare la codifica URL ai caratteri seguenti:

     Character | Codifica URL
     ----------|---------
     Spazio     | %20
     &         | %25

- È possibile specificare più valori delimitandoli con uno spazio singolo.

   Ad esempio: *Parametro1=ValoreA* *ValoreB* *Parametro1=ValoreC&Parametro2=ValoreD*

- Questa condizione verrà soddisfatta solo da corrispondenze esatte ad almeno uno dei modelli di stringa di query specificati.
    
- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per controllare la distinzione tra maiuscole e minuscole nei confronti delle stringhe di query.
    
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
   - Completa riempimento cache
   - Max-Age interno predefinito
   - Forza Max-Age interno
   - Ignora origine No-Cache
   - Max-Stale interno

#### <a name="sample-scenarios"></a>Scenari di esempio
L'esempio seguente mostra il funzionamento di questa opzione in situazioni specifiche:

 NOME                 | DESCRIZIONE
 ---------------------|------------
user=joe              | La corrispondenza a questo modello viene soddisfatta quando la stringa di query per un URL richiesto è "?user=joe".
\*user=\* \*optout=\* | La corrispondenza a questo modello viene soddisfatta quando la query URL della rete CDN contiene il parametro user o optout.

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica della rete per la distribuzione di contenuti di Azure](cdn-overview.md)
* [Informazioni di riferimento sul motore regole](cdn-rules-engine-reference.md)
* [Espressioni condizionali del motore regole](cdn-rules-engine-reference-conditional-expressions.md)
* [Funzionalità del motore regole](cdn-rules-engine-reference-features.md)
* [Override del comportamento HTTP predefinito mediante il motore regole](cdn-rules-engine.md)

