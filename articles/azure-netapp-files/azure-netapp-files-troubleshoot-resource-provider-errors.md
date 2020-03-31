---
title: Risolvere gli errori del provider di risorse di file di Azure NetApp Documenti Microsoft
description: Vengono descritte le cause, le soluzioni e le soluzioni alternative per gli errori comuni del provider di risorse di File di rete di Azure.Describes causes, solutions, and workarounds for common Azure NetApp Files Resource Provider errors.
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
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: 62e67d4965444df0e731b4387808ed3b89e4673a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72597202"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Risolvere i problemi del provider di risorse di Azure NetApp Files 

Questo articolo descrive gli errori comuni del provider di risorse dei file di Azure, le relative cause, soluzioni e soluzioni alternative (se disponibili).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Errori comuni del provider di risorse dei file di Azure

***BareMetalTenantId non può essere modificato.***  

Questo errore si verifica quando si tenta `BaremetalTenantId` di aggiornare o applicare patch a un volume e la proprietà ha un valore modificato.

* Causa:   
Si sta tentando di aggiornare un volume e la proprietà ha un valore diverso dal valore archiviato in Azure.You are trying to update a volume and the `BaremetalTenantId` property has a different value from the value stored in Azure.
* Soluzione   
Non includere `BaremetalTenantId` nella richiesta di patch e aggiornamento (inserire). In alternativa, `BaremetalTenantId` assicurarsi che sia lo stesso nella richiesta.

***Impossibile modificare ServiceLevel.***  

Questo errore si verifica quando si tenta di aggiornare o applicare patch a un pool di capacità con un livello di servizio diverso quando il pool di capacità contiene già volumi.

* Causa:   
Si sta tentando di aggiornare un livello di servizio del pool di capacità quando il pool contiene volumi.
* Soluzione   
Eliminare tutti i volumi dal pool di capacità, quindi modificare il livello di servizio.
* Soluzione alternativa:   
Creare un altro pool di capacità, quindi creare nuovamente i volumi nel nuovo pool di capacità.

***Impossibile modificare PoolId***  

Questo errore si verifica quando si tenta di `PoolId` aggiornare o applicare patch a un pool di capacità con una proprietà modificata.

* Causa:   
Si sta tentando di `PoolId` aggiornare una proprietà del pool di capacità. La `PoolId` proprietà è di sola lettura e non può essere modificata.
* Soluzione   
Non includere `PoolId` nella richiesta di patch e aggiornamento (inserire).  In alternativa, `PoolId` assicurarsi che sia lo stesso nella richiesta.

***Impossibile modificare CreationToken.***

Questo errore si verifica quando si`CreationToken`tenta di modificare il percorso del file ( ) dopo la creazione del volume. Il percorso`CreationToken`del file ( ) deve essere impostato al momento della creazione del volume e non può essere modificato in un secondo momento.

* Causa:   
Si sta tentando di modificare`CreationToken`il percorso del file ( ) dopo la creazione del volume, che non è un'operazione supportata. 
* Soluzione   
Se la modifica del percorso del file non è necessaria, è consigliabile rimuovere il parametro dalla richiesta per ignorare il messaggio di errore.
* Soluzione alternativa:   
Se è necessario modificare il`CreationToken`percorso del file ( ), è possibile creare un nuovo volume con un nuovo percorso di file e quindi eseguire la migrazione dei dati nel nuovo volume.

***CreationToken deve avere una lunghezza di almeno 16 caratteri.***

Questo errore si verifica`CreationToken`quando il percorso del file ( ) non soddisfa il requisito di lunghezza. La lunghezza del percorso del file deve essere lunga almeno un carattere.

* Causa:   
Il percorso del file è vuoto.  Quando si crea un volume utilizzando l'API, è necessario un token di creazione. Se si usa il portale di Azure, il percorso del file viene generato automaticamente.
* Soluzione   
Immettere almeno un carattere`CreationToken`come percorso del file ( ).

***Il nome di dominio non può essere modificato.***

Questo errore si verifica quando si tenta di modificare il nome di dominio in Active Directory.

* Causa:   
Si sta tentando di aggiornare la proprietà del nome di dominio.
* Soluzione    
No. Non è possibile modificare il nome di dominio.
* Soluzione alternativa:   
Eliminare tutti i volumi utilizzando la configurazione di Active Directory. Quindi eliminare la configurazione di Active Directory e ricreare i volumi.

***Errore di valore duplicato per l'oggetto ExportPolicy.Rules[RuleIndex].***

Questo errore si verifica quando il criterio di esportazione non è definito con un indice univoco. Quando si definiscono i criteri di esportazione, tutte le regole dei criteri di esportazione devono avere un indice univoco compreso tra 1 e 5.

* Causa:   
Il criterio di esportazione definito non soddisfa il requisito per le regole dei criteri di esportazione. È necessario disporre di una regola dei criteri di esportazione almeno e cinque regole dei criteri di esportazione al massimo.
* Soluzione   
Assicurarsi che l'indice non sia già utilizzato e che sia compreso nell'intervallo da 1 a 5.
* Soluzione alternativa:   
Utilizzare un indice diverso per la regola che si sta tentando di impostare.

***Errore: azione, nometiporisorsa.***

Questo errore viene visualizzato quando un'altra gestione degli errori non è riuscita a gestire l'errore durante l'esecuzione di un'azione su una risorsa.   Include il testo 'Errore'. Il `{action}` valore può`getting`essere `creating` `updating`qualsiasi `deleting`di ( , , , o ).  Il `{resourceTypeName}` è `resourceTypeName` il (ad `capacityPool` `volume`esempio, `netAppAccount`, , e così via).

* Causa:   
Questo errore è un'eccezione non gestita in cui la causa non è nota.
* Soluzione   
Contattare il Centro di supporto di Azure per segnalare il motivo dettagliato nei log.
* Soluzione alternativa:   
No.

***Il nome del percorso del file può contenere solo lettere, numeri e trattini (""-"").***

Questo errore si verifica quando il percorso del file contiene caratteri non supportati, ad esempio un punto ("."), una virgola (","), un carattere di sottolineatura ("_" ) o un simbolo di dollaro ("-").

* Causa:   
Il percorso del file contiene caratteri non supportati, ad esempio un punto ("."), una virgola (","), un carattere di sottolineatura ("_") o un simbolo del dollaro (".").
* Soluzione   
Rimuovere i caratteri che non sono lettere alfabetiche, numeri o trattini ("-") dal percorso del file immesso.
* Soluzione alternativa:   
È possibile sostituire un trattino con un trattino o utilizzare lettere maiuscole anziché spazi per indicare l'inizio di nuove parole.  Ad esempio, utilizzare "NewVolume" anziché "nuovo volume".

***Impossibile modificare FileSystemId.***

Questo errore si verifica `FileSystemId`quando si tenta di modificare .  La `FileSystemdId` modifica non è supportata. 

* Causa:   
L'ID del file system viene impostato al momento della creazione del volume. `FileSystemId`non può essere modificato successivamente.
* Soluzione   
Non includere `FileSystemId` in una richiesta di patch e aggiornamento (inserire).  In alternativa, `FileSystemId` assicurarsi che sia lo stesso nella richiesta.

***ActiveDirectory con id: ''stringa'' non esiste.***

La `{string}` parte è il valore `ActiveDirectoryId` immesso nella proprietà per la connessione di Active Directory.

* Causa:   
Quando è stato creato un account con la configurazione `ActiveDirectoryId` di Active Directory, è stato immesso un valore per questo dovrebbe essere vuoto.
* Soluzione   
Non includere `ActiveDirectoryId` nella richiesta di creazione (inserisci).

***Versione api non valida.***

La versione dell'API non viene inviata o contiene un valore non valido.

* Causa:   
Il valore nel `api-version` parametro query contiene un valore non valido.
* Soluzione   
Usare il valore corretto della versione dell'API.  Il provider di risorse supporta molte versioni API. Il valore è nel formato aaaa-mm-gg.

***È stato ricevuto un valore non {1}valido ''value'' per .***

Questo messaggio indica un errore `RuleIndex`nei `AllowedClients` `UnixReadOnly`campi `UnixReadWrite` `Nfsv3`per `Nfsv4`, , , , e .

* Causa:   
La richiesta di convalida dell'input non è `RuleIndex` `AllowedClients`riuscita per almeno uno dei seguenti campi: `UnixReadOnly`, , , `UnixReadWrite`, 3 `Nfsv`e `Nfsv4`.
* Soluzione   
Assicurarsi di impostare tutti i parametri obbligatori e non in conflitto nella riga di comando. Ad esempio, non è `UnixReadOnly` `UnixReadWrite` possibile impostare contemporaneamente entrambi i parametri e .
* Soluzione alternativa:   
Vedere la soluzione precedente.

***L'intervallo {0} IP {1} {2} per vlan è già in uso***

Questo errore si verifica perché i record interni degli intervalli IP utilizzati presentano un conflitto con l'indirizzo IP appena assegnato.

* Causa:   
L'indirizzo IP assegnato per la creazione del volume è già registrato.
Il motivo potrebbe essere una creazione di volumi non riuscita in precedenza.
* Soluzione   
Contattare il Centro di supporto di Azure.Contact Azure Support Center.

***Valore mancante per ''property''.***

Questo errore indica che una proprietà obbligatoria non è presente nella richiesta. La stringa "property" contiene il nome della proprietà mancante.

* Causa:   
La richiesta di convalida dell'input non è riuscita per almeno una delle proprietà.
* Soluzione   
Assicurarsi di impostare tutte le proprietà obbligatorie e non in conflitto nella richiesta, in particolare, la proprietà dal messaggio di errore.

***MountTargets non può essere modificato.***

Questo errore si verifica quando un utente sta tentando di aggiornare o applicare una patch alla proprietà MountTargets del volume.

* Causa:   
Si sta tentando di `MountTargets` aggiornare la proprietà del volume. La modifica di questa proprietà non è supportata.
* Soluzione   
Non includere `MountTargets` in una richiesta di patch e aggiornamento (inserire).  In alternativa, assicurarsi che `MountTargets` nella richiesta sia lo stesso.

***Nome già in uso.***

Questo errore indica che il nome della risorsa è già in uso.

* Causa:   
Si sta tentando di creare una risorsa con un nome utilizzato per una risorsa esistente.
* Soluzione   
Utilizzare un nome univoco durante la creazione della risorsa.

***Percorso del file già in uso.***

Questo errore indica che il percorso del file per il volume è già in uso.

* Causa:   
Si sta tentando di creare un volume con un percorso di file uguale a quello di un volume esistente.
* Soluzione   
Utilizzare un percorso di file univoco durante la creazione del volume.

***Nome troppo lungo.***

Questo errore indica che il nome della risorsa non soddisfa il requisito di lunghezza massima.

* Causa:   
Il nome della risorsa è troppo lungo.
* Soluzione   
Usare un nome più breve per la risorsa.

***Percorso del file troppo lungo.***

Questo errore indica che il percorso del file per il volume non soddisfa il requisito di lunghezza massima.

* Causa:   
Il percorso del file di volume è troppo lungo.
* Soluzione   
Utilizzare un percorso di file più breve.

***Nome troppo corto.***

Questo errore indica che il nome della risorsa non soddisfa il requisito di lunghezza minima.

* Causa:   
Il nome della risorsa è troppo breve.
* Soluzione   
Usare un nome più lungo per la risorsa.

***Percorso del file troppo breve.***

Questo errore indica che il percorso del file del volume non soddisfa il requisito di lunghezza minima.

* Causa:   
Il percorso del file di volume è troppo breve.
* Soluzione   
Aumentare la lunghezza del percorso del file di volume.

***API dei file NetApp di Azure non raggiungibile.***

L'API di Azure si basa sull'API dei file NetApp di Azure per gestire i volumi. Questo errore indica un problema con la connessione API.

* Causa:   
L'API sottostante non risponde, generando un errore interno. È probabile che questo errore sia temporaneo.
* Soluzione   
Il problema è probabilmente temporaneo. La richiesta dovrebbe avere esito positivo dopo qualche tempo.
* Soluzione alternativa:   
No. L'API sottostante è essenziale per la gestione dei volumi.

***Nessun ID risultato{0}dell'operazione trovato per ' '.***

Questo errore indica che un errore interno impedisce il completamento dell'operazione.

* Causa:   
Si è verificato un errore interno e ne ha impedito il completamento dell'operazione.
* Soluzione   
È probabile che questo errore sia temporaneo. Attendere alcuni minuti e riprovare. Se il problema persiste, creare un ticket per fare in modo che il supporto tecnico esamini il problema.
* Soluzione alternativa:   
Attendere alcuni minuti e verificare se il problema persiste.

***Non è consentito combinare i tipi di protocollo CIFS e NFS***

Questo errore si verifica quando si tenta di creare un volume e sono presenti entrambi i tipi di protocollo CIFS (SMB) e NFS nelle proprietà del volume.

* Causa:   
Entrambi i tipi di protocollo CIFS (SMB) e NFS vengono utilizzati nelle proprietà del volume.
* Soluzione   
Rimuovere uno dei tipi di protocollo.
* Soluzione alternativa:   
Lasciare la proprietà del tipo di protocollo vuota o null.

***Numero di elementi: "value" per l'oggetto: ExportPolicy.Rules[RuleIndex] non è compreso nell'intervallo min-max.***

Questo errore si verifica quando le regole dei criteri di esportazione non soddisfano il requisito di intervallo minimo o massimo. Se si definisce il criterio di esportazione, è necessario che sia una regola dei criteri di esportazione almeno e cinque regole dei criteri di esportazione al massimo.

* Causa:   
Il criterio di esportazione definito non soddisfa l'intervallo richiesto.
* Soluzione   
Assicurarsi che l'indice non sia già utilizzato e che sia compreso nell'intervallo da 1 a 5.
* Soluzione alternativa:   
Non è obbligatorio utilizzare i criteri di esportazione sui volumi. È possibile omettere completamente il criterio di esportazione se non è necessario utilizzare le regole dei criteri di esportazione.

***È consentita una sola directory attiva***

Questo errore si verifica quando si tenta di creare una configurazione di Active Directory e ne esiste già una per la sottoscrizione nell'area. L'errore può verificarsi anche quando si tenta di creare più di una configurazione di Active Directory.

* Causa:   
Si sta tentando di creare (non aggiornare) una directory attiva, ma ne esiste già una.
* Soluzione   
Se la configurazione di Active Directory non è in uso, è innanzitutto possibile eliminare la configurazione esistente e quindi ritentare l'operazione di creazione.
* Soluzione alternativa:   
No. È consentito un solo Active Directory.

***Operazione ''operation'' non supportata.***

Questo errore indica che l'operazione non è disponibile per la sottoscrizione o la risorsa attiva.

* Causa:   
L'operazione non è disponibile per la sottoscrizione o la risorsa.
* Soluzione   
Assicurarsi che l'operazione sia stata immessa correttamente e che sia disponibile per la risorsa e la sottoscrizione in uso.

***OwnerId non può essere modificato***

Questo errore si verifica quando si tenta di modificare la proprietà OwnerId del volume. La modifica di OwnerId non è supportata. 

* Causa:   
La `OwnerId` proprietà viene impostata quando viene creato il volume. La proprietà non può essere modificata successivamente.
* Soluzione   
Non includere `OwnerId` in una richiesta di patch e aggiornamento (inserire). In alternativa, assicurarsi che `OwnerId` nella richiesta sia lo stesso.

***Pool padre non trovato***

Questo errore si verifica quando si tenta di creare un volume e il pool di capacità in cui si sta creando il volume non viene trovato.

* Causa:   
Il pool di capacità in cui viene creato il volume non viene trovato.
* Soluzione   
Molto probabilmente il pool non è stato completamente creato o è già stato eliminato al momento della creazione del volume.

***L'operazione di patch non è supportata per questo tipo di risorsa.***

Questo errore si verifica quando si tenta di modificare la destinazione di montaggio o snapshot.

* Causa:   
La destinazione di montaggio viene definita al momento della creazione e non può essere modificata successivamente.
Gli snapshot non contengono proprietà che possono essere modificate.
* Soluzione   
No. Tali risorse non dispongono di proprietà che possono essere modificate.

***Dimensioni del pool troppo piccole per le dimensioni totali del volume.***

Questo errore si verifica quando si aggiorna la dimensione del `usedBytes` pool di capacità e la dimensione è inferiore al valore totale di tutti i volumi in tale pool di capacità.  Questo errore può verificarsi anche quando si crea un nuovo volume o si ridimensiona un volume esistente e la nuova dimensione del volume supera lo spazio libero nel pool di capacità.

* Causa:   
Si sta tentando di aggiornare il pool di capacità a una dimensione inferiore rispetto a i byte usati in tutti i volumi del pool di capacità.  In alternativa, si sta tentando di creare un volume maggiore dello spazio libero nel pool di capacità.  In alternativa, si sta tentando di ridimensionare un volume e le nuove dimensioni superano lo spazio libero nel pool di capacità.
* Soluzione   
Impostare le dimensioni del pool di capacità su un valore maggiore o creare un volume inferiore per un volume.
* Soluzione alternativa:   
Rimuovere volumi sufficienti in modo che le dimensioni del pool di capacità possano essere aggiornate a queste dimensioni.

***La proprietà: La posizione per l'istantanea deve essere la stessa del volume***

Questo errore si verifica quando si crea uno snapshot con posizione diversa dal volume proprietario dello snapshot.

* Causa:   
Valore non valido nella proprietà Location per lo snapshot.
* Soluzione   
Impostare una stringa valida nella proprietà Location.

***Il nome del tipo di risorsa deve essere uguale al nome dell'identificatore di risorsa.***

Questo errore si verifica quando si crea una risorsa e si compila la `resourceId`proprietà name con un valore diverso dalla proprietà name di .

* Causa:   
Valore non valido nella proprietà name quando si crea una risorsa.
* Soluzione   
Lasciare vuota la proprietà name o consentire l'utilizzo dello stesso valore della proprietà name (tra l'ultima barra rovesciata "/" e il punto interrogativo "?") in `resourceId`.

***Tipo di protocollo "valore" non noto***

Questo errore si verifica quando si crea un volume con un tipo di protocollo sconosciuto.  I valori validi sono "NFSv3", "NFSv4" e "CIFS".

* Causa:   
Si sta tentando di impostare `protocolType` un valore non valido nella proprietà del volume.
* Soluzione   
Impostare una `protocolType`stringa valida in .
* Soluzione alternativa:   
Impostare `protocolType` come null.

***I tipi di protocollo non possono essere modificati***

Questo errore si verifica quando `ProtocolType` si tenta di aggiornare o applicare una patch per un volume.  La modifica di ProtocolType non è supportata.

* Causa:   
La `ProtocolType` proprietà viene impostata quando viene creato il volume.  Non può essere aggiornato.
* Soluzione   
No.
* Soluzione alternativa:   
Creare un altro volume con nuovi tipi di protocollo.

***La creazione della risorsa di tipo ,resourceType, supererebbe la quota delle risorse di tipo "tiporisorsa" per "tipoDirisorsa". Il numero di risorse corrente è "currentCount", eliminare alcune risorse di questo tipo prima di crearne una nuova.***

Questo errore si verifica quando si`NetAppAccount`tenta `CapacityPool` `Volume`di `Snapshot`creare una risorsa ( , , , o ), ma la quota ha raggiunto il limite.

* Causa:   
Si sta tentando di creare una risorsa, ma `NetAppAccounts` viene raggiunto il limite di quota (ad esempio, per sottoscrizione o `CapacityPools` per `NetAppAccount`).
* Soluzione   
Aumentare il limite di quota.
* Soluzione alternativa:   
Eliminare le risorse inutilizzate dello stesso tipo e crearle di nuovo.

***Ricevuto un valore per la proprietà di sola lettura ''propertyName''.***

Questo errore si verifica quando si definisce un valore per una proprietà che non può essere modificata. Ad esempio, non è possibile modificare l'ID del volume.

* Causa:   
Si sta tentando di modificare un parametro (ad esempio, l'ID del volume) che non può essere modificato.
* Soluzione   
Non modificare un valore per la proprietà.

***Impossibile trovare l'oggetto di risorse richiesto.***

Questo errore si verifica quando si tenta di fare riferimento a una risorsa inesistente, ad esempio un volume o uno snapshot. È possibile che la risorsa sia stata eliminata o che mandisponga di un nome di risorsa con errori di posta.

* Causa:   
Si sta tentando di fare riferimento a una risorsa inesistente (ad esempio, un volume o uno snapshot) che è già stata eliminata o ha un nome di risorsa con errori di ortografia.
* Soluzione   
Controllare la richiesta di errori di ortografia per assicurarsi che venga fatto riferimento correttamente.
* Soluzione alternativa:   
Vedere la sezione Soluzione precedente.

***Il livello di servizio ''volumeServiceLevel'' è più alto del padre ''poolServiceLevel''***

Questo errore si verifica quando si crea o si aggiorna un volume ed è stato impostato il livello di servizio su un livello superiore rispetto al pool di capacità che lo contiene.

* Causa:   
Si sta tentando di creare o aggiornare un volume con un livello di servizio classificato superiore rispetto al pool di capacità padre.
* Soluzione   
Impostare il livello di servizio sullo stesso rango o su un rango inferiore rispetto al pool di capacità padre.
* Soluzione alternativa:   
Creare il volume in un altro pool di capacità con un livello di servizio corretto. In alternativa, eliminare tutti i volumi dal pool di capacità e impostare il livello di servizio per il pool di capacità su un livello superiore.

***Il nome del server SMB non può essere più lungo di 10 caratteri.***

Questo errore si verifica quando si crea o si aggiorna una configurazione di Active Directory per un account.

* Causa:   
La lunghezza del nome del server SMB supera i 10 caratteri.
* Soluzione   
Utilizzare un nome di server più breve. La lunghezza massima è di 10 caratteri.
* Soluzione alternativa:   
No.  Vedere la soluzione precedente. 

***SubnetId non può essere modificato.***

Questo errore si verifica quando `subnetId` si tenta di modificare il dopo che il volume è stato creato.  `SubnetId`deve essere impostato quando il volume viene creato e non può essere modificato in un secondo momento.

* Causa:   
Si sta tentando `subnetId` di modificare il dopo che il volume è stato creato, che non è un'operazione supportata. 
* Soluzione   
Se la `subnetId` modifica di non è necessaria, è consigliabile rimuovere il parametro dalla richiesta per ignorare il messaggio di errore.
* Soluzione alternativa:   
Se è necessario `subnetId`modificare , è possibile creare `subnetId`un nuovo volume con un nuovo , quindi eseguire la migrazione dei dati nel nuovo volume.

***SubnetId è in formato non valido.***

Questo errore si verifica quando si tenta `subnetId` di `resourceId` creare un nuovo volume, ma il non è un per una subnet.

* Causa:   
Questo errore si verifica quando si tenta `subnetId` di creare `resourceId` un nuovo volume, ma il è un per una subnet. 
* Soluzione   
Controllare il valore `subnetId` di per il `resourceId` per assicurarsi che contenga un per la subnet utilizzata.
* Soluzione alternativa:   
No. Vedere la soluzione precedente. 

***La subnet deve avere una delega 'Microsoft.NetApp/volumes'.***

Questo errore si verifica quando si crea un volume `Microsoft.NetApp/volumes`e la subnet selezionata non viene delegata a .

* Causa:   
Si è tentato di creare il volume ed `Microsoft.NetApp/volumes`è stata selezionata una subnet non delegata a .
* Soluzione   
Selezionare un'altra subnet `Microsoft.NetApp/volumes`delegata a .
* Soluzione alternativa:   
Aggiungere una delega corretta alla subnet.

***Il tipo di risorsa specificato è sconosciuto/non applicabile.***

Questo errore si verifica quando è stato richiesto un controllo del nome su un tipo di risorsa non applicabile o per un tipo di risorsa sconosciuto.

* Causa:   
È stato richiesto il controllo del nome per un tipo di risorsa sconosciuto o non supportato.
* Soluzione   
Verificare che la risorsa per cui si sta eseguendo la richiesta sia supportata o non contenga errori di ortografia.
* Soluzione alternativa:   
Vedere la soluzione precedente.

***Errore sconosciuto dei file NetApp di Azure.***

L'API di Azure si basa sull'API dei file NetApp di Azure per gestire i volumi. L'errore indica un problema nella comunicazione con l'API.

* Causa:   
L'API sottostante sta inviando un errore sconosciuto. È probabile che questo errore sia temporaneo.
* Soluzione   
È probabile che il problema sia temporaneo e che la richiesta abbia esito positivo dopo un certo periodo di tempo. Se il problema persiste, creare un ticket di supporto per analizzare il problema.
* Soluzione alternativa:   
No. L'API sottostante è essenziale per la gestione dei volumi.

***Valore ricevuto per una proprietà sconosciuta ' .***

Questo errore si verifica quando vengono fornite proprietà inesistenti per una risorsa, ad esempio il volume, lo snapshot o la destinazione di montaggio.

* Causa:   
La richiesta dispone di un set di proprietà che possono essere utilizzate con ogni risorsa. Non è possibile includere proprietà inesistenti nella richiesta.
* Soluzione   
Assicurarsi che tutti i nomi delle proprietà siano stati digitati correttamente e che le proprietà siano disponibili per la sottoscrizione e la risorsa.
* Soluzione alternativa:   
Ridurre il numero di proprietà definite nella richiesta per eliminare la proprietà che causa l'errore.

***L'operazione di aggiornamento non è supportata per questo tipo di risorsa.***

Solo i volumi possono essere aggiornati. Questo errore si verifica quando si tenta di eseguire un'operazione di aggiornamento non supportata, ad esempio l'aggiornamento di uno snapshot.

* Causa:   
La risorsa che si sta tentando di aggiornare non supporta l'operazione di aggiornamento. Solo i volumi possono avere le loro proprietà modificate.
* Soluzione   
No. La risorsa che si sta tentando di aggiornare non supporta l'operazione di aggiornamento. Pertanto, non può essere modificato.
* Soluzione alternativa:   
Per un volume, creare una nuova risorsa con l'aggiornamento sul posto ed eseguire la migrazione dei dati.

***Impossibile creare il volume in un pool che non è in stato riuscito.***

Questo errore si verifica quando si tenta di creare un volume in un pool che non è nello stato di esito positivo. Molto probabilmente, l'operazione di creazione per il pool di capacità non è riuscita per qualche motivo.

* Causa:   
Il pool di capacità contenente il nuovo volume è in stato di errore.
* Soluzione   
Verificare che il pool di capacità sia stato creato correttamente e che non si sia in stato di errore.
* Soluzione alternativa:   
Creare un nuovo pool di capacità e creare il volume nel nuovo pool.

***Il volume è in fase di creazione e non può essere eliminato al momento.***

Questo errore si verifica quando si tenta di eliminare un volume che è ancora in fase di creazione.

* Causa:   
Un volume è ancora in fase di creazione quando si tenta di eliminare il volume.
* Soluzione   
Attendere il completamento della creazione del volume, quindi ripetere l'eliminazione.
* Soluzione alternativa:   
Vedere la soluzione precedente.

***Il volume è in fase di eliminazione e non può essere eliminato al momento.***

Questo errore si verifica quando si tenta di eliminare un volume quando è già in fase di eliminazione.

* Causa:   
Un volume è già in fase di eliminazione quando si tenta di eliminare il volume.
* Soluzione   
Attendere il completamento dell'operazione di eliminazione corrente.
* Soluzione alternativa:   
Vedere la soluzione precedente.

***Il volume è in fase di aggiornamento e non può essere eliminato al momento.***

Questo errore si verifica quando si tenta di eliminare un volume che viene aggiornato.

* Causa:   
Un volume viene aggiornato quando si tenta di eliminare il volume.
* Soluzione   
Attendere il completamento dell'operazione di aggiornamento, quindi ripetere l'eliminazione.
* Soluzione alternativa:   
Vedere la soluzione precedente.

***Volume non trovato o non stato creato correttamente.***

Questo errore si verifica quando la creazione del volume non è riuscita e si sta tentando di modificare il volume o creare uno snapshot per il volume.

* Causa:   
Il volume non esiste o la creazione non è riuscita.
* Soluzione   
Verificare che si stia modificando il volume corretto e che la creazione del volume sia stata eseguita correttamente. In alternativa, verificare che il volume per cui si sta creando uno snapshot esista.
* Soluzione alternativa:   
No.  Vedere la soluzione precedente. 

***Il token di creazione specificato esiste già***

Questo errore si verifica quando si tenta di creare un volume e si specifica un token di creazione (percorso di esportazione) per il quale esiste già un volume.

* Causa:   
Il token di creazione (percorso di esportazione) specificato durante la creazione del volume è già associato a un altro volume. 
* Soluzione   
Scegliere un token di creazione diverso.  In alternativa, eliminare l'altro volume.

***Il token di creazione specificato è riservato***

Questo errore si verifica quando si tenta di creare un volume e si specifica "default" o "none" come percorso del file (token di creazione).

* Causa:    
Si sta tentando di creare un volume e si specifica "default" o "none" come percorso del file (token di creazione).
* Soluzione   
Scegliere un percorso di file diverso (token di creazione).
 
***Le credenziali di Active Directory sono in uso***

Questo errore si verifica quando si tenta di eliminare la configurazione di Active Directory da un account in cui esiste ancora almeno un volume SMB.  Il volume SMB è stato creato utilizzando la configurazione di Active Directory che si sta tentando di eliminare.

* Causa:   
Si sta tentando di eliminare la configurazione di Active Directory da un account, ma esiste ancora almeno un volume SMB creato inizialmente utilizzando la configurazione di Active Directory. 
* Soluzione   
Eliminare innanzitutto tutti i volumi SMB creati utilizzando la configurazione di Active Directory.  Quindi ritentare l'eliminazione della configurazione.

***Impossibile modificare l'assegnazione dell'unità organizzativa se le credenziali sono in uso***

Questo errore si verifica quando si tenta di modificare l'unità organizzativa di una configurazione di Active Directory, ma almeno un volume SMB esiste ancora.  Il volume SMB è stato creato utilizzando tale configurazione di Active Directory che si sta tentando di eliminare.

* Causa:   
Si sta tentando di modificare l'unità organizzativa di una configurazione di Active Directory.  Ma almeno un volume SMB esiste ancora che è stato creato inizialmente utilizzando la configurazione di Active Directory.
* Soluzione   
 Eliminare innanzitutto tutti i volumi SMB creati utilizzando la configurazione di Active Directory.  Quindi ritentare l'eliminazione della configurazione. 

***Aggiornamento di Active Directory già in corso***

Questo errore si verifica quando si tenta di modificare una configurazione di Active Directory per cui è già in corso un'operazione di modifica.

* Causa:   
Si sta tentando di modificare una configurazione di Active Directory, ma è già in corso un'altra operazione di modifica.
* Soluzione   
Attendere il completamento dell'operazione di modifica attualmente in esecuzione.

***Eliminare prima tutti i volumi utilizzando le credenziali selezionate***

Questo errore si verifica quando si tenta di eliminare una configurazione di Active Directory, ma esiste ancora almeno un volume SMB.  Il volume SMB è stato creato utilizzando la configurazione di Active Directory che si sta tentando di eliminare.

* Causa:   
Si sta tentando di eliminare una configurazione di Active Directory, ma esiste ancora almeno un volume SMB creato inizialmente utilizzando la configurazione di Active Directory.
* Soluzione   
Eliminare innanzitutto tutti i volumi SMB creati utilizzando la configurazione di Active Directory.  Quindi ritentare l'eliminazione della configurazione. 

***Nessuna credenziale di Active Directory trovata nell'area***

Questo errore si verifica quando si tenta di creare un volume SMB, ma non è stata aggiunta alcuna configurazione di Active Directory all'account per l'area.

* Causa:   
Si sta tentando di creare un volume SMB, ma non è stata aggiunta alcuna configurazione di Active Directory all'account. 
* Soluzione   
Aggiungere una configurazione di Active Directory all'account prima di creare un volume SMB.

***Impossibile eseguire una query sul server DNS. Verificare che la configurazione di rete sia corretta e che i server DNS siano disponibili.***

Questo errore si verifica quando si tenta di creare un volume SMB, ma un server DNS (specificato nella configurazione di Active Directory) non è raggiungibile. 

* Causa:   
Si sta tentando di creare un volume SMB, ma un server DNS (specificato nella configurazione di Active Directory) non è raggiungibile.
* Soluzione   
Esaminare la configurazione di Active Directory e assicurarsi che gli indirizzi IP del server DNS siano corretti e raggiungibili.
Se non ci sono problemi con gli indirizzi IP del server DNS, verificare che nessun firewall stia bloccando l'accesso.

***Troppi processi simultanei***

Questo errore si verifica quando si tenta di creare uno snapshot quando sono già in corso altre tre operazioni di creazione di snapshot per la sottoscrizione.

* Causa:   
Si sta tentando di creare uno snapshot quando sono già in corso altre tre operazioni di creazione dello snapshot per la sottoscrizione. 
* Soluzione   
I processi di creazione di istantanee richiedono al massimo alcuni secondi.  Attendere alcuni secondi e ripetere l'operazione di creazione dello snapshot.

***Impossibile generare processi aggiuntivi. Attendere il completamento dei processi in corso e riprovare***

Questo errore può verificarsi quando si tenta di creare o eliminare un volume in circostanze specifiche.

* Causa:   
Si sta tentando di creare o eliminare un volume in circostanze specifiche.
* Soluzione   
Attendere circa un minuto e ritentare l'operazione.

***Il volume sta già passando da uno stato all'altro***

Questo errore può verificarsi quando si tenta di eliminare un volume che si trova attualmente in uno stato di transizione (ovvero, attualmente nello stato di creazione, aggiornamento o eliminazione).

* Causa:   
Si sta tentando di eliminare un volume che si trova attualmente in uno stato di transizione.
* Soluzione   
Attendere il completamento dell'operazione di transizione dello stato corrente e quindi ripetere l'operazione.

***Impossibile dividere il nuovo volume dallo snapshot del volume di origine***

 Questo errore può verificarsi quando si tenta di creare un volume da uno snapshot.  

* Causa:   
Si tenta di creare un volume da uno snapshot e il volume termina in uno stato di errore.
* Soluzione   
Eliminare il volume, quindi ripetere l'operazione di creazione del volume dallo snapshot.

 
## <a name="next-steps"></a>Passaggi successivi

* [Sviluppare per Azure NetApp Files con API REST](azure-netapp-files-develop-with-rest-api.md)
