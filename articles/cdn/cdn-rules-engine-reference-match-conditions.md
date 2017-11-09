---
title: Condizioni di corrispondenza del motore regole della rete CDN di Azure | Documentazione Microsoft
description: "Documentazione di riferimento per le funzionalità e condizioni di corrispondenza del motore regole della rete CDN di Azure."
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
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: f4886b1d78dfa87cf25737fb46c12b5963034f27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Condizioni di corrispondenza del motore regole della rete CDN di Azure
Questo argomento elenca descrizioni dettagliate delle condizioni di corrispondenza disponibili per il [motore regole](cdn-rules-engine.md)della rete per la distribuzione di contenuti (rete CDN) di Azure.

La seconda parte di una regola è la condizione di corrispondenza. Una condizione di corrispondenza identifica specifici tipi di richieste per cui verrà eseguito un set di funzionalità.

Ad esempio, può essere usata per filtrare le richieste di contenuto in una particolare posizione, le richieste generate da un paese o un indirizzo IP specifico oppure in base alle informazioni di intestazione.

## <a name="always"></a>Sempre

La condizione di corrispondenza Sempre è progettata per applicare un set predefinito di funzionalità a tutte le richieste.

## <a name="device"></a>Dispositivo

La condizione di corrispondenza Dispositivo identifica le richieste effettuate da un dispositivo mobile in base alle relative proprietà.  Il rilevamento del dispositivo mobile viene eseguito tramite [WURFL](http://wurfl.sourceforge.net/).  Le funzionalità di WURFL e le variabili del motore regole della rete CDN sono elencate di seguito.

> [!NOTE] 
> Le variabili seguenti sono supportate nelle funzionalità **Modify Client Request Header** (Modifica intestazione richiesta client) e **Modify Client Response Header** (Modifica intestazione risposta client).

Funzionalità | Variabile | Descrizione | Valore/i di esempio
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
Download progressivo | %{wurfl_cap_progressive_download} | Valore booleano che indica se il dispositivo supporta la riproduzione di audio/video mentre il download è ancora in corso. | true
Data di rilascio | %{wurfl_cap_release_date} | Stringa che indica il mese e l'anno in cui il dispositivo è stato aggiunto al database WURFL.<br/><br/>Formato: `yyyy_mm` | 2013_december
Resolution Height (Altezza risoluzione) | %{wurfl_cap_resolution_height} | Numero intero che indica l'altezza del dispositivo in pixel. | 768
Resolution Width (Larghezza risoluzione) | %{wurfl_cap_resolution_width} | Numero intero che indica la larghezza del dispositivo in pixel. | 1024


## <a name="location"></a>Percorso

Queste condizioni di corrispondenza sono progettate per identificare le richieste in base alla posizione del richiedente.

Nome | Scopo
-----|--------
Numero AS | Identifica le richieste che hanno origine da una determinata rete.
Paese | Identifica le richieste che hanno origine dai paesi specificati.


## <a name="origin"></a>Origine

Queste condizioni di corrispondenza sono progettate per identificare le richieste che puntano all'archivio della rete CDN o a un server di origine del cliente.

Nome | Scopo
-----|--------
Origine CDN | Identifica le richieste di contenuto nell'archivio della rete CDN.
Origine cliente | Identifica le richieste di contenuto in uno specifico server di origine del cliente.


## <a name="request"></a>Richiesta

Queste condizioni di corrispondenza sono progettate per identificare le richieste in base alle relative proprietà.

Nome | Scopo
-----|--------
Indirizzo IP client | Identifica le richieste che hanno origine da un determinato indirizzo IP.
Parametro cookie | Controlla il valore specificato nei cookie associati a ogni richiesta.
Espressione regolare parametro cookie | Controlla l'espressione regolare specificata nei cookie associati a ogni richiesta.
CNAME periferico | Identifica le richieste che fanno riferimento a un CNAME periferico specifico.
Dominio di riferimento | Identifica le richieste con riferimenti da uno o più nomi host specificati.
Valore letterale intestazione richiesta | Identifica le richieste che contengono l'intestazione specificata impostata su uno o più valori specificati.
Espressione regolare intestazione richiesta | Identifica le richieste che contengono l'intestazione specificata impostata su un valore che corrisponde all'espressione regolare specificata.
Carattere jolly intestazione richiesta | Identifica le richieste che contengono l'intestazione specificata impostata su un valore che corrisponde al modello specificato.
Metodo richiesta | Identifica le richieste in base al relativo metodo HTTP.
Schema richiesta | Identifica le richieste in base al relativo protocollo HTTP.

## <a name="url"></a>URL

Queste condizioni di corrispondenza sono progettate per identificare le richieste in base ai relativi URL.

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


## <a name="next-steps"></a>Passaggi successivi
* [Panoramica della rete CDN di Azure](cdn-overview.md)
* [Informazioni di riferimento sul motore regole](cdn-rules-engine-reference.md)
* [Espressioni condizionali del motore regole](cdn-rules-engine-reference-conditional-expressions.md)
* [Funzionalità del motore regole](cdn-rules-engine-reference-features.md)
* [Override del comportamento HTTP predefinito mediante il motore regole](cdn-rules-engine.md)

