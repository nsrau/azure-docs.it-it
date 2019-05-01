---
title: Distribuzione continua per Funzioni di Azure | Documentazione Microsoft
description: Usare le funzionalità di distribuzione continua del servizio App di Azure per pubblicare le funzioni.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: cb3f3ad3bb7b42429654ea4bf9b49f7e230db1da
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943890"
---
# <a name="continuous-deployment-for-azure-functions"></a>Distribuzione continua per Funzioni di Azure
Funzioni di Azure semplifica la distribuzione di app per le funzioni usando l'integrazione continua. Le funzioni si integra con i repository di codice principali e le origini di distribuzione. Questa integrazione consente a un flusso di lavoro in cui Aggiorna codice di funzione effettuata tramite una di queste distribuzioni del trigger dei servizi in Azure. Se si ha familiarità con funzioni di Azure, iniziare con il [Panoramica di funzioni di Azure](functions-overview.md).

Distribuzione continua è un'ottima opzione per i progetti in cui si esegue l'integrazione di più e contributi frequenti. Consente inoltre di mantenere il controllo di origine nel codice della funzione. Funzioni di Azure supporta le origini di distribuzione seguenti:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Archivio esterno (Git o Mercurial)
* [Archivio locale GIT](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Azure DevOps](https://azure.microsoft.com/services/devops/)

Le distribuzioni sono configurate in base alla singola app per le funzioni. Dopo aver abilitato la distribuzione continua, l'accesso al codice di funzione nel portale viene impostato su *sola lettura*.

## <a name="requirements-for-continuous-deployment"></a>Requisiti per la distribuzione continua

Prima di configurare la distribuzione continua, è necessario il codice della funzione e l'origine della distribuzione configurata nell'origine della distribuzione. In una distribuzione di app di funzione, ogni funzione è in una sottodirectory denominata, dove il nome della directory è il nome della funzione.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Per poter distribuire da Azure DevOps, è prima necessario collegare l'organizzazione di Azure DevOps nella sottoscrizione di Azure. Per altre informazioni, consultare [Impostare la fatturazione per l'organizzazione di Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Configurare la distribuzione continua
Usare la procedura seguente per configurare la distribuzione continua per un'app per le funzioni esistente. Questi passaggi illustrano come effettuare l'integrazione con un repository di GitHub, ma passaggi simili sono validi anche per Azure DevOps o altri servizi di distribuzione.

1. Nell'app per le funzioni nel [portale di Azure](https://portal.azure.com), selezionare **funzionalità della piattaforma** > **opzioni di distribuzione**. 
   
    ![Selezioni per l'apertura di opzioni di distribuzione](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Nel **distribuzioni** blade, selezionare **il programma di installazione**.
 
    ![Pannello distribuzioni](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Nel **origine distribuzione** blade, selezionare **scegliere l'origine**. Immettere le informazioni per l'origine della distribuzione scelta e quindi selezionare **OK**.
   
    ![Scelta di un'origine di distribuzione](./media/functions-continuous-deployment/choose-deployment-source.png)

Dopo aver configurato la distribuzione continua, vengono copiate tutte le modifiche ai file nell'origine della distribuzione di app per le funzioni e viene attivata una distribuzione completa del sito. Il sito viene ridistribuito quando vengono aggiornati i file nell'origine.

## <a name="deployment-scenarios"></a>Scenari di distribuzione

Scenari di distribuzione tipici includono la creazione di una distribuzione di staging e lo spostamento di funzioni esistenti per la distribuzione continua.

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Creare una distribuzione di staging

App per le funzioni non supportano ancora slot di distribuzione. Ma è comunque possibile gestire distribuzioni di gestione temporanea e produzione separate tramite l'integrazione continua.

Il processo per la configurazione e l'uso di una distribuzione di staging è in genere simile al seguente:

1. Creare due App per le funzioni nella sottoscrizione: uno per il codice di produzione e uno per la gestione temporanea. 

1. Creare un'origine della distribuzione, se non è già presente. Questo esempio usa [GitHub].

1. Per l'app per le funzioni di produzione, eseguire i passaggi illustrati sopra in [Configurare la distribuzione continua](#set-up-continuous-deployment) e impostare il ramo di distribuzione sul ramo master dell'archivio GitHub.
   
    ![Selezioni per scegliere un ramo di distribuzione](./media/functions-continuous-deployment/choose-deployment-branch.png)

1. Ripetere il passaggio 3 per l'app di funzione di gestione temporanea, ma scegliere invece il ramo di staging nel repository GitHub. Se l'origine della distribuzione non supporta le diramazioni, usare una cartella diversa.
    
1. Apportare aggiornamenti al codice nel ramo di staging o nella cartella e quindi verificare che la distribuzione di gestione temporanea riflette le modifiche.

1. Dopo i test, unire le modifiche dal ramo di staging nel ramo master. Questa unione attiva la distribuzione nell'app per le funzioni di produzione. Se l'origine della distribuzione non supporta i rami, sovrascrivere i file nella cartella di produzione con i file dalla cartella di staging.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Trasferire le funzioni esistenti nella distribuzione continua
Quando si hanno funzioni esistenti che sono state create e gestite nel portale, è necessario scaricare i file di codice di funzione tramite FTP o il repository Git locale prima è possibile impostare la distribuzione continua come descritto in precedenza. È possibile farlo nelle impostazioni del servizio App di Azure per app per le funzioni. Dopo aver scaricato i file, è possibile caricarli per l'origine della distribuzione continua scelto.

> [!NOTE]
> Dopo aver configurato l'integrazione continua, è non possibile modificare non è più i file di origine nel portale funzioni.

<a name="credentials"></a>
#### <a name="configure-deployment-credentials"></a>Configurare le credenziali di distribuzione
Prima di poter scaricare i file dall'app per le funzioni con FTP o un repository Git locale, è necessario configurare le credenziali per accedere al sito. Le credenziali vengono impostate a livello di app di funzione. Usare la procedura seguente per impostare le credenziali di distribuzione nel portale di Azure:

1. Nell'app per le funzioni nel [portale di Azure](https://portal.azure.com), selezionare **funzionalità della piattaforma** > **le credenziali di distribuzione**.
   
1. Immettere un nome utente e password e quindi selezionare **salvare**. 

   ![Selezioni per l'impostazione delle credenziali di distribuzione locale](./media/functions-continuous-deployment/setup-deployment-credentials.png)

È ora possibile usare queste credenziali per accedere all'app per le funzioni da FTP o dal repository Git predefinito.

<a name="downftp"></a>
#### <a name="download-files-by-using-ftp"></a>Scaricare file tramite FTP

1. Nell'app per le funzioni nel [portale di Azure](https://portal.azure.com), selezionare **funzionalità della piattaforma** > **proprietà**. Copiare quindi i valori per **utente FTP/distribuzione**, **nome Host FTP**, e **nome Host FTPS**.  

   **Utente FTP/distribuzione** deve essere immesso come visualizzato nel portale, includendo il nome dell'app, per rendere disponibile un contesto appropriato per il server FTP.
   
   ![Selezioni per ottenere le informazioni sulla distribuzione](./media/functions-continuous-deployment/get-deployment-credentials.png)

1. Dal client FTP, usare le informazioni di connessione raccolte per connettersi all'app e scaricare i file di origine per le funzioni.

<a name="downgit"></a>
#### <a name="download-files-by-using-a-local-git-repository"></a>Scaricare i file usando un repository Git locale

1. Nell'app per le funzioni nel [portale di Azure](https://portal.azure.com), selezionare **funzionalità della piattaforma** > **opzioni di distribuzione**. 
   
    ![Selezioni per l'apertura di opzioni di distribuzione](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Quindi, nella **distribuzioni** blade, selezionare **il programma di installazione**.
 
    ![Pannello distribuzioni](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Nel **origine distribuzione** blade, selezionare **repository Git locale** > **OK**.

1. Nelle **funzionalità della piattaforma**, selezionare **proprietà** e prendere nota del valore dell'URL Git. 
   
    ![Selezioni per ottenere l'URL Git](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

1. Clonare il repository nel computer locale usando un prompt dei comandi compatibile con Git o lo strumento Git preferito. Il comando clone GIT è simile al seguente:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

1. Recuperare i file dell'app per le funzioni nel clone nel computer locale, come nell'esempio seguente:
   
        git pull origin master
   
    Se richiesto inserire le [credenziali di distribuzione configurate](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
