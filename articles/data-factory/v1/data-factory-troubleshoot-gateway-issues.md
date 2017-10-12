---
title: Risolvere i problemi del gateway di gestione dati | Documentazione Microsoft
description: Questo articolo offre suggerimenti per risolvere i problemi correlati al gateway di gestione dati.
services: data-factory
author: nabhishek
manager: jhubbard
editor: monicar
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: d8ad132ec47a81971a3405970eeddab3b1598ff3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Risolvere i problemi nell'uso del gateway di gestione dati
Questo articolo offre informazioni sulla risoluzione dei problemi nell'uso del gateway di gestione dati.

> [!NOTE]
> Vedere l'articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) per informazioni dettagliate sul gateway. Vedere l'articolo [Spostare dati tra origini locali e il cloud](data-factory-move-data-between-onprem-and-cloud.md) per una procedura dettagliata sull'uso del gateway per spostare i dati da un database di SQL Server locale all'Archiviazione BLOB di Azure.
>
>

## <a name="failed-to-install-or-register-gateway"></a>Impossibile installare o registrare il gateway
### <a name="1-problem"></a>1. Problema
Questo messaggio di errore viene visualizzato durante l'installazione e la registrazione di un gateway, in particolare durante il download del file di installazione del gateway.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Causa
Il computer in cui si prova a installare il gateway non è riuscito a scaricare il file di installazione del gateway più recente dall'area download a causa di un problema di rete.

#### <a name="resolution"></a>Risoluzione
Verificare se nel firewall del server proxy sono presenti impostazioni che bloccano la connessione di rete dal computer all'[Area download](https://download.microsoft.com/) e aggiornare le impostazioni di conseguenza.

In alternativa, è possibile scaricare il file di installazione del gateway più recente dall'[Area download](https://www.microsoft.com/download/details.aspx?id=39717) su altri computer che possono accedere all'area download. Dopo averlo scaricato, copiare il file di installazione nel computer host del gateway ed eseguirlo manualmente per installare e aggiornare il gateway.

### <a name="2-problem"></a>2. Problema
Questo errore viene visualizzato quando si prova a installare un gateway facendo clic su **Installa direttamente in questo computer** nel portale di Azure.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Causa
È già installato un gateway nel computer.

#### <a name="resolution"></a>Risoluzione
Disinstallare il gateway esistente nel computer e fare nuovamente clic sul collegamento **Installa direttamente in questo computer**.

### <a name="3-problem"></a>3. Problema
Questo errore può verificarsi quando si registra un nuovo gateway.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Causa
Il messaggio può essere visualizzato per uno dei motivi seguenti:

* Il formato della chiave del gateway non è valido.
* La chiave del gateway è stata invalidata.
* La chiave del gateway è stata rigenerata dal portale.  

#### <a name="resolution"></a>Risoluzione
Accertarsi che venga usata la chiave del gateway corretta del portale. Se necessario, rigenerare una chiave e usarla per registrare il gateway.

### <a name="4-problem"></a>4. Problema
Durante la registrazione di un gateway può essere visualizzato il messaggio di errore seguente:

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Il contenuto o il formato della chiave non è valido](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Causa
Il contenuto o il formato della chiave del gateway di input non è corretto. È possibile che sia stata copiata solo una parte della chiave dal portale oppure che la chiave usata non sia valida.

#### <a name="resolution"></a>Risoluzione
Generare una chiave del gateway nel portale e usare il pulsante Copia per copiare la chiave intera. Quindi incollarla in questa finestra per registrare il gateway.

### <a name="5-problem"></a>5. Problema
Durante la registrazione di un gateway può essere visualizzato il messaggio di errore seguente:

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![La chiave del gateway non è valida oppure è vuota](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Causa
La chiave del gateway è stata rigenerata oppure il gateway è stato eliminato nel portale di Azure. Il problema può verificarsi anche quando la versione del programma di installazione del Gateway di gestione dati non è la più recente.

#### <a name="resolution"></a>Risoluzione
Controllare che la versione del programma di installazione del Gateway di gestione dati sia la più recente (fare riferimento al [Download Center](https://go.microsoft.com/fwlink/p/?LinkId=271260) di Microsoft).

Se la versione del programma di installazione è quella attuale o la più recente e il gateway è ancora presente sul Portale, rigenerare la chiave del gateway nel Portale di Azure e usare il pulsante Copia per copiare l'intera chiave, quindi incollarla in questa finestra per registrare il gateway. In caso contrario ricreare il gateway e ricominciare.

### <a name="6-problem"></a>6. Problema
Durante la registrazione di un gateway può essere visualizzato il messaggio di errore seguente:

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![La chiave del gateway non è valida oppure è vuota](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Causa
Questo errore può verificarsi perché il gateway è stato eliminato oppure la chiave associata è stata rigenerata.

#### <a name="resolution"></a>Risoluzione
Se il gateway è stato eliminato, ricrearlo dal portale, fare clic su **Registra**, copiare la chiave dal portale, incollarla e provare a registrare il gateway.

Se il gateway è ancora esistente, ma la chiave è stata rigenerata, usare la nuova chiave per registrare il gateway. Se la chiave non è disponibile, rigenerarla nuovamente dal portale.

### <a name="7-problem"></a>7. Problema
Quando si registra un gateway, potrebbe essere necessario immettere il percorso e la password di un certificato.

![Specificare un certificato](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Causa
Il gateway è stato registrato in precedenza in altri computer. Durante la registrazione iniziale di un gateway è stato associato un certificato di crittografia al gateway stesso. Il certificato può essere generato automaticamente dal gateway oppure specificato dall'utente.  Questo certificato viene usato per crittografare le credenziali dell'archivio dati (servizio collegato).  

![Esportare il certificato](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Quando si ripristina il gateway in un computer host diverso, la registrazione guidata richiede questo certificato per decrittografare le credenziali crittografate in precedenza con questo certificato.  Senza questo certificato, le credenziali non possono essere decrittografate dal nuovo gateway e le successive attività di copia associate al nuovo gateway avranno esito negativo.  

#### <a name="resolution"></a>Risoluzione
Se il certificato delle credenziali è stato esportato dal computer gateway originale con il pulsante **Esporta** disponibile nella scheda **Impostazioni** di Gestione configurazione di Gateway di gestione dati, usare il certificato in quel computer.

Non è possibile ignorare questo passaggio quando si recupera un gateway. Se il certificato è mancante, è necessario eliminare il gateway dal portale e crearne uno nuovo.  Inoltre, tutti i servizi collegati relativi al gateway devono essere aggiornati immettendo di nuovo le credenziali.

### <a name="8-problem"></a>8. Problema
È possibile visualizzare il messaggio di errore seguente:

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Causa
Questo errore si verifica quando il gateway si trova in un ambiente che richiede un proxy HTTP per accedere alle risorse Internet oppure quando la password di autenticazione del proxy viene modificata, ma non aggiornata di conseguenza nel gateway.

#### <a name="resolution"></a>Risoluzione
Seguire le istruzioni indicate nella sezione [Considerazioni sui server proxy](#proxy-server-considerations) di questo documento e configurare le impostazioni del proxy con Gestione configurazione di Gateway di gestione dati.

## <a name="gateway-is-online-with-limited-functionality"></a>Il gateway è online con funzionalità limitate
### <a name="1-problem"></a>1. Problema
Lo stato visualizzato per il gateway è online con funzionalità limitata.

#### <a name="cause"></a>Causa
Lo stato visualizzato per il gateway è online con funzionalità limitata per uno dei motivi seguenti:

* Il gateway non può connettersi al servizio cloud tramite il bus di servizio di Azure.
* Il servizio cloud non può connettersi al gateway tramite il bus di servizio.

Quando il gateway è online con funzionalità limitate, potrebbe non essere possibile usare la copia guidata di Data Factory per creare pipeline di dati per copiare i dati da e verso archivi dati locali. Come soluzione alternativa, è possibile usare l'editor di Data Factory nel portale, oppure Visual Studio o Azure PowerShell.

#### <a name="resolution"></a>Risoluzione
La risoluzione di questo problema (online con funzionalità limitata) dipende dall'eventualità che il gateway non possa connettersi al servizio cloud o viceversa. Le sezioni seguenti illustrano queste risoluzioni.

### <a name="2-problem"></a>2. Problema
Viene visualizzato l'errore seguente:

`Error: Gateway cannot connect to cloud service through service bus`

![Il gateway non riesce a connettersi al servizio cloud](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa
Il gateway non può connettersi al servizio cloud tramite il bus di servizio.

#### <a name="resolution"></a>Risoluzione
Seguire questi passaggi per fare in modo che il gateway torni online:

1. Consentire le regole in uscita degli indirizzi IP nel computer gateway e nel firewall aziendale. È possibile trovare gli indirizzi IP nel registro eventi di Windows (ID == 401): si è verificato un tentativo di accesso a un socket in modalità non consentite dalle relative autorizzazioni di accesso XX.XX.XX.XX:9350.
* Configurare le impostazioni proxy nel gateway. Per conoscere i dettagli, vedere la sezione [Considerazioni sui server proxy](#proxy-server-considerations).
* Abilitare le porte in uscita 5671 e 9350-9354 sia su Windows Firewall nel computer gateway che nel firewall aziendale. Per conoscere i dettagli, vedere la sezione [Porte e firewall](#ports-and-firewall). Questo passaggio è facoltativo, ma consigliato per considerazioni relative alle prestazioni.

### <a name="3-problem"></a>3. Problema
Viene visualizzato l'errore seguente:

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Causa
Errore temporaneo nella connettività di rete.

#### <a name="resolution"></a>Risoluzione
Seguire questi passaggi per fare in modo che il gateway torni online:

1. Attendere un paio di minuti; la connettività verrà ripristinata automaticamente quando l'errore non sarà più visualizzato.
* Se l'errore persiste, riavviare il servizio gateway.

## <a name="failed-to-author-linked-service"></a>Impossibile creare il servizio collegato
### <a name="problem"></a>Problema
Questo errore può verificarsi quando si prova a usare Gestione credenziali nel portale per inserire le credenziali per un nuovo servizio collegato o aggiornare le credenziali per un servizio collegato esistente.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Quando viene visualizzato questo errore, la pagina Impostazioni di Gestione configurazione di Gateway di gestione dati potrebbe essere simile alla seguente.

![Impossibile raggiungere il database](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Causa
È possibile che il certificato SSL sia andato perso nel computer gateway. Il gateway non può caricare il certificato usato attualmente per la crittografia SSL. Nel registro può essere visualizzato un messaggio di errore analogo al seguente.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Risoluzione
Seguire questa procedura per risolvere il problema:

1. Avviare Gestione configurazione di Gateway di gestione dati.
2. Passare alla scheda **Impostazioni** .  
3. Fare clic sul pulsante **Cambia** per cambiare il certificato SSL.

   ![Pulsate Cambia per cambiare il certificato](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Selezionare un nuovo certificato come certificato SSL. È possibile usare qualsiasi certificato SSL generato dall'utente o da qualsiasi organizzazione.

   ![Specificare un certificato](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>L'attività di copia ha esito negativo
### <a name="problem"></a>Problema
Dopo aver impostato una pipeline nel portale di Azure viene visualizzato l'errore "UserErrorFailedToConnectToSqlserver".

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Causa
Questo problema può verificarsi per diversi motivi e la mitigazione varia di conseguenza.

#### <a name="resolution"></a>Risoluzione
Consentire le connessioni TCP in uscita sulla porta TCP/1433 o sul lato client del gateway di gestione dati prima di connettersi a un database SQL.

Se il database di destinazione è un database SQL di Azure, verificare anche le impostazioni del firewall del server SQL di Azure.

Vedere la sezione seguente per testare la connessione all'archivio dati locale.

## <a name="data-store-connection-or-driver-related-errors"></a>Errori correlati alla connessione all'archivio dati o al driver
Se vengono visualizzati errori relativi alla connessione dell'archivio dati o al driver, attenersi alla procedura seguente:

1. Avviare Gestione configurazione di Gateway di gestione dati sul computer del gateway.
2. Passare alla scheda **Diagnostica** .
3. In **Test di connessione**, aggiungere i valori del gruppo del gateway.
4. Fare clic su **Test** per verificare se è possibile connettersi all'origine dati locale dal computer del gateway tramite le informazioni di connessione e le credenziali. Se il test della connessione non riesce dopo aver installato un driver, riavviare il gateway in modo che rilevi la modifica più recente.

![Test connessione nella scheda Diagnostica](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Log del gateway
### <a name="send-gateway-logs-to-microsoft"></a>Inviare i log del gateway a Microsoft
Quando si contatta il supporto Microsoft per ricevere assistenza nella risoluzione dei problemi con il gateway, all'utente potrebbe essere richiesto di condividere i log del gateway. La versione del gateway consente di condividere facilmente i log necessari con pochi clic in Gestione configurazione di Gateway di gestione dati.    

1. In Gestione configurazione di Gateway di gestione dati aprire la scheda **Diagnostica**.

    ![Gateway di gestione dati - Scheda Diagnostica](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Fare clic sul collegamento **Invia log** per aprire la finestra di dialogo seguente.

    ![Gateway di gestione dati - Invia log](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (facoltativo) Fare clic su **Visualizza log** per esaminare i log nel visualizzatore eventi.
4. (facoltativo) Fare clic su **Privacy** per esaminare l'informativa sulla privacy per i servizi Web di Microsoft.
5. Quando tutto è pronto per il caricamento, fare clic su **Invia log** per inviare effettivamente i log degli ultimi sette giorni a Microsoft per la risoluzione dei problemi. Lo stato dell'operazione dovrebbe corrispondere a quello mostrato nell'immagine in basso:

    ![Gateway di gestione dati - Stato dell'operazione Invia log](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Al termine dell'operazione viene visualizzata una finestra di dialogo simile alla seguente:

    ![Gateway di gestione dati - Stato dell'operazione Invia log](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Annotare l'**ID report** e condividerlo con il supporto Microsoft. L'ID report serve per individuare i log del gateway caricati per la risoluzione dei problemi.  Viene anche salvato nel visualizzatore eventi.  È possibile trovarlo cercando l'ID evento "25" e verificando la data e l'ora.

    ![Gateway di gestione dati - ID report dell'operazione Invia log](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Archiviare i log del gateway nel computer host del gateway
Esistono dei casi in cui si verificano problemi con il gateway e non è possibile condividere direttamente i log del gateway:

* quando si installa e registra il gateway manualmente;
* quando si tenta di registrare il gateway con una chiave rigenerata in Gestione configurazione di Gateway di gestione dati;
* quando si tenta di inviare i log ma non è possibile connettersi al servizio che ospita il gateway.

In questi casi, è possibile salvare i log del gateway come file zip e condividerlo quando si contatta il supporto Microsoft. Ad esempio, se si verifica un errore durante la registrazione del gateway come illustrato nella schermata seguente.   

![Gateway di gestione dati - Errore di registrazione](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Fare clic sul collegamento **Archivia log del gateway** per archiviare e salvare i log, quindi condividere il file zip con il supporto Microsoft.

![Gateway di gestione dati - Archivia log](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Trovare i log del gateway
Per informazioni dettagliate sui log del gateway, vedere il registro eventi di Windows.

1. Avviare il **Visualizzatore eventi** di Windows.
2. Individuare i log nella cartella **Registri applicazioni e servizi** > **Gateway di gestione dati**.

 Per risolvere i problemi correlati al gateway, cercare gli eventi a livello di errore nel Visualizzatore eventi.

![Gateway di gestione dati - Log nel Visualizzatore eventi](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
