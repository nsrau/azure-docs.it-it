<properties
 pageTitle="Che cos'è l'Utilità di pianificazione?"
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
 ms.topic="get-started-article" 
 ms.date="05/12/2015"
 ms.author="krisragh"/>

# Che cos'è l'Utilità di pianificazione?

L'Utilità di pianificazione di Azure consente di descrivere in modo dichiarativo le azioni da eseguire nel cloud e quindi pianifica ed esegue tali azioni automaticamente. A tale scopo, l'Utilità di pianificazione di Azure può usare [il portale di Azure](scheduler-get-started-portal.md), il codice, l'[API REST](https://msdn.microsoft.com/library/dn528946) o PowerShell.

L'Utilità di pianificazione di Azure gestisce, pianifica e richiama il lavoro pianificato, ma non ospita carichi di lavoro né esegue codice. _Richiama_ soltanto codice ospitato altrove, ad esempio in Azure, in locale o presso un altro provider. Il codice può essere richiamato tramite HTTP, HTTPS o una coda di archiviazione.

L'Utilità di pianificazione di Azure pianifica i processi, mantiene una cronologia dei risultati dell'esecuzione dei processi su cui è possibile eseguire interrogazioni e pianifica in modo deterministico e attendibile i carichi di lavoro da eseguire. Gli script pianificati di Servizi mobili di Azure, i processi Web di App Web di Azure e altre funzionalità di pianificazione di Azure sono basati su questa utilità. L'[API REST dell'Utilità di pianificazione](https://msdn.microsoft.com/library/dn528946) consente di gestire le comunicazioni per queste azioni. Sono quindi supportate [pianificazioni complesse e operazioni ricorrenti avanzate](scheduler-advanced-complexity.md).

L'Utilità di pianificazione di Azure può essere usata in diversi scenari. Ad esempio:

+ _Azioni ricorrenti delle applicazioni_: raccolta periodica di dati da Twitter e raccolta dei dati nel feed.
+ _Manutenzione giornaliera_: eliminazione giornaliera dei log, esecuzione di backup e altre attività di manutenzione. Ad esempio, un amministratore può scegliere di eseguire il backup del database alle ore 13.00 ogni giorno per i 9 mesi successivi.

Con l'Utilità di pianificazione è possibile creare, aggiornare, eliminare, visualizzare e gestire [raccolte di processi e singoli processi](scheduler-concepts-terms.md) a livello di codice, tramite script e nel portale.

## Vedere anche

 [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione](scheduler-concepts-terms.md)

 [Introduzione all'uso dell'Utilità di pianificazione nel portale di gestione](scheduler-get-started-portal.md)

 [Piani e fatturazione nell'Utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Come creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure](scheduler-advanced-complexity.md)

 [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione](https://msdn.microsoft.com/library/dn528946)

 [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione](scheduler-powershell-reference.md)

 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione](scheduler-high-availability-reliability.md)

 [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione](scheduler-limits-defaults-errors.md)

 [Autenticazione in uscita dell'Utilità di pianificazione](scheduler-outbound-authentication.md)
 

<!---HONumber=July15_HO2-->