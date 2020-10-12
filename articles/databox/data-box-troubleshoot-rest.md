---
title: Azure Data Box la risoluzione dei problemi per l'uso dell'interfaccia REST | Microsoft Docs
description: Viene descritto come risolvere i problemi riscontrati in Azure Data Box quando la copia dei dati avviene tramite l'interfaccia REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: b950f80ba8c2bdbaf7a515dc1ce127b934723177
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85558553"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Risolvere i problemi relativi all'archiviazione BLOB Azure Data Box

Questo articolo illustra in dettaglio come risolvere i problemi che possono verificarsi quando si usa l'archiviazione BLOB di Data Box tramite l'interfaccia REST nel Data Box per copiare i dati. Questi problemi emergono quando si usa Data Box archiviazione BLOB con altre applicazioni o librerie client come Azure Storage Explorer, AzCopy o la libreria di archiviazione di Azure per Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Errori visualizzati in Azure Storage Explorer

In questa sezione vengono illustrati alcuni dei problemi riscontrati durante l'utilizzo di Azure Storage Explorer con Data Box archiviazione BLOB.

|Messaggio di errore  |Azione consigliata |
|---------|---------|
|Impossibile recuperare le risorse figlio. Il valore per una delle intestazioni HTTP non è nel formato corretto.|Dal menu **modifica** selezionare **destinazione API Azure stack**. <br>Riavviare Azure Storage Explorer.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Verificare che il nome dell'endpoint `<accountname>.blob.<serialnumber>.microsoftdatabox.com` venga aggiunto al file hosts nel percorso seguente: <li>`C:\Windows\System32\drivers\etc\hosts` in Windows o </li><li> `/etc/hosts` in Linux.</li>|
|Impossibile recuperare le risorse figlio. <br>Dettagli: certificato autofirmato |Importare il certificato TLS/SSL per il dispositivo in Azure Storage Explorer: <li>Scaricare il certificato dal portale di Azure. Per ulteriori informazioni, vedere [scaricare il certificato](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Scegliere **certificati SSL** dal menu **modifica** , quindi selezionare **Importa certificati**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Errori visualizzati in AzCopy per Windows

Questa sezione illustra in dettaglio alcuni dei problemi riscontrati quando si usa AzCopy per Windows con Data Box archiviazione BLOB.

|Messaggio di errore  |Azione consigliata |
|---------|---------|
|Il comando AzCopy sembra smettere di rispondere per un minuto prima di visualizzare questo errore: <br>Impossibile enumerare la directory https://... Non è stato possibile risolvere il nome remoto `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Verificare che il nome dell'endpoint `<accountname>.blob.<serialnumber>.microsoftdatabox.com` venga aggiunto al file hosts in: `C:\Windows\System32\drivers\etc\hosts` .|
|Il comando AzCopy sembra smettere di rispondere per un minuto prima di visualizzare questo errore: <br>Errore durante l'analisi del percorso di origine. Connessione sottostante chiusa: Impossibile stabilire una relazione di trust per il canale sicuro SSL/TLS.|Importare il certificato TLS/SSL per il dispositivo nell'archivio certificati del sistema. Per ulteriori informazioni, vedere [scaricare il certificato](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Errori visualizzati in AzCopy per Linux

Questa sezione illustra in dettaglio alcuni dei problemi riscontrati quando si usa AzCopy per Linux con Data Box archiviazione BLOB.

|Messaggio di errore  |Azione consigliata |
|---------|---------|
|Il comando AzCopy sembra smettere di rispondere per 20 minuti prima di visualizzare questo errore: <br>Errore durante l'analisi del percorso di origine `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>` . Nessun dispositivo o indirizzo di questo tipo|Verificare che il nome dell'endpoint `<accountname>.blob.<serialnumber>.microsoftdatabox.com` venga aggiunto al file hosts in: `/etc/hosts` .|
|Il comando AzCopy sembra smettere di rispondere per 20 minuti prima di visualizzare questo errore: <br>Errore durante l'analisi del percorso di origine... Non è stato possibile stabilire la connessione SSL.|Importare il certificato TLS/SSL per il dispositivo nell'archivio certificati del sistema. Per ulteriori informazioni, vedere [scaricare il certificato](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Errori visualizzati nella libreria di archiviazione di Azure per Python

Questa sezione illustra alcuni dei principali problemi riscontrati durante la distribuzione di Data Box Disk quando si usa un client Linux per la copia dei dati.

|Messaggio di errore  |Azione consigliata |
|---------|---------|
|Il valore per una delle intestazioni HTTP non è nel formato corretto. |La versione installata della libreria Archiviazione di Microsoft Azure per Python non è supportata da Data Box. Per informazioni sulle versioni supportate, vedere Azure Data Box requisiti di archiviazione BLOB.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...|Prima di eseguire python, impostare la variabile di ambiente REQUESTS_CA_BUNDLE sul percorso del file di certificato TLS con codifica Base64 (vedere How to [download the certificate](data-box-deploy-copy-data-via-rest.md#download-certificate)). <br>Ad esempio:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>In alternativa, aggiungere il certificato all'archivio certificati del sistema, quindi impostare questa variabile di ambiente sul percorso di tale archivio. <br> Ad esempio, in Ubuntu:  <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Errori comuni

Questi errori non sono specifici di alcuna applicazione.

|Messaggio di errore  |Azione consigliata |
|---------|---------|
|Timeout della connessione. |Accedere al dispositivo Data Box e verificare che sia sbloccato. Ogni volta che il dispositivo viene riavviato, rimane bloccato fino a quando l'utente non esegue l'accesso.|

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui requisiti di sistema per l' [archiviazione Blob Data Box](data-box-system-requirements-rest.md).
