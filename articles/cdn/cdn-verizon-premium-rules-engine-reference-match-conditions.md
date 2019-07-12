---
title: Condizioni di corrispondenza del motore di regole di rete CDN di Azure fornita da Verizon Premium | Microsoft Docs
description: Documentazione di riferimento per la rete CDN di Azure da Verizon Premium condizioni di corrispondenza del motore di regole.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593210"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Condizioni di corrispondenza di rete CDN di Azure fornita da Verizon Premium motore regole di Business

Questo articolo fornisce descrizioni dettagliate delle condizioni di corrispondenza disponibili per il contenuto recapito rete (CDN) di Azure da Verizon Premium [motore regole di business](cdn-verizon-premium-rules-engine.md).

La seconda parte di una regola è la condizione di corrispondenza. Una condizione di corrispondenza identifica specifici tipi di richieste per cui verrà eseguito un set di funzionalità.

Ad esempio, è possibile usare una condizione di corrispondenza per:

- Filtrare le richieste per il contenuto in base a una posizione specifica.
- Filtrare le richieste generate da un particolare indirizzo IP o il paese/area geografica.
- Filtrare le richieste in base alle informazioni dell'intestazione.

## <a name="always-match-condition"></a>Condizione di corrispondenza Sempre

La condizione di corrispondenza Sempre applica un set predefinito di funzionalità a tutte le richieste.

NOME | Scopo
-----|--------
[Sempre](#always) | Applica un set predefinito di funzionalità a tutte le richieste.

## <a name="device-match-condition"></a>Condizione di corrispondenza Dispositivo

La condizione di corrispondenza Dispositivo identifica le richieste effettuate da un dispositivo mobile in base alle relative proprietà.  

Name | Scopo
-----|--------
[Dispositivo](#device) | Identifica le richieste effettuate da un dispositivo mobile in base alle relative proprietà.

## <a name="location-match-conditions"></a>Condizioni di corrispondenza Posizione

Le condizioni di corrispondenza Posizione identificano le richieste in base alla posizione del richiedente.

Name | Scopo
-----|--------
[Numero AS](#as-number) | Identifica le richieste che hanno origine da una determinata rete.
[Paese](#country) | Identifica le richieste che provengono da paesi/aree geografiche specificati.

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

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

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

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

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

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

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

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Parametro cookie

L'opzione **Matches** (Corrisponde)/**Does Not Matches** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.

- **Matches** (Corrisponde): prevede che una richiesta contenga il cookie specificato con un valore che corrisponda ad almeno uno dei valori definiti nella condizione di corrispondenza.
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
  - Un valore di cookie può usare [caratteri jolly](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Se non è stato specificato un carattere jolly, solo una corrispondenza esatta potrà soddisfare questa condizione di corrispondenza. Se ad esempio si specifica "Valore", verrà trovata una corrispondenza con "Valore", ma non con "Valore1" o "Valore2".
  - Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per determinare se effettuare un controllo rispetto al valore di cookie della richiesta tenendo conto o meno della distinzione tra maiuscole e minuscole.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Espressione regolare parametro cookie

La condizione di corrispondenza Espressione regolare parametro cookie definisce un nome e un valore del cookie. Per definire il valore di cookie desiderato, è possibile usare [espressioni regolari](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

L'opzione **Matches** (Corrisponde)/**Does Not Matches** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.

- **Matches** (Corrisponde): prevede che la richiesta contenga il cookie specificato con un valore che corrisponde all'espressione regolare specificata.
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

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Country

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

- Funzionalità Deny Access (403) (Nega accesso - 403): abilitare la funzionalità [Deny Access (403)](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) (Nega accesso - 403) per replicare la parte Consenti o Blocca della funzionalità del filtro dei paesi.

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Origine cliente

Informazioni chiave:

- La condizione di corrispondenza Customer Origin (Origine cliente) viene soddisfatta indipendentemente dal fatto che il contenuto venga richiesto tramite un URL della rete CDN o un URL CNAME perimetrale che punta all'origine cliente selezionata.
- Non è possibile eliminare dalla pagina Origine cliente una configurazione di origine cliente a cui fa riferimento una regola. Prima di provare a eliminare una configurazione di origine cliente, assicurarsi che le configurazioni seguenti non vi facciano riferimento:
  - Una condizione di corrispondenza Origine cliente
  - Una configurazione CNAME perimetrale
- Non usare l'istruzione AND IF per combinare determinate condizioni di corrispondenza. Ad esempio, se si combina una condizione di corrispondenza Origine cliente con una condizione di corrispondenza Origine rete CDN, viene generato un criterio di corrispondenza che non potrà mai restituire risultati. Per questo motivo, due condizioni di corrispondenza Origine cliente non possono essere combinate tramite un'istruzione AND IF.

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

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

Type     | Descrizione
---------|------------
Literal (Valore letterale)  | Selezionare questa opzione per impedire alla maggior parte dei caratteri di assumere un significato speciale usando il rispettivo [valore letterale](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard (Carattere jolly) | Selezionare questa opzione per trarre vantaggio da tutti i [caratteri jolly]([caratteri jolly](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex (Espressione regolare)    | Selezionare questa opzione per usare [espressioni regolari](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Le espressioni regolari sono utili per definire un modello di caratteri.

#### <a name="wurfl-capabilities"></a>Funzionalità WURFL

Una funzionalità WURFL fa riferimento a una categoria che descrive i dispositivi mobili. La funzionalità selezionata determina il tipo di descrizione del dispositivo mobile usata per identificare le richieste.

La tabella seguente elenca le funzionalità WURFL e le rispettive variabili per il motore regole.

> [!NOTE]
> Le variabili seguenti sono supportate nelle funzionalità **Modify Client Request Header** (Modifica intestazione richiesta client) e **Modify Client Response Header** (Modifica intestazione risposta client).

Funzionalità | Variabile | Descrizione | Valori di esempio
-----------|----------|-------------|----------------
Brand Name (Nome marchio) | %{wurfl_cap_brand_name} | Stringa che indica il nome del marchio del dispositivo. | Samsung
Device OS (Sistema operativo dispositivo) | %{wurfl_cap_device_os} | Stringa che indica il sistema operativo installato nel dispositivo. | IOS
Device OS Version (Versione sistema operativo dispositivo) | %{wurfl_cap_device_os_version} | Stringa che indica il numero di versione del sistema operativo installato nel dispositivo. | 1.0.1
Dual Orientation (Orientamento doppio) | %{wurfl_cap_dual_orientation} | Valore booleano che indica se il dispositivo supporta l'orientamento doppio. | true
HTML Preferred DTD (DTD preferito HTML) | %{wurfl_cap_html_preferred_dtd} | Stringa che indica la definizione DTD (Document Type Definition) preferita del dispositivo mobile per il contenuto HTML. | none<br/>xhtml_basic<br/>html5
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

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

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

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Dominio di riferimento

Il nome host associato al referrer tramite cui il contenuto è stato richiesto determina se viene soddisfatta o meno la condizione Dominio di riferimento.

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza:

- **Matches** (Corrisponde): richiede che il nome host corrisponda al valore specificato. 
- **Does Not Match** (Non corrisponde): richiede che il nome host non corrisponda al valore specificato.

Informazioni chiave:

- È possibile specificare più nomi host delimitandoli con uno spazio singolo.
- Questa condizione di corrispondenza supporta i [caratteri jolly](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Se il valore specificato non contiene un asterisco, deve essere una corrispondenza esatta per il nome host del referrer. Ad esempio, se si specifica "miodominio.com" non verrà trovata una corrispondenza con "www.miodominio.com".
- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per determinare se il controllo deve essere eseguito tenendo conto o meno della distinzione tra maiuscole e minuscole.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Valore letterale intestazione richiesta

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.

- **Matches** (Corrisponde): prevede che la richiesta contenga l'intestazione specificata. Il relativo valore deve corrispondere al valore definito in questa condizione di corrispondenza.
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

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Espressione regolare intestazione richiesta

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.

- **Matches** (Corrisponde): prevede che la richiesta contenga l'intestazione specificata. Il valore deve corrispondere al modello definito nell'[espressione regolare](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) specificata.
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

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Carattere jolly intestazione richiesta

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.

- **Matches** (Corrisponde): prevede che la richiesta contenga l'intestazione specificata. Il relativo valore deve corrispondere ad almeno uno dei valori definiti in questa condizione di corrispondenza.
- **Does Not Match** (Non corrisponde): specifica che la richiesta soddisfi uno dei criteri seguenti:
  - Non contiene l'intestazione specificata.
  - Contiene l'intestazione specificata, ma il relativo valore non corrisponde ad alcun valore specificato.
  
Informazioni chiave:

- Nome intestazione:
  - I confronti tra nomi di intestazioni non fanno distinzione tra maiuscole e minuscole.
  - Nel nome dell'intestazione gli spazi devono essere sostituiti con "%20". È anche possibile usare questo valore per specificare gli spazi in un valore dell'intestazione.
- Valore dell'intestazione:
  - Un valore di intestazione può usare [caratteri jolly](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per controllare la distinzione tra maiuscole e minuscole nei confronti dei valori di intestazione.
  - Questa condizione di corrispondenza verrà soddisfatta quando un valore di intestazione corrisponde esattamente ad almeno uno dei modelli specificati.
  - È possibile specificare più valori delimitandoli con uno spazio singolo.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Metodo richiesta

La condizione Metodo di richiesta verrà soddisfatta solo quando gli asset vengono richiesti tramite il metodo di richiesta selezionato. I metodi richiesta disponibili sono:

- GET
- HEAD
- INSERISCI
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

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

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

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

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

  Questo URL punta a questo nome host della rete CDN Verizon: http:\//wpc.0001.&lt;dominio&gt;/800001/myorigin/**cartella**/index.htm

- Un URL CNAME perimetrale viene riscritto in un URL della rete CDN prima del confronto degli URL.

    Ad esempio, entrambi gli URL seguenti puntano allo stesso asset e di conseguenza hanno lo stesso percorso URL.
  - URL della rete CDN: http:\//wpc.0001.&lt;dominio&gt;/800001/CustomerOrigin/path/asset.htm
    
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

- Per specificare un percorso URL è possibile usare [caratteri jolly](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per determinare se effettuare un controllo tenendo conto o meno della distinzione tra maiuscole e minuscole.

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

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

Valori: asp aspx php html

Questa condizione di corrispondenza verrà soddisfatta quando vengono trovati URL che terminano con le estensioni seguenti:

- .asp
- .aspx
- .php
- .html

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

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

- Un nome file può usare [caratteri jolly](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Ad esempio, ogni modello di nome file può contenere uno o più asterischi (*), in cui ogni asterisco corrisponde a una sequenza di uno o più caratteri.

- Se non vengono specificati caratteri jolly, solo una corrispondenza esatta potrà soddisfare questa condizione di corrispondenza.

    Ad esempio, se si specifica "presentazione.ppt", un asset denominato "presentazione.ppt" soddisferà la condizione, ma non uno denominato "presentazione.pptx".

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

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

    Ad esempio: http:\//wpc.0001.&lt;dominio&gt;/**800001/myorigin/cartella/index.htm**

  - **Origine**: indica che il punto di confronto degli URL deve iniziare dopo il punto di accesso al contenuto, ad esempio /000001 o /800001/myorigin. Poiché per impostazione predefinita l'URL CNAME \*.azureedge.net viene creato relativamente alla directory di origine nel nome host della rete CDN Verizon, gli utenti della rete CDN di Azure devono usare il valore **Origine**. 

    Ad esempio: https:\//&lt;endpoint&gt;.azureedge.net/**cartella/index.htm**

  Questo URL punta a questo nome host della rete CDN Verizon: http:\//wpc.0001.&lt;dominio&gt;/800001/myorigin/**cartella/index.htm**

- Un URL CNAME perimetrale viene riscritto in un URL della rete CDN prima del confronto degli URL.

Ad esempio, entrambi gli URL seguenti puntano allo stesso asset e di conseguenza hanno lo stesso percorso URL:

- URL della rete CDN: http:\//wpc.0001.&lt;dominio&gt;/800001/CustomerOrigin/path/asset.htm
- URL CNAME perimetrale: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Informazioni aggiuntive:
    
    - Percorso URL (relativo alla radice): /800001/CustomerOrigin/path/asset.htm
   
    - Percorso URL (relativo all'origine): /path/asset.htm

- Le stringhe di query nell'URL vengono ignorate.
- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per determinare se effettuare un controllo tenendo conto o meno della distinzione tra maiuscole e minuscole.
- Il valore specificato per questa condizione di corrispondenza viene confrontato al percorso relativo della richiesta esatta eseguita dal client.

- Per trovare la corrispondenza di tutte le richieste effettuate in una directory particolare, usare la condizione di corrispondenza [URL Path Directory](#url-path-directory) (Directory percorso URL) o [URL Path Wildcard](#url-path-wildcard) (Carattere jolly percorso URL).

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>Espressione regolare percorso URL

Confronta il percorso URL di una richiesta con l'[espressione regolare](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) specificata.

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.

- **Matches** (Corrisponde): specifica che la richiesta deve contenere un percorso URL che corrisponda all'espressione regolare specificata.
- **Does Not Match** (Non corrisponde): specifica che la richiesta deve contenere un percorso URL che non corrisponda all'espressione regolare specificata.

Informazioni chiave:

- Un URL CNAME perimetrale viene riscritto in un URL della rete CDN prima del confronto degli URL.

    Ad esempio, entrambi gli URL puntano allo stesso asset e di conseguenza hanno lo stesso percorso URL.

     - URL della rete CDN: http:\//wpc.0001.&lt;dominio&gt;/800001/CustomerOrigin/path/asset.htm

     - URL CNAME perimetrale: http:\//my.domain.com/path/asset.htm

    Informazioni aggiuntive:
    
     - Percorso URL: /800001/CustomerOrigin/path/asset.htm

- Le stringhe di query nell'URL vengono ignorate.
    
- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per determinare se effettuare un controllo tenendo conto o meno della distinzione tra maiuscole e minuscole.
    
- Gli spazi nel percorso URL devono essere sostituiti con "%20".

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

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

       Ad esempio: http:\//wpc.0001.&lt;dominio&gt;/**800001/myorigin/cartella/index.htm**

     - **Origine**: indica che il punto di confronto degli URL deve iniziare dopo il punto di accesso al contenuto, ad esempio /000001 o /800001/myorigin. Poiché per impostazione predefinita l'URL CNAME \*.azureedge.net viene creato relativamente alla directory di origine nel nome host della rete CDN Verizon, gli utenti della rete CDN di Azure devono usare il valore **Origine**. 

       Ad esempio: https:\//&lt;endpoint&gt;.azureedge.net/**cartella/index.htm**

     Questo URL punta a questo nome host della rete CDN Verizon: http:\//wpc.0001.&lt;dominio&gt;/800001/myorigin/**cartella/index.htm**

- Un URL CNAME perimetrale viene riscritto in un URL della rete CDN prima del confronto degli URL.

    Ad esempio, entrambi gli URL seguenti puntano allo stesso asset e di conseguenza hanno lo stesso percorso URL:
     - URL della rete CDN: http://wpc.0001.&lt ;domain&gt; /800001/CustomerOrigin/path/asset.htm
     - URL CNAME perimetrale: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Informazioni aggiuntive:
    
     - Percorso URL (relativo alla radice): /800001/CustomerOrigin/path/asset.htm
    
     - Percorso URL (relativo all'origine): /path/asset.htm
    
- Specificare più percorsi URL delimitandoli con uno spazio singolo.

   Ad esempio: /marketing/asset.* /sales/\*.htm

- Le stringhe di query nell'URL vengono ignorate.
    
- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per determinare se effettuare un controllo tenendo conto o meno della distinzione tra maiuscole e minuscole.
    
- Sostituire gli spazi nel percorso URL con "%20".
    
- Il valore specificato in un percorso URL può usare [caratteri jolly](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Ogni modello di percorso URL può contenere uno o più asterischi (*), in cui ogni asterisco può corrispondere a una sequenza di uno o più caratteri.

#### <a name="sample-scenarios"></a>Scenari di esempio

Le configurazioni di esempio nella tabella seguente presuppongono che questa condizione di corrispondenza venga soddisfatta quando una richiesta corrisponde al modello URL specificato:

Value                   | Relative to (Relativo a)    | Risultato 
------------------------|----------------|-------
\*/test.html \*/test.php  | Radice o Origine | Le richieste di asset denominati "test.html" o "test.php" in qualsiasi cartella soddisfano la corrispondenza a questo modello.
/80ABCD/origin/text/*   | Radice           | L'asset richiesto corrisponde a questo modello quando soddisfa i criteri seguenti: <br />- Si trova in un'origine cliente denominata "origin". <br />- Il percorso relativo inizia con una cartella denominata "text". Questo significa che l'asset richiesto può trovarsi nella cartella "text" o in una delle sottocartelle ricorsive.
*/css/* */js/*          | Radice o Origine | Tutti gli URL CNAME perimetrali che contengono una cartella css o js soddisfano la corrispondenza a questo modello.
*.jpg *.gif *.png       | Radice o Origine | Tutti gli URL CNAME perimetrali o di rete CDN che terminano con .jpg, .gif o .png. soddisfano la corrispondenza a questo modello. Un metodo alternativo per specificare questo modello è la condizione di corrispondenza [URL Path Extension](#url-path-extension) (Estensione percorso URL).
/images/\* /media/\*      | Origine         | Gli URL CNAME perimetrali o di rete CDN il cui percorso relativo inizia con una cartella "images" o "media" soddisfano la corrispondenza a questo modello. <br />- URL della rete CDN: http:\//wpc.0001.&lt;dominio&gt;/800001/myorigin/images/sales/event1.png<br />- URL CNAME perimetrale di esempio: http:\//cdn.mydomain.com/images/sales/event1.png

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

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

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

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
- I valori di parametro possono includere [caratteri jolly](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
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

Name  | Value |  Risultato
------|-------|--------
Utente  | Joe   | La corrispondenza a questo modello viene soddisfatta quando la stringa di query per un URL richiesto è "?user=joe".
Utente  | *     | La corrispondenza a questo modello viene soddisfatta quando la stringa di query per un URL richiesto contiene un parametro User.
Email | Joe\* | La corrispondenza a questo modello viene soddisfatta quando la stringa di query per un URL richiesto contiene un parametro Email che inizia con "Joe".

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>Espressione regolare query URL

Identifica le richieste che contengono il parametro della stringa di query specificato. Questo parametro è impostato su un valore che corrisponde a un'[espressione regolare](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) specificata.

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.

- **Matches** (Corrisponde): specifica che la richiesta deve contenere una stringa di query URL che corrisponda all'espressione regolare specificata.
- **Does Not Match** (Non corrisponde): specifica che la richiesta deve contenere una stringa di query URL che non corrisponda all'espressione regolare specificata.

Informazioni chiave:

- Solo le corrispondenze esatte all'espressione regolare specificata soddisfano questa condizione di corrispondenza.
    
- Usare l'opzione **Ignore Case** (Ignora maiuscole/minuscole) per controllare la distinzione tra maiuscole e minuscole nei confronti delle stringhe di query.
    
- Ai fini di questa opzione, una stringa di query inizia con il primo carattere dopo il delimitatore punto interrogativo (?) per la stringa di query.
    
- Determinati caratteri richiedono la codifica URL. Usare il simbolo di percentuale per applicare la codifica URL ai caratteri seguenti:

   Character | Codifica URL | Value
   ----------|--------------|------
   Spazio     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`20
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`25
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   I simboli di percentuale devono essere preceduti da un carattere di escape.

- I caratteri speciali nelle espressioni regolari devono essere preceduti da un doppio carattere di escape, ad esempio \^$.+, per includere una barra rovesciata nell'espressione regolare.

   Ad esempio:

   Value | Interpretato come 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
   - Completa riempimento cache
   - Max-Age interno predefinito
   - Forza Max-Age interno
   - Ignora origine No-Cache
   - Max-Stale interno

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>Carattere jolly query URL

Confronta uno o più valori specificati con la stringa di query della richiesta.

L'opzione **Matches** (Corrisponde)/**Does Not Match** (Non corrisponde) determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.

- **Matches** (Corrisponde): specifica che la richiesta deve contenere una stringa di query URL che corrisponda al carattere jolly specificato.
- **Does Not Match** (Non corrisponde): specifica che la richiesta deve contenere una stringa di query URL che non corrisponda al carattere jolly specificato.

Informazioni chiave:

- Ai fini di questa opzione, una stringa di query inizia con il primo carattere dopo il delimitatore punto interrogativo (?) per la stringa di query.
- I valori di parametro possono includere [caratteri jolly](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Ogni modello di valore di parametro può contenere uno o più asterischi (*), in cui ogni asterisco può corrispondere a una sequenza di uno o più caratteri.
   - Determinati caratteri richiedono la codifica URL. Usare il simbolo di percentuale per applicare la codifica URL ai caratteri seguenti:

     Character | Codifica URL
     ----------|---------
     Spazio     | %20
     &         | %25

- È possibile specificare più valori delimitandoli con uno spazio singolo.

   Ad esempio:  *Parametro1=ValoreA* *ValoreB* *Parametro1=ValoreC&Parametro2=ValoreD*

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

 Name                 | DESCRIZIONE
 ---------------------|------------
user=joe              | La corrispondenza a questo modello viene soddisfatta quando la stringa di query per un URL richiesto è "?user=joe".
\*user=\* \*optout=\* | La corrispondenza a questo modello viene soddisfatta quando la query URL della rete CDN contiene il parametro user o optout.

[Torna all'inizio](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della rete per la distribuzione di contenuti di Azure](cdn-overview.md)
- [Informazioni di riferimento sul motore regole](cdn-verizon-premium-rules-engine-reference.md)
- [Espressioni condizionali del motore regole](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funzionalità del motore regole](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override del comportamento HTTP predefinito mediante il motore regole](cdn-verizon-premium-rules-engine.md)
