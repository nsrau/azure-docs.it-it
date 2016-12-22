---
title: Distribuzione continua per Funzioni di Azure | Documentazione Microsoft
description: "Per pubblicare Funzioni di Azure, usare le funzionalità di distribuzione continua del servizio app di Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 66753c80223e33c3add9d37aa4d81656472391ca
ms.openlocfilehash: e1a869d3c3512f32eab7840f5abc17abd8ce8004


---
# <a name="continuous-deployment-for-azure-functions"></a>Distribuzione continua per Funzioni di Azure
Funzioni di Azure semplifica la configurazione della distribuzione continua per l'app per le funzioni. Funzioni usa l'integrazione del servizio app di Azure con BitBucket, Dropbox, GitHub e Visual Studio Team Services (VSTS) per consentire un flusso di lavoro di distribuzione continua in cui Azure effettua il pull degli aggiornamenti al codice delle funzioni quando vengono pubblicati in uno di questi servizi. Se non si ha familiarità con Funzioni di Azure, iniziare con [Panoramica di Funzioni di Azure](functions-overview.md).

La distribuzione continua è un'ottima opzione per i progetti in cui vengono integrati contributi numerosi e frequenti. Consente anche di mantenere il controllo dell'origine del codice funzione. Sono attualmente supportate le origini di distribuzione seguenti:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://bitbucket.org/)
* [Repository Git locale](../app-service-web/app-service-deploy-local-git.md)
* Repository Git esterno
* [GitHub]
* Repository Mercurial esterno
* [OneDrive](https://onedrive.live.com/)
* [Visual Studio Team Services](https://www.visualstudio.com/team-services/)

Le distribuzioni sono configurate in base alla singola app per le funzioni. Dopo aver abilitato la distribuzione continua, l'accesso al codice di funzione nel portale viene impostato su *sola lettura*.

## <a name="continuous-deployment-requirements"></a>Requisiti per la distribuzione continua

È necessario che l'origine della distribuzione sia configurata e che il codice funzioni sia presente nell'origine della distribuzione prima di configurare la distribuzione continua. In una determinata distribuzione di app per le funzioni, ogni funzione si trova in una sottodirectory denominata, dove il nome della directory è il nome della funzione. Questa struttura di cartelle è essenzialmente il codice del sito. 

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="set-up-continuous-deployment"></a>Configurare la distribuzione continua
Usare la procedura seguente per configurare la distribuzione continua per un'app per le funzioni esistente:

1. Nell'app per le funzioni nel [portale Funzioni di Azure](https://functions.azure.com/signin) fare clic su **Impostazioni dell'app per le funzioni** > **Configura integrazione continua** > **Configurazione**.
   
    ![Configurare la distribuzione continua](./media/functions-continuous-deployment/setup-deployment.png)
   
    ![Configurare la distribuzione continua](./media/functions-continuous-deployment/setup-deployment-1.png)
   
    È anche possibile accedere al pannello Distribuzioni dall'avvio rapido Funzioni facendo clic su **Iniziare dal controllo del codice sorgente**.
2. Nel pannello **Origine distribuzione** fare clic su **Scegliere l'origine**, quindi digitare le informazioni per l'origine della distribuzione scelta e fare clic su **OK**.
   
    ![Scegliere l'origine della distribuzione](./media/functions-continuous-deployment/choose-deployment-source.png)

Dopo aver configurato la distribuzione continua, tutti i file delle modifiche nell'origine della distribuzione vengono copiati nell'app per le funzioni e viene attivata una distribuzione completa del sito. Il sito viene ridistribuito quando vengono aggiornati i file nell'origine.

## <a name="deployment-options"></a>Opzioni di distribuzione

Di seguito sono indicati alcuni scenari di distribuzione tipici:

- [Creare una distribuzione di staging](#staging)
- [Trasferire le funzioni esistenti nella distribuzione continua](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Creare una distribuzione di staging

Le app per le funzioni non supportano ancora slot di distribuzione. È comunque possibile gestire distribuzioni di staging e di produzione separate tramite l'integrazione continua.

Il processo per la configurazione e l'uso di una distribuzione di staging è in genere simile al seguente:

1. Creare due app per le funzioni nella sottoscrizione, una per il codice di produzione e una per lo staging. 

2. Creare un'origine della distribuzione, se non è già presente. Questo esempio usa [GitHub].

3. Per l'app per le funzioni di produzione, eseguire i passaggi illustrati sopra in **Configurare la distribuzione continua** e impostare il ramo di distribuzione sul ramo master del repository GitHub.
   
    ![Scegliere il ramo della distribuzione](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Ripetere questo passaggio per l'app per le funzioni di staging, ma scegliere il ramo di staging nel repository GitHub. Se l'origine della distribuzione non supporta le diramazioni, usare una cartella diversa.

5. Apportare aggiornamenti al codice nel ramo o nella cartella di staging, quindi verificare che le modifiche vengono apportate nella distribuzione di staging.

6. Dopo i test, unire le modifiche dal ramo di staging nel ramo master. Verrà attivata la distribuzione nell'app per le funzioni di produzione. Se l'origine della distribuzione non supporta i rami, sovrascrivere i file nella cartella di produzione con i file dalla cartella di staging.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Trasferire le funzioni esistenti nella distribuzione continua
Quando si hanno funzioni esistenti che sono state create e gestite nel portale, è necessario scaricare i file del codice di funzione esistenti tramite FTP o il repository Git locale prima di configurare la distribuzione continua come descritto in precedenza. Questa operazione può essere eseguita nelle impostazioni del servizio app dell'app per le funzioni. Dopo aver scaricato i file, è possibile caricarli nell'origine scelta per la distribuzione continua.

> [!NOTE]
> Dopo aver configurato l'integrazione continua, non sarà più possibile modificare i file di origine nel portale di Funzioni.

- [Procedura: Configurare le credenziali di distribuzione](#credentials)
- [Procedura: Scaricare i file con FTP](#downftp)
- [Procedura: Scaricare file usando il repository Git locale](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>Procedura: Configurare le credenziali di distribuzione
Prima di poter scaricare i file dall'app per le funzioni con FTP o il repository Git locale, è necessario configurare le credenziali per accedere al sito. Questa operazione può essere eseguita dal portale. Le credenziali vengono impostate a livello di app per le funzioni.

1. Nell'app per le funzioni nel [portale Funzioni di Azure](https://functions.azure.com/signin) fare clic su **Impostazioni dell'app per le funzioni** > **Passa a Impostazioni del servizio app** > **Credenziali per la distribuzione**.
   
    ![Impostare le credenziali di distribuzione locali](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Immettere un nome utente e una password, quindi fare clic su **Salva**. È ora possibile usare queste credenziali per accedere all'app per le funzioni da FTP o dal repository Git predefinito.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>Procedura: Scaricare i file tramite FTP

1. Nell'app per le funzioni nel [portale Funzioni di Azure](https://functions.azure.com/signin) fare clic su **Impostazioni dell'app per le funzioni** > **Passa a Impostazioni del servizio app** > **Proprietà** e copiare i valori di **Utente FTP/distribuzione**, **Nome host FTP** e **Nome host FTPS**.  

    **Utente FTP/distribuzione** deve essere immesso come visualizzato dal portale, includendo il nome dell'app, per rendere disponibile un contesto appropriato per il server FTP.
   
    ![Ottenere le informazioni di distribuzione](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. Dal client FTP, usare le informazioni di connessione raccolte per connettersi all'app e scaricare i file di origine per le funzioni.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-the-local-git-repository"></a>Procedura: Scaricare file tramite il repository Git locale

1. Nell'app per le funzioni nel [portale Funzioni di Azure](https://functions.azure.com/signin) fare clic su **Impostazioni dell'app per le funzioni** > **Configura integrazione continua** > **Configurazione**.

2. Nel pannello Distribuzioni fare clic su **Scegliere l'origine**, **Repository Git locale** e quindi fare clic su **OK**.

3. Fare clic su **Passa a Impostazioni del servizio app** > **Proprietà** e prendere nota del valore di URL GIT. 
   
    ![Configurare la distribuzione continua](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Clonare il repository nel computer locale tramite riga di comando compatibile con Git o con lo strumento Git preferito. Il comando git clone è simile al seguente:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Recuperare i file dell'app per le funzioni nel clone nel computer locale, come nell'esempio seguente:
   
        git pull origin master
   
    Se richiesto, specificare nome utente e password per la distribuzione dell'app per le funzioni.  

[GitHub]: https://github.com/



<!--HONumber=Nov16_HO4-->


