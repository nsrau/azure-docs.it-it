<properties
 pageTitle="Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione"
 description="Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione"
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
 ms.date="08/16/2016"
 ms.author="krisragh"/>  


# Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione

## Livelli elevati di disponibilità dell'Utilità di pianificazione

In quanto servizio core della piattaforma Azure, l’Utilità di pianificazione di Azure ha un’elevata disponibilità e funzionalità di distribuzione del servizio con ridondanza geografica e replica geografica internazionale del processo.

### Distribuzione del servizio con ridondanza geografica

L’Utilità di pianificazione di Azure è disponibile tramite l'interfaccia utente in quasi ogni area geografica attualmente in Azure. L'elenco delle regioni in cui è disponibile l'Utilità di pianificazione di Azure è [disponibile qui](https://azure.microsoft.com/regions/#services). Se un data center in un'area servita non è disponibile, le funzionalità di failover dell'Utilità di pianificazione di Azure fanno sì che il servizio venga reso disponibile da un altro data center.

### Replica geografica internazionale dei processi

Non solo l'Utilità di pianificazione di Azure è disponibile per le richieste di gestione, ma il proprio processo è anche replicato geograficamente. Quando si verifica un'interruzione di servizio in un'area, l’Utilità di pianificazione di Azure va in failover e assicura che il processo venga eseguito da un altro data center nell'area geografica associata.

Ad esempio, se è stato creato un processo negli USA centro-meridionali, l’Utilità di pianificazione di Azure replica automaticamente tale processo negli USA centro-settentrionale. Quando si verifica un errore negli USA centro-meridionali, l’Utilità di pianificazione Azure garantisce che il processo venga eseguito dagli USA centro-settentrionali.

![][1]

Di conseguenza, l’Utilità di pianificazione Azure garantisce che i dati rimangano nella stessa zona geografica di riferimento in caso di un problema in Azure. Di conseguenza, non è necessario duplicare i processi per ottenere una disponibilità elevata – l’Utilità di pianificazione di Azure fornisce automaticamente disponibilità elevata ai processi.

## Affidabilità dell'Utilità di pianificazione di Azure

L’Utilità di pianificazione di Azure garantisce la propria disponibilità elevata e adotta un approccio diverso per i processi creati dall'utente. Ad esempio, il processo può richiamare un endpoint HTTP che non è disponibile. L’Utilità di pianificazione di Azure comunque tenta di eseguire correttamente il processo, mediante l'assegnazione di opzioni alternative per la gestione degli errore. L’Utilità di pianificazione di Azure fa ciò in due modi:

### Criteri di tentativi ripetuti configurabili tramite "retryPolicy"

L’Utilità di pianificazione di Azure consente di configurare un criterio per i tentativi di ripetizione. Per impostazione predefinita, se un processo fallisce, l’Utilità di pianificazione tenterà di eseguire nuovamente il processo quattro volte, a intervalli di 30 secondi. È possibile ri-configurare questo criterio di ripetizione per essere più aggressivo (ad esempio, dieci volte a intervalli di 30 secondi) o più allentato (ad esempio, due volte, a distanza di un giorno l’una dall’altra).

Come esempio di quando ciò può essere utile, è possibile creare un processo che viene eseguito una volta alla settimana e richiama un endpoint HTTP. Se l'endpoint HTTP è inattivo per alcune ore durante l’esecuzione del processo, si potrebbe non voler attendere un’ulteriore settimana per eseguire di nuovo il processo, dato che il criterio di ripetizione predefinito avrà esito negativo. In tali casi, si possono riconfigurare i criteri standard per i tentativi ripetuti per farli ripetere ogni tre ore (per esempio) invece che ogni 30 secondi.

Per informazioni su come configurare un criterio di ripetizione, fare riferimento a [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### Configurabilità endpoint alternativo tramite "errorAction"

Se l'endpoint di destinazione per il proprio processo pianificato di Azure rimane non raggiungibile, l’Utilità di pianificazione Azure ricorre all'endpoint alternativo indicato nella gestione degli errori alternativo dopo aver seguito i criteri di ripetizione. Se è configurato un endpoint alternativo per la gestione degli errori, l’Utilità di pianificazione Azure lo richiama. Con un endpoint alternativo i propri processi sono a disponibilità elevata in caso di errore.

Ad esempio, nel diagramma riportato di seguito, l’Utilità di pianificazione Azure segue il criterio di ripetizione per connettersi a un servizio web di New York. Dopo che i tentativi hanno esito negativo, verifica se esiste un'alternativa. A seguire, comincia a effettuare richieste all’alternativa con lo stesso criterio di ripetizione.

![][2]

Si noti che si applica lo stesso criterio di ripetizione dell'azione originale e all'azione alternativa in caso di errore. È inoltre possibile avere un’alternativa con un tipo di azione diverso da quella principale. Ad esempio, mentre l'azione principale può consistere nella chiamata di un endpoint HTTP, l'azione in caso di errore può essere invece un'azione della coda di archiviazione, della coda del bus di servizio o di un argomento del bus di servizio che esegue la registrazione degli errori.

Per informazioni su come configurare un endpoint alternativo, fare riferimento a [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## Vedere anche

 [Che cos'è l'Utilità di pianificazione?](scheduler-intro.md)

 [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)

 [Introduzione all'uso dell'Utilità di pianificazione di Azure nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione nell'utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Come creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure](scheduler-advanced-complexity.md)

 [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](https://msdn.microsoft.com/library/mt629143)

 [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione di Azure](scheduler-powershell-reference.md)

 [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione di Azure](scheduler-limits-defaults-errors.md)

 [Autenticazione in uscita dell'Utilità di pianificazione di Azure](scheduler-outbound-authentication.md)


[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png

<!---HONumber=AcomDC_0824_2016-->