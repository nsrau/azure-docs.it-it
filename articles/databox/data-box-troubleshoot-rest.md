---
title: Risoluzione dei problemi di Azure Data Box per l'utilizzo dell'interfaccia REST Documenti Microsoft
description: Viene descritto come risolvere i problemi riscontrati in Azure Data Box quando la copia dei dati avviene tramite l'interfaccia REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 7fe5afbc4984c430cbf393e4e2b44122bdd43983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297138"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Risolvere i problemi relativi all'archiviazione BLOB di Azure Data BoxTroubleshoot issues related to Azure Data Box Blob storage

Questo articolo illustra in dettaglio le informazioni su come risolvere i problemi che possono verificarsi quando si usa l'archiviazione BLOB di data box tramite l'interfaccia REST nella casella dati per copiare i dati. Questi problemi emergono quando si usa l'archiviazione BLOB della casella di dati con altre applicazioni o librerie client, ad esempio Azure Storage Explorer, AzCopy o la libreria di archiviazione di Azure per Python.These issues surface when you are using Data Box Blob storage with other applications or client libraries such as Azure Storage Explorer, AzCopy, or Azure Storage library for Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Errori rilevati in Esplora archivi di AzureErrors seen in Azure Storage Explorer

Questa sezione descrive in dettaglio alcuni dei problemi affrontati quando si usa Esplora archivi di Azure con l'archiviazione BLOB di casella di dati.

|Messaggio di errore  |Azione consigliata |
|---------|---------|
|Impossibile recuperare le risorse figlio. Il valore per una delle intestazioni HTTP non è nel formato corretto.|Scegliere API di **Azure Stack di destinazione**dal menu **Modifica** . <br>Riavviare Esplora archivi di Azure.Restart Azure Storage Explorer.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Verificare che `<accountname>.blob.<serialnumber>.microsoftdatabox.com` il nome dell'endpoint venga aggiunto al file hosts in questo percorso: <li>`C:\Windows\System32\drivers\etc\hosts`su Windows, o </li><li> `/etc/hosts`su Linux.</li>|
|Impossibile recuperare le risorse figlio. <br>Dettagli: certificato autofirmato |Importare il certificato TLS/SSL per il dispositivo in Azure Storage Explorer:Import the TLS/SSL certificate for your device into Azure Storage Explorer: <li>Scaricare il certificato dal portale di Azure.Download the certificate from the Azure portal. Per ulteriori informazioni, visitare [Scaricare il certificato](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Dal menu **Modifica** , selezionare **Certificati SSL** e quindi selezionare **Importa certificati**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Errori visualizzati in AzCopy per WindowsErrors seen in AzCopy for Windows

Questa sezione descrive in dettaglio alcuni dei problemi affrontati quando si usa AzCopy per Windows con l'archiviazione BLOB di data box.

|Messaggio di errore  |Azione consigliata |
|---------|---------|
|AzCopy comando sembra bloccarsi per un minuto prima di visualizzare questo errore:AzCopy command appears to hang for a minute before displaying this error: <br>Impossibile enumerare i https:// di directory... Impossibile risolvere il nome remoto`<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Verificare che `<accountname>.blob.<serialnumber>.microsoftdatabox.com` il nome dell'endpoint `C:\Windows\System32\drivers\etc\hosts`venga aggiunto al file hosts in: .|
|AzCopy comando sembra bloccarsi per un minuto prima di visualizzare questo errore:AzCopy command appears to hang for a minute before displaying this error: <br>Errore durante l'analisi del percorso di origine. La connessione sottostante è stata chiusa: impossibile stabilire una relazione di trust per il canale sicuro SSL/TLS.|Importare il certificato TLS/SSL per il dispositivo nell'archivio certificati del sistema. Per ulteriori informazioni, visitare [Scaricare il certificato](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Errori visualizzati in AzCopy per LinuxErrors seen in AzCopy for Linux

Questa sezione descrive in dettaglio alcuni dei problemi affrontati quando si usa AzCopy per Linux con l'archiviazione BLOB di Data Box.This section details some of the issues affrontato when using AzCopy for Linux with Data Box Blob storage.

|Messaggio di errore  |Azione consigliata |
|---------|---------|
|AzCopy comando sembra bloccarsi per 20 minuti prima di visualizzare questo errore:AzCopy command appears to hang for 20 minutes before displaying this error: <br>Errore durante l'analisi del percorso `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`di origine . Nessun dispositivo o indirizzo di questo tipo|Verificare che `<accountname>.blob.<serialnumber>.microsoftdatabox.com` il nome dell'endpoint `/etc/hosts`venga aggiunto al file hosts in: .|
|AzCopy comando sembra bloccarsi per 20 minuti prima di visualizzare questo errore:AzCopy command appears to hang for 20 minutes before displaying this error: <br>Errore durante l'analisi del percorso di origine in corso... Impossibile stabilire la connessione SSL.|Importare il certificato TLS/SSL per il dispositivo nell'archivio certificati del sistema. Per ulteriori informazioni, visitare [Scaricare il certificato](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Errori osservati nella libreria di archiviazione di Azure per PythonErrors seen in Azure Storage library for Python

Questa sezione illustra alcuni dei principali problemi riscontrati durante la distribuzione di Data Box Disk quando si usa un client Linux per la copia dei dati.

|Messaggio di errore  |Azione consigliata |
|---------|---------|
|Il valore per una delle intestazioni HTTP non è nel formato corretto. |La versione installata di Microsoft Azure Storage Library per Python non è supportata da Data Box. Vedere Requisiti di archiviazione BLOB di Azure Data Box per le versioni supportate.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] ...|Prima di eseguire Python, impostare la variabile di ambiente REQUESTS_CA_BUNDLE sul percorso del file del certificato TLS con codifica Base64 (vedere come [scaricare il certificato](data-box-deploy-copy-data-via-rest.md#download-certificate)). <br>Ad esempio:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>In alternativa, aggiungere il certificato all'archivio certificati del sistema e quindi impostare questa variabile di ambiente sul percorso dell'archivio. <br> Ad esempio, in Ubuntu:  <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Errori comuni

Questi errori non sono specifici di alcuna applicazione.

|Messaggio di errore  |Azione consigliata |
|---------|---------|
|Timeout della connessione. |Accedere al dispositivo Data Box e verificare che sia sbloccato. Ogni volta che il dispositivo si riavvia, rimane bloccato fino a quando qualcuno non accede.|

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui requisiti di [sistema di archiviazione Data Box Blob](data-box-system-requirements-rest.md).
