---
title: Risolvere gli errori di Provider di risorse di Azure NetApp file | Microsoft Docs
description: Vengono descritte le cause, soluzioni e le soluzioni alternative per gli errori di Provider di risorse di Azure NetApp file comuni.
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
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: d4e06429aa1efec7c3301c7d0f0e7e17800fd520
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "63769437"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Risolvere i problemi del provider di risorse di Azure NetApp Files
Questo articolo descrive comuni Provider di risorse di Azure NetApp file gli errori, le cause, soluzioni e le soluzioni alternative. 

<a name="error_01"></a>***Azure Key Vault non è configurato.***   
Azure Key Vault archivia le credenziali necessarie per l'accesso all'API sottostante. Questo errore indica che Azure Key Vault non ha ricevuto le credenziali complete per l'accesso all'API sottostante.

* Causa  
Azure Key Vault non ha ricevuto le credenziali corrette o le credenziali sono incomplete.  

* Soluzione   
Il servizio file di Azure NetApp Usa Azure Key Vault. Azure Key Vault per eseguire l'autenticazione usando un token da Azure Active Directory. Pertanto, il proprietario dell'applicazione deve registrare l'applicazione in Azure Active Directory.

* Soluzione alternativa   
No.  Azure Key Vault deve essere configurata correttamente per usare file di Azure NetApp.  

<a name="error_02"></a>***La creazione di Token non può essere modificato.***   
Questo errore si verifica quando si prova a modificare il token di creazione dopo che è stato creato il volume.
Token di creazione deve essere impostato quando il volume viene creato e non può essere modificato in un secondo momento.

* Causa   
Si sta provando a modificare il token di creazione dopo che il volume è stato creato, che non è un'operazione supportata.

* Soluzione   
Dopo aver creato il Volume, prendere in considerazione la rimozione del parametro della richiesta per chiudere il messaggio di errore.

* Soluzione alternativa   
Se è necessario modificare il token di creazione, è possibile creare un nuovo volume con un nuovo token di creazione e quindi migrare i dati nel nuovo volume.


<a name="error_03"></a>***La creazione di Token deve essere almeno 16 caratteri.***   
Questo errore si verifica quando il token di creazione non soddisfa il requisito di lunghezza. La lunghezza del token di creazione deve essere almeno 16 caratteri.

* Causa   
Il token di creazione non soddisfa il requisito di lunghezza.  Quando si crea un volume usando l'API, è necessario un token di creazione. Se si usa il portale, il token può essere generato automaticamente.

* Soluzione   
Aumentare la lunghezza del token di creazione. Ad esempio, è possibile aggiungere un'altra parola all'inizio o alla fine del token di creazione.

* Soluzione alternativa   
La minima richiesta del token di creazione della lunghezza non può essere ignorata.  È possibile utilizzare un prefisso o suffisso per aumentare la lunghezza di token di creazione.


<a name="error_04"></a>***Errore durante l'eliminazione di un volume che non è stato trovato nel file di Azure NetApp.***   
Questo errore si è verificato perché il Registro di sistema interno delle risorse non è sincronizzato.

* Causa   
Il volume potrebbe rimanere visualizzato nel portale per qualche tempo dopo che è stato eliminato. Se si elimina il volume usando l'API, è possibile che il volume non è stato specificato correttamente. L'errore può essere causato anche dalla cache del browser obsoleto.

* Soluzione   
Cache del browser chiaro se si usa il portale. È inoltre disponibile una cache interna che viene aggiornata ogni 10 minuti.  È possibile provare a cancellare nuovamente della cache.  Se il problema persiste dopo 10 minuti, è possibile creare un ticket di supporto.

* Soluzione alternativa   
Usare un volume diverso nel frattempo e ignorare quello esistente.


<a name="error_05"></a>***Errore durante l'inserimento di un nuovo Volume trovato nel file di Azure NetApp.***   
Questo errore si verifica perché il Registro di sistema interno delle risorse non è sincronizzato.

* Causa   
Il volume potrebbe rimangono visualizzato nel portale per qualche tempo dopo che è stato eliminato. Se si elimina il volume usando l'API, è possibile che il volume non è stato specificato correttamente.

* Soluzione   
Se si usa il portale, il volume è già stato creato.  Il volume deve apparire automaticamente. Se il problema persiste, è possibile creare un ticket di supporto.

* Soluzione alternativa   
È possibile creare un volume con un nome diverso e un token di creazione diverso.


<a name="error_06"></a>***Il nome di percorso di file può contenere lettere, numeri e trattini (""-"") solo.***   
Questo errore si verifica quando il percorso del file contiene caratteri non supportati, ad esempio, un punto ("."), virgola (","), un carattere di sottolineatura ("\_"), o segno di dollaro ("$").

* Causa   
Il percorso del file contiene caratteri non supportati, ad esempio, un punto ("."), virgola (","), un carattere di sottolineatura ("\_"), o segno di dollaro ("$").

* Soluzione   
Rimuovere i caratteri che non sono caratteri alfabetici, numeri o trattini ("-") dal percorso del file è stato immesso.

* Soluzione alternativa   
È possibile sostituire un carattere di sottolineatura con un trattino o usare l'uso delle maiuscole anziché gli spazi per indicare l'inizio di nuove parole (ad esempio, utilizzando "NewVolume" anziché "nuovo volume").


<a name="error_07"></a>***ID del volume non può essere modificato.***   
Questo errore si verifica quando si prova a modificare l'ID del volume.  Modifica l'ID del volume non è un'operazione supportata.

* Causa   
L'ID del file system viene impostato quando viene creato il volume. L'ID del volume non può essere modificato successivamente.

* Soluzione   
No.

* Soluzione alternativa   
No.  L'ID del volume viene generato quando il volume viene creato e non può essere modificato successivamente.


<a name="error_08"></a>***Un valore non valido '{0}' è stato ricevuto per {1}.***   
Questo messaggio indica un errore nei campi per RuleIndex AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 e Nfsv4.

* Causa   
La richiesta di convalida dell'input non è riuscita per almeno uno dei seguenti campi: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3, and Nfsv4.

* Soluzione   
Verificare di aver impostato tutti i parametri obbligatori e non in conflitto nella riga di comando. Ad esempio, è possibile impostare parametri UnixReadOnly sia UnixReadWrite nello stesso momento.

* Soluzione alternativa   
Vedere la sezione soluzione.  


<a name="error_09"></a>***Valore mancante per '{0}'.***   
Questo errore indica che un attributo obbligatorio è mancano dalla richiesta per almeno uno dei parametri seguenti: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3, and Nfsv4.

* Causa   
La richiesta di convalida dell'input non è riuscita per almeno uno dei seguenti campi: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3, and Nfsv4.

* Soluzione   
Verificare di aver impostato tutti i parametri obbligatori e non in conflitto nella riga di comando. Ad esempio, è possibile impostare parametri UnixReadOnly sia UnixReadWrite nello stesso momento

* Soluzione alternativa   
Vedere la sezione soluzione.  


<a name="error_10"></a> ***{0} già in uso.***   
Questo errore indica che il nome della risorsa è già stato utilizzato.

* Causa   
Si sta tentando di creare un volume con un nome che equivale a un volume esistente.

* Soluzione   
Usare un nome univoco durante la creazione di un volume.

* Soluzione alternativa   
Se necessario, è possibile modificare il nome del volume esistente in modo che il nuovo volume possa usare il nome previsto.


<a name="error_11"></a> ***{0} troppo breve.***   
Questo errore indica che il nome del volume non soddisfa il requisito di lunghezza minima.

* Causa   
Il nome del volume è troppo breve.

* Soluzione   
Aumentare la lunghezza del nome del volume.  

* Soluzione alternativa   
È possibile aggiungere un prefisso comune o un suffisso al nome del volume.


<a name="error_12"></a>***API di file NetApp Azure irraggiungibile.***   
L'API di Azure si basa sull'API di file NetApp di Azure per gestire i volumi.  Questo errore indica un problema con la connessione API.

* Causa   
L'API sottostante non risponde, generando un errore interno. Questo errore è probabilmente temporanea.

* Soluzione   
Il problema è probabilmente temporanea.  La richiesta avrà esito positivo tra qualche minuto.

* Soluzione alternativa   
No. L'API sottostante è essenziale per la gestione dei volumi.  


<a name="error_13"></a>***Le credenziali non trovato per la sottoscrizione '{0}'.***   
Questo errore indica che le credenziali specificate non sono valide o non sono state impostate correttamente nella sottoscrizione.

* Causa   
Credenziali non valide o non impostata correttamente impediscono l'accesso al servizio per la gestione dei volumi.

* Soluzione   
Assicurarsi che le credenziali vengono impostate e immesso correttamente nella riga di comando.

* Soluzione alternativa   
No.  Impostazione delle credenziali in modo corretto è essenziale per l'uso di file di Azure NetApp.  


<a name="error_14"></a>***Nessun id risultato operazione trovata per '{0}'.***   
Questo errore indica un errore interno che impedisce il completamento dell'operazione.

* Causa   
Errore interno si è verificato e ha impedito il completamento dell'operazione.

* Soluzione   
Questo errore è probabilmente temporanea.  Attendere qualche minuto e riprovare. Se il problema persiste, creare un ticket di supporto tecnico per analizzare il problema.

* Soluzione alternativa   
Attendere alcuni minuti e verificare se il problema persiste.


<a name="error_15"></a>***Operazione '{0}' non è supportato.***   
Questo errore indica che il comando non è disponibile per la sottoscrizione attiva o la risorsa.

* Causa   
L'operazione non è disponibile per la sottoscrizione o risorsa.

* Soluzione   
Assicurarsi che il comando viene immesso in modo corretto e disponibile per la risorsa e una sottoscrizione che si sta utilizzando.

* Soluzione alternativa   
Vedere la sezione soluzione.  


<a name="error_16"></a>***L'operazione patch non è supportata per questo tipo di risorsa.***   
Questo errore si verifica quando si prova a modificare la destinazione di montaggio o snapshot.

* Causa   
La destinazione di montaggio viene definita quando viene creato e non può essere modificata successivamente.

* Soluzione   
No.  La destinazione di montaggio non può essere modificata dopo aver creato il volume.

* Soluzione alternativa   
No.


<a name="error_17"></a>***Ricevuto un valore per proprietà di sola lettura '{0}'.***   
Questo errore si verifica quando si definisce un valore per una proprietà che non può essere modificato. Ad esempio, non è possibile modificare l'ID del volume.

* Causa   
Si è tentato di modificare un parametro (ad esempio, l'ID del volume) che non può essere modificato.

* Soluzione   
No. Il parametro per l'ID del volume non può essere modificato.

* Soluzione alternativa   
L'ID del volume non dovrebbe richiedere variazioni.  Pertanto, una soluzione alternativa non è necessaria.

<a name="error_18"></a>***La richiesta {0} non è stato trovato.***   
Questo errore si verifica quando si tenta di fare riferimento a una risorsa non esistente, ad esempio, un volume o uno snapshot. La risorsa potrebbe essere stata eliminata o dispone di un nome di risorsa errore ortografico.

* Causa   
Si sta provando a fare riferimento a una risorsa inesistente (ad esempio, un volume o snapshot) che è già stata eliminata o dispone di un nome di risorsa sia stata digitata in modo non corretto.

* Soluzione   
Controllare la richiesta per gli errori di ortografia per assicurarsi che vi fanno riferimento in modo corretto.

* Soluzione alternativa   
Vedere la sezione soluzione.

<a name="error_19"></a>***Non è possibile ottenere le credenziali per la sottoscrizione '{0}'.***   
Questo errore indica che le credenziali specificate non sono valide o impostare in modo non corretto nella sottoscrizione.

* Causa   
Le credenziali che non sono validi o in modo non corretto set nella sottoscrizione di impedire l'accesso al servizio per la gestione dei volumi.

* Soluzione   
Assicurarsi che le credenziali vengono impostate e immesso correttamente nella riga di comando.

* Soluzione alternativa   
No.  Correttamente insieme di credenziali sono essenziali per l'uso di file di Azure NetApp.

<a name="error_20"></a>***Errore di file sconosciuti NetApp di Azure.***   
L'API di Azure si basa sull'API di file NetApp di Azure per gestire i volumi. L'errore indica un problema nella comunicazione per l'API.

* Causa   
L'API sottostante sta inviando un errore sconosciuto.  Questo errore è probabilmente temporanea.

* Soluzione   
Il problema è probabilmente temporanea e dovrebbe avere esito positivo della richiesta più tardi. Se il problema persiste, creare un ticket di supporto con il problema esaminato.

* Soluzione alternativa   
No.  L'API sottostante è essenziale per la gestione dei volumi.

<a name="error_21"></a>***Valore ricevuto per una proprietà sconosciuta '{0}'.***   
Questo errore si verifica quando vengono fornite proprietà inesistente per una risorsa, ad esempio il volume, snapshot o la destinazione di montaggio.

* Causa   
La richiesta include un set di proprietà che possono essere usate con ogni risorsa.  Nella richiesta non è possibile includere qualsiasi proprietà inesistenti.

* Soluzione   
Assicurarsi che tutti i nomi di proprietà siano stati digitati correttamente e le proprietà sono disponibili per le sottoscrizioni e risorse.

* Soluzione alternativa   
Ridurre il numero di proprietà definite nella richiesta per eliminare la proprietà che ha causato l'errore.


<a name="error_22"></a>***Operazione di aggiornamento non è supportata per questo tipo di risorsa.***   
È possibile aggiornare solo i volumi. Questo errore si verifica quando si prova a eseguire un'operazione di aggiornamento non supportato, ad esempio, l'aggiornamento di uno snapshot.

* Causa   
La risorsa che si sta tentando di aggiornare non supporta l'operazione di aggiornamento.  Solo i volumi possono avere le relative proprietà modificata.

* Soluzione   
No.  La risorsa che si sta tentando di aggiornare non supporta l'operazione di aggiornamento. Pertanto, non può essere modificato.

* Soluzione alternativa   
Per un volume, creare una nuova risorsa con l'aggiornamento posto e la migrazione dei dati.


<a name="error_23"></a>***Numero di elementi: {0} per l'oggetto: {1} è inclusa nell'intervallo min-max.***   
Questo errore si verifica quando le regole dei criteri di esportazione non soddisfano il requisito minimo o massimo dell'intervallo.  Se si definiscono i criteri di esportazione, deve avere un'esportazione regola di criteri minimo e cinque regole di criteri di esportazione al massimo.

* Causa   
I criteri di esportazione che è definito non soddisfano l'intervallo richiesto.  

* Soluzione   
Assicurarsi che l'indice non è già usato e che è compreso nell'intervallo da 1 a 5.

* Soluzione alternativa   
Non è obbligatorio usare l'esportazione dei criteri nei volumi. Pertanto, è possibile omettere i criteri di esportazione completamente se non è necessario avere regole dei criteri di esportazione.


<a name="error_24"></a>***Valore di errore per l'oggetto duplicata {0}.***   
Questo errore si verifica quando i criteri di esportazione non sono definito con un indice univoco.  Quando si definiscono criteri di esportazione, tutte le regole di criteri di esportazione devono avere un indice univoco compreso tra 1 e 5.

* Causa   
I criteri di esportazione specificata non soddisfano il requisito per le regole dei criteri di esportazione. È necessario disporre di un'esportazione regola di criteri minimo e cinque regole di criteri di esportazione al massimo.  

* Soluzione   
Assicurarsi che l'indice non è già usato e che sia nell'intervallo da 1 a 5.

* Soluzione alternativa   
Usare un altro indice per la regola che si sta provando a impostare.


