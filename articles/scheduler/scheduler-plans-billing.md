<properties 
 pageTitle="Piani e fatturazione nell'utilità di pianificazione di Azure" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="08/04/2015" 
 ms.author="krisragh"/>
 
# Piani e fatturazione nell'utilità di pianificazione di Azure

## Piani di raccolta di processi

Raccolte di processi sono entità fatturabili nell'utilità di pianificazione di Azure. Raccolte di processi contengono un numero di processi e sono disponibili in tre combinazioni – Free, Standard e Premium: descritti di seguito.

|**Piano di raccolta di processi**|**Numero massimo di processi per ogni raccolta di processi**|**Max ricorrenza**|**Raccolte di processi Max per ogni sottoscrizione**|**Limiti**|
|:---|:---|:---|:---|:---|
|**Free**|5 processi per la raccolta attività|Una volta ogni ora. Impossibile eseguire i processi più spesso di una volta all'ora|Una sottoscrizione è consentita la raccolta di processi gratuita fino a 1|Non è possibile utilizzare [oggetto autorizzazione in uscita HTTP](scheduler-outbound-authentication.md)
|**Standard**|50 processi per la raccolta attività|Una volta al minuto. Impossibile eseguire i processi più spesso di una volta all'ora|Una sottoscrizione è consentita per un massimo di 100 raccolte di processi standard|Accesso al set completo di funzionalità dell'utilità di pianificazione|
|**Premium**|50 processi per la raccolta attività|Una volta al minuto. Impossibile eseguire i processi più spesso di una volta all'ora|Una sottoscrizione è consentita per un massimo di 10,000 raccolte di processi standard. <a href="mailto:wapteams@microsoft.com">Contattaci</a> per ulteriori informazioni.|Accesso al set completo di funzionalità dell'utilità di pianificazione|

## Effettua il downgrade dei piani di raccolta di processi e gli aggiornamenti

È possibile aggiornare o effettuare il downgrade di un processo piano di raccolta in qualsiasi momento tra i piani Free, Standard e Premium. Tuttavia, quando il downgrade a una raccolta di processi gratuita, il declassamento potrebbe non riuscire per uno dei seguenti motivi:

- Esiste già una raccolta di processi gratuita nella sottoscrizione
- Un processo nella raccolta con una frequenza superiore a quello consentito per i processi in raccolte di processi gratuita. È la ricorrenza massima consentita in una raccolta di processi gratuita una volta ogni ora
- Sono presenti più di 5 processi nella raccolta di processi
- Un processo nella raccolta di processi con un'azione HTTP o HTTPS che utilizza un [oggetto autorizzazione in uscita HTTP](scheduler-outbound-authentication.md)

## Piani di fatturazione e Azure

Le sottoscrizioni non vengono addebitate gratuitamente raccolte di processi. Se si dispone di più di 100 raccolte di processi standard (10 unità fatturazione standard), è un ottimo affare affinché tutte le raccolte di processi nel piano premium.

Se si dispone di una raccolta di processi standard e premium di un processo insieme, sono fatturate un'unità di fatturazione standard _e_ un'unità di fatturazione premium. Gli effetti del servizio Utilità di pianificazione in base al numero di raccolte di processi attivi impostati su standard o premium; questo viene illustrato più dettagliatamente in due sezioni successive.

## Unità fatturabili standard

Un'unità fatturabile standard può includere fino a 10 raccolte di processi standard. Poiché una raccolta di processi standard può avere al massimo da 50 processi per ogni raccolta di processi, un'unità di fatturazione standard consente una sottoscrizione di processi fino a 500: un massimo di esecuzioni del processo quasi 22 milioni al mese.

Se si trovano tra 1 e 10 raccolte di processi standard, verrà addebitata per 1 unità standard di fatturazione. Se si trovano tra 11 e 20 raccolte di processi standard, verranno addebitate 2 unità standard di fatturazione. Se si trovano tra 21 e 30 raccolte di processi standard, verranno addebitate 3 unità standard di fatturazione.

## Unità fatturabili premium

Unità fatturabili premium può includere fino a 10.000 raccolte di processi premium. Poiché una raccolta di processi premium può avere al massimo da 50 processi per ogni raccolta di processi, un'unità di fatturazione premium consente una sottoscrizione di processi fino a 500.000 – fino a quasi 22 miliardi esecuzioni del processo ogni mese.

Se si trovano tra 1 e 10.000 raccolte processi premium, verrà addebitata 1 unità fatturazione premium. Se si trovano tra processi e raccolte 20.000 premium di intervallo 10.001, verrà addebitato 2 unità fatturazione premium e così via.

Pertanto, raccolte di processi premium hanno la stessa funzionalità come le raccolte di processi standard ma forniscono un'interruzione di prezzo, nel caso in cui l'applicazione richiede una notevole quantità di raccolte di processi.

## Stato attivo e fatturazione

Raccolte di processi sono sempre attive, a meno che l'intera sottoscrizione rientra in uno stato temporaneo disabilitato a causa di problemi di fatturazione. È l'unico modo per assicurarsi che non viene addebitata una raccolta di processi per impostarlo sul piano _Gratuito_ o di eliminare la raccolta di processi.

Anche se è possibile disabilitare tutti i processi all'interno di una raccolta di processi in un'unica operazione, non modifica lo stato di fatturazione della raccolta processi, la raccolta di processi verrà _ancora_ fatturato. Analogamente, raccolte di processi vuoto vengono considerati attivi e verranno fatturati.

## Prezzi

Per informazioni sui prezzi, vedere [Utilità di pianificazione prezzi](http://azure.microsoft.com/pricing/details/scheduler/).

## Vedere anche
 
 [Che cos'è l'Utilità di pianificazione?](scheduler-intro.md)
 
 [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione](scheduler-concepts-terms.md)

 [Introduzione all'uso dell'Utilità di pianificazione nel portale di gestione](scheduler-get-started-portal.md)

 [Come creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure](scheduler-advanced-complexity.md)

 [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione](https://msdn.microsoft.com/library/dn528946)

 [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione](scheduler-powershell-reference.md)
 
 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione](scheduler-high-availability-reliability.md)

 [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione](scheduler-limits-defaults-errors.md)

 [Autenticazione in uscita dell'Utilità di pianificazione](scheduler-outbound-authentication.md)
  

<!---HONumber=August15_HO7-->