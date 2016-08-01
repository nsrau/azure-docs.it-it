<properties
   pageTitle="Impostare come scaduti i dati in DocumentDB usando la durata (TTL) | Microsoft Azure"
   description="Con l'impostazione TTL, Microsoft Azure DocumentDB offre la possibilità di eliminare automaticamente i documenti dal sistema dopo un periodo di tempo determinato."
   services="documentdb"
   documentationCenter=""
   keywords="Durata (TTL)"
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2016"
   ms.author="kipandya"/>

# Impostare automaticamente come scaduti i dati nelle raccolte DocumentDB usando la durata (TTL)

Le applicazioni posso produrre e archiviare grandi quantità di dati. Alcuni di questi, come i dati eventi generati da computer, i registri e le informazioni sulle sessioni utente sono utili per un periodo di tempo limitato. Quando i dati eccedono le esigenze dell'applicazione è possibile eliminarli e ridurre le risorse di archiviazione necessarie per l'applicazione.

Con l'impostazione della durata (TTL), Microsoft Azure DocumentDB offre la possibilità di eliminare automaticamente i documenti dal database dopo un periodo di tempo determinato. La durata predefinita può essere impostata a livello di raccolta ed è possibile eseguirne l'override in base ai singoli documenti. Quando il valore di TTL è impostato, come impostazione predefinita di una raccolta o a livello di documento, DocumentDB rimuove automaticamente i documenti esistenti dopo un numero di secondi dall'ultima modifica pari a tale valore.

In DocumentDB la durata usa la differenza dall'ora dell'ultima modifica al documento. A tale scopo viene usato il campo \_ts che esiste in ogni documento. Il campo \_ts è un timestamp Epoch di tipo Unix che rappresenta la data e l'ora e viene aggiornato ogni volta che si modifica un documento.

## Comportamento di TTL

La funzionalità TTL è controllata dalle proprietà TTL a livello di raccolta e a livello di documento. I valori sono espressi in secondi e vengono trattati come differenziale dal \_ts dell'ultima modifica al documento.

 1.  DefaultTTL per la raccolta:
  * Se non è presente o è impostata su Null, i documenti non vengono eliminati automaticamente.
  
  * Se è presente e il valore è -1 = infinito, i documenti non scadono per impostazione predefinita.
  
  * Se è presente e il valore è un numero "n", i documenti scadono "n" secondi dopo l'ultima modifica.

 2.  TTL per i documenti:
  * La proprietà è applicabile solo se DefaultTTL è presente per la raccolta padre.
  
  * Esegue l'override del valore di DefaultTTL per la raccolta padre.

Non appena il documento scade, ovvero quando TTL + \_ts >= ora corrente del server, il documento viene contrassegnato come scaduto. Da questo momento sui documenti scaduti non è possibile eseguire alcuna operazione e vengono esclusi dai risultati delle query. I documenti vengono eliminati fisicamente dal sistema e vengono eliminati in background in base alle esigenze in un secondo momento. L'operazione non utilizza le [unità richiesta (UR)](documentdb-request-units.md) del budget della raccolta.

La logica precedente può essere illustrata in questa matrice:

| | DefaultTTL mancante o non impostata nella raccolta | DefaultTTL = -1 nella raccolta | DefaultTTL = n nella raccolta|
| ------------- |:-------------|:-------------|:-------------|
| TTL mancante nel documento| Non viene eseguito l'override a livello di documento, perché sia il documento che la raccolta sono privi di TTL. | I documenti in questa raccolta non scadono. | I documenti nella raccolta scadono al termine dell'intervallo n. |
| Durata TTL = -1 nel documento | Non viene eseguito l'override a livello di documento, perché la raccolta non definisce la proprietà DefaultTTL di cui è possibile eseguire l'override a livello di documento. L'impostazione TTL a livello di documento non viene interpretata dal sistema. | I documenti in questa raccolta non scadono. | Il documento con TTL =-1 in questa raccolta non scade. Tutti gli altri documenti scadono dopo l'intervallo di tempo n. |
| TTL = n nel documento | Non viene eseguito l'override a livello di documento. L'impostazione TTL a livello di documento non viene interpretata dal sistema. | Il documento con TTL = n scade dopo l'intervallo di tempo n, espresso in secondi. Gli altri documenti ereditano l'intervallo -1 e non scadono. | Il documento con TTL = n scade dopo l'intervallo di tempo n, espresso in secondi. Gli altri documenti ereditano l'intervallo n dalla raccolta. |


## Configurazione di TTL

Per impostazione predefinita, la durata è disabilitata in tutte le raccolte DocumentDB e in tutti i documenti.

## Abilitazione di TTL

Per abilitare la durata (TTL) in una raccolta o nei documenti all'interno di una raccolta, è necessario impostare la proprietà DefaultTTL della raccolta su -1 o su un numero positivo diverso da zero. Quando DefaultTTL = -1, per impostazione predefinita tutti i documenti nella raccolta hanno durata infinita. Il servizio DocumentDB deve tuttavia monitorare i documenti per cui viene eseguito l'override di questa impostazione predefinita nella raccolta.

## Configurazione del valore TTL predefinito in una raccolta

È possibile configurare una durata predefinita a livello di raccolta.

Per impostare la durata (TTL) in una raccolta, è necessario specificare un numero positivo diverso da zero che indica il periodo di tempo, espresso in secondi, per la scadenza di tutti i documenti nella raccolta dopo l'ultimo timestamp di modifica del documento (\_ts).

In alternativa, è possibile impostare il valore predefinito su -1, per fare in modo che tutti i documenti inseriti nella raccolta abbiano una durata illimitata per impostazione predefinita.

## Impostazione di TTL in un documento

Oltre al valore TTL predefinito in una raccolta è possibile impostare una durata (TTL) specifica a livello di documento. Questa impostazione esegue l'override dell'impostazione predefinita della raccolta.

Per impostare la durata (TTL) in un documento, è necessario specificare un numero positivo diverso da zero che indica il periodo di tempo, espresso in secondi, per la scadenza del documento dopo l'ultimo timestamp di modifica del documento (\_ts).

Per impostare l'offset di scadenza, specificare un valore nel campo TTL del documento.

Se il documento non ha il campo TTL, viene applicata l'impostazione predefinita della raccolta.

Se la funzionalità TTL è disabilitata a livello di raccolta, il campo TTL del documento viene ignorato finché non viene abilitata nuovamente la funzionalità TTL per la raccolta.


## Estensione di TTL in un documento esistente

Per reimpostare il valore TTL in un documento è possibile eseguire una qualsiasi operazione di scrittura nel documento. L'operazione imposta il \_ts sull'ora corrente e fa ripartire il conto alla rovescia per la scadenza del documento, data dal valore TTL.

Per modificare il valore TTL di un documento, è possibile aggiornare il campo come si fa con qualsiasi altro campo impostabile.


## Rimozione di TTL da un documento

Se è stato impostato un valore TTL per un documento ma si preferisce non farlo scadere, è possibile recuperare il documento, rimuovere il campo TTL e sostituire il documento nel server.

Quando il campo TTL viene rimosso dal documento, viene applicata l'impostazione predefinita della raccolta.

Per impedire la scadenza di un documento e fare in modo che non erediti dalla raccolta, è necessario impostare il valore TTL su -1.


## Disabilitazione di TTL

Per disabilitare del tutto la durata (TTL) in una raccolta e impedire al processo in background di cercare documenti scaduti, è necessario eliminare la proprietà DefaultTTL dalla raccolta.

Eliminare questa proprietà non equivale a impostarla su -1. Se la proprietà è impostata su -1, i nuovi documenti aggiunti alla raccolta non hanno scadenza ma è possibile eseguire l'override dell'impostazione per documenti specifici nella raccolta.

Se la proprietà viene rimossa dalla raccolta, nessuno dei documenti ha una scadenza, anche se sono presenti documenti con override esplicito di una impostazione predefinita precedente.


## Domande frequenti

**Quanto costa la durata (TTL)?**

Non sono previsti costi aggiuntivi per l'impostazione di una durata (TTL) in un documento.

**Quanto tempo è necessario per eliminare il documento dopo la scadenza?**

I documenti vengono contrassegnati come non disponibili subito dopo la scadenza, ovvero quando TTL + \_ts >= ora corrente del server. Da questo momento sui documenti scaduti non è possibile eseguire alcuna operazione e vengono esclusi dai risultati delle query. I documenti vengono eliminati fisicamente dal sistema in background. Questa operazione non utilizza le unità richiesta (UR) del budget della raccolta.

**Nell'eventuale periodo di tempo necessario per eliminare i documenti, vengono conteggiati nella quota e addebitati fino all'eliminazione?**

No, l'archiviazione dei documenti scaduti non viene addebitata e le dimensioni di tali documenti non vengono conteggiate nella quota di archiviazione di una raccolta.

**La durata (TTL) impostata per un documento influisce sugli addebiti delle unità richiesta?**

No, le operazioni eseguite sui documenti all'interno di DocumentDB non influiscono in alcun modo sugli addebiti delle unità richiesta.

**L'eliminazione di documenti influisce sulla velocità effettiva di cui è stato effettuato il provisioning nella raccolta?**

No, la gestione delle richieste relative alla raccolta è prioritaria rispetto all'esecuzione del processo in background di eliminazione dei documenti. L'aggiunta della durata (TTL) ai documenti non influisce sulla velocità effettiva.

**Quanto tempo rimane nella raccolta un documento scaduto prima dell'eliminazione?**

Immediatamente dopo la scadenza il documento non è più accessibile. Il tempo di permanenza esatto di un documento nella raccolta prima che venga effettivamente eliminato è non deterministico e dipende dal momento in cui il processo in background può eliminare il documento.

**I documenti scaduti vengono eliminati in tutti i nodi o è prevista la "coerenza finale"?**

Il documento smette di essere disponibile in tutti i nodi e in tutte le aree contemporaneamente.

**È previsto un costo in unità richiesta per le attività in background di monitoraggio durata (TTL)?**

No, non è previsto alcun costo in unità richiesta per tali attività.

**Con quale frequenza vengono controllate le scadenze TTL?**

Il controllo delle scadenze TTL non avviene come processo in background. Quando risponde a una richiesta, il servizio back-end esegue i controlli inline ed esclude gli eventuali documenti scaduti. L'eliminazione fisica del documento è l'unico processo che viene eseguito in modo asincrono in background. La frequenza del processo è determinata dalle unità richiesta disponibili nella raccolta.

**La funzionalità TTL si applica solo all'intero documento o è possibile impostare come scaduti singoli valori delle proprietà di un documento?**

La funzionalità TTL si applica all'intero documento. Per impostare come scaduta solo una parte di un documento, è consigliabile estrarla dal documento principale, inserirla in un documento "collegato" separato e usare la funzionalità TTL sul documento estratto.

**La funzionalità TTL ha requisiti di indicizzazione specifici?**

Sì. I [criteri di indicizzazione](documentdb-indexing-policies.md) della raccolta devono essere impostati su Lazy o Consistent. Il tentativo di impostare DefaultTTL in una raccolta la cui indicizzazione è impostata su None genera un errore, come anche il tentativo di disabilitare l'indicizzazione in una raccolta in cui la proprietà DefaultTTL è già impostata.


## Passaggi successivi

Per altre informazioni su Azure DocumentDB, vedere la pagina della [*documentazione*](https://azure.microsoft.com/documentation/services/documentdb/) del servizio.

<!---HONumber=AcomDC_0720_2016-->