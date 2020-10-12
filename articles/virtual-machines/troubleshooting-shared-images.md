---
title: Risolvere i problemi delle immagini condivise in Azure
description: Informazioni su come risolvere i problemi delle raccolte di immagini condivise.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 06/15/2020
ms.author: cynthn
ms.reviewer: cynthn
ms.openlocfilehash: 3a206a7aabee9f75524ab4715afa30ec05c612bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328064"
---
# <a name="troubleshooting-shared-image-galleries-in-azure"></a>Risoluzione dei problemi relativi alle raccolte di immagini condivise in Azure

Se si verificano problemi durante l'esecuzione di operazioni su raccolte di immagini condivise, definizioni di immagini e versioni di immagini, eseguire di nuovo il comando per cui si è verificato l'errore in modalità di debug. La modalità di debug viene attivata passando l' `--debug` opzione con l'interfaccia della riga di comando e l' `-Debug` opzione con PowerShell. Dopo aver individuato il problema, seguire questo documento per risolvere gli errori.


## <a name="unable-to-create-a-shared-image-gallery"></a>Impossibile creare una raccolta di immagini condivisa

Possibili cause:

*Il nome della raccolta non è valido.*

I caratteri consentiti per i nomi delle raccolte sono lettere maiuscole o minuscole, numeri e punti. Il nome della raccolta non può contenere trattini. Modificare il nome della raccolta e riprovare. 

*Il nome della raccolta non è univoco all'interno della sottoscrizione.*

Scegliere un nome diverso per la raccolta e riprovare.


## <a name="unable-to-create-an-image-definition"></a>Impossibile creare una definizione dell'immagine 

Possibili cause:

*Il nome della definizione dell'immagine non è valido.*

I caratteri consentiti per la definizione dell'immagine sono lettere maiuscole o minuscole, cifre, punti, trattini e punti. Modificare il nome della definizione dell'immagine e riprovare.

*Le proprietà obbligatorie per la creazione di una definizione dell'immagine non sono popolate.*

Le proprietà come nome, editore, offerta, sku e tipo di sistema operativo sono obbligatorie. Verificare se vengono passate tutte le proprietà.

Assicurarsi che la **OSType**, Linux o Windows, della definizione dell'immagine corrisponda all'origine usata per creare la versione dell'immagine. 


## <a name="unable-to-create-an-image-version"></a>Impossibile creare una versione dell'immagine 

Possibili cause:

*Il nome della versione dell'immagine non è valido.*

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion. MinorVersion. patch*. Modificare il nome della versione dell'immagine e riprovare.

*Non è possibile trovare l'immagine gestita di origine da cui viene creata la versione dell'immagine.* 

Controllare se l'immagine di origine esiste e si trova nella stessa area della versione dell'immagine.

*Il provisioning dell'immagine gestita non è stato completato.*

Assicurarsi che lo stato provisioning dell'immagine gestita di origine sia **Riuscito**.

*L'elenco delle aree di destinazione non include l'area di origine.*

L'elenco delle aree di destinazione deve includere l'area di origine della versione dell'immagine. Assicurarsi che l'area di origine sia stata inclusa nell'elenco delle aree di destinazione in cui si vuole che Azure replichi la versione dell'immagine.

*La replica in tutte le aree di destinazione non è stata completata.*

Usare il flag **--expand ReplicationStatus** per controllare se la replica in tutte le aree di destinazione specificate è stata completata. In caso contrario, attendere fino a sei ore per il completamento del processo. In caso di esito negativo, eseguire nuovamente il comando per creare e replicare la versione dell'immagine. Se la versione dell'immagine viene replicata in numerose aree di destinazione, valutare se eseguire la replica in fasi.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Impossibile creare una macchina virtuale o un set di scalabilità 

Possibili cause:

*L'utente che cerca di creare una macchina virtuale o un set di scalabilità di macchine virtuali non ha accesso in lettura alla versione dell'immagine.*

Contattare il proprietario della sottoscrizione e chiedere di concedere l'accesso in lettura alla versione dell'immagine o alle risorse padre, ad esempio la raccolta di immagini condivise o la definizione dell'immagine, tramite il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles). 

*Non è possibile trovare la versione dell'immagine.*

Verificare che l'area in cui si sta cercando di creare una macchina virtuale o un set di scalabilità di macchine virtuali sia inclusa nell'elenco delle aree di destinazione della versione dell'immagine. Se l'area è già presente nell'elenco delle aree di destinazione, verificare se il processo di replica è stato completato. È possibile usare il flag **-ReplicationStatus** per controllare se la replica in tutte le aree di destinazione specificate è stata completata. 

*La creazione della macchina virtuale o del set di scalabilità di macchine virtuali richiede molto tempo.*

Verificare che il **OSType** della versione dell'immagine che si sta tentando di creare la VM o il set di scalabilità di macchine virtuali abbia lo stesso **OSType** dell'origine usato per creare la versione dell'immagine. 

## <a name="unable-to-share-resources"></a>Impossibile condividere le risorse

La condivisione delle risorse della raccolta di immagini condivise, della definizione di immagini e della versione immagine tra le sottoscrizioni viene abilitata usando il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles). 

## <a name="replication-is-slow"></a>La replica è lenta

Usare il flag **--expand ReplicationStatus** per controllare se la replica in tutte le aree di destinazione specificate è stata completata. In caso contrario, attendere fino a sei ore per il completamento del processo. In caso di esito negativo, attivare nuovamente il comando per creare e replicare la versione dell'immagine. Se la versione dell'immagine viene replicata in numerose aree di destinazione, valutare se eseguire la replica in fasi.

## <a name="azure-limits-and-quotas"></a>Limiti e quote di Azure 

[Limiti e quote di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) si applicano a tutte le risorse della raccolta di immagini condivisa, della definizione dell'immagine e della versione dell'immagine. Assicurarsi di rispettare i limiti per le sottoscrizioni. 


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [raccolte di immagini condivise](./linux/shared-image-galleries.md).
