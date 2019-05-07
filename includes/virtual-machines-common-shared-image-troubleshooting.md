---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 1476830313296615591a69a2cadd04bcc56b22bc
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149642"
---
Se si verificano problemi durante l'esecuzione di operazioni su raccolte di immagini condivise, definizioni di immagini e versioni di immagini, eseguire di nuovo il comando per cui si è verificato l'errore in modalità di debug. La modalità di debug viene attivata passando l'opzione **-debug** con l'interfaccia della riga di comando e l'opzione **-Debug** con PowerShell. Dopo aver individuato il problema, seguire questo documento per risolvere gli errori.


## <a name="unable-to-create-a-shared-image-gallery"></a>Impossibile creare una raccolta di immagini condivisa

Possibili cause:

*Il nome della raccolta non è valido.*

I caratteri consentiti per i nomi delle raccolte sono lettere maiuscole o minuscole, numeri e punti. Il nome della raccolta non può contenere trattini. Modificare il nome della raccolta e riprovare. 

*Il nome della raccolta non è univoco all'interno della sottoscrizione.*

Scegliere un nome diverso per la raccolta e riprovare.


## <a name="unable-to-create-an-image-definition"></a>Impossibile creare una definizione dell'immagine 

Possibili cause:

*Il nome della definizione dell'immagine non è valido.*

Caratteri consentiti per la definizione dell'immagine sono periodi, cifre, punti, trattini e lettere maiuscole o minuscole. Modificare il nome della definizione dell'immagine e riprovare.

*Le proprietà obbligatorie per la creazione di una definizione dell'immagine non sono popolate.*

Le proprietà come nome, editore, offerta, sku e tipo di sistema operativo sono obbligatorie. Verificare se vengono passate tutte le proprietà.

Assicurarsi che il valore di **OSType**, Linux o Windows, della definizione dell'immagine corrisponda a quello dell'immagine gestita di origine che si sta usando per creare la versione dell'immagine. 


## <a name="unable-to-create-an-image-version"></a>Impossibile creare una versione dell'immagine 

Possibili cause:

*Il nome della versione dell'immagine non è valido.*

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion.MinorVersion.Patch*. Modificare il nome della versione dell'immagine e riprovare.

*Non è possibile trovare l'immagine gestita di origine da cui viene creata la versione dell'immagine.* 

Controllare se l'immagine di origine esiste e si trova nella stessa area della versione dell'immagine.

*Il provisioning dell'immagine gestita non è stato completato.*

Assicurarsi che lo stato provisioning dell'immagine gestita di origine sia **Riuscito**.

*Elenco delle aree di destinazione non include l'area di origine.*

L'elenco delle aree di destinazione deve includere l'area di origine della versione dell'immagine. Assicurarsi che l'area di origine sia stata inclusa nell'elenco delle aree di destinazione in cui si vuole che Azure replichi la versione dell'immagine.

*La replica in tutte le aree di destinazione non è stata completata.*

Usare il flag **--expand ReplicationStatus** per controllare se la replica in tutte le aree di destinazione specificate è stata completata. In caso contrario, attendere fino a sei ore per il completamento del processo. In caso di esito negativo, eseguire nuovamente il comando per creare e replicare la versione dell'immagine. Se la versione dell'immagine viene replicata in numerose aree di destinazione, valutare se eseguire la replica in fasi.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Impossibile creare una macchina virtuale o un set di scalabilità 

Possibili cause:

*L'utente che cerca di creare una macchina virtuale o un set di scalabilità di macchine virtuali non ha accesso in lettura alla versione dell'immagine.*

Contattare il proprietario della sottoscrizione e chiedere di concedere l'accesso in lettura alla versione dell'immagine o alle risorse padre, come la raccolta di immagini condivisa o la definizione dell'immagine, tramite [Controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles). 

*Non è possibile trovare la versione dell'immagine.*

Verificare che l'area in cui si sta cercando di creare una macchina virtuale o un set di scalabilità di macchine virtuali sia inclusa nell'elenco delle aree di destinazione della versione dell'immagine. Se l'area è già presente nell'elenco delle aree di destinazione, verificare se il processo di replica è stato completato. È possibile usare il flag **-ReplicationStatus** per controllare se la replica in tutte le aree di destinazione specificate è stata completata. 

*La creazione della macchina virtuale o del set di scalabilità di macchine virtuali richiede molto tempo.*

Verificare che il valore di **OSType** della versione dell'immagine da cui si sta cercando di creare la macchina virtuale o il set di scalabilità di macchine virtuali corrisponda al valore di **OSType** dell'immagine gestita di origine usata per creare la versione dell'immagine. 

## <a name="unable-to-share-resources"></a>Impossibile condividere le risorse

La condivisione della raccolta di immagini condivise, definizione dell'immagine e le risorse della versione immagine tra le sottoscrizioni attivata mediante [Role-Based Access Control](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

## <a name="replication-is-slow"></a>La replica è lenta

Usare il flag **--expand ReplicationStatus** per controllare se la replica in tutte le aree di destinazione specificate è stata completata. In caso contrario, attendere fino a sei ore per il completamento del processo. In caso di esito negativo, attivare nuovamente il comando per creare e replicare la versione dell'immagine. Se la versione dell'immagine viene replicata in numerose aree di destinazione, valutare se eseguire la replica in fasi.

## <a name="azure-limits-and-quotas"></a>Limiti e quote di Azure 

[Limiti e quote di Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits) si applicano a tutte le risorse della raccolta di immagini condivisa, della definizione dell'immagine e della versione dell'immagine. Assicurarsi di rispettare i limiti per le sottoscrizioni. 



