---
title: Configurare l'autenticazione in Amazon Web Services | Documentazione Microsoft
description: Questo articolo descrive come creare e convalidare le credenziali di AWS per i runbook in Automazione di Azure che gestiscono le risorse di AWS.
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
keywords: autenticazione AWS, configurare AWS
ms.assetid: b6dde4bb-26ac-4876-9aa9-e586bed30d6b
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 81e5e5d56a7e6149409e11aca2e5fdf28d6a7134
ms.contentlocale: it-it
ms.lasthandoff: 04/15/2017

---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autenticare runbook con Amazon Web Services
I runbook di automazione di Azure consentono di automatizzare le attività comuni relative alle risorse in Amazon Web Services (AWS).  Esattamente come avviene per le risorse in Azure, con i runbook di Automazione è possibile automatizzare numerose attività in AWS.  A questo scopo, sono necessari due elementi:

* Una sottoscrizione di AWS e un set di credenziali.  Nello specifico, si tratta della chiave di accesso e della chiave privata di AWS.  Per altre informazioni, vedere l'articolo relativo all' [uso delle credenziali di AWS](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Una sottoscrizione di Azure e un account di Automazione.  Per altre informazioni su come configurare un account di Automazione di Azure, vedere l'articolo [Configurare l'account RunAs di Azure](automation-sec-configure-azure-runas-account.md).  

Per eseguire l'autenticazione in AWS è necessario specificare un set di credenziali di AWS per autenticare i runbook in esecuzione in Automazione di Azure. Se è già stato creato un account di Automazione e si vuole usarlo per l'autenticazione in AWS, seguire la procedura illustrata nella sezione successiva.  Se si vuole usare un account dedicato per i runbook destinati alle risorse di AWS, è consigliabile creare prima di tutto un nuovo [account di Automazione](automation-offering-get-started.md) (ignorando l'opzione per creare un'entità servizio) e quindi seguire questa procedura.

## <a name="configure-automation-account"></a>Configurare l'account di Automazione
Per consentire ad Automazione di Azure di comunicare con AWS, è prima di tutto necessario recuperare le credenziali di AWS e archiviarle come asset in Automazione di Azure.  Seguire questa procedura illustrata nel documento AWS relativo alla [gestione delle chiavi di accesso per l'account AWS](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) per creare una chiave di accesso e quindi copiare i valori di **Access Key ID** e **Secret Access Key** oppure scaricare il file di chiave e archiviarlo in una posizione sicura.

Dopo aver creato e copiato le chiavi di sicurezza di AWS, è necessario creare un asset credenziali con un account di Automazione di Azure per archiviarle in modo sicuro e farvi riferimento con i runbook.  Seguire la procedura illustrata nella sezione **Creazione di un nuovo asset credenziali** dell'articolo [Asset credenziali in Automazione di Azure](automation-credentials.md#to-create-a-new-credential-asset-with-the-azure-portal) e specificare le informazioni seguenti:

1. Nella casella **Nome** immettere **AWScred** o un valore appropriato in base agli standard di denominazione in uso.  
2. Nella casella **Nome utente** digitare il valore di **Access Key ID** e immettere il valore di **Secret Access Key** nelle caselle **Password** e **Conferma password**.   

## <a name="next-steps"></a>Passaggi successivi
* Vedere l'articolo della soluzione [Automazione della distribuzione di una macchina virtuale in Amazon Web Services](automation-scenario-aws-deployment.md) per informazioni su come creare runbook per automatizzare le attività in AWS.


