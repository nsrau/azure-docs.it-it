---
title: Risoluzione dei problemi dell'analizzatore Internet di Azure | Microsoft Docs
description: Informazioni di riferimento sulla risoluzione dei problemi dell'analizzatore Internet di Azure.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: 0c9e203d7e8be7b24c711f73e2152a7745a57dac
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745475"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Risoluzione dei problemi dell'analizzatore Internet di Azure

Questo articolo contiene i passaggi per la risoluzione dei problemi comuni dell'analizzatore Internet.

## <a name="things-to-keep-in-mind"></a>Aspetti da considerare
- Lo script client deve essere incorporato in un sito Web **HTTPS**. Le misure non verranno raccolte se lo script viene eseguito in un sito Web di testo non crittografato (**http://** ) o locale (**file://** ).
- I dati di misura verranno raccolti solo se lo script client del profilo dell'analizzatore Internet è stato incorporato in un'applicazione che riceve traffico utente reale. Il traffico sintetico (ad esempio i test delle prestazioni dell'app Web di Azure) in genere non esegue codice JavaScript incorporato, quindi nessuna misura verrà generata da tale tipo di traffico.

## <a name="azure-portal"></a>Portale di Azure
**"Non è stata generata una scorecard per la combinazione di filtri selezionata" nella sezione Scorecard**
- Le scorecard vengono generate su base giornaliera (alla fine di ogni giornata, ora UTC).
- Le scorecard vengono generate solo se sono state raccolte più di 100 misure per la combinazione di filtri selezionata (test, periodo di tempo, Paese/regione e così via).

Il **"Numero totale delle misure" è zero per uno o entrambi gli endpoint in un test**
- I conteggi delle serie temporali e delle misure vengono calcolati una volta ogni ora, quindi è necessario attendere almeno questo intervallo di tempo per visualizzare i nuovi dati di misura.
- Ai fini dell'analisi, l'analizzatore Internet conteggia unicamente le misure con esito positivo (ad esempio, risposte HTTP 200). Se uno o entrambi gli endpoint di un test non sono raggiungibili o restituiscono un codice HTTP non 200, verranno visualizzati con zero misure totali.

## <a name="next-steps"></a>Passaggi successivi
Leggere le [Domande frequenti sull'analizzatore Internet](internet-analyzer-faq.md)
