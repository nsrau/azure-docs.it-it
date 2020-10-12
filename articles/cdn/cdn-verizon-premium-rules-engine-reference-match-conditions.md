---
title: Condizioni di corrispondenza del motore regole della rete CDN di Azure da Verizon Premium
description: Documentazione di riferimento per le condizioni di corrispondenza del motore regole della rete CDN da Verizon Premium.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: efd6e6a93cd4ca79e6c4b6de69f8514e2d71b252
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84323315"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Condizioni di corrispondenza del motore regole della rete CDN di Azure da Verizon Premium

Questo articolo offre descrizioni dettagliate delle condizioni di corrispondenza disponibili per il [motore regole](cdn-verizon-premium-rules-engine.md) della rete CDN di Azure da Verizon Premium.

La seconda parte di una regola è la condizione di corrispondenza. Una condizione di corrispondenza identifica specifici tipi di richieste per cui verrà eseguito un set di funzionalità.

Ad esempio, è possibile usare una condizione di corrispondenza per:

- Filtrare le richieste per il contenuto in base a una posizione specifica.
- Filtrare le richieste generate da un indirizzo IP o da un paese o da un'area specifica.
- Filtrare le richieste in base alle informazioni dell'intestazione.

## <a name="match-conditions"></a><a name="top"></a>Condizioni di corrispondenza

* [Sempre](#always)
* [Dispositivo](#device)
* [Posizione](#location)
* [Origine](#origin)
* [Richiesta](#request)
* [URL](#url)

### <a name="always"></a><a name="always"></a>Sempre

[La condizione di corrispondenza sempre](https://docs.vdms.com/cdn/Content/HRE/M/Always.htm) è progettata per applicare un set predefinito di funzionalità a tutte le richieste.

### <a name="device"></a><a name="device"></a>Dispositivo

Queste condizioni di corrispondenza sono progettate per identificare le richieste in base all'agente utente del client.

| Nome       | Scopo                                                           |
|------------|-------------------------------------------------------------------|
| Brand Name (Nome marchio) | Identifica le richieste in base al fatto che il nome del marchio del dispositivo corrisponda a: <br> **-** Valore specifico (valore[letterale nome del marchio](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Literal.htm)) <br> **-** Espressione regolare ([Regex nome del marchio](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Regex.htm)) <br> **-** Modello specifico ([carattere jolly del nome del marchio](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Wildcard.htm)) |
| Device OS (Sistema operativo dispositivo) | Identifica le richieste in base al fatto che il sistema operativo del dispositivo corrisponda a: <br> **-** Valore specifico (valore[letterale del sistema operativo del dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Literal.htm)) <br> **-** Espressione regolare ([Regex OS del dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Regex.htm)) <br> **-** Modello specifico ([carattere jolly del sistema operativo del dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Wildcard.htm)) |
| Device OS Version (Versione sistema operativo dispositivo) | Identifica le richieste in base al fatto che la versione del sistema operativo del dispositivo corrisponda a: <br> **-** Valore specifico (valore[letterale versione sistema operativo dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Literal.htm)) <br> **-** Espressione regolare ([Regex versione sistema operativo del dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Regex.htm)) <br> **-** Modello specifico ([carattere jolly della versione del sistema operativo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Wildcard.htm)) |
| [Orientamento doppio?](https://docs.vdms.com/cdn/Content/HRE/M/D-Dual-Orientation.htm) | Identifica le richieste in base al fatto che il dispositivo supporti l'orientamento doppio. |
| HTML Preferred DTD (DTD preferito HTML) | Identifica le richieste in base al fatto che la DTD preferita HTML del dispositivo corrisponda a: <br> **-** Valore specifico (valore[letterale DTD preferito HTML](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Literal.htm)) <br> **-** Espressione regolare ([Regex per la DTD preferita HTML](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Regex.htm)) <br> **-** Modello specifico ([carattere jolly della DTD preferito HTML](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Wildcard.htm)) |
| [Incorporamento dell'immagine?](https://docs.vdms.com/cdn/Content/HRE/M/D-Image-Inlining.htm) | Identifica le richieste in base al fatto che il dispositivo supporti immagini con codifica Base64. |
| [Android?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Android.htm) | Identifica le richieste in base al fatto che il dispositivo usi il sistema operativo Android. |
| [App?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-App.htm) | Identifica le richieste in base al fatto che un'applicazione nativa abbia richiesto contenuto. |
| [Si tratta di un desktop completo?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Full-Desktop.htm) | Identifica le richieste in base al fatto che il dispositivo fornisca un'esperienza desktop completa. |
| [IOS?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-iOS.htm) | Identifica le richieste in base al fatto che il dispositivo usi iOS. |
| [Robot?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Robot.htm) | Identifica le richieste in base al fatto che il dispositivo sia considerato un client HTTP automatizzato (ad esempio, un robot crawler). |
| [Smart TV?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smart-TV.htm) | Identifica le richieste in base al fatto che il dispositivo sia una Smart TV. |
| [Lo smartphone è?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smartphone.htm) | Identifica le richieste in base al fatto che il dispositivo sia uno smartphone.
| [Il tablet è?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Tablet.htm) | Identifica le richieste in base al fatto che il dispositivo sia un tablet. |
| [Il touchscreen è?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Touchscreen.htm) | Identifica le richieste in base al fatto che il dispositivo di puntamento principale del dispositivo sia un touchscreen. |
| [Windows Phone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Windows-Phone.htm) | Identifica le richieste in base al fatto che il dispositivo sia un Windows Mobile 6.5/Windows Phone 7 o versione successiva. |
| [Il dispositivo è wireless?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Wireless-Device.htm) | Identifica le richieste in base al fatto che il dispositivo sia wireless. 
| Marketing Name (Nome marketing) | Identifica le richieste in base al fatto che il nome di marketing del dispositivo corrisponda a: <br> **-** Valore specifico (valore[letterale nome marketing](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Literal.htm)) <br> **-** Espressione regolare ([Regex nome marketing](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Regex.htm)) <br> **-** Modello specifico ([carattere jolly nome marketing](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Wildcard.htm)) |
| Mobile Browser (Browser per dispositivi mobili) | Identifica le richieste in base al fatto che il browser del dispositivo corrisponda a: <br> **-** Valore specifico (valore[letterale del browser mobile](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Literal.htm)) <br> **-** Espressione regolare ([Regex del browser per dispositivi mobili](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Regex.htm)) <br> **-** Modello specifico ([carattere jolly del browser per dispositivi mobili](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Wildcard.htm)) |
| Mobile Browser Version (Versione browser per dispositivi mobili) | Identifica le richieste in base al fatto che la versione del browser del dispositivo corrisponda a: <br> **-** Valore specifico (valore[letterale versione browser mobile](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Literal.htm)) <br> **-** Espressione regolare ([Regex versione del browser mobile](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Regex.htm)) <br> **-** Modello specifico ([carattere jolly della versione del browser mobile](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Wildcard.htm)) |
| Model Name (Nome modello) | Identifica le richieste in base a se il nome del modello del dispositivo corrisponde a un: <br> **-** Valore specifico (valore[letterale nome modello](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Literal.htm)) <br> **-** Espressione regolare ([Regex nome modello](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Regex.htm)) <br> **-** Modello specifico ([carattere jolly nome modello](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Wildcard.htm)) |
| [Download progressivo?](https://docs.vdms.com/cdn/Content/HRE/M/D-Progressive-Download.htm) | Identifica le richieste in base al fatto che il dispositivo supporti il download progressivo. |
| Data di rilascio | Identifica le richieste in base al fatto che la data di rilascio del dispositivo corrisponda a: <br> **-** Valore specifico (valore[letterale data di rilascio](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Literal.htm)) <br> **-** Espressione regolare ([Regex Data di rilascio](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Regex.htm)) <br> **-** Modello specifico ([carattere jolly della data di rilascio](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Wildcard.htm)) |
| [Resolution Height (Altezza risoluzione)](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Height.htm) | Identifica le richieste in base all'altezza del dispositivo. |
| [Resolution Width (Larghezza risoluzione)](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Width.htm) | Identifica le richieste in base alla larghezza del dispositivo. |

**[Torna all'inizio](#top)**

### <a name="location"></a><a name="location"></a>Percorso

Queste condizioni di corrispondenza sono progettate per identificare le richieste in base alla posizione del richiedente.

| Nome       | Scopo                                                           |
|------------|-------------------------------------------------------------------|
| [Numero AS](https://docs.vdms.com/cdn/Content/HRE/M/AS-Number.htm) | Identifica le richieste che hanno origine da una determinata rete. |
| Nome della città | Identifica le richieste in base al fatto che provengano da una città il cui nome corrisponde a un: <br> **-** Valore specifico (valore[letterale nome città](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Literal.htm)) <br> **-** Espressione regolare ([Regex nome città](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Regex.htm)) |
| [Continente](https://docs.vdms.com/cdn/Content/HRE/M/Continent.htm) | Identifica le richieste originate dai continenti specificati. |
| [Paese](https://docs.vdms.com/cdn/Content/HRE/M/Country.htm) | Identifica le richieste che hanno origine dai paesi specificati. |
| [Codice DMA](https://docs.vdms.com/cdn/Content/HRE/M/DMA-Code.htm) | Identifica le richieste originate dalle metro specificate (aree di mercato designate). |
| [Latitudine](https://docs.vdms.com/cdn/Content/HRE/M/Latitude.htm) | Identifica le richieste che hanno origine dalle latitudini specificate. |
| [Longitudine](https://docs.vdms.com/cdn/Content/HRE/M/Longitude.htm) | Identifica le richieste che hanno origine dalle longitudine specificate. |
| [Codice Metro](https://docs.vdms.com/cdn/Content/HRE/M/Metro-Code.htm) | Identifica le richieste originate dalle metro specificate (aree di mercato designate). |
| [CAP](https://docs.vdms.com/cdn/Content/HRE/M/Postal-Code.htm) | Identifica le richieste che hanno origine dai codici postali specificati. |
| [Codice area](https://docs.vdms.com/cdn/Content/HRE/M/Region-Code.htm) | Identifica le richieste originate dalle aree specificate. |

> [!NOTE]
> **È consigliabile usare codice metro o codice DMA?** <br>
Entrambe le condizioni di corrispondenza forniscono la stessa funzionalità. Tuttavia, è consigliabile usare la condizione di corrispondenza del codice Metro per identificare le richieste in base alla DMA.

**[Torna all'inizio](#top)**

### <a name="origin"></a><a name="origin"></a>Origine

Queste condizioni di corrispondenza sono progettate per identificare le richieste che puntano all'archivio della rete CDN o a un server di origine del cliente.

| Nome       | Scopo                                                           |
|------------|-------------------------------------------------------------------|
| [Origine CDN](https://docs.vdms.com/cdn/Content/HRE/M/CDN-Origin.htm) | Identifica le richieste di contenuto nell'archivio della rete CDN. |
| [Origine cliente](https://docs.vdms.com/cdn/Content/HRE/M/Customer-Origin.htm) | Identifica le richieste di contenuto in uno specifico server di origine del cliente. |

**[Torna all'inizio](#top)**

### <a name="request"></a><a name="request"></a>Richiesta

Queste condizioni di corrispondenza sono progettate per identificare le richieste in base alle relative proprietà.

| Nome              | Scopo                                                                |
|-------------------|------------------------------------------------------------------------|
| [Indirizzo IP client](https://docs.vdms.com/cdn/Content/HRE/M/Client-IP-Address.htm) | Identifica le richieste che hanno origine da un determinato indirizzo IP. |
| Parametro cookie  | Identifica una richiesta se contiene un cookie che corrisponde a un oggetto: <br> **-** Valore specifico (valore[letterale parametro cookie](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Literal.htm)) <br> **-** Espressione regolare ([Regex parametro cookie](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Regex.htm) <br> **-** Modello specifico ([carattere jolly del parametro cookie](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Wildcard.htm)) |
| [CNAME perimetrale](https://docs.vdms.com/cdn/Content/HRE/M/Edge-CNAME.htm) | Identifica le richieste che fanno riferimento a un CNAME periferico specifico. |
| Dominio di riferimento | Identifica una richiesta se è stato fatto riferimento a un nome host che corrisponde a un oggetto: <br> **-** Valore specifico (valore[letterale del dominio di riferimento](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Literal.htm)) <br> **-** Modello specifico ([carattere jolly del dominio di riferimento](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Wildcard.htm)) |
| Intestazione della richiesta | Identifica una richiesta se contiene un'intestazione che corrisponde a un oggetto: <br> **-** Valore specifico (valore[letterale intestazione richiesta](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Literal.htm)) <br> **-** Espressione regolare ([Regex intestazione della richiesta](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Regex.htm)) <br> **-** Modello specifico ([carattere jolly dell'intestazione della richiesta](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Wildcard.htm)) |
| [Metodo di richiesta](https://docs.vdms.com/cdn/Content/HRE/M/Request-Method.htm) | Identifica le richieste in base al relativo metodo HTTP. |
| [Schema richiesta](https://docs.vdms.com/cdn/Content/HRE/M/Request-Scheme.htm) | Identifica le richieste in base al relativo protocollo HTTP. |

**[Torna all'inizio](#top)**

### <a name="url"></a><a name="url"></a>URL

| Nome              | Scopo                                                                |
|-------------------|------------------------------------------------------------------------|
| percorso URL | Identifica le richieste in base al fatto che il percorso relativo, incluso filename, corrisponda a: <br> **-** Valore specifico (valore[letterale percorso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Literal.htm)) <br> **-** Espressione regolare ([Regex Path URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Regex.htm)) <br> **-** Modello specifico ([carattere jolly percorso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Wildcard.htm)) |
| Directory percorso URL | Identifica le richieste in base al fatto che il percorso relativo corrisponda a un oggetto: <br> **-** Valore specifico (valore[letterale directory percorso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Literal.htm)) <br> **-** Modello specifico ([carattere jolly directory percorso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Wildcard.htm)) |
| Estensione percorso URL | Identifica le richieste in base al fatto che l'estensione di file corrisponda a: <br> **-** Valore specifico (valore[letterale estensione percorso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Literal.htm)) <br> **-** Modello specifico ([carattere jolly estensione percorso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Wildcard.htm)) |
| Nome file percorso URL | Identifica le richieste in base al fatto che il nome file corrisponda a: <br> **-** Valore specifico (valore[letterale percorso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Literal.htm)) <br> **-** Modello specifico ([carattere jolly nome file percorso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Wildcard.htm)) |
| Query URL | Identifica le richieste in base al fatto che la stringa di query corrisponda a: <br> **-** Valore specifico (valore[letterale query URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Literal.htm)) <br> **-** Espressione regolare ([Regex query URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Regex.htm)) <br> **-** Modello specifico ([carattere jolly query URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Wildcard.htm)) |
| Parametro query URL | Identifica le richieste in base al fatto che contengano un parametro della stringa di query impostato su un valore che corrisponde a un oggetto: <br> **-** Valore specifico (valore[letterale parametro query URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Literal.htm)) <br> **-** Modello specifico ([carattere jolly del parametro di query URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Wildcard.htm)) |

**[Torna all'inizio](#top)**

Per le condizioni di corrispondenza più recenti, vedere la [documentazione del motore regole di Verizon](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della rete per la distribuzione di contenuti di Azure](cdn-overview.md)
- [Informazioni di riferimento sul motore regole](cdn-verizon-premium-rules-engine-reference.md)
- [Espressioni condizionali del motore regole](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funzionalità del motore regole](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override del comportamento HTTP predefinito mediante il motore regole](cdn-verizon-premium-rules-engine.md)
