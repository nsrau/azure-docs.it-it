---
title: Procedure consigliate per la distribuzione-servizio app Azure | Microsoft Docs
description: Informazioni sui componenti chiave della distribuzione nel servizio app Azure.
keywords: servizio app di Azure, app Web, distribuzione, distribuzione, pipeline, compilazione
services: app-service
documentationcenter: ''
author: jasonfreeberg
manager: ''
editor: ''
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.custom: ''
ms.openlocfilehash: 2beb35002cb98119db90f4cd278decc185ea35d7
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536495"
---
# <a name="deployment-best-practices"></a>Procedure consigliate per la distribuzione

Ogni team di sviluppo dispone di requisiti specifici che possono rendere difficile l'implementazione di una pipeline di distribuzione efficiente in qualsiasi servizio cloud. Questo articolo presenta i tre componenti principali della distribuzione nel servizio app: origini di distribuzione, pipeline di compilazione e meccanismi di distribuzione. Questo articolo illustra anche alcune procedure consigliate e suggerimenti per gli stack di linguaggi specifici.

## <a name="deployment-components"></a>Componenti di distribuzione

### <a name="deployment-source"></a>Origine distribuzione

Un'origine di distribuzione è il percorso del codice dell'applicazione. Per le app di produzione, l'origine della distribuzione è in genere un repository ospitato dal software di controllo della versione, ad esempio [GitHub, Bitbucket o Azure Repos](deploy-continuous-deployment.md). Per gli scenari di sviluppo e test, l'origine della distribuzione può essere [un progetto nel computer locale](deploy-local-git.md). Il servizio app supporta anche [le cartelle OneDrive e Dropbox](deploy-content-sync.md) come origini di distribuzione. Sebbene le cartelle cloud possano facilitare l'introduzione al servizio app, non è in genere consigliabile usare questa origine per le applicazioni di produzione di livello aziendale. 

### <a name="build-pipeline"></a>Pipeline di compilazione

Una volta scelta un'origine di distribuzione, il passaggio successivo consiste nel scegliere una pipeline di compilazione. Una pipeline di compilazione legge il codice sorgente dall'origine della distribuzione ed esegue una serie di passaggi, ad esempio la compilazione di codice, minimizzazione HTML e JavaScript, l'esecuzione di test e la creazione di pacchetti di componenti, per ottenere l'applicazione in uno stato eseguibile. I comandi specifici eseguiti dalla pipeline di compilazione dipendono dallo stack della lingua. Queste operazioni possono essere eseguite in un server di compilazione, ad esempio Azure Pipelines, oppure eseguite localmente.

### <a name="deployment-mechanism"></a>Meccanismo di distribuzione

Il meccanismo di distribuzione è l'azione usata per inserire l'applicazione compilata nella directory */Home/site/wwwroot* dell'app Web. La directory */wwwroot* è una posizione di archiviazione montata condivisa da tutte le istanze dell'app Web. Quando il meccanismo di distribuzione inserisce l'applicazione in questa directory, le istanze riceveranno una notifica per sincronizzare i nuovi file. Il servizio app supporta i meccanismi di distribuzione seguenti:

- Endpoint Kudu: [Kudu](https://github.com/projectkudu/kudu/wiki) è lo strumento open source per la produttività degli sviluppatori che viene eseguito come processo separato nel servizio app di Windows e come secondo contenitore nel servizio app Linux. Kudu gestisce le distribuzioni continue e fornisce endpoint HTTP per la distribuzione, ad esempio zipdeploy.
- FTP e WebDeploy: Usando le [credenziali del sito o dell'utente](deploy-configure-credentials.md), è possibile caricare i file [tramite FTP](deploy-ftp.md) o WebDeploy. Questi meccanismi non passano attraverso Kudu.  

Gli strumenti di distribuzione come Azure Pipelines, Jenkins e i plug-in dell'editor usano uno di questi meccanismi di distribuzione.

## <a name="language-specific-considerations"></a>Considerazioni specifiche della lingua

### <a name="java"></a>Java

Usare l'API Kudu [zipdeploy/](deploy-zip.md) per la distribuzione di applicazioni jar e [wardeploy/](deploy-zip.md#deploy-war-file) per le app War. Se si usa Jenkins, è possibile usare tali API direttamente nella fase di distribuzione. Per altre informazioni, vedere [questo articolo](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Nodo

Per impostazione predefinita, Kudu esegue i passaggi di compilazione per l'applicazione Node`npm install`(). Se si usa un servizio di compilazione come Azure DevOps, la compilazione Kudu non è necessaria. Per disabilitare la compilazione Kudu, creare un'impostazione dell' `SCM_DO_BUILD_DURING_DEPLOYMENT` `false`app,, con il valore.

### <a name="net"></a>.NET 

Per impostazione predefinita, Kudu esegue i passaggi di compilazione per l'applicazione .NET`dotnet build`(). Se si usa un servizio di compilazione come Azure DevOps, la compilazione Kudu non è necessaria. Per disabilitare la compilazione Kudu, creare un'impostazione dell' `SCM_DO_BUILD_DURING_DEPLOYMENT` `false`app,, con il valore.

## <a name="other-deployment-considerations"></a>Altre considerazioni sulla distribuzione

### <a name="use-deployment-slots"></a>Usare gli slot di distribuzione

Quando possibile, usare gli [slot di distribuzione](deploy-staging-slots.md) quando si distribuisce una nuova compilazione di produzione. Quando si usa un piano di servizio app standard o una soluzione migliore, è possibile distribuire l'app in un ambiente di staging, convalidare le modifiche ed eseguire test di fumo. Quando si è pronti, è possibile scambiare gli slot di gestione temporanea e di produzione. L'operazione di scambio consente di scaldare le istanze del ruolo di lavoro necessarie in base alla scala di produzione, eliminando così i tempi di inattività. 

### <a name="local-cache"></a>cache locale

Il contenuto del Servizio app di Azure viene memorizzato in Archiviazione di Azure e presentato in modo permanente come condivisione del contenuto. Tuttavia, alcune app necessitano solo di un archivio del contenuto a prestazioni elevate di sola lettura che possono essere eseguite con disponibilità elevata. Queste app possono trarre vantaggio dall'uso [della cache locale](overview-local-cache.md). La cache locale non è consigliata per i siti di gestione dei contenuti, ad esempio WordPress.

Usare sempre la cache locale insieme a [slot di distribuzione] (deploy-Staging-slot MD) per evitare tempi di inattività. Per informazioni sull'uso combinato di queste funzionalità, vedere [questa sezione](overview-local-cache.md#best-practices-for-using-app-service-local-cache) .

### <a name="high-cpu-or-memory"></a>CPU o memoria elevata

Se il piano di servizio app usa oltre il 90% della CPU o della memoria disponibile, la macchina virtuale sottostante potrebbe avere problemi di elaborazione della distribuzione. Quando si verifica questo problema, aumentare temporaneamente il numero di istanze per eseguire la distribuzione. Al termine della distribuzione, è possibile restituire il numero di istanze al valore precedente.
