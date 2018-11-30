---
title: Configurare l'autenticazione in Amazon Web Services
description: Questo articolo descrive come creare e convalidare le credenziali di AWS per i runbook in Automazione di Azure che gestiscono le risorse di AWS.
keywords: autenticazione AWS, configurare AWS
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 20d51062bd73a3d722a82eac2f00e5eac3275734
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284283"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autenticare runbook con Amazon Web Services

I runbook di automazione di Azure consentono di automatizzare le attività comuni relative alle risorse in Amazon Web Services (AWS). Esattamente come avviene per le risorse in Azure, con i runbook di Automazione è possibile automatizzare numerose attività in AWS. A questo scopo, sono necessari due elementi:

* Una sottoscrizione di AWS e un set di credenziali. Nello specifico, si tratta della chiave di accesso e della chiave privata di AWS. Per altre informazioni, vedere l'articolo [Using AWS Credentials](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html) (Uso di credenziali AWS).
* Una sottoscrizione di Azure e un account di Automazione.

Per eseguire l'autenticazione in AWS è necessario specificare un set di credenziali di AWS per autenticare i runbook in esecuzione in Automazione di Azure. Se è già stato creato un account di automazione e si desidera usarlo per l'autenticazione con AWS, è possibile seguire i passaggi nella sezione seguente: se si desidera dedicare un account per il targeting di runbook delle risorse AWS, è necessario creare prima un nuovo [Account di automazione](automation-offering-get-started.md) (ignorare l'opzione per creare un'entità servizio) e attenersi alla procedura seguente:

## <a name="configure-automation-account"></a>Configurare l'account di Automazione

Per consentire ad Automazione di Azure di comunicare con AWS, è prima di tutto necessario recuperare le credenziali di AWS e archiviarle come asset in Automazione di Azure. Seguire questa procedura illustrata nel documento AWS relativo alla [gestione delle chiavi di accesso per l'account AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) per creare una chiave di accesso e quindi copiare i valori di **Access Key ID** e **Secret Access Key** oppure scaricare il file di chiave e archiviarlo in una posizione sicura.

Dopo aver creato e copiato le chiavi di sicurezza di AWS, è necessario creare un asset credenziali con un account di Automazione di Azure per archiviarle in modo sicuro e farvi riferimento con i runbook. Seguire la procedura illustrata nella sezione **Creazione di un nuovo asset credenziali** dell'articolo [Asset credenziali in Automazione di Azure](automation-credentials.md#to-create-a-new-credential-asset-with-the-azure-portal) e specificare le informazioni seguenti:

1. Nella casella **Nome** immettere **AWScred** o un valore appropriato in base agli standard di denominazione in uso.
2. Nella casella **Nome utente** digitare il valore in **ID accesso** e immettere il valore di **Secret Access Key** nelle caselle **Password** e **Conferma password**.

## <a name="next-steps"></a>Passaggi successivi

* Vedere l'articolo della soluzione [Automazione della distribuzione di una macchina virtuale in Amazon Web Services](automation-scenario-aws-deployment.md) per informazioni su come creare runbook per automatizzare le attività in AWS.