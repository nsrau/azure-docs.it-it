---
title: Distribuire l&quot;app nel servizio app di Azure usando FTP/S | Documentazione Microsoft
description: Informazioni su come distribuire l&quot;app nel servizio app di Azure usando FTP o FTPS.
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: cephalin;dariac
translationtype: Human Translation
ms.sourcegitcommit: 816113f7635a003e22a5172113e5039dbcc1ceac
ms.openlocfilehash: 5dc546849bd02ccf4d02f3e6363a3e2fc3898259


---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Distribuire l'app nel servizio app di Azure usando FTP/S
Questo articolo illustra come usare FTP o FTPS per distribuire l'app Web, il back-end dell'app per dispositivi mobili o l'app per le API nel [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

L'endpoint FTP/S per l'app è già attivo. Non è necessaria alcuna configurazione per abilitare la distribuzione FTP/S. 

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>Passaggio 1: Impostare le credenziali per la distribuzione

Per accedere al server FTP per l'app, prima sono necessarie le credenziali per la distribuzione. 

Per impostare o reimpostare le credenziali per la distribuzione, vedere [Credenziali per la distribuzione del Servizio app di Azure](app-service-deployment-credentials.md). Questa esercitazione illustra l'uso di credenziali a livello di utente.

## <a name="step-2-get-ftp-connection-information"></a>Passaggio 2: Ottenere informazioni di connessione a FTP

1. Nel [portale di Azure](https://portal.azure.com) aprire il [pannello della risorsa](../azure-resource-manager/resource-group-portal.md#manage-resources) dell'app.
2. Scegliere **Panoramica** dal menu a sinistra, quindi prendere nota dei valori per **Utente FTP/distribuzione**, **Nome host FTP** e **Nome host FTPS**. 

    ![Informazioni di connessione a FTP](./media/web-sites-deploy/FTP-Connection-Info.PNG)

    > [!NOTE]
    > Il valore di **Utente FTP/distribuzione** visualizzato dal portale di Azure include il nome dell'app per rendere disponibile un contesto appropriato al server FTP.
    > È possibile trovare le stesse informazioni scegliendo **Proprietà** dal menu a sinistra. 
    >
    > La password per la distribuzione inoltre non viene mai visualizzata. Se si dimentica la password per la distribuzione, tornare al [passaggio 1](#step1) e reimpostarla.
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>Passaggio 3: Distribuire file in Azure

1. Dal client FTP ([Visual Studio](https://www.visualstudio.com/vs/community/), [FileZilla](https://filezilla-project.org/download.php?type=client) e così via) usare le informazioni di connessione raccolte per connettersi all'app.
3. Copiare i file e la struttura di directory corrispondente nella directory [**/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) in Azure o nella directory **/site/wwwroot/App_Data/Jobs/** per i processi Web.
4. Passare all'URL dell'app per verificare che l'applicazione venga eseguita correttamente. 

> [!NOTE] 
> Diversamente dalle [distribuzioni basate su Git](app-service-deploy-local-git.md), la distribuzione FTP non supporta le automazioni di distribuzione seguenti: 
>
> - Ripristino delle dipendenze (ad esempio, automazioni NuGet, NPM, PIP e Composer)
> - Compilazione di file binari .NET
> - Generazione di web.config ([esempio di Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Questi file necessari devono essere ripristinati, compilati e generati manualmente nel computer locale e distribuiti con l'app.
>
>

## <a name="next-steps"></a>Passaggi successivi

Per scenari di distribuzione più avanzati, provare a eseguire la [distribuzione in Azure con Git](app-service-deploy-local-git.md). La distribuzione basata su Git in Azure abilita il controllo della versione, il ripristino del pacchetto, MSBuild e altro ancora.

## <a name="more-resources"></a>Altre risorse

* [Creare un'app Web PHP-MySQL e distribuirla tramite FTP](web-sites-php-mysql-deploy-use-ftp.md).
* [Credenziali per la distribuzione del Servizio app di Azure](app-service-deploy-ftp.md)



<!--HONumber=Jan17_HO4-->


