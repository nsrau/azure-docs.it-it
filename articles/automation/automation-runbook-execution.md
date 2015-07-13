<properties
   pageTitle="Esecuzione di runbook in Automazione di Azure"
   description="Descrive i dettagli dell'elaborazione di un runbook in Automazione di Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/28/2015"
   ms.author="bwren" />

# Esecuzione di runbook in Automazione di Azure


Quando si avvia un runbook in Automazione di Azure, viene creato un processo. Un processo è una singola istanza di esecuzione di un runbook. Per eseguire ogni processo, viene assegnato un computer di lavoro di Automazione di Azure. I computer di lavoro sono condivisi da più account Azure, mentre i processi di account di automazione diversi sono isolati l'uno dall'altro. Non si dispone inoltre di alcun controllo sul computer di lavoro che gestirà la richiesta per il processo. In un singolo runbook possono venire eseguiti più processi contemporaneamente. Quando si visualizza l'elenco dei runbook nel portale di Azure, è visibile lo stato dell'ultimo processo avviato per ogni runbook. È possibile visualizzare l'elenco dei processi per il singolo runbook per tenere traccia dello stato di ognuno. Per una descrizione dei diversi stati dei processi, vedere [Stati dei processi](automation-viewing-the-status-of-a-runbook-job.md#job-statuses).

![Stati dei processi](./media/automation-runbook-execution/job-statuses.png)


I processi accederanno alle risorse di Azure effettuando una connessione alla sottoscrizione di Azure. Potranno accedere solo alle risorse del data center dell'utente se tali risorse sono accessibili dal cloud pubblico.

## Autorizzazioni

I runbook di Automazione di Azure in genere richiedono l'accesso alle risorse nella sottoscrizione di Azure. L'articolo relativo all'[autenticazione in Azure con Azure Active Directory](http://aka.ms/runbookauthor/authentication) illustra come configurare Azure Active Directory e le [credenziali]() in Automazione di Azure per l'autenticazione per le risorse di Azure. Questo argomento contiene anche informazioni sull'uso del [cmdlet Add-AzureAccount](http://aka.ms/runbookauthor/azurecmdlets/addazureaccount) per accedere alle risorse di Azure nei runbook creati. Per i relativi requisiti di sicurezza, vedere la documentazione per i singoli runbook importati.

## Condivisione equa

Per condividere le risorse tra tutti i runbook nel cloud, Automazione di Azure scaricherà temporaneamente qualsiasi processo in esecuzione da tre ore e quindi lo riavvierà dall'ultimo [checkpoint](http://aka.ms/runbookauthor/checkpoints). Durante questo periodo, per il processo verrà visualizzato lo stato Running, waiting for resources. Se il runbook non dispone di alcun checkpoint o non ha raggiunto il primo checkpoint prima di essere scaricato, verrà riavviato dall'inizio.

Se il runbook viene riavviato dallo stesso checkpoint o dall'inizio per tre volte consecutive, verrà terminato con lo stato Failed, waiting for resources. Questo avviene per impedire che i runbook vengano eseguiti all'infinito senza essere completati, in quanto non riusciranno a giungere al checkpoint successivo senza essere scaricati di nuovo. In tal caso, l'operazione avrà esito negativo e si riceverà un'eccezione che indica quanto segue.

L'esecuzione non potrà proseguire perché il processo è stato rimosso ripetutamente dallo stesso checkpoint. Verificare che il runbook non esegua operazioni di lunga durata senza rendere persistente il proprio stato.

Quando si crea un runbook, è consigliabile assicurarsi che il tempo necessario per eseguire qualsiasi attività tra due checkpoint non superi le tre ore. Potrebbe essere necessario aggiungere checkpoint al runbook per essere certi che non raggiunga tale limite. Potrebbe anche essere necessario suddividere le operazioni che richiedono una lunga esecuzione. Ad esempio, il runbook potrebbe eseguire una reindicizzazione su un database SQL di grandi dimensioni. Se questa singola operazione non viene completata entro il limite di condivisione equa, il processo verrà scaricato e riavviato dall'inizio. In tal caso, è opportuno suddividere l'operazione di reindicizzazione in più passaggi, ad esempio specificando la reindicizzazione di una tabella alla volta, e quindi inserire un checkpoint dopo ogni operazione in modo che il processo possa riprendere dopo l'ultima operazione da completare.

## Articoli correlati

- [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook)
- [Visualizzazione dello stato di un processo di runbook in Automazione di Azure](automation-viewing-the-status-of-a-runbook-job)
 

<!---HONumber=62-->