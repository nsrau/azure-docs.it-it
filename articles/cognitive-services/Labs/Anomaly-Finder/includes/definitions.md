---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 5ad589c4adb60369f81979e214935f73d9eb0755
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309509"
---
<a name="definitions"></a>
## <a name="definitions"></a>Definizioni

<a name="point"></a>
### <a name="point"></a>Point

|NOME|DESCRIZIONE|SCHEMA|
|---|---|---|
|**Timestamp**  <br>*facoltativo*|Timestamp del punto dati. Assicurarsi che sia allineato alla mezzanotte e che usi una stringa di data e ora UTC, ad esempio 2017-08-01T00:00:00Z.|string (date-time)|
|**Valore**  <br>*facoltativo*|Valore di misura dei dati.|numero (double)|


<a name="request"></a>
### <a name="request"></a>Richiesta

|NOME|DESCRIZIONE|SCHEMA|
|---|---|---|
|**Periodo**  <br>*facoltativo*|Periodo dei punti dati. Se il valore è Null o non è presente, l'API determina il periodo automaticamente.|numero (double)|
|**Punti**  <br>*facoltativo*|Punti dati di serie temporali. I dati devono essere ordinati per timestamp crescente per restituire i risultati sulle anomalie. Se i dati non sono ordinati correttamente o è presente un timestamp duplicato, l'API rileverà correttamente i punti di anomalia, ma non sarà possibile trovare la corrispondenza tra i punti restituiti e l'input. In tal caso, verrà aggiunto un messaggio di avviso nella risposta.|Matrice < [punto](#point) >|


<a name="response"></a>
### <a name="response"></a>Risposta

|NOME|DESCRIZIONE|SCHEMA|
|---|---|---|
|**ExpectedValues**  <br>*facoltativo*|Valore stimato dal modello basato sull'apprendimento. Se i punti dati di input sono ordinati per timestamp crescente, si può usare l'indice della matrice per eseguire il mapping del valore previsto al valore originale.|Matrice < numero (double) >|
|**IsAnomaly**  <br>*facoltativo*|Risultato che indica se i punti dati sono anomalie o meno in entrambe le direzioni (picchi o flessioni). True indica che il punto è un'anomalia, False che non lo è. Se i punti dati di input sono ordinati per timestamp crescente, si può usare l'indice della matrice per eseguire il mapping del valore previsto al valore originale.|Matrice < booleano >|
|**IsAnomaly_Neg**  <br>*facoltativo*|Risultato che indica se i punti dati sono anomalie in direzione negativa (flessioni). True indica che la direzione dell'anomalia è negativa. Se i punti dati di input sono ordinati per timestamp crescente, si può usare l'indice della matrice per eseguire il mapping del valore previsto al valore originale.|Matrice < booleano >|
|**IsAnomaly_Pos**  <br>*facoltativo*|Risultato che indica se i punti dati sono anomalie in direzione positiva (picchi). True indica che la direzione dell'anomalia è positiva. Se i punti dati di input sono ordinati per timestamp crescente, si può usare l'indice della matrice per eseguire il mapping tra valore previsto e originale.|Matrice < booleano >|
|**LowerMargin**  <br>*facoltativo*|La differenza tra ExpectedValue e LowerMargin determina il limite inferiore entro il quale il punto dati viene ancora considerato normale. Se i punti dati di input sono ordinati per timestamp crescente, si può usare l'indice della matrice per eseguire il mapping del valore previsto al valore originale.|Matrice < numero (double) >|
|**Periodo**  <br>*facoltativo*|Periodo usato dall'API per rilevare i punti di anomalie.|numero (float)|
|**UpperMargin**  <br>*facoltativo*|La somma di ExpectedValue e UpperMargin determina il limite superiore entro il quale il punto dati viene ancora considerato normale. Se i punti dati di input sono ordinati per timestamp crescente, si può usare l'indice della matrice per eseguire il mapping del valore previsto al valore originale.|Matrice < numero (double) >|
|**WarningText**  <br>*facoltativo*|Se i punti dati di input forniti non seguono la regola richiesta dall'API e i dati possono essere comunque rilevati dall'API, i dati verranno analizzati e il messaggio di avviso verrà aggiunto in questo campo.|string|



