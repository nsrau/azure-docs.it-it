---
title: Risolvere i problemi relativi ai criteri snapshot per Azure NetApp Files | Microsoft Docs
description: Vengono descritti i messaggi di errore e le risoluzioni che consentono di risolvere i problemi di gestione dei criteri snapshot per Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 6ba8b18876bdae2754a6a772ce3909ff2f5a71b7
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651005"
---
# <a name="troubleshoot-snapshot-policies"></a>Risolvere i problemi relativi ai criteri dello snapshot

Questo articolo descrive gli scenari di errore che possono verificarsi durante la gestione dei criteri di Azure NetApp Files snapshot. Fornisce anche soluzioni che consentono di risolvere i problemi.

## <a name="error-conditions-and-resolutions"></a>Condizioni di errore e risoluzioni 

|     Condizione di errore    |     Soluzione    |
|-|-|
| La creazione dei criteri snapshot non riesce con il nome del criterio snapshot non valido. | Si verifica un errore durante la creazione dei criteri snapshot se il nome del criterio snapshot non è valido. Le linee guida seguenti si applicano ai nomi dei criteri di snapshot:  <ul><li> Il nome dei criteri snapshot non può contenere caratteri speciali o non ASCII. </li> <li> Il nome dei criteri snapshot deve iniziare con una lettera o un numero e può contenere solo lettere, numeri, caratteri di sottolineatura (' _') e trattini ('-'). </li> <li> Il nome del criterio snapshot deve avere una lunghezza compresa tra 1 e 64 caratteri.  </li></ul> Rivedere il nome dei criteri di snapshot secondo le linee guida.  |
| La creazione dei criteri snapshot non riesce con valori non validi. | Azure NetApp Files non riesce a creare un criterio snapshot se si immette un valore non valido per un campo, ad esempio `Number of snapshots to keep` o `Minute on the hour to take snapshot` . I valori validi sono i seguenti:  <ul><li>Il valore deve essere un numero valido.</li> <li>Il valore deve essere compreso tra 0 e 59.</li></ul> Assicurarsi che venga fornito un valore valido per i campi. | 
| La creazione dei criteri snapshot non riesce con l'errore `Total number of snapshots to keep exceeds 255` . | Ogni volume può avere un [massimo di 255 snapshot](azure-netapp-files-resource-limits.md). Il valore massimo include la somma di tutti gli snapshot orari, giornalieri, settimanali e mensili. <br> Ridurre il `Snapshots to keep` valore e riprovare. |
| L'assegnazione dei criteri a un volume ha esito negativo con l'errore `Total snapshot policy is over the max '255'` . | Ogni volume può avere un [massimo di 255 snapshot](azure-netapp-files-resource-limits.md). Quando la somma di tutti gli snapshot su richiesta, orari, giornalieri, settimanali e mensili supera il limite massimo, si verifica un errore. <br> Ridurre il `snapshots to keep` valore o eliminare alcuni snapshot su richiesta e riprovare. | 

## <a name="next-steps"></a>Passaggi successivi  

* [Gestire i criteri di snapshot](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
