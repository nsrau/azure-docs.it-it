<properties
   pageTitle="Test dell'offerta di modello di soluzione per il Marketplace | Microsoft Azure"
   description="Informazioni su come testare l'offerta di modello di soluzione per Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/04/2015"
   ms.author="hascipio; v-divte" />

# Test dell'offerta di modello di soluzione in gestione temporanea
Per gestione temporanea si intende la distribuzione dell'offerta in un ambiente "sandbox" privato, in cui è possibile testarne e verificarne le funzionalità prima di eseguirne il push in produzione. L'offerta viene visualizzata nella gestione temporanea esattamente come verrebbe mostrata a un cliente che l'ha distribuita. L'offerta deve essere certificata per il push nella gestione temporanea.

Quando l'offerta è in gestione temporanea, è possibile visualizzarla e testarla nel [portale di Azure](https://portal.azure.com/).

Per il push dell'offerta in gestione temporanea e l'esecuzione del test nel [portale di Azure](https://portal.azure.com/), seguire questa procedura:

1.	Passare al [portale di pubblicazione](https://publish.windowsazure.com)-> scheda **Modelli di soluzione** -> la propria offerta -> **Pubblica** -> **Passa a gestione temporanea**.
2.	Specificare l'elenco di sottoscrizioni di Azure che verrà usato per la visualizzazione in anteprima e il test dell'offerta.
3.	Accedere al portale di anteprima di Azure usando l'ID sottoscrizione usato nel passaggio precedente.
4.	Eseguire almeno un ciclo di test nel portale di anteprima di Azure sui punti riportati di seguito:
  -	Assicurarsi che il contenuto di marketing venga visualizzato correttamente in Azure Marketplace.
  -	Distribuzione end-to-end della topologia.
  -	Eseguire test delle prestazioni e test di stress.
  -	Assicurarsi che la topologia sia conforme alle procedure consigliate.

## Passaggi successivi
Se si è soddisfatti dei risultati, è possibile procedere alla fase di pubblicazione dell'offerta finale, ovvero il **passaggio 4**, [Distribuzione dell'offerta in Azure Marketplace](marketplace-publishing-push-to-production.md). In caso contrario, apportare le modifiche all'offerta e richiedere nuovamente la certificazione.

> [AZURE.NOTE]Per le modifiche ai contenuti marketing, la certificazione non è necessaria.

Per una guida a tutte le attività del server di pubblicazione, vedere [Come pubblicare un'offerta in Microsoft Azure Marketplace](marketplace-publishing-getting-started.md).

<!---HONumber=AcomDC_1210_2015-->