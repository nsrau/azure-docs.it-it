---
title: Preparazione della soluzione Dynamics 365 | Microsoft Docs
description: Framework per installare, disinstallare e creare pacchetti dei componenti
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricarod.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 2ea62a826b9f99e264cb1b2242f8d8108453d8e7
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448772"
---
# <a name="dynamics-365-solution-preparation"></a>Preparazione della soluzione Dynamics 365

Il sistema di soluzione di Dynamics 365 è un framework per installare, disinstallare e creare pacchetti dei componenti che forniscono funzionalità aziendali specifiche. Le soluzioni vengono usate dagli ISV e da altri partner di Microsoft Dynamics 365 per distribuire le estensioni create.

È molto probabile che un ISV esistente di Dynamics 365 (xRM) abbia già creato una soluzione gestita e che disponga di un file di soluzione .zip. Nella soluzione, assicurarsi che i campi "Nome visualizzato" e "Descrizione" corrispondano alle informazioni che si desidera mostrare ai clienti. Queste vengono visualizzate nell'interfaccia di amministrazione CRM Online.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Nota:** nell'esempio del pacchetto che segue si ipotizza che il nome della soluzione sia "SampleSolution.zip"_

Per i nuovi ISV, è possibile ottenere altre informazioni sulla creazione di una soluzione qui: [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Se la soluzione richiede il supporto di dati:

* Creare i dati di esempio nell'ambiente di testing
* Usare lo strumento di migrazione della configurazione per creare uno schema con regole di confronto dei dati.
* Salvare lo schema di configurazione con i file di progetto. Questa operazione sarà necessaria in un secondo momento, in caso di aggiornamento dei dati di configurazione.
* Esportare i dati di configurazione. Assicurarsi di assegnare al file di esportazione un nome significativo per l'esportazione.
