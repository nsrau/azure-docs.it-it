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
ms.openlocfilehash: 1d7a91de8fa505fe4c2b06eea59f3acc2ae1f7e8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343316"
---
# <a name="troubleshoot-snapshot-policies"></a>Risolvere i problemi relativi ai criteri snapshot

Questo articolo descrive gli scenari di errore che possono verificarsi durante la gestione dei criteri di Azure NetApp Files snapshot. Fornisce anche soluzioni che consentono di risolvere i problemi.

## <a name="snapshot-policy-creation-failing-with-invalid-snapshot-policy-name"></a>Creazione dei criteri di snapshot non riuscita con nome dei criteri di snapshot non valido

Si verifica un errore durante la creazione dei criteri snapshot se il nome del criterio snapshot non è valido. Le linee guida seguenti si applicano ai nomi dei criteri di snapshot:  

* Il nome dei criteri snapshot non può contenere caratteri speciali o non ASCII.
* Il nome dei criteri snapshot deve iniziare con una lettera o un numero e può contenere solo lettere, numeri, caratteri di sottolineatura (' _') e trattini ('-').
* Il nome del criterio snapshot deve avere una lunghezza compresa tra 1 e 64 caratteri.  

È necessario rivedere il nome dei criteri di snapshot secondo le linee guida.  

## <a name="snapshot-policy-creation-failing-with-invalid-values"></a>Creazione dei criteri di snapshot non riuscita con valori non validi 

Azure NetApp Files non riesce a creare un criterio snapshot se si immette un valore non valido per un campo, ad esempio `Number of snapshots to keep` o `Minute on the hour to take snapshot` .  
 
I valori validi sono i seguenti:   

* Il valore deve essere un numero valido.
* Il valore deve essere compreso tra 0 e 59.

Assicurarsi che venga fornito un valore valido per i campi.

## <a name="snapshot-policy-creation-failing-with-total-number-of-snapshots-to-keep-exceeds-255-error"></a>Creazione dei criteri di snapshot non riuscita con errore "numero totale di snapshot da contenere superiore a 255" 

Ogni volume può avere un [massimo di 255 snapshot](azure-netapp-files-resource-limits.md). Il valore massimo include la somma di tutti gli snapshot orari, giornalieri, settimanali e mensili. È consigliabile ridurre il `Snapshots to keep` valore e riprovare.

## <a name="assigning-policy-to-a-volume-failing-with-total-snapshot-policy-is-over-the-max-255-error"></a>Assegnazione dei criteri a un volume non riuscito con "totale criteri snapshot supera il numero massimo di ' 255' errore

Ogni volume può avere un [massimo di 255 snapshot](azure-netapp-files-resource-limits.md). Quando la somma di tutti gli snapshot su richiesta, orari, giornalieri, settimanali e mensili supera il limite massimo, si verifica un errore. Ridurre il `snapshots to keep` valore o eliminare alcuni snapshot su richiesta e riprovare.

## <a name="next-steps"></a>Passaggi successivi  

* [Gestire i criteri di snapshot](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
