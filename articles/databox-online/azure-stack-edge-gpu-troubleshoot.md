---
title: Usare la portale di Azure per risolvere i problemi relativi a Azure Stack Edge Pro con GPU | Microsoft Docs
description: Viene descritto come risolvere i problemi di Azure Stack GPU Pro Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: alkohli
ms.openlocfilehash: 026f476b888380b6f262a6a52c064c939e27e931
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743200"
---
# <a name="troubleshoot-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Risolvere i problemi relativi al dispositivo GPU Azure Stack Edge Pro 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Questo articolo descrive come risolvere i problemi del dispositivo GPU Azure Stack Edge Pro. 


## <a name="run-diagnostics"></a>Eseguire la diagnostica

Per diagnosticare e risolvere gli errori di qualsiasi dispositivo, è possibile eseguire i test di diagnostica. Per eseguire i test di diagnostica, usare la procedura seguente nell'interfaccia utente Web locale.

1. Nell'interfaccia utente Web locale passare a **Risoluzione dei problemi > Test diagnostici**. Selezionare il test che si desidera eseguire e selezionare **Esegui test**. Vengono eseguiti i test per diagnosticare eventuali problemi con la rete, il dispositivo, il proxy Web, l'ora o le impostazioni cloud. Si riceve una notifica in cui si comunica che il dispositivo sta eseguendo dei test.

    ![Selezionare i test ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. Al termine dei test, vengono visualizzati i risultati. 

    ![Visualizzare i risultati dei test](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    Se un test non riesce, viene visualizzato un URL per l'azione consigliata. Selezionare l'URL per visualizzare l'azione consigliata.
 
    ![Esaminare gli avvisi per i test non superati](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Ottenere il pacchetto per il supporto

Un pacchetto di log è costituito da tutti i log rilevanti utili al supporto tecnico Microsoft nella risoluzione dei problemi del dispositivo. Un pacchetto di log può essere generato con l'interfaccia utente Web locale.

Per ottenere un pacchetto di supporto, usare la procedura seguente. 

1. Nell'interfaccia utente Web locale passare a **Risoluzione dei problemi > Supporto**. Selezionare **Crea pacchetto**per il supporto. Il sistema inizia a ottenere il pacchetto per il supporto. Questa operazione potrebbe richiedere alcuni minuti.

    ![Selezionare Aggiungi utente](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. Dopo aver creato il pacchetto per il supporto, selezionare **Scarica pacchetto**per il supporto. Un pacchetto compresso viene scaricato nel percorso selezionato. È possibile decomprimere il pacchetto e visualizzare i file di log del registro di sistema.

    ![Selezionare Aggiungi utente 2](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>Raccogli log di sicurezza avanzati

I registri di sicurezza avanzati possono essere log di intrusioni hardware o software per il dispositivo Azure Stack Edge Pro.

### <a name="software-intrusion-logs"></a>Log di intrusione software

L'intrusione software o i log del firewall predefiniti vengono raccolti per il traffico in ingresso e in uscita. 

- Quando si crea un'immagine del dispositivo nella Factory, la registrazione del firewall predefinita è abilitata. Per impostazione predefinita, questi log vengono aggregati nel pacchetto per il supporto quando si crea un pacchetto per il supporto tramite l'interfaccia utente locale o tramite l'interfaccia di Windows PowerShell del dispositivo.

- Se nel pacchetto del supporto sono necessari solo i log del firewall per esaminare eventuali intrusioni software (NW) nel dispositivo, usare l' `-Include FirewallLog` opzione quando si crea il pacchetto per il supporto. 

- Se non viene specificata alcuna opzione di inclusione specifica, il log del firewall viene incluso come predefinito nel pacchetto per il supporto.

- Nel pacchetto del supporto, il registro del firewall è il `pfirewall.log` e si trova nella cartella radice. Di seguito è riportato un esempio del registro di intrusione software per il dispositivo Azure Stack Edge Pro. 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>Log delle intrusioni hardware

Per rilevare eventuali intrusioni hardware nel dispositivo, vengono registrati tutti gli eventi chassis, ad esempio l'apertura o la chiusura dello chassis. 

- Il registro eventi di sistema dal dispositivo viene letto usando il `racadm` cmdlet. Questi eventi vengono quindi filtrati per l'evento relativo al chassis in un `HWIntrusion.txt` file.

- Per ottenere solo il registro intrusione hardware nel pacchetto per il supporto, usare l' `-Include HWSelLog` opzione quando si crea il pacchetto per il supporto. 

- Se non viene specificata alcuna opzione di inclusione specifica, il log di intrusione hardware viene incluso come predefinito nel pacchetto per il supporto.

- Nel pacchetto per il supporto, il registro intrusione hardware è `HWIntrusion.txt` e si trova nella cartella radice. Di seguito è riportato un esempio di log delle intrusioni hardware per il dispositivo Azure Stack Edge Pro. 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="use-logs-to-troubleshoot"></a>Usare i log per risolvere i problemi

Gli eventuali errori riscontrati durante i processi di caricamento e aggiornamento vengono inclusi nei file di log degli errori corrispondenti.

1. Per visualizzare i file degli errori, passare alla condivisione e selezionare la condivisione per visualizzarne il contenuto. 


2. Selezionare la _cartella Microsoft Data Box Edge_. Questa cartella contiene due sottocartelle:

    - Una cartella di caricamento contenente i file di log per gli errori di caricamento
    - Una cartella di aggiornamento per gli errori durante l'aggiornamento

    Ecco come appare un file di log per l'aggiornamento:

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. Quando viene visualizzato un errore in questo file (evidenziato nell'esempio), annotare il codice di errore, che in questo caso è 16001. Cercare la descrizione di questo codice di errore con il riferimento di errore seguente.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="use-error-lists-to-troubleshoot"></a>Usare elenchi di errori per la risoluzione dei problemi

Gli elenchi di errori vengono compilati da scenari identificati e possono essere usati per la diagnosi automatica e la risoluzione dei problemi. 

## <a name="azure-resource-manager"></a>Azure Resource Manager

Di seguito sono riportati gli errori che possono verificarsi durante la configurazione di Azure Resource Manager per accedere al dispositivo. 

| **Problema/errori** |  **Risoluzione** | 
|------------|-----------------|
|Problemi generali|<li>[Verificare che il dispositivo perimetrale sia configurato correttamente](#verify-the-device-is-configured-properly).<li> [Verificare che il client sia configurato correttamente](#verify-the-client-is-configured-properly)|
|Add-AzureRmEnvironment: si è verificato un errore durante l'invio della richiesta.<br>Alla riga: 1 carattere: 1<br>+ Add-AzureRmEnvironment-Name AZ3-ARMEndpoint " https://management.dbe ...|Questo errore indica che il dispositivo Azure Stack Edge Pro non è raggiungibile o configurato in modo corretto. Verificare che il dispositivo perimetrale e il client siano configurati correttamente. Per informazioni aggiuntive, vedere la riga relativa ai **problemi generali** in questa tabella.|
|Il servizio ha restituito un errore. Controllare InnerException per altri dettagli: la connessione sottostante è stata chiusa: Impossibile stabilire una relazione di trust per il canale sicuro SSL/TLS. |   Questo errore è probabilmente causato da uno o più passaggi di Bring your own certificate in modo errato. [Qui](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-connect-resource-manager#step-2-create-and-install-certificates)è possibile trovare informazioni aggiuntive. |
|L'operazione ha restituito un codice di stato ' ServiceUnavailable ' non valido <br> Il codice di stato della risposta non indica esito positivo: 503 (servizio non disponibile). | Questo errore potrebbe essere dovuto a una di queste condizioni.<li>ArmStsPool è in stato interrotto.</li><li>Uno dei siti Web del servizio token di sicurezza/Azure Resource Manager è inattivo.</li><li>La risorsa cluster Azure Resource Manager è inattiva.</li><br><strong>Nota:</strong> Il riavvio dell'appliance potrebbe risolvere il problema, ma è necessario raccogliere il pacchetto per il supporto in modo che sia possibile eseguirne il debug.|
|AADSTS50126: nome utente o password non valida.<br>ID traccia: 29317da9-52fc-4ba0-9778-446ae5625e5a<br>ID correlazione: 1b9752c4-8cbf-4304-A714-8a16527410f4<br>Timestamp: 2019-11-15 09:21:57Z: il server remoto ha restituito un errore: (400) richiesta non valida.<br>Alla riga: 1 carattere: 1 |Questo errore potrebbe essere dovuto a una di queste condizioni.<li>Per un nome utente e una password non validi, verificare che il cliente abbia modificato la password da portale di Azure attenendosi [alla procedura seguente](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-set-azure-resource-manager-password) e quindi usando la password corretta.<li>Per un ID tenant non valido, l'ID tenant è un GUID fisso e deve essere impostato su `c0257de7-538f-415c-993a-1b87a031879d`</li>|
|Connect-AzureRmAccount: AADSTS90056: la risorsa è disabilitata o non esiste. Controllare il codice dell'app per verificare di avere specificato l'URL della risorsa esatta cui si sta tentando di accedere.<br>ID traccia: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>ID correlazione: 75c8ef5a-830e-48B5-B039-595a96488ff9 timestamp: 2019-11-18 07:00:51Z: il server remoto ha restituito un errore: (400) non valido |Gli endpoint di risorsa usati nel `Add-AzureRmEnvironment` comando non sono corretti.|
|Non è possibile ottenere gli endpoint dal cloud.<br>Assicurarsi di disporre di una connessione di rete. Dettagli errore: HTTPSConnectionPool (host =' Management. dbg-of4k6suvm.microsoftdatabox.com ', Port = 30005): numero massimo di tentativi superato con URL:/Metadata/Endpoints? API-Version = 2015-01-01 (causata da SSLError (SSLError ("handshake errato: Error ([(' routine SSL ',' tls_process_server_certificate ',' certificate Verify failed ')],)",),)) |Questo errore viene visualizzato principalmente in un ambiente Mac/Linux ed è dovuto ai problemi seguenti:<li>Un certificato di formato PEM non è stato aggiunto all'archivio certificati Python.</li> |

### <a name="verify-the-device-is-configured-properly"></a>Verificare che il dispositivo sia configurato correttamente

1. Dall'interfaccia utente locale, verificare che la rete del dispositivo sia configurata correttamente.

2. Verificare che i certificati vengano aggiornati per tutti gli endpoint, come indicato di [seguito](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates).

3. Ottenere l'endpoint di accesso e gestione Azure Resource Manager dalla pagina del **dispositivo** nell'interfaccia utente locale.

4. Verificare che il dispositivo sia attivato e registrato in Azure.


### <a name="verify-the-client-is-configured-properly"></a>Verificare che il client sia configurato correttamente

1. Verificare che sia installata la versione corretta di PowerShell come indicato [qui](azure-stack-edge-j-series-connect-resource-manager.md#step-3-install-powershell-on-the-client).

2. Verificare che siano installati i moduli di PowerShell corretti come indicato [qui](azure-stack-edge-j-series-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client).

3. Verificare che gli endpoint di Azure Resource Manager e di accesso siano raggiungibili. È possibile provare a effettuare il ping degli endpoint. Ad esempio:

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   Se non sono raggiungibili, aggiungere le voci del file DNS/host come indicato [qui](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution).
   
4. Verificare che i certificati client siano installati come indicato [qui](azure-stack-edge-j-series-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell).

5. Se il cliente usa PowerShell, è necessario abilitare la preferenza di debug per visualizzare i messaggi dettagliati eseguendo questo comando di PowerShell. 

    `$debugpreference = "continue"`

## <a name="blob-storage-on-device"></a>Archiviazione BLOB nel dispositivo 

Ecco gli errori correlati all'archiviazione BLOB nel dispositivo Azure Stack Edge Pro/Data Box Gateway.

| **Problema/errori** |  **Risoluzione** | 
|--------------------|-----------------|
|Impossibile recuperare le risorse figlio. Il valore per una delle intestazioni HTTP non è nel formato corretto.| Dal menu **modifica** selezionare **destinazione API Azure stack**. Quindi, riavviare Azure Storage Explorer.|
|funzione getaddrinfo ENOTFOUND <accountname> . blob. <serialnumber> . microsoftdatabox.com|Verificare che il nome dell'endpoint `<accountname>.blob.<serialnumber>.microsoftdatabox.com` venga aggiunto al file hosts nel percorso seguente: `C:\Windows\System32\drivers\etc\hosts` in Windows o `/etc/hosts` in Linux.|
|Impossibile recuperare le risorse figlio.<br> Dettagli: certificato autofirmato |Importare il certificato SSL del dispositivo in Azure Storage Explorer: <ol><li>Scaricare il certificato dal portale di Azure. Per ulteriori informazioni, vedere [scaricare il certificato](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#download-certificate).</li><li>Scegliere certificati SSL dal menu **modifica** , quindi selezionare **Importa certificati**.</li></ol>|
|Il comando AzCopy sembra smettere di rispondere per un minuto prima di visualizzare questo errore:<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Verificare che il nome dell'endpoint `<accountname>.blob.<serialnumber>.microsoftdatabox.com` venga aggiunto al file hosts in: `C:\Windows\System32\drivers\etc\hosts` .|
|Il comando AzCopy sembra smettere di rispondere per un minuto prima di visualizzare questo errore:<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |Importare il certificato SSL del dispositivo nell'archivio certificati del sistema. Per ulteriori informazioni, vedere [scaricare il certificato](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#download-certificate).|
|Il comando AzCopy sembra smettere di rispondere per 20 minuti prima di visualizzare questo errore:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |Verificare che il nome dell'endpoint `<accountname>.blob.<serialnumber>.microsoftdatabox.com` venga aggiunto al file hosts in: `/etc/hosts` .|
|Il comando AzCopy sembra smettere di rispondere per 20 minuti prima di visualizzare questo errore:<br>`Error parsing source location… The SSL connection could not be established`. |Importare il certificato SSL del dispositivo nell'archivio certificati del sistema. Per ulteriori informazioni, vedere [scaricare il certificato](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#download-certificate).|
|Il comando AzCopy sembra smettere di rispondere per 20 minuti prima di visualizzare questo errore:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|Verificare che il nome dell'endpoint `<accountname>.blob.<serialnumber>.microsoftdatabox.com` venga aggiunto al file hosts in: `/etc/hosts` .|
|Il comando AzCopy sembra smettere di rispondere per 20 minuti prima di visualizzare l'errore seguente: `Error parsing source location… The SSL connection could not be established` .|Importare il certificato SSL del dispositivo nell'archivio certificati del sistema. Per ulteriori informazioni, vedere [scaricare il certificato](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#download-certificate).|
|Il valore per una delle intestazioni HTTP non è nel formato corretto.|La versione installata della libreria Archiviazione di Microsoft Azure per Python non è supportata da Data Box. Per informazioni sulle versioni supportate, vedere Azure Data Box requisiti di archiviazione BLOB.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...| Prima di eseguire python, impostare la variabile di ambiente REQUESTS_CA_BUNDLE sul percorso del file di certificato SSL con codifica Base64 (vedere How to [download the certificate](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#download-certificate). Ad esempio:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>In alternativa, aggiungere il certificato all'archivio certificati del sistema, quindi impostare questa variabile di ambiente sul percorso di tale archivio. Ad esempio, in Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`.|
|Timeout della connessione.|Accedere al Azure Stack Edge Pro e verificare che sia sbloccato. Ogni volta che il dispositivo viene riavviato, rimane bloccato fino a quando l'utente non esegue l'accesso.|


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [problemi noti in questa versione](azure-stack-edge-gpu-2008-release-notes.md).
