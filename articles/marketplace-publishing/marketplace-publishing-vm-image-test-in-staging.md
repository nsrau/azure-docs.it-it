<properties
   pageTitle="Testare la propria offerta di macchine virtuali per il Marketplace | Microsoft Azure"
   description="Informazioni su come testare l'immagine della macchina virtuale per Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio" />

# Testare la propria offerta di macchine virtuali per il Marketplace nella gestione temporanea

Per gestione temporanea si intende la distribuzione della SKU in un ambiente "sandbox" privato, in cui è possibile testarne e convalidarne le funzionalità prima di distribuirla nel Marketplace. La SKU verrà visualizzata nella gestione temporanea esattamente come verrebbe mostrata a un cliente che l'ha distribuita. L'immagine della macchina virtuale deve essere certificata per il push nella gestione temporanea.

## Passaggio 1: push dell'offerta nella gestione temporanea

1. Fare clic su **Passa a gestione temporanea** nella scheda **Pubblica**.

  ![disegno](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Correggere gli eventuali errori o discrepanze che potrebbero essere segnalati dal portale di pubblicazione a questo punto.
3.	Specificare nella finestra popup l'elenco delle sottoscrizioni di Azure che verrà usato per visualizzare in anteprima l'offerta nel [portale di anteprima di Azure](https://portal.azure.com), come illustrato nella schermata precedente.
4. Accedere al [portale di anteprima di Azure](http://portal.azure.com) usando una delle sottoscrizioni di Azure elencate nel passaggio precedente.
5. Individuare la propria offerta e convalidare i punti dell'immagine della macchina virtuale:
  1. I contenuti di marketing vengono visualizzati correttamente nella raccolta

      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

  2. Distribuzione end-to-end dell'immagine della macchina virtuale

> [AZURE.IMPORTANT]L'offerta resterà nella fase di gestione temporanea fino a quando non si notifica a Microsoft tramite il portale di pubblicazione [**Pubblica** > **"Richiedi approvazione per push in produzione"**] che si è pronti per passare alla fase di produzione. A questo punto, è opportuno chiedere a tutti i membri del team di verificare tutti gli aspetti preliminari all'attivazione dell'offerta.

> La replica tra i data center può richiedere fino a 24-48 ore. Una volta completata la replica, l'offerta verrà elencata in [Azure Marketplace](http://azure.microsoft.com/marketplace).

## Passaggi successivi
Ora che l'offerta è in "gestione temporanea" e ne sono stati testati le funzionalità e i contenuti di marketing, è possibile procedere alla fase di pubblicazione dell'offerta finale e/o della SKU, ovvero il **passaggio 4**, [Distribuzione dell'offerta nel Marketplace](marketplace-publishing-push-to-production.md)

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Oct15_HO3-->