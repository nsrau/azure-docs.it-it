---
title: Risolvere gli errori del provider di risorse Azure NetApp Files | Microsoft Docs
description: Vengono descritte le cause, le soluzioni e le soluzioni alternative per gli errori comuni del provider di risorse Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: b-juche
ms.openlocfilehash: f417d83a67f2f3afa33a83a56a72d0d82c64ab0d
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850014"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Risolvere i problemi del provider di risorse di Azure NetApp Files 

Questo articolo descrive gli errori comuni del provider di risorse Azure NetApp Files, le cause, le soluzioni e le soluzioni alternative (se disponibili).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Errori comuni del provider di risorse Azure NetApp Files

***BareMetalTenantId non può essere modificato.***  

Questo errore si verifica quando si tenta di aggiornare o applicare patch a un `BaremetalTenantId` volume e la proprietà ha un valore modificato.

* Causa:   
Si sta provando ad aggiornare un volume e la `BaremetalTenantId` proprietà ha un valore diverso dal valore archiviato in Azure.
* Soluzione:   
Non includere `BaremetalTenantId` nella richiesta patch e Update (put). In alternativa, verificare che `BaremetalTenantId` sia uguale nella richiesta.

***ServiceLevel non può essere modificato.***  

Questo errore si verifica quando si tenta di aggiornare o applicare patch a un pool di capacità con un livello di servizio diverso quando nel pool di capacità sono già presenti volumi.

* Causa:   
Si sta provando ad aggiornare un livello di servizio del pool di capacità quando il pool contiene volumi.
* Soluzione:   
Eliminare tutti i volumi dal pool di capacità, quindi modificare il livello di servizio.
* Soluzione alternativa:   
Creare un altro pool di capacità, quindi creare nuovamente i volumi nel nuovo pool di capacità.

***Non è possibile modificare PoolId***  

Questo errore si verifica quando si tenta di aggiornare o applicare patch a un pool di `PoolId` capacità con una proprietà modificata.

* Causa:   
Si sta tentando di aggiornare una proprietà `PoolId` del pool di capacità. La `PoolId` proprietà è una proprietà di sola lettura e non può essere modificata.
* Soluzione:   
Non includere `PoolId` nella richiesta patch e Update (put).  In alternativa, verificare che `PoolId` sia uguale nella richiesta.

***CreationToken non può essere modificato.***

Questo errore si verifica quando si tenta di modificare il percorso del`CreationToken`file () dopo che il volume è stato creato. Il percorso del`CreationToken`file () deve essere impostato al momento della creazione del volume e non può essere modificato in un secondo momento.

* Causa:   
Si sta tentando di modificare il percorso del`CreationToken`file () dopo che è stato creato il volume, che non è un'operazione supportata. 
* Soluzione:   
Se la modifica del percorso del file non è necessaria, provare a rimuovere il parametro dalla richiesta per ignorare il messaggio di errore.
* Soluzione alternativa:   
Se è necessario modificare il percorso del file (`CreationToken`), è possibile creare un nuovo volume con un nuovo percorso di file e quindi eseguire la migrazione dei dati nel nuovo volume.

***La lunghezza di CreationToken deve essere di almeno 16 caratteri.***

Questo errore si verifica quando il percorso del`CreationToken`file () non soddisfa il requisito di lunghezza. La lunghezza del percorso del file deve contenere almeno un carattere.

* Causa:   
Il percorso del file è vuoto.  Quando si crea un volume usando l'API, è necessario un token di creazione. Se si usa il portale di Azure, il percorso del file viene generato automaticamente.
* Soluzione:   
Immettere almeno un carattere come percorso del file (`CreationToken`).

***Non è possibile modificare il nome di dominio.***

Questo errore si verifica quando si tenta di modificare il nome di dominio in Active Directory.

* Causa:   
Si sta tentando di aggiornare la proprietà del nome di dominio.
* Soluzione:    
No. Non è possibile modificare il nome di dominio.
* Soluzione alternativa:   
Eliminare tutti i volumi usando la configurazione Active Directory. Eliminare quindi la configurazione di Active Directory e ricreare i volumi.

***Errore di valore duplicato per l'oggetto ExportPolicy. Rules [RuleIndex].***

Questo errore si verifica quando i criteri di esportazione non sono definiti con un indice univoco. Quando si definiscono i criteri di esportazione, tutte le regole dei criteri di esportazione devono disporre di un indice univoco compreso tra 1 e 5.

* Causa:   
Il criterio di esportazione definito non soddisfa i requisiti per le regole di esportazione dei criteri. È necessario avere una regola di esportazione dei criteri minima e cinque regole dei criteri di esportazione al massimo.
* Soluzione:   
Assicurarsi che l'indice non sia già in uso e che sia compreso tra 1 e 5.
* Soluzione alternativa:   
Utilizzare un indice diverso per la regola che si sta tentando di impostare.

***Errore {Action} {resourceTypeName}***

Questo errore viene visualizzato quando la gestione degli errori non riesce a gestire l'errore durante l'esecuzione di un'azione su una risorsa.   Include il testo "Error". `getting` `creating` `updating`Può essere qualsiasi di (,, o`deleting`). `{action}`  `netAppAccount` È`capacityPool`(ad esempio, ,,ecosìvia).`volume` `{resourceTypeName}` `resourceTypeName`

* Causa:   
Questo errore è un'eccezione non gestita in cui la cause non è nota.
* Soluzione:   
Contattare il supporto tecnico di Azure per segnalare il motivo dettagliato nei log.
* Soluzione alternativa:   
No.

***Il nome del percorso del file può contenere solo lettere, numeri e trattini (""-"").***

Questo errore si verifica quando il percorso del file contiene caratteri non supportati, ad esempio un punto ("."), una virgola (","), un carattere di sottolineatura ("_") o un segno di dollaro ("$").

* Causa:   
Il percorso del file contiene caratteri non supportati, ad esempio un punto ("."), una virgola (","), un carattere di sottolineatura ("_") o un segno di dollaro ("$").
* Soluzione:   
Rimuovere i caratteri che non sono lettere alfabetiche, numeri o trattini ("-") dal percorso del file immesso.
* Soluzione alternativa:   
È possibile sostituire un carattere di sottolineatura con un trattino o usare le maiuscole anziché gli spazi per indicare l'inizio di nuove parole.  Ad esempio, usare "NewVolume" invece di "nuovo volume".

***FileSystemId non può essere modificato.***

Questo errore si verifica quando si tenta di `FileSystemId`modificare.  La `FileSystemdId` modifica non è un'operazione supportata. 

* Causa:   
L'ID del file system viene impostato al momento della creazione del volume. `FileSystemId`non può essere modificato successivamente.
* Soluzione:   
Non includere `FileSystemId` in una richiesta patch e Update (put).  In alternativa, verificare che `FileSystemId` sia uguale nella richiesta.

***ActiveDirectory con ID:' {String}' non esiste.***

La `{string}` parte è il valore immesso `ActiveDirectoryId` nella proprietà per la connessione Active Directory.

* Causa:   
Quando è stato creato un account con la configurazione di Active Directory, è stato immesso `ActiveDirectoryId` un valore per che dovrebbe essere vuoto.
* Soluzione:   
Non includere `ActiveDirectoryId` nella richiesta di creazione (put).

***Versione API non valida.***

La versione dell'API non è stata inviata o contiene un valore non valido.

* Causa:   
Il valore nel parametro `api-version` della query contiene un valore non valido.
* Soluzione:   
Usare il valore corretto della versione dell'API.  Il provider di risorse supporta molte versioni dell'API. Il valore è nel formato aaaa-mm-gg.

***È stato ricevuto un valore ' {value}' non {1}valido per.***

Questo messaggio indica un errore nei campi per `RuleIndex`, `AllowedClients`, `UnixReadOnly`, `UnixReadWrite`, `Nfsv3`e `Nfsv4`.

* Causa:   
La richiesta di convalida di input non è riuscita per almeno uno dei campi seguenti `RuleIndex`: `AllowedClients` `UnixReadOnly`,, `UnixReadWrite`, `Nfsv`, 3 e `Nfsv4`.
* Soluzione:   
Assicurarsi di impostare tutti i parametri obbligatori e non in conflitto nella riga di comando. Ad esempio, non è possibile impostare contemporaneamente `UnixReadOnly` i `UnixReadWrite` parametri e.
* Soluzione alternativa:   
Vedere la soluzione sopra.

***L'intervallo {0} IP {1} per la {2} VLAN è già in uso***

Questo errore si verifica perché i record interni degli intervalli IP usati presentano un conflitto con l'indirizzo IP appena assegnato.

* Causa:   
L'indirizzo IP assegnato per la creazione del volume è già registrato.
Il motivo potrebbe essere la creazione di un volume precedentemente non riuscito.
* Soluzione:   
Contattare il supporto tecnico di Azure.

***Valore mancante per ' {Property}'.***

Questo errore indica che nella richiesta manca una proprietà obbligatoria. La stringa {Property} contiene il nome della proprietà mancante.

* Causa:   
La richiesta di convalida di input non è riuscita per almeno una delle proprietà.
* Soluzione:   
Assicurarsi di impostare tutte le proprietà obbligatorie e non in conflitto nella richiesta, in particolare la proprietà del messaggio di errore.

***MountTargets non può essere modificato.***

Questo errore si verifica quando un utente tenta di aggiornare o applicare patch alla proprietà MountTargets del volume.

* Causa:   
Si sta tentando di aggiornare la `MountTargets` proprietà volume. La modifica di questa proprietà non è supportata.
* Soluzione:   
Non includere `MountTargets` in una richiesta patch e Update (put).  In alternativa, verificare che `MountTargets` sia uguale nella richiesta.

***Nome già in uso.***

Questo errore indica che il nome della risorsa è già in uso.

* Causa:   
Si sta provando a creare una risorsa con un nome usato per una risorsa esistente.
* Soluzione:   
Usare un nome univoco quando si crea la risorsa.

***Il percorso del file è già in uso.***

Questo errore indica che il percorso del file per il volume è già in uso.

* Causa:   
Si sta provando a creare un volume con un percorso di file identico a quello di un volume esistente.
* Soluzione:   
Quando si crea il volume, utilizzare un percorso file univoco.

***Nome troppo lungo.***

Questo errore indica che il nome della risorsa non soddisfa il requisito di lunghezza massima.

* Causa:   
Il nome della risorsa è troppo lungo.
* Soluzione:   
Usare un nome più breve per la risorsa.

***Percorso del file troppo lungo.***

Questo errore indica che il percorso del file per il volume non soddisfa il requisito di lunghezza massima.

* Causa:   
Il percorso del file di volume è troppo lungo.
* Soluzione:   
Usare un percorso di file più breve.

***Nome troppo breve.***

Questo errore indica che il nome della risorsa non soddisfa il requisito di lunghezza minima.

* Causa:   
Il nome della risorsa è troppo breve.
* Soluzione:   
Usare un nome più lungo per la risorsa.

***Il percorso del file è troppo breve.***

Questo errore indica che il percorso del file di volume non soddisfa il requisito di lunghezza minima.

* Causa:   
Il percorso del file di volume è troppo breve.
* Soluzione:   
Aumentare la lunghezza del percorso del file del volume.

***API di Azure NetApp Files irraggiungibile.***

L'API di Azure si basa sull'API Azure NetApp Files per gestire i volumi. Questo errore indica un problema relativo alla connessione API.

* Causa:   
L'API sottostante non risponde, causando un errore interno. Questo errore è probabile che sia temporaneo.
* Soluzione:   
Il problema è probabile che sia temporaneo. La richiesta deve avere esito positivo in un secondo momento.
* Soluzione alternativa:   
No. L'API sottostante è essenziale per la gestione dei volumi.

***Non è stato trovato alcun ID risultato{0}operazione per ''.***

Questo errore indica che un errore interno impedisce il completamento dell'operazione.

* Causa:   
Si è verificato un errore interno che ha impedito il completamento dell'operazione.
* Soluzione:   
Questo errore è probabile che sia temporaneo. Attendere alcuni minuti e riprovare. Se il problema persiste, creare un ticket per il supporto tecnico per esaminare il problema.
* Soluzione alternativa:   
Attendere alcuni minuti e verificare se il problema persiste.

***Non è consentito combinare i tipi di protocollo CIFS e NFS***

Questo errore si verifica quando si tenta di creare un volume ed esistono entrambi i tipi di protocollo CIFS (SMB) e NFS nelle proprietà del volume.

* Causa:   
Nelle proprietà del volume vengono utilizzati sia i tipi di protocollo CIFS (SMB) che NFS.
* Soluzione:   
Rimuovere uno dei tipi di protocollo.
* Soluzione alternativa:   
Lasciare vuota la proprietà del tipo di protocollo o null.

***Numero di elementi: {value} per l'oggetto: ExportPolicy. Rules [RuleIndex] non è compreso nell'intervallo min-max.***

Questo errore si verifica quando le regole dei criteri di esportazione non soddisfano il requisito di intervallo minimo o massimo. Se si definiscono i criteri di esportazione, è necessario che sia presente una regola di esportazione minima e cinque regole dei criteri di esportazione al massimo.

* Causa:   
Il criterio di esportazione definito non soddisfa l'intervallo richiesto.
* Soluzione:   
Assicurarsi che l'indice non sia già in uso e che sia compreso tra 1 e 5.
* Soluzione alternativa:   
Non è obbligatorio usare i criteri di esportazione sui volumi. È possibile omettere completamente i criteri di esportazione se non è necessario utilizzare le regole di esportazione dei criteri.

***È consentito un solo Active Directory***

Questo errore si verifica quando si tenta di creare una configurazione Active Directory e ne esiste già una per la sottoscrizione nell'area. Questo errore può verificarsi anche quando si tenta di creare più di una Active Directory configurazione.

* Causa:   
Si sta tentando di creare (non aggiornare) un'Active Directory, ma ne esiste già una.
* Soluzione:   
Se la configurazione del Active Directory non è in uso, è possibile eliminare prima la configurazione esistente, quindi ripetere l'operazione di creazione.
* Soluzione alternativa:   
No. È consentita una sola Active Directory.

***L'operazione ' {Operation}' non è supportata.***

Questo errore indica che l'operazione non è disponibile per la sottoscrizione o la risorsa attiva.

* Causa:   
Operazione non disponibile per la sottoscrizione o la risorsa.
* Soluzione:   
Verificare che l'operazione sia stata immessa correttamente e che sia disponibile per la risorsa e la sottoscrizione in uso.

***Non è possibile modificare OwnerId***

Questo errore si verifica quando si tenta di modificare la proprietà OwnerId del volume. La modifica di OwnerId non è un'operazione supportata. 

* Causa:   
La `OwnerId` proprietà viene impostata quando viene creato il volume. La proprietà non può essere modificata successivamente.
* Soluzione:   
Non includere `OwnerId` in una richiesta patch e Update (put). In alternativa, verificare che `OwnerId` sia uguale nella richiesta.

***Pool padre non trovato***

Questo errore si verifica quando si tenta di creare un volume e il pool di capacità in cui si sta creando il volume non è stato trovato.

* Causa:   
Il pool di capacità in cui è in corso la creazione del volume non è stato trovato.
* Soluzione:   
Probabilmente il pool non è stato completamente creato oppure è già stato eliminato al momento della creazione del volume.

***L'operazione patch non è supportata per questo tipo di risorsa.***

Questo errore si verifica quando si tenta di modificare la destinazione di montaggio o lo snapshot.

* Causa:   
La destinazione di montaggio viene definita al momento della creazione e non può essere modificata successivamente.
Gli snapshot non contengono proprietà che possono essere modificate.
* Soluzione:   
No. Tali risorse non dispongono di proprietà che possono essere modificate.

***Dimensioni del pool troppo piccole per la dimensione totale del volume.***

Questo errore si verifica quando si aggiornano le dimensioni del pool di capacità e le dimensioni sono inferiori al `usedBytes` valore totale di tutti i volumi nel pool di capacità.  Questo errore può verificarsi anche quando si crea un nuovo volume o si ridimensiona un volume esistente e la nuova dimensione del volume supera lo spazio disponibile nel pool di capacità.

* Causa:   
Si sta tentando di aggiornare il pool di capacità a una dimensione inferiore rispetto a usedBytes in tutti i volumi del pool di capacità.  In alternativa, si sta provando a creare un volume maggiore dello spazio disponibile nel pool di capacità.  In alternativa, si sta provando a ridimensionare un volume e la nuova dimensione supera lo spazio disponibile nel pool di capacità.
* Soluzione:   
Impostare le dimensioni del pool di capacità su un valore più grande oppure creare un volume più piccolo per un volume.
* Soluzione alternativa:   
Rimuovere un numero sufficiente di volumi in modo che le dimensioni del pool di capacità possano essere aggiornate a queste dimensioni.

***Proprietà: Il percorso per lo snapshot deve essere uguale a quello del volume***

Questo errore si verifica quando si crea uno snapshot con una posizione diversa dal volume proprietario dello snapshot.

* Causa:   
Valore non valido nella proprietà Location per lo snapshot.
* Soluzione:   
Impostare una stringa valida nella proprietà location.

***Il nome {resourceType} deve corrispondere al nome dell'identificatore di risorsa.***

Questo errore si verifica quando si crea una risorsa e si compila la proprietà Name con un valore diverso da quello della proprietà Name di `resourceId`.

* Causa:   
Valore non valido nella proprietà Name quando si crea una risorsa.
* Soluzione:   
Lasciare vuota la proprietà Name o consentirne l'utilizzo dello stesso valore della proprietà Name (tra l'ultima barra rovesciata "/" e il punto interrogativo "? `resourceId`") in.

***Il tipo di protocollo {value} non è noto***

Questo errore si verifica quando si crea un volume con un tipo di protocollo sconosciuto.  I valori validi sono "NFSv3" e "CIFS".

* Causa:   
Si sta tentando di impostare un valore non valido nella `protocolType` proprietà volume.
* Soluzione:   
Impostare una stringa valida in `protocolType`.
* Soluzione alternativa:   
Impostato `protocolType` su null.

***Non è possibile modificare i tipi di protocollo***

Questo errore si verifica quando si tenta di aggiornare o `ProtocolType` applicare una patch per un volume.  La modifica di ProtocolType non è un'operazione supportata.

* Causa:   
La `ProtocolType` proprietà viene impostata quando viene creato il volume.  Non può essere aggiornata.
* Soluzione:   
No.
* Soluzione alternativa:   
Creare un altro volume con i nuovi tipi di protocollo.

***La creazione della risorsa di tipo {resourceType} supera la quota delle risorse {quota} di tipo {resourceType} per {parentResourceType}. Il numero corrente di risorse è {currentCount}. eliminare alcune risorse di questo tipo prima di crearne uno nuovo.***

Questo errore si verifica quando si tenta di creare una risorsa (`NetAppAccount` `Volume`, `CapacityPool`, o `Snapshot`), ma la quota ha raggiunto il limite.

* Causa:   
Si sta provando a creare una risorsa, ma è stato raggiunto il limite di quota ( `NetAppAccounts` ad esempio per `CapacityPools` sottoscrizione `NetAppAccount`o per).
* Soluzione:   
Aumentare il limite di quota.
* Soluzione alternativa:   
Eliminare le risorse inutilizzate dello stesso tipo e crearle nuovamente.

***È stato ricevuto un valore per la proprietà di sola lettura ' {propertyName}'.***

Questo errore si verifica quando si definisce un valore per una proprietà che non può essere modificata. Ad esempio, non è possibile modificare l'ID del volume.

* Causa:   
Si sta provando a modificare un parametro (ad esempio, l'ID del volume) che non può essere modificato.
* Soluzione:   
Non modificare un valore per la proprietà.

***La risorsa {Resource} richiesta non è stata trovata.***

Questo errore si verifica quando si tenta di fare riferimento a una risorsa inesistente, ad esempio un volume o uno snapshot. È possibile che la risorsa sia stata eliminata o che sia presente un nome di risorsa non ortografico.

* Causa:   
Si sta provando a fare riferimento a una risorsa inesistente, ad esempio un volume o uno snapshot, che è già stata eliminata o che contiene un nome di risorsa con errori di ortografia.
* Soluzione:   
Controllare la richiesta di errori di ortografia per assicurarsi che venga fatto riferimento correttamente.
* Soluzione alternativa:   
Vedere la sezione della soluzione precedente.

***Il livello di servizio ' {volumeServiceLevel}' è più alto del padre ' {poolServiceLevel}'***

Questo errore si verifica quando si crea o si aggiorna un volume e il livello di servizio è stato impostato su un livello superiore rispetto al pool di capacità che lo contiene.

* Causa:   
Si sta provando a creare o aggiornare un volume con un livello di servizio classificato più alto rispetto al pool di capacità padre.
* Soluzione:   
Impostare il livello di servizio sullo stesso o su un rango inferiore rispetto al pool di capacità padre.
* Soluzione alternativa:   
Creare il volume in un altro pool di capacità con un livello di servizio corretto. In alternativa, eliminare tutti i volumi dal pool di capacità e impostare il livello di servizio per il pool di capacità su un rango superiore.

***Il nome del server SMB non può contenere più di 10 caratteri.***

Questo errore si verifica quando si crea o si aggiorna una configurazione Active Directory per un account.

* Causa:   
La lunghezza del nome del server SMB supera i 10 caratteri.
* Soluzione:   
Usare un nome di server più breve. La lunghezza massima è di 10 caratteri.
* Soluzione alternativa:   
No.  Vedere la soluzione sopra. 

***SubnetId non può essere modificato.***

Questo errore si verifica quando si tenta di modificare `subnetId` il dopo che il volume è stato creato.  `SubnetId`è necessario impostare quando il volume viene creato e non può essere modificato in un secondo momento.

* Causa:   
Si sta provando a modificare il `subnetId` dopo che il volume è stato creato, che non è un'operazione supportata. 
* Soluzione:   
Se la `subnetId` modifica di non è necessaria, provare a rimuovere il parametro dalla richiesta per ignorare il messaggio di errore.
* Soluzione alternativa:   
Se è necessario modificare `subnetId`, è possibile creare un nuovo volume con un nuovo `subnetId`, quindi eseguire la migrazione dei dati nel nuovo volume.

***Il formato di SubnetId non è valido.***

Questo errore si verifica quando si tenta di creare un nuovo volume, `subnetId` ma non è `resourceId` un oggetto per una subnet.

* Causa:   
Questo errore si verifica quando si tenta di creare un nuovo volume, ma `subnetId` non è un `resourceId` oggetto per una subnet. 
* Soluzione:   
Controllare il valore `subnetId` di per verificare che contenga un oggetto `resourceId` per la subnet utilizzata.
* Soluzione alternativa:   
No. Vedere la soluzione sopra. 

***La subnet deve avere una delega ' Microsoft. NetApp/volumes '.***

Questo errore si verifica quando si crea un volume e la subnet selezionata non è delegata `Microsoft.NetApp/volumes`a.

* Causa:   
Si è tentato di creare un volume ed è stata selezionata una subnet non delegata `Microsoft.NetApp/volumes`.
* Soluzione:   
Selezionare un'altra subnet delegata a `Microsoft.NetApp/volumes`.
* Soluzione alternativa:   
Aggiungere una delega corretta alla subnet.

***Il tipo di risorsa specificato è sconosciuto/non applicabile.***

Questo errore si verifica quando è stata richiesta una verifica del nome in un tipo di risorsa non applicabile o per un tipo di risorsa sconosciuto.

* Causa:   
È stato richiesto il controllo del nome per un tipo di risorsa sconosciuto o non supportato.
* Soluzione:   
Verificare che la risorsa per la quale si sta eseguendo la richiesta sia supportata o che non contenga errori di ortografia.
* Soluzione alternativa:   
Vedere la soluzione sopra.

***Errore di Azure NetApp Files sconosciuto.***

L'API di Azure si basa sull'API Azure NetApp Files per gestire i volumi. L'errore indica un problema di comunicazione con l'API.

* Causa:   
L'API sottostante sta inviando un errore sconosciuto. Questo errore è probabile che sia temporaneo.
* Soluzione:   
È probabile che il problema sia temporaneo e che la richiesta abbia esito positivo in un secondo momento. Se il problema persiste, creare un ticket di supporto per esaminare il problema.
* Soluzione alternativa:   
No. L'API sottostante è essenziale per la gestione dei volumi.

***Valore ricevuto per una proprietà sconosciuta ' {propertyName}'.***

Questo errore si verifica quando vengono fornite proprietà inesistenti per una risorsa, ad esempio il volume, lo snapshot o la destinazione di montaggio.

* Causa:   
La richiesta dispone di un set di proprietà che possono essere usate con ogni risorsa. Non è possibile includere nella richiesta alcuna proprietà inesistente.
* Soluzione:   
Verificare che tutti i nomi di proprietà siano stati digitati correttamente e che le proprietà siano disponibili per la sottoscrizione e la risorsa.
* Soluzione alternativa:   
Ridurre il numero di proprietà definite nella richiesta per eliminare la proprietà che causa l'errore.

***L'operazione di aggiornamento non è supportata per questo tipo di risorsa.***

È possibile aggiornare solo i volumi. Questo errore si verifica quando si tenta di eseguire un'operazione di aggiornamento non supportata, ad esempio l'aggiornamento di uno snapshot.

* Causa:   
La risorsa che si sta tentando di aggiornare non supporta l'operazione di aggiornamento. È possibile modificare le proprietà solo dei volumi.
* Soluzione:   
No. La risorsa che si sta tentando di aggiornare non supporta l'operazione di aggiornamento. Pertanto, non può essere modificato.
* Soluzione alternativa:   
Per un volume, creare una nuova risorsa con l'aggiornamento sul posto ed eseguire la migrazione dei dati.

***Non è possibile creare il volume in un pool con stato non riuscito.***

Questo errore si verifica quando si tenta di creare un volume in un pool che non si trova nello stato succeeded. Probabilmente, per qualche motivo l'operazione di creazione per il pool di capacità non è riuscita.

* Causa:   
Il pool di capacità contenente il nuovo volume è in stato di errore.
* Soluzione:   
Verificare che il pool di capacità sia stato creato correttamente e che non si trovi in uno stato di errore.
* Soluzione alternativa:   
Creare un nuovo pool di capacità e creare il volume nel nuovo pool.

***Il volume è in fase di creazione e non può essere eliminato al momento.***

Questo errore si verifica quando si tenta di eliminare un volume che è ancora in fase di creazione.

* Causa:   
È ancora in corso la creazione di un volume quando si tenta di eliminare il volume.
* Soluzione:   
Attendere il completamento della creazione del volume, quindi riprovare l'eliminazione.
* Soluzione alternativa:   
Vedere la soluzione sopra.

***Il volume viene eliminato e non può essere eliminato al momento.***

Questo errore si verifica quando si tenta di eliminare un volume quando è già in fase di eliminazione.

* Causa:   
È già in corso l'eliminazione di un volume quando si tenta di eliminare il volume.
* Soluzione:   
Attendere il completamento dell'operazione di eliminazione corrente.
* Soluzione alternativa:   
Vedere la soluzione sopra.

***Il volume è in fase di aggiornamento e non può essere eliminato al momento.***

Questo errore si verifica quando si tenta di eliminare un volume da aggiornare.

* Causa:   
È in corso l'aggiornamento di un volume quando si tenta di eliminare il volume.
* Soluzione:   
Attendere il completamento dell'operazione di aggiornamento, quindi riprovare l'eliminazione.
* Soluzione alternativa:   
Vedere la soluzione sopra.

***Il volume non è stato trovato o non è stato creato correttamente.***

Questo errore si verifica quando la creazione del volume ha avuto esito negativo e si sta tentando di modificare il volume o creare uno snapshot per il volume.

* Causa:   
Il volume non esiste o la creazione non è riuscita.
* Soluzione:   
Verificare che sia in corso la modifica del volume corretto e che la creazione del volume abbia avuto esito positivo. In alternativa, verificare che il volume per il quale si sta creando uno snapshot esista.
* Soluzione alternativa:   
No.  Vedere la soluzione sopra. 

***Il token di creazione specificato esiste già***

Questo errore si verifica quando si tenta di creare un volume e si specifica un token di creazione (percorso di esportazione) per il quale esiste già un volume.

* Causa:   
Il token di creazione (percorso di esportazione) specificato durante la creazione del volume è già associato a un altro volume. 
* Soluzione:   
Scegliere un token di creazione diverso.  In alternativa, eliminare l'altro volume.

***Il token di creazione specificato è riservato***

Questo errore si verifica quando si tenta di creare un volume e si specifica "default" o "None" come percorso del file (token di creazione).

* Causa:    
Si sta provando a creare un volume e si specifica "default" o "None" come percorso del file (token di creazione).
* Soluzione:   
Scegliere un percorso di file diverso (token di creazione).
 
***Active Directory le credenziali sono in uso***

Questo errore si verifica quando si tenta di eliminare la configurazione Active Directory da un account in cui è ancora presente almeno un volume SMB.  Il volume SMB è stato creato usando la configurazione di Active Directory che si sta tentando di eliminare.

* Causa:   
Si sta provando a eliminare la configurazione Active Directory da un account, ma almeno un volume SMB esiste ancora che è stato creato inizialmente con la configurazione Active Directory. 
* Soluzione:   
Per prima cosa, eliminare tutti i volumi SMB creati usando la configurazione Active Directory.  Quindi ripetere l'eliminazione della configurazione.

***Non è possibile modificare l'assegnazione di unità organizzativa se le credenziali sono in uso***

Questo errore si verifica quando si tenta di modificare l'unità organizzativa di una configurazione di Active Directory, ma è ancora presente almeno un volume SMB.  Il volume SMB è stato creato usando tale Active Directory configurazione che si sta provando a eliminare.

* Causa:   
Si sta provando a modificare l'unità organizzativa di una configurazione Active Directory.  Tuttavia esiste almeno un volume SMB creato inizialmente usando la configurazione Active Directory.
* Soluzione:   
 Per prima cosa, eliminare tutti i volumi SMB creati usando la configurazione Active Directory.  Quindi ripetere l'eliminazione della configurazione. 

***Aggiornamento Active Directory già in corso***

Questo errore si verifica quando si tenta di modificare una configurazione di Active Directory per cui è già in corso un'operazione di modifica.

* Causa:   
Si sta provando a modificare una configurazione di Active Directory, ma è già in corso un'altra operazione di modifica.
* Soluzione:   
Attendere il completamento dell'operazione di modifica attualmente in esecuzione.

***Elimina prima tutti i volumi usando le credenziali selezionate***

Questo errore si verifica quando si tenta di eliminare una configurazione di Active Directory, ma è ancora presente almeno un volume SMB.  Il volume SMB è stato creato usando la configurazione di Active Directory che si sta tentando di eliminare.

* Causa:   
Si sta provando a eliminare una configurazione di Active Directory, ma è ancora presente almeno un volume SMB creato inizialmente con la configurazione Active Directory.
* Soluzione:   
Per prima cosa, eliminare tutti i volumi SMB creati usando la configurazione Active Directory.  Quindi ripetere l'eliminazione della configurazione. 

***Non sono state trovate credenziali Active Directory nell'area***

Questo errore si verifica quando si tenta di creare un volume SMB, ma non è stata aggiunta alcuna configurazione Active Directory all'account per l'area.

* Causa:   
Si sta provando a creare un volume SMB, ma non è stata aggiunta alcuna configurazione Active Directory all'account. 
* Soluzione:   
Aggiungere una configurazione Active Directory all'account prima di creare un volume SMB.

***Non è stato possibile eseguire una query sul server DNS. Verificare che la configurazione di rete sia corretta e che i server DNS siano disponibili.***

Questo errore si verifica quando si tenta di creare un volume SMB, ma non è possibile raggiungere un server DNS (specificato nella configurazione di Active Directory). 

* Causa:   
Si sta provando a creare un volume SMB, ma non è possibile raggiungere un server DNS (specificato nella configurazione di Active Directory).
* Soluzione:   
Esaminare la configurazione di Active Directory e verificare che gli indirizzi IP del server DNS siano corretti e raggiungibili.
Se non si verificano problemi con gli indirizzi IP del server DNS, verificare che nessun firewall blocchi l'accesso.

***Troppi processi simultanei***

Questo errore si verifica quando si tenta di creare uno snapshot quando sono già in corso tre altre operazioni di creazione snapshot per la sottoscrizione.

* Causa:   
Si sta tentando di creare uno snapshot quando sono già in corso tre altre operazioni di creazione di snapshot per la sottoscrizione. 
* Soluzione:   
Per il completamento dei processi di creazione dello snapshot sono necessari alcuni secondi.  Attendere alcuni secondi e ripetere l'operazione di creazione dello snapshot.

***Non è possibile generare processi aggiuntivi. Attendere il completamento dei processi in corso e riprovare***

Questo errore può verificarsi quando si tenta di creare o eliminare un volume in circostanze specifiche.

* Causa:   
Si sta provando a creare o eliminare un volume in circostanze specifiche.
* Soluzione:   
Attendere un minuto e ripetere l'operazione.

***Il volume è già in fase di transizione tra gli Stati***

Questo errore può verificarsi quando si tenta di eliminare un volume attualmente in uno stato di transizione (ovvero, attualmente nello stato di creazione, aggiornamento o eliminazione).

* Causa:   
Si sta provando a eliminare un volume attualmente in uno stato di transizione.
* Soluzione:   
Attendere che l'operazione attualmente in esecuzione (transizione dello stato) sia terminata, quindi ripetere l'operazione.

***Non è stato possibile dividere il nuovo volume dallo snapshot del volume di origine***

 Questo errore può verificarsi quando si tenta di creare un volume da uno snapshot.  

* Causa:   
Si tenta di creare un volume da uno snapshot e il volume termina con uno stato di errore.
* Soluzione:   
Eliminare il volume, quindi riprovare a eseguire l'operazione di creazione del volume dallo snapshot.

 
## <a name="next-steps"></a>Passaggi successivi

* [Sviluppare per Azure NetApp Files con l'API REST](azure-netapp-files-develop-with-rest-api.md)
