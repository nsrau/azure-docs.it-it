---
title: Azure Data Box per risolvere i problemi usando l'interfaccia REST | Microsoft Docs
description: Viene descritto come risolvere gli errori riscontrati in Azure Data Box quando copia dei dati è tramite l'interfaccia REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: ee2820d78e95924e09a0219753f87d6910c0e736
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014127"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Risolvere i problemi correlati all'archiviazione Blob di Azure Data finestra

Queste informazioni i dettagli di articolo su come risolvere i problemi riscontrabili quando si usa l'archiviazione Blob di dati finestra tramite l'interfaccia REST di Data Box per copiare i dati. Questi problemi nell'area quando si Usa archiviazione Blob di dati finestra con altre applicazioni o librerie client, ad esempio la libreria Azure Storage Explorer, AzCopy o archiviazione di Azure per Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Errori riscontrati in Azure Storage Explorer

Questa sezione vengono illustrati alcuni dei problemi riscontrati quando si usa Azure Storage Explorer con archiviazione Blob di dati finestra.

|Messaggio di errore  |Azione consigliata |
|---------|---------|
|Impossibile recuperare le risorse figlio. Il valore di una delle intestazioni HTTP non è nel formato corretto.|Dal **Edit** dal menu **Target Azure Stack API**. <br>Riavviare Azure Storage Explorer.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Verificare che il nome dell'endpoint `<accountname>.blob.<serialnumber>.microsoftdatabox.com` viene aggiunto al file degli host nel percorso: <li>`C:\Windows\System32\drivers\etc\hosts` in Windows, o </li><li> `/etc/hosts` in Linux.</li>|
|Impossibile recuperare le risorse figlio. <br>Dettagli: certificato autofirmato |Importare il certificato SSL per il dispositivo in Azure Storage Explorer: <li>Scaricare il certificato dal portale di Azure. Per altre informazioni, visitare [scaricare il certificato](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Dal **Edit** dal menu **i certificati SSL** e quindi selezionare **Importa certificati**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Errori visualizzati in AzCopy per Windows

Questa sezione vengono illustrati alcuni dei problemi riscontrati quando si usa AzCopy per Windows con l'archiviazione Blob di dati finestra.

|Messaggio di errore  |Azione consigliata |
|---------|---------|
|Comando AzCopy sembra bloccarsi per un minuto prima di visualizzare questo errore: <br>Non è riuscito a enumerare directory https://... Non è stato possibile risolvere il nome remoto `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Verificare che il nome dell'endpoint `<accountname>.blob.<serialnumber>.microsoftdatabox.com` viene aggiunto al file degli host in: `C:\Windows\System32\drivers\etc\hosts`.|
|Comando AzCopy sembra bloccarsi per un minuto prima di visualizzare questo errore: <br>Percorso origine di analisi degli errori. La connessione sottostante chiusa: Impossibile stabilire una relazione di trust per il canale sicuro SSL/TLS.|Importare il certificato SSL per il dispositivo nell'archivio certificati del sistema. Per altre informazioni, visitare [scaricare il certificato](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Errori visualizzati in AzCopy per Linux

Questa sezione vengono illustrati alcuni dei problemi riscontrati quando si usa AzCopy per Linux con l'archiviazione Blob di dati finestra.

|Messaggio di errore  |Azione consigliata |
|---------|---------|
|Comando AzCopy sembra bloccarsi per 20 minuti prima di visualizzare questo errore: <br>Errore di analisi del percorso di origine `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`. Dispositivo o indirizzo non presente|Verificare che il nome dell'endpoint `<accountname>.blob.<serialnumber>.microsoftdatabox.com` viene aggiunto al file degli host in: `/etc/hosts`.|
|Comando AzCopy sembra bloccarsi per 20 minuti prima di visualizzare questo errore: <br>Percorso origine di analisi degli errori... Non è stato possibile stabilire la connessione SSL.|Importare il certificato SSL per il dispositivo nell'archivio certificati del sistema. Per altre informazioni, visitare [scaricare il certificato](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Errori visualizzati nella libreria di archiviazione di Azure per Python

Questa sezione illustra alcuni dei principali problemi riscontrati durante la distribuzione di Data Box Disk quando si usa un client Linux per la copia dei dati.

|Messaggio di errore  |Azione consigliata |
|---------|---------|
|Il valore di una delle intestazioni HTTP non è nel formato corretto. |La versione installata della libreria di archiviazione di Microsoft Azure per Python non è supportata da Data Box. Vedere i requisiti di archiviazione Blob di Azure Data finestra per le versioni supportate.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] …|Prima di eseguire Python, impostare la variabile di ambiente REQUESTS_CA_BUNDLE al percorso del file del certificato SSL con codifica base 64 (vedere come [scaricare il certificato]()). <br>Ad esempio: <br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>In alternativa, aggiungere il certificato all'archivio certificati del sistema e quindi impostare questa variabile di ambiente per il percorso dell'archivio. <br> Ad esempio, in Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Errori comuni

Questi errori non sono specifici per qualsiasi applicazione.

|Messaggio di errore  |Azione consigliata |
|---------|---------|
|Timeout della connessione. |Accedere al dispositivo Data Box e verificare che non sia bloccato. Ogni volta che il riavvio del dispositivo, rimane bloccata fino a quando un utente esegue l'accesso.|

## <a name="next-steps"></a>Passaggi successivi

- Scopri le [requisiti di sistema di archiviazione Blob di dati finestra](data-box-system-requirements-rest.md).
