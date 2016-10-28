<properties
   pageTitle="Testare la propria offerta di macchine virtuali per il Marketplace | Microsoft Azure"
   description="Informazioni su come testare l'immagine della macchina virtuale per Azure Marketplace."
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
   ms.date="08/01/2016"
   ms.author="hascipio" />

# Testare la propria offerta di VM per Azure Marketplace nella gestione temporanea

Per gestione temporanea si intende la distribuzione della SKU in un ambiente "sandbox" privato, in cui è possibile testarne e convalidarne le funzionalità prima di distribuirla nel Marketplace. La SKU viene visualizzata nella gestione temporanea esattamente come verrebbe mostrata a un cliente che l'ha distribuita. L'immagine della macchina virtuale deve essere certificata per il push nella gestione temporanea.

## Passaggio 1: push dell'offerta nella gestione temporanea

1. Nella scheda **Pubblica** fare clic su **Passa a gestione temporanea**.

    ![disegno](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Se il portale di pubblicazione notifica eventuali errori, correggerli.
3.	Nella finestra di dialogo **Chi può accedere all'offerta di gestione temporanea?** immettere l'elenco delle sottoscrizioni di Azure che verrà utilizzato per l'anteprima dell’offerta nel [portale di anteprima di Azure](https://portal.azure.com).

    >[AZURE.NOTE] Per le macchine virtuali e i modelli di soluzioni, **non** aggiungere all'elenco degli elementi consentiti le sottoscrizioni di tipo CSP, DreamSpark o Azure in Open.


    > Per le macchine virtuali, quando si fa clic sul pulsante **PASSA A GESTIONE TEMPORANEA**, dietro le quinte vengono eseguiti i passaggi seguenti. Sarà quindi possibile visualizzare lo stato di avanzamento di ogni passaggio nella scheda PUBBLICA nel portale di pubblicazione. È necessario controllare questa pagina regolarmente (fino a quando lo stato diventa ANTEPRIMA) per eventuali informazioni di errore che richiedono correzioni.

    > - Inizialmente la richiesta di gestione temporanea viene inviata al team di certificazione che convalida il VHD. Se invece la richiesta include solo modifiche commerciali, il passaggio di certificazione viene ignorato.
    > - Una volta completata la certificazione, viene avviata la replica dell'offerta in tutti i data center di Azure. Il completamento della replica richiede in genere 24-48 ore ma potrebbe richiedere fino a una settimana a seconda della dimensione del VHD. Se invece la richiesta include solo modifiche commerciali, la replica è più veloce.
    > - Una volta completata la replica, l'offerta sarà disponibile nel [portale di Azure](http:/portal.azure.com). A quel punto lo stato diventerà ANTEPRIMA nel portale di pubblicazione. Un'offerta in gestione temporanea è visibile nel [portale di Azure](http:/portal.azure.com) solo usando gli ID di posta elettronica associati con la sottoscrizione con cui l'offerta è gestita temporaneamente.

4. Accedere al [portale di anteprima di Azure](https://portal.azure.com) usando una delle sottoscrizioni di Azure elencate nel passaggio precedente.
5. Individuare la propria offerta e convalidare i punti dell'immagine della macchina virtuale:
  - Assicurarsi che il contenuto di marketing venga visualizzato correttamente nel Marketplace.
  - Distribuzione end-to-end dell'immagine della VM.

      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [AZURE.IMPORTANT] L'offerta resterà nella fase di gestione temporanea fino a quando non si notifica a Microsoft tramite il portale di pubblicazione [scheda **Pubblica** > **clic sul pulsante "Richiedi approvazione per push in produzione"**] che si è pronti per passare alla fase di produzione. A questo punto, è opportuno chiedere a tutti i membri del team di verificare tutti gli aspetti preliminari all'elencazione dell'offerta.

> La piattaforma di gestione temporanea è progettata per il test dell'offerta in una modalità di anteprima da parte dell'autore. È fortemente sconsigliato l'uso di questa piattaforma a fini commerciali.

## Passaggi successivi
Ora che l'offerta è in "gestione temporanea" e ne sono stati testati le funzionalità e i contenuti di marketing, è possibile procedere alla fase di pubblicazione finale, ovvero il **Passaggio 4**: [Distribuzione dell'offerta nel Marketplace](marketplace-publishing-push-to-production.md).

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0803_2016-->