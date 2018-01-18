---
title: Condizioni di corrispondenza del motore regole della rete CDN di Azure | Microsoft Docs
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
ms.openlocfilehash: 9986e654b076df099e3912f9da628728723b5c3d
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="match-conditions-for-the-azure-cdn-rules-engine"></a>Condizioni di corrispondenza del motore regole della rete CDN di Azure
Questo articolo fornisce descrizioni dettagliate delle condizioni di corrispondenza disponibili per il [motore regole](cdn-rules-engine.md) della rete per la distribuzione di contenuti (rete CDN) di Azure.

La seconda parte di una regola è la condizione di corrispondenza. Una condizione di corrispondenza identifica specifici tipi di richieste per cui verrà eseguito un set di funzionalità.

Ad esempio, è possibile usare una condizione di corrispondenza per:
- Filtrare le richieste per il contenuto in base a una posizione specifica.
- Filtrare le richieste generate da un indirizzo IP o da un paese specifico.
- Filtrare le richieste in base alle informazioni dell'intestazione.

## <a name="always-match-condition"></a>Condizione di corrispondenza Sempre

La condizione di corrispondenza Sempre applica un set predefinito di funzionalità a tutte le richieste.

Nome | Scopo
-----|--------
[Sempre](#always) | Applica un set predefinito di funzionalità a tutte le richieste.

## <a name="device-match-condition"></a>Condizione di corrispondenza Dispositivo

La condizione di corrispondenza Dispositivo identifica le richieste effettuate da un dispositivo mobile in base alle relative proprietà.  

Nome | Scopo
-----|--------
[Dispositivo](#device) | Identifica le richieste effettuate da un dispositivo mobile in base alle relative proprietà.

## <a name="location-match-conditions"></a>Condizioni di corrispondenza Posizione

Le condizioni di corrispondenza Posizione identificano le richieste in base alla posizione del richiedente.

Nome | Scopo
-----|--------
[Numero AS](#as-number) | Identifica le richieste che hanno origine da una determinata rete.
[Paese](#country) | Identifica le richieste che hanno origine dai paesi specificati.

## <a name="origin-match-conditions"></a>Condizioni di corrispondenza Origine

Le condizioni di corrispondenza Origine identificano le richieste che puntano all'archivio della rete CDN o a un server di origine del cliente.

Nome | Scopo
-----|--------
[Origine rete CDN](#cdn-origin) | Identifica le richieste per il contenuto archiviato nell'archivio della rete CDN.
[Origine cliente](#customer-origin) | Identifica le richieste di contenuto in uno specifico server di origine del cliente.

## <a name="request-match-conditions"></a>Condizioni di corrispondenza Richiesta

Le condizioni di corrispondenza Richiesta identificano le richieste in base alle relative proprietà.

Nome | Scopo
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

Nome | Scopo
-----|--------
Directory percorso URL | Identifica le richieste in base al percorso relativo.
Estensione percorso URL | Identifica le richieste in base all'estensione del nome file.
Nome file percorso URL | Identifica le richieste in base al nome del file.
Valore letterale percorso URL | Confronta il percorso relativo di una richiesta con il valore specificato.
Espressione regolare percorso URL | Confronta il percorso relativo di una richiesta con l'espressione regolare specificata.
Carattere jolly percorso URL | Confronta il percorso relativo di una richiesta con il modello specificato.
Valore letterale query URL | Confronta la stringa di query di una richiesta con il valore specificato.
Parametro query URL | Identifica le richieste che contengono il parametro della stringa di query specificato impostato su un valore che corrisponde a un modello specificato.
Espressione regolare query URL | Identifica le richieste che contengono il parametro della stringa di query specificato impostato su un valore che corrisponde a un'espressione regolare specificata.
Carattere jolly query URL | Confronta uno o più valori specificati con la stringa di query della richiesta.


## <a name="reference-for-rules-engine-match-conditions"></a>Riferimento per le condizioni di corrispondenza del motore regole

---
### <a name="always"></a>Sempre

La condizione di corrispondenza Sempre applica un set predefinito di funzionalità a tutte le richieste.

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>Numero AS 
La rete Numero AS è definita dal relativo numero di sistema autonomo (ASN). È disponibile un'opzione per indicare se la condizione verrà soddisfatta quando la rete di un client "Corrisponde" o "Non corrisponde" al numero di sistema autonomo (ASN) specificato.

Informazioni chiave:
- È possibile specificare più numeri di sistema autonomo delimitandoli con uno spazio singolo. Ad esempio, 64514 64515 corrisponde alle richieste provenienti da 64514 o 64515.
- Determinate richieste potrebbero non restituire un numero di sistema autonomo (ASN) valido. Un punto interrogativo (?) contrassegnerà le richieste per cui non è stato possibile determinare un numero di sistema autonomo (ASN) valido.
- È necessario specificare l'intero numero di sistema autonomo per la rete desiderata. Non verrà effettuata una corrispondenza con valori parziali.
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
- È stato richiesto contenuto dall'archivio della rete per la distribuzione di contenuti.
- L'URI della richiesta usa il punto di accesso al contenuto, ad esempio /000001, definito nella condizione di corrispondenza.
  - URL della rete per la distribuzione di contenuti: l'URI della richiesta deve contenere il punto di accesso al contenuto selezionato.
  - URL CNAME perimetrale: la configurazione CNAME perimetrale corrispondente deve puntare al punto di accesso al contenuto selezionato.
  
Informazioni chiave:
 - Il punto di accesso al contenuto identifica il servizio che deve gestire il contenuto richiesto.
 - Non usare l'istruzione AND IF per combinare determinate condizioni di corrispondenza. Ad esempio, se si combina una condizione di corrispondenza Origine rete CDN con una condizione di corrispondenza Origine cliente, viene generato un criterio di corrispondenza che non potrà mai restituire risultati. Per questo motivo, due condizioni di corrispondenza Origine rete CDN non possono essere combinate tramite un'istruzione AND IF.

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>Indirizzo IP client
È disponibile un'opzione per indicare se la condizione Indirizzo IP client verrà soddisfatta quando l'indirizzo IP di un client "Corrisponde" o "Non corrisponde" all'indirizzo IP specificato.

Informazioni chiave:
- Assicurarsi di usare la notazione CIDR.
- Per specificare più indirizzi IP e/o blocchi di indirizzi IP, delimitarli con uno spazio singolo.
  - **Esempio per un indirizzo IPv4**: 1.2.3.4 10.20.30.40 corrisponde alle richieste provenienti da 1.2.3.4 o 10.20.30.40.
  - **Esempio per un indirizzo IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 corrisponde alle richieste provenienti da 1:2:3:4:5:6:7:8 o 10:20:30:40:50:60:70:80.
- La sintassi per un blocco di indirizzi IP è l'indirizzo IP di base seguito da una barra e dalle dimensioni del prefisso.
  - **Esempio per un indirizzo IPv4**: 5.5.5.64/26 corrisponde alle richieste provenienti da 5.5.5.64 fino a 5.5.5.127.
  - **Esempio per un indirizzo IPv6**: 1:2:3:/48 corrisponde alle richieste provenienti da 1:2:3:0:0:0:0:0 fino a 1:2:3:ffff:ffff:ffff:ffff:ffff.
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
L'opzione **Corrisponde**/**Non corrisponde** determina le condizioni in base alle quali verrà soddisfatta la condizione di corrispondenza Parametro cookie.
- **Corrisponde**: prevede che una richiesta contenga il cookie specificato con un valore che corrisponda ad almeno uno dei valori definiti nella condizione di corrispondenza.
- **Non corrisponde**: prevede che la richiesta soddisfi uno dei criteri seguenti:
  - Non contiene il cookie specificato.
  - Contiene il cookie specificato, ma il relativo valore non corrisponde ad alcun valore definito nella condizione di corrispondenza.
  
Informazioni chiave:
- Nome cookie: 
  - Quando si specifica un nome di cookie, i caratteri speciali, incluso l'asterisco, non sono supportati. Questo significa che solo le corrispondenze esatte per il nome del cookie sono idonee per il confronto.
  - È possibile specificare un solo nome di cookie per istanza della condizione di corrispondenza.
  - I confronti tra nomi di cookie non fanno distinzione tra maiuscole e minuscole.
- Valore cookie: 
  - È possibile specificare più valori cookie delimitandoli con uno spazio singolo.
  - Per i valori cookie è possibile usare i caratteri speciali. 
  - Se non è stato specificato un carattere jolly, solo una corrispondenza esatta potrà soddisfare questa condizione di corrispondenza. Se ad esempio si specifica "Valore", verrà trovata una corrispondenza con "Valore", ma non con "Valore1" o "Valore2".
  - L'opzione **Ignora maiuscole/minuscole** determina se verrà effettuato un confronto con il valore del cookie della richiesta tenendo conto di maiuscole/minuscole o meno.
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
La condizione di corrispondenza Espressione regolare parametro cookie definisce un nome e un valore del cookie. Per definire il valore del cookie desiderato, è possibile usare le espressioni regolari. 

L'opzione **Corrisponde**/**Non corrisponde** determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Corrisponde**: prevede che la richiesta contenga il cookie specificato con un valore che corrisponde all'espressione regolare specificata.
- **Non corrisponde**: prevede che la richiesta soddisfi uno dei criteri seguenti:
  - Non contiene il cookie specificato.
  - Contiene il cookie specificato, ma il relativo valore non corrisponde all'espressione regolare specificata.
  
Informazioni chiave:
- Nome cookie: 
  - Quando si specifica un nome di cookie, le espressioni regolari e i caratteri speciali, incluso l'asterisco, non sono supportati. Questo significa che solo le corrispondenze esatte per il nome del cookie sono idonee per il confronto.
  - È possibile specificare un solo nome di cookie per istanza della condizione di corrispondenza.
  - I confronti tra nomi di cookie non fanno distinzione tra maiuscole e minuscole.
- Valore cookie: 
  - Per i valori cookie è possibile usare le espressioni regolari.
  - L'opzione **Ignora maiuscole/minuscole** determina se verrà effettuato un confronto con il valore del cookie della richiesta tenendo conto di maiuscole/minuscole o meno.
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
È possibile specificare un paese tramite il codice paese corrispondente. È disponibile un'opzione per indicare se la condizione verrà soddisfatta quando il paese da cui ha origine una richiesta "Corrisponde" o "Non corrisponde" ai valori specificati.

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

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>Origine cliente

Informazioni chiave: 
- La condizione di corrispondenza Origine cliente verrà soddisfatta indipendentemente dal fatto che il contenuto venga richiesto tramite un URL della rete per la distribuzione di contenuti o un URL CNAME perimetrale che punti all'origine cliente selezionata.
- Non è possibile eliminare dalla pagina Origine cliente una configurazione di origine cliente a cui fa riferimento una regola. Prima di provare a eliminare una configurazione di origine cliente, assicurarsi che le configurazioni seguenti non vi facciano riferimento:
  - Una condizione di corrispondenza Origine cliente
  - Una configurazione CNAME perimetrale
- Non usare l'istruzione AND IF per combinare determinate condizioni di corrispondenza. Ad esempio, se si combina una condizione di corrispondenza Origine cliente con una condizione di corrispondenza Origine rete CDN, viene generato un criterio di corrispondenza che non potrà mai restituire risultati. Per questo motivo, due condizioni di corrispondenza Origine cliente non possono essere combinate tramite un'istruzione AND IF.

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>Dispositivo

La condizione di corrispondenza Dispositivo identifica le richieste effettuate da un dispositivo mobile in base alle relative proprietà. Il rilevamento del dispositivo mobile viene eseguito tramite [WURFL](http://wurfl.sourceforge.net/). La tabella seguente elenca le funzionalità WURFL e le relative variabili per il motore regole della rete per la distribuzione di contenuti.
<br>
> [!NOTE] 
> Le variabili seguenti sono supportate nelle funzionalità **Modify Client Request Header** (Modifica intestazione richiesta client) e **Modify Client Response Header** (Modifica intestazione risposta client).

Funzionalità | Variabile | Descrizione | Valori di esempio
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
- L'elenco dei CNAME perimetrali disponibili è limitato a quelli che sono stati configurati nella pagina CNAME perimetrali corrispondente alla piattaforma in cui viene configurato il motore regole HTTP.
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
Il nome host associato al referrer tramite cui il contenuto è stato richiesto determina se viene soddisfatta o meno la condizione Dominio di riferimento. È disponibile un'opzione per indicare se la condizione verrà soddisfatta quando il nome host di riferimento "Corrisponde" o "Non corrisponde" ai valori specificati.

Informazioni chiave:
- È possibile specificare più nomi host delimitandoli con uno spazio singolo.
- Questa condizione di corrispondenza supporta i caratteri speciali.
- Se il valore specificato non contiene un asterisco, deve essere una corrispondenza esatta per il nome host del referrer. Ad esempio, se si specifica "miodominio.com" non verrà trovata una corrispondenza con "www.miodominio.com".
- L'opzione **Ignora maiuscole/minuscole** determina se verrà effettuato un confronto tenendo conto di maiuscole/minuscole o meno.
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
L'opzione **Corrisponde**/**Non corrisponde** determina le condizioni in base alle quali verrà soddisfatta questa condizione di corrispondenza.
- **Corrisponde**: prevede che la richiesta contenga l'intestazione specificata. Il relativo valore deve corrispondere al valore definito in questa condizione di corrispondenza.
- **Non corrisponde**: prevede che la richiesta soddisfi uno dei criteri seguenti:
  - Non contiene l'intestazione specificata.
  - Contiene l'intestazione specificata, ma il relativo valore non corrisponde al valore definito in questa condizione di corrispondenza.
  
Informazioni chiave:
- I confronti tra nomi di intestazioni non fanno mai distinzione tra maiuscole e minuscole. L'opzione **Ignora maiuscole/minuscole** determina se nei confronti tra valori dell'intestazione verrà fatta distinzione tra maiuscole e minuscole.
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
L'opzione **Corrisponde**/**Non corrisponde** determina le condizioni in base alle quali verrà soddisfatta la condizione di corrispondenza Espressione regolare intestazione richiesta.
- **Corrisponde**: prevede che la richiesta contenga l'intestazione specificata. Il relativo valore deve corrispondere al criterio definito nell'espressione regolare specificata.
- **Non corrisponde**: prevede che la richiesta soddisfi uno dei criteri seguenti:
  - Non contiene l'intestazione specificata.
  - Contiene l'intestazione specificata, ma il relativo valore non corrisponde all'espressione regolare specificata.

Informazioni chiave:
- Nome intestazione: 
  - I confronti tra nomi di intestazioni non fanno distinzione tra maiuscole e minuscole.
  - Nel nome dell'intestazione gli spazi devono essere sostituiti con "%20". 
- Valore dell'intestazione: 
  - Per i valori delle impostazioni è possibile usare le espressioni regolari.
  - L'opzione **Ignora maiuscole/minuscole** determina se nei confronti tra valori dell'intestazione verrà fatta distinzione tra maiuscole e minuscole.
  - Questa condizione verrà soddisfatta solo se il valore esatto dell'intestazione corrisponde ad almeno uno dei criteri specificati.
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
L'opzione **Corrisponde**/**Non corrisponde** determina le condizioni in base alle quali verrà soddisfatta la condizione di corrispondenza Carattere jolly intestazione richiesta.
- **Corrisponde**: prevede che la richiesta contenga l'intestazione specificata. Il relativo valore deve corrispondere ad almeno uno dei valori definiti in questa condizione di corrispondenza.
- **Non corrisponde**: prevede che la richiesta soddisfi uno dei criteri seguenti:
  - Non contiene l'intestazione specificata.
  - Contiene l'intestazione specificata, ma il relativo valore non corrisponde ad alcun valore specificato.
  
Informazioni chiave:
- Nome intestazione: 
  - I confronti tra nomi di intestazioni non fanno distinzione tra maiuscole e minuscole.
  - Nel nome dell'intestazione gli spazi devono essere sostituiti con "%20". È anche possibile usare questo valore per specificare gli spazi in un valore dell'intestazione.
- Valore dell'intestazione: 
  - Per i valori dell'intestazione è possibile usare i caratteri speciali.
  - L'opzione **Ignora maiuscole/minuscole** determina se nei confronti tra valori dell'intestazione verrà fatta distinzione tra maiuscole e minuscole.
  - Questa condizione verrà soddisfatta solo se il valore esatto dell'intestazione corrisponde ad almeno uno dei criteri specificati.
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
Solo gli asset richiesti tramite il metodo di richiesta selezionato potranno soddisfare la condizione Metodo richiesta. I metodi richiesta disponibili sono:
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
Solo gli asset richiesti tramite il protocollo selezionato potranno soddisfare la condizione Schema richiesta. I protocolli disponibili sono HTTP e HTTPS.

Informazioni chiave:
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa condizione di corrispondenza sia incompatibile con le funzionalità seguenti:
  - Completa riempimento cache
  - Max-Age interno predefinito
  - Forza Max-Age interno
  - Ignora origine No-Cache
  - Max-Stale interno

[Torna all'inizio](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica della rete per la distribuzione di contenuti di Azure](cdn-overview.md)
* [Informazioni di riferimento sul motore regole](cdn-rules-engine-reference.md)
* [Espressioni condizionali del motore regole](cdn-rules-engine-reference-conditional-expressions.md)
* [Funzionalità del motore regole](cdn-rules-engine-reference-features.md)
* [Override del comportamento HTTP predefinito mediante il motore regole](cdn-rules-engine.md)

