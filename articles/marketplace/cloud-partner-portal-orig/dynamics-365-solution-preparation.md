---
title: Preparazione della soluzione Dynamics 365
description: Framework per installare, disinstallare e creare pacchetti dei componenti
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: ac1e4fa541e945f20904ced114a36b58d14585ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278587"
---
# <a name="dynamics-365-solution-preparation"></a>Preparazione della soluzione Dynamics 365

Il sistema di soluzione di Dynamics 365 è un framework per installare, disinstallare e creare pacchetti dei componenti che forniscono funzionalità aziendali specifiche. Le soluzioni vengono usate dagli ISV e da altri partner di Microsoft Dynamics 365 per distribuire le estensioni create.

È molto probabile che un ISV esistente di Dynamics 365 (xRM) abbia già creato una soluzione gestita e che disponga di un file di soluzione .zip. Nella soluzione, assicurarsi che i campi "Nome visualizzato" e "Descrizione" corrispondano alle informazioni che si desidera mostrare ai clienti. Queste vengono visualizzate nell'interfaccia di amministrazione CRM Online.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Nota:** nell'esempio del pacchetto che segue si ipotizza che il nome della soluzione sia "SampleSolution.zip"_

Se sei un nuovo ISV, puoi ottenere maggiori dettagli sulla creazione di una soluzione qui:[https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Se la soluzione richiede il supporto di dati:

* Creare i dati di esempio nell'ambiente di testing
* Usare lo strumento di migrazione della configurazione per creare uno schema con regole di confronto dei dati.
* Salvare lo schema di configurazione con i file di progetto. Questa operazione sarà necessaria in un secondo momento, in caso di aggiornamento dei dati di configurazione.
* Esportare i dati di configurazione. Assicurarsi di assegnare al file di esportazione un nome significativo per l'esportazione.
