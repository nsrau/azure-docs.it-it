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
   ms.date="10/08/2015"
   ms.author="hascipio; v-divte" />

# Test dell'offerta di modello di soluzione in gestione temporanea
Per gestione temporanea si intende la distribuzione dell'offerta in un ambiente "sandbox" privato, in cui è possibile testarne e verificarne le funzionalità prima di eseguirne il push in produzione. L'offerta verrà visualizzata nella gestione temporanea esattamente come verrebbe mostrata a un cliente che l'ha distribuita. L'offerta **deve essere certificata per il push nella gestione temporanea**.

Una volta che l'offerta è in gestione temporanea, è possibile visualizzarla e testarla nel [portale di anteprima di Azure](https://ms.portal.azure.com/).

Eseguire i passaggi seguenti per il push dell'offerta in gestione temporanea e l'esecuzione del test nel [portale di anteprima di Azure](https://ms.portal.azure.com/).

1.	Passare al [portale di pubblicazione](https://publish.windowsazure.com)-> selezionare la scheda **Modelli di soluzione** -> la propria offerta -> **Pubblica** -> Fare clic su **“Passa a gestione temporanea"**
2.	Specificare l'elenco di sottoscrizioni di Azure che verrà usato per la visualizzazione in anteprima e il test dell'offerta.
3.	Accedere al portale di anteprima di Azure usando lo stesso ID di sottoscrizione con cui è stato eseguito il passaggio dell'offerta alla gestione temporanea nel passaggio precedente.
4.	Eseguire almeno un ciclo di test nel portale di anteprima di Azure sui punti riportati di seguito.
  -	I contenuti di marketing vengono visualizzati correttamente nella raccolta
  -	Distribuzione end-to-end della topologia
  -	Eseguire test delle prestazioni e test di stress
  -	Assicurarsi che la topologia sia conforme alle procedure consigliate.

## Passaggio successivo
Se si è soddisfatti dei risultati, è possibile procedere alla fase di pubblicazione dell'offerta finale, ovvero il **passaggio 4**, [Distribuzione dell'offerta nel Marketplace](marketplace-publishing-push-to-production.md). In caso contrario, apportare le modifiche all'offerta e richiedere nuovamente la certificazione.

> [AZURE.NOTE]Per le modifiche ai contenuti marketing, la certificazione non è necessaria.

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Nov15_HO3-->