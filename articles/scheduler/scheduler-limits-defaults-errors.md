<properties
 pageTitle="Limiti e impostazioni predefinite dell'Utilità di pianificazione"
 description="Limiti e impostazioni predefinite dell'Utilità di pianificazione"
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
 ms.date="08/18/2016"
 ms.author="krisragh"/>

# Limiti e impostazioni predefinite dell'Utilità di pianificazione

## Quote, limiti, impostazioni predefinite e limiti dell'utilità di pianificazione

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## L'intestazione x-ms-request-id

Ogni richiesta effettuata per il servizio dell’Utilità di pianificazione restituisce un'intestazione di risposta denominata **x-ms-request-id**. Questa intestazione contiene un valore opaco che identifica in modo univoco la richiesta.

Se una richiesta fallisce sistematicamente e si è verificato che la richiesta è formulata in modo appropriato, si può usare questo valore per riportare l'errore a Microsoft. Nel report includere il valore di x-ms-request-id, l'ora approssimativa in cui è stata eseguita la richiesta, l'identificativo della sottoscrizione, la raccolta processi e/o il processo e il tipo di operazione tentata con la richiesta.

## Vedere anche


 [Che cos'è l'Utilità di pianificazione?](scheduler-intro.md)

 [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)

 [Introduzione all'uso dell'Utilità di pianificazione di Azure nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione nell'utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](https://msdn.microsoft.com/library/mt629143)

 [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione di Azure](scheduler-powershell-reference.md)

 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione di Azure](scheduler-high-availability-reliability.md)

 [Autenticazione in uscita dell'Utilità di pianificazione di Azure](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0824_2016-->