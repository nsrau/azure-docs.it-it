---
title: Risoluzione dei problemi dell'analizzatore Internet di Azure | Microsoft Docs
description: Informazioni di riferimento sulla risoluzione dei problemi dell'analizzatore Internet di Azure.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: troubleshooting
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: fe173ec4d4f28444d43739ea2ed51e43021916b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744358"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Risoluzione dei problemi dell'analizzatore Internet di Azure

Questo articolo contiene i passaggi per la risoluzione dei problemi comuni dell'analizzatore Internet.

## <a name="things-to-keep-in-mind"></a>Aspetti da considerare
- Lo script client deve essere incorporato in un sito Web **HTTPS**. Le misure non verranno raccolte se lo script viene eseguito in un sito Web di testo non crittografato (**http://** ) o locale (**file://** ).
- I dati di misura verranno raccolti solo se lo script client del profilo dell'analizzatore Internet è stato incorporato in un'applicazione che riceve traffico utente reale. Il traffico sintetico, ad esempio i test delle prestazioni di Azure WebApp, in genere non esegue codice JavaScript incorporato, quindi nessuna misura verrà generata da tale tipo di traffico.

## <a name="azure-portal"></a>Portale di Azure
**"Non è stata generata una scorecard per la combinazione di filtri selezionata" nella sezione Scorecard**
- Le scorecard vengono generate su base giornaliera (alla fine di ogni giornata, ora UTC).
- Le scorecard vengono generate solo se sono state raccolte più di 100 misure per la combinazione di filtri selezionata (test, periodo di tempo, Paese/regione e così via).

Il **"Numero totale delle misure" è zero per uno o entrambi gli endpoint in un test**
- I conteggi delle serie temporali e delle misure vengono calcolati una volta ogni ora, quindi è necessario attendere almeno questo intervallo di tempo per visualizzare i nuovi dati di misura.
- Ai fini dell'analisi, l'analizzatore Internet conteggia unicamente le misure con esito positivo (ad esempio, risposte HTTP 200). Se uno o entrambi gli endpoint di un test non sono raggiungibili o restituiscono un codice HTTP non 200, verranno visualizzati con zero misure totali.

## <a name="next-steps"></a>Passaggi successivi
Leggere le [Domande frequenti sull'analizzatore Internet](internet-analyzer-faq.md)
