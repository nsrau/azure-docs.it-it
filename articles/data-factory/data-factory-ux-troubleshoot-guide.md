---
title: Risolvere i problemi di Azure Data Factory UX
description: Informazioni su come risolvere i problemi di Azure Data Factory UX.
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9f23155df6d9e63448b35974c331bf78c3e5f90c
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89426224"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Risolvere i problemi di Azure Data Factory UX

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i metodi comuni per la risoluzione dei problemi per Azure Data Factory UX.

## <a name="adf-ux-not-loading"></a>Non è in corso il caricamento del file ADF

> [!NOTE]
> Il Azure Data Factory UX supporta ufficialmente Microsoft Edge e Google Chrome. L'uso di altri Web browser può causare un comportamento imprevisto o non documentato.

### <a name="third-party-cookies-blocked"></a>Cookie di terze parti bloccati

L'esperienza utente di ADF usa i cookie del browser per rendere permanente la sessione utente e consentire esperienze di monitoraggio e sviluppo interattive. È possibile che il browser blocchi i cookie di terze parti perché si sta usando una sessione in incognito o è abilitato un blocco ad. Il blocco dei cookie di terze parti può causare problemi durante il caricamento del portale, ad esempio se viene reindirizzato a una pagina vuota, https://adf.azure.com/accesstoken.html o viene visualizzato un messaggio di avviso che informa che i cookie di terze parti sono bloccati. Per risolvere questo problema, abilitare le opzioni dei cookie di terze parti nel browser attenendosi alla procedura seguente:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Consenti tutti i cookie

1. Visitare **Chrome://Settings/cookies** nel browser.
1. Selezionare l'opzione **Consenti tutti i cookie** 

    ![Consenti tutti i cookie in Chrome](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Aggiornare l'esperienza utente di ADF e riprovare.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Consenti solo ad ADF UX di usare i cookie
Se non si desidera consentire tutti i cookie, facoltativamente è possibile consentire solo ad ADF UX:
1. Visitare **Chrome://Settings/cookies**.
1. Selezionare **Aggiungi** in **siti che possono usare sempre l'opzione cookie** 

    ![Aggiungere l'UX di ADF ai siti consentiti in Chrome](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Aggiungere il sito di **ADF.Azure.com** , selezionare l'opzione **tutti i cookie** e salvare. 

    ![Consenti tutti i cookie dal sito UX di ADF](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Aggiornare l'esperienza utente di ADF e riprovare.

### <a name="microsoft-edge"></a>Microsoft Edge

1. Visitare **Edge://settings/content/cookies** nel browser.
1. Assicurarsi che l'opzione **Consenti ai siti di salvare e leggere i dati dei cookie** sia abilitata e che l'opzione **Blocca cookie di terze parti** sia disabilitata 

    ![Consenti tutti i cookie in Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Aggiornare l'esperienza utente di ADF e riprovare.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Consenti solo ad ADF UX di usare i cookie

Se non si desidera consentire tutti i cookie, facoltativamente è possibile consentire solo ad ADF UX:

1. Visitare **Edge://settings/content/cookies**.
1. Nella sezione **Consenti** selezionare **Aggiungi** e Aggiungi sito di **ADF.Azure.com** . 

    ![Aggiungere l'esperienza utente di ADF ai siti consentiti in Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Aggiornare l'esperienza utente di ADF e riprovare.

## <a name="connection-failed-on-adf-ux"></a>Connessione non riuscita nell'esperienza utente di ADF

In alcuni casi è possibile che si verifichino errori di connessione nell'UX di ADF simile allo screenshot seguente dopo aver fatto clic su **Test connessione**, **Anteprima**e così via.

![Connessione non riuscita](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

In questo caso, è possibile provare prima di tutto la stessa operazione con la modalità InPrivate Browsing nel browser.

Se non funziona ancora, nel browser premere F12 per aprire **strumenti di sviluppo**. Passare alla scheda **rete** , selezionare **Disabilita cache**, ripetere l'operazione non riuscita e trovare la richiesta non riuscita (in rosso).

![Richiesta non riuscita](media/data-factory-ux-troubleshoot-guide/failed-request.png)

Trovare quindi il **nome host** (in questo caso, **DPNORTHEUROPE.svc.DATAFACTORY.Azure.com**) dall' **URL della richiesta** non riuscita.

Digitare il **nome host** direttamente nella barra degli indirizzi del browser. Se nel browser viene visualizzato 404, significa che il lato client è OK e il problema si trova sul lato del servizio ADF. Archiviare un ticket di supporto con l' **ID attività** dal messaggio di errore UX di ADF.

![Resource not found](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

In caso contrario, in genere viene visualizzato un errore simile al seguente nel browser, che in genere indica un problema lato client. Seguire le procedure di risoluzione dei problemi.

![Errore lato client](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

Aprire il **prompt dei comandi** e digitare **nslookup dpnortheurope.svc.DataFactory.Azure.com**. Una risposta normale dovrebbe avere un aspetto simile al seguente:

![Risposta comando 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

Se viene visualizzata una normale risposta DNS, contattare il supporto IT locale per verificare le impostazioni del firewall se la connessione HTTPS al nome host è bloccata o meno. Se non è stato possibile risolvere il problema, inviare un ticket di supporto con l' **ID attività** dal messaggio di errore UX di ADF.

Se si verificano altri elementi di questo genere, significa che si è verificato un errore nel server DNS durante la risoluzione del nome DNS. In genere, la modifica del provider di servizi Internet (ISP) o DNS (ad esempio, in Google DNS 8.8.8.8) potrebbe essere una possibile soluzione alternativa. Se il problema persiste, è possibile provare a usare **nslookup DataFactory.Azure.com** e **nslookup Azure.com** per vedere a quale livello la risoluzione DNS non è riuscita e inviare tutte le informazioni al supporto IT locale o all'ISP per la risoluzione dei problemi. Se si ritiene che il problema sia ancora sul lato Microsoft, archiviare un ticket di supporto con l' **ID attività** dal messaggio di errore UX di ADF.

![Risposta comando 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>Modificare il tipo di servizio collegato nei set di impostazioni

Il set di dati del formato di file può essere usato con tutti i connettori basati su file, ad esempio, è possibile configurare un set di dati parquet in un BLOB di Azure o in Azure Data Lake Storage Gen2. Nota ogni connettore supporta diversi set di impostazioni correlate all'archivio dati nell'attività e con un modello di app diverso. 

Nell'interfaccia utente di creazione di ADF, quando si usa un set di dati in formato di file in un'attività, incluse le attività di copia, ricerca, GetMetadata, eliminazione e nel set di dati, si desidera puntare a un servizio collegato di tipo diverso dall'oggetto corrente (ad esempio, passare dal file System al ADLS Gen2), verrà visualizzato il seguente messaggio di avviso. Per assicurarsi che si tratta di un commutatore pulito, previo consenso, le pipeline e le attività, che fanno riferimento a questo set di dati verranno modificate in modo da usare anche il nuovo tipo, e le impostazioni esistenti dell'archivio dati, che non sono compatibili con il nuovo tipo, verranno cancellate perché non sono più valide.

Per altre informazioni sulle impostazioni dell'archivio dati supportate per ogni connettore, è possibile passare all'articolo del connettore corrispondente. > le proprietà dell'attività di copia per visualizzare l'elenco dettagliato delle proprietà. Vedere [Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

![Messaggio di avviso](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi, usare le risorse seguenti:

* [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
* [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
* [Video di Azure](https://azure.microsoft.com/resources/videos/index/)
* [Pagina delle domande di Domande e risposte Microsoft](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
