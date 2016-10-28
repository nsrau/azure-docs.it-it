<properties
   pageTitle="Distribuire l'offerta in Azure Marketplace | Microsoft Azure"
   description="Informazioni e istruzioni dettagliate per distribuire l'offerta (immagine di macchina virtuale, servizio per sviluppatori, servizio dati e così via) in Azure Marketplace."
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
   ms.date="08/02/2016"
   ms.author="hascipio" />

# Distribuire l'offerta in Azure Marketplace
Quando si è soddisfatti dell'offerta (ad esempio, è stato eseguito il testing degli scenari dei clienti, del contenuto marketing e così via) e si è pronti per il lancio, richiedere **Push in produzione** nella scheda **Pubblica**.

1. I quattro passaggi della pagina della PROCEDURA DETTAGLIATA nel portale di pubblicazione devono essere completati e apparire in verde. Per le offerte per macchina virtuale, assicurarsi di seguire le linee guida riportate di seguito.

    ![disegno][img-pubportal-walkthru-checked]

2. Selezionare la scheda **Pubblica** dall'elenco a sinistra.

    ![disegno][img-pubportal-menu-publish]

3. Fare clic sul pulsante **Richiedi approvazione per push in produzione**. Una volta effettuata la richiesta, il team di approvazione esegue una verifica finale e quindi l'offerta sarà disponibile in Azure Marketplace.

    ![disegno][img-pubportal-publish-pushproduction]

>[AZURE.IMPORTANT] Nel caso delle macchine virtuali, quando si fa clic sul pulsante Approvazione richiesta per il push in produzione, dietro le quinte vengono eseguiti i passaggi seguenti. Sarà quindi possibile visualizzare lo stato di avanzamento di ogni passaggio nella scheda PUBBLICA nel portale di pubblicazione. È necessario controllare questa pagina regolarmente (fino a quando lo stato diventa "Elencato") per eventuali informazioni di errore che richiedono correzioni.

> - Inizialmente la richiesta di produzione viene inviata al team di certificazione che convalida il VHD. Se invece si sta aggiornando un'offerta già elencata e la richiesta include solo modifiche commerciali, il passaggio di certificazione viene ignorato.
> - Nel passaggio successivo la richiesta arriva al team di convalida del contenuto che verifica il contenuto commerciale dell'offerta.
> - Se i passaggi precedenti hanno esito positivo, l'offerta viene approvata per la produzione. A questo punto lo stato diventa "Elencato" nel portale di pubblicazione. Questo stato "Elencato" non implica tuttavia che il processo sia stato completato. È necessario completare i passaggi seguenti prima che l'offerta sia disponibile in Azure Marketplace.
> - Quando l'offerta viene approvata per la produzione nel passaggio precedente, viene avviata la replica dell'offerta in tutti i data center di Azure. Il completamento della replica richiede in genere 24-48 ore ma potrebbe richiedere fino a una settimana a seconda della dimensione del VHD. Se invece si sta aggiornando un'offerta già elencata inserendo solo modifiche commerciali, la replica è più veloce.
> - Una volta completata la replica, l'offerta sarà disponibile in Azure Marketplace.

> È sempre possibile eliminare l'offerta mentre è in uno stato **Bozza** (ovvero quando non è **Push in gestione temporanea** o **Push in produzione**). Nella scheda **Cronologia** fare clic su **Elimina bozza** nella parte inferiore della pagina per eliminare una bozza.


## Elenco di controllo di produzione per tutte le offerte per macchina virtuale

- Assicurarsi di essere un partner Microsoft Azure Certified
- Nella scheda SKU, l'opzione "Hide this SKU from the Marketplace because it should always be bought via a solution template" (Nascondi questo SKU da Marketplace perché deve essere sempre acquistato usando un modello di soluzione) deve essere impostata su YES (SÌ) solo se lo SKU fa parte di un modello di soluzione. In tutti gli altri casi l'opzione deve sempre essere impostata su "NO".
- Ricordare: evitare di modificare l'impostazione di visibilità dello SKU quando lo SKU è elencato. Questa funzionalità non è supportata.
- Assicurarsi che i logo siano conformi alle linee guida per i logo di Azure Marketplace riportate di seguito.
- L'offerta e la descrizione dello SKU non devono essere uguali.
- Il titolo dello SKU e il riepilogo lungo dell'offerta non devono essere uguali.
- Il titolo dello SKU e il riepilogo dell'offerta non devono essere uguali.
- Per un'offerta con più SKU, i titoli degli SKU non devono essere identici.

**Linee guida per i logo in Azure Marketplace**

- La progettazione di Azure ha una tavolozza dei colori semplice. Nel logo usare un numero ridotto di colori primari e secondari.
- I colori del tema del portale di Azure sono il bianco e il nero. Pertanto evitare di usare questi colori per lo sfondo dei logo. Usare un colore che faccia risaltare i logo nel portale di Azure. Si consiglia di usare colori primari semplici. Se si usa lo sfondo trasparente, verificare che il logo e il testo non siano bianchi o neri.
- Non usare uno sfondo sfumato sul logo.
- Evitare di inserire testo, anche il nome del marchio o della società, sul logo.
- L'aspetto del logo deve essere semplice e senza sfumature.
- Il logo non può essere allungato.

**Indicazioni aggiuntive per il logo alto:**

- Il logo alto è facoltativo. L'autore può scegliere di non caricare un logo alto. **Tuttavia, una volta caricata, l'icona del logo alto non può essere eliminata dal portale di pubblicazione. A quel punto il partner deve seguire le istruzioni di Azure Marketplace per le icone del logo alto o l'offerta non verrà approvata per la produzione.**
- Il nome visualizzato dell'autore, il titolo dello SKU e il riepilogo lungo dell'offerta sono visualizzati con il testo bianco. Di conseguenza non è consigliabile usare un colore chiaro come sfondo dell'icona del logo alto. Lo sfondo nero, bianco o trasparente non è ammesso per le icone del logo alto.
- Il nome visualizzato dell'autore, il titolo dello SKU, il riepilogo lungo dell'offerta e il pulsante Crea vengono incorporati a livello di codice all'interno del logo alto quando l'offerta viene elencata. Pertanto non è necessario inserire testo mentre si progetta il logo alto. Lasciare spazio vuoto a destra in quanto il testo (nome visualizzato dell'autore, titolo dello SKU, riepilogo lungo dell'offerta) verrà incluso a livello di codice da Microsoft in questa posizione. Lo spazio vuoto per il testo deve essere 415 x 100 a destra e viene spostato con un offset di 370 px da sinistra.


## Elenco di controllo di produzione aggiuntivo per le offerte per macchina virtuale già elencate

- Controllare se è già presente un'offerta della propria azienda con lo stesso nome. Se è così, è necessario aggiungere una nuova versione dello SKU nell'offerta esistente anziché creare una nuova offerta duplicata.
- Il disco dati non deve cambiare tra due versioni dello stesso SKU.
- Azure Marketplace non supporta la modifica dei prezzi degli SKU elencati, in quanto ciò implicherebbe modifiche di fatturazione per i clienti esistenti. Assicurarsi di non modificare il prezzo degli SKU elencati nelle aree in cui uno SKU è disponibile. È possibile tuttavia aggiungere nuovi SKU o aggiungere nuove aree per uno SKU esistente.


## Passaggi successivi
Una volta che l'offerta è pubblicata, testare gli scenari dei clienti per convalidare il corretto funzionamento di tutti i contratti e le funzionalità nell'ambiente di produzione, come testato e convalidato nell'ambiente di gestione temporanea.

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]: media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]: media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]: media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png

<!---HONumber=AcomDC_0803_2016-->