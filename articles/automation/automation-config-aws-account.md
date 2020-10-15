---
title: Autenticare i runbook di Automazione di Azure con Amazon Web Services
description: Questo articolo descrive come autenticare i runbook con Amazon Web Services.
keywords: autenticazione AWS, configurare AWS
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: acb056fb959fda320a14059e2b36a0f5e3fb0b37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83837187"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autenticare i runbook con Amazon Web Services

I runbook di automazione di Azure consentono di automatizzare le attività comuni relative alle risorse in Amazon Web Services (AWS). Esattamente come avviene per le risorse in Azure, con i runbook di Automazione è possibile automatizzare numerose attività in AWS. Per l'autenticazione, è necessario avere una sottoscrizione di Azure.

## <a name="obtain-aws-subscription-and-credentials"></a>Ottenere la sottoscrizione AWS e le credenziali

Per eseguire l'autenticazione in AWS è necessario ottenere una sottoscrizione AWS e specificare un set di credenziali di AWS per autenticare i runbook in esecuzione in Automazione di Azure. Le credenziali specifiche necessarie sono la chiave di accesso e la chiave privata di AWS. Vedere [Uso di credenziali AWS](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).

## <a name="configure-automation-account"></a>Configurare l'account di Automazione

Per eseguire l'autenticazione con AWS, è possibile usare un account di automazione esistente. In alternativa, è possibile dedicare un account per runbook destinati alle risorse AWS. In questo caso, creare un nuovo [Account di automazione](automation-create-standalone-account.md).  

## <a name="store-aws-credentials"></a>Archiviare le credenziali AWS

È necessario archiviare le credenziali di AWS come asset in Automazione di Azure. Vedere [Gestione delle chiavi di accesso per l'account AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) per istruzioni su come creare la chiave di accesso e la chiave privata. Quando le chiavi sono disponibili, copiare l'ID della chiave di accesso e l'ID della chiave privata in un luogo sicuro. È possibile scaricare il file di chiave per archiviarlo in un luogo sicuro.

## <a name="create-credential-asset"></a>Creare un asset di credenziali

Dopo aver creato e copiato le chiavi di sicurezza di AWS, è necessario creare un asset di credenziali con l'Account di automazione. L'asset consente di archiviare in modo sicuro le chiavi AWS e di farvi riferimento nei runbook. Vedere [Creare un nuovo asset di credenziali con il portale di Azure](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal). Immettere le informazioni AWS seguenti nei campi specificati:
    
* **Nome** - **AWScred** o un valore appropriato in base agli standard di denominazione in uso
* **Nome utente**: ID di accesso
* **Password**: nome della chiave privata 

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come creare runbook per automatizzare le attività in AWS, vedere [Distribuire una macchina virtuale di Amazon Web Services con un runbook](automation-scenario-aws-deployment.md).