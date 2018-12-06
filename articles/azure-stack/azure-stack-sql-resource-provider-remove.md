---
title: Rimuovere il provider di risorse SQL in Azure Stack | Microsoft Docs
description: Informazioni su come è possibile rimuovere il provider di risorse SQL dalla distribuzione di Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 8b6257519c3b1db2180335523047e8dc55558d57
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52954593"
---
# <a name="remove-the-sql-resource-provider"></a>Rimuovere il provider di risorse SQL

Prima di rimuovere il provider di risorse SQL, è necessario rimuovere tutte le dipendenze del provider. È necessario anche una copia del pacchetto di distribuzione che è stato usato per installare il provider di risorse.

> [!NOTE]
> È possibile trovare i collegamenti di download per la risorsa di programmi di installazione di provider [distribuisce i prerequisiti di provider di risorse](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Il provider di risorse SQL di rimozione non elimina i database tenant dal server di hosting.

## <a name="dependency-cleanup"></a>Pulizia delle dipendenze

Sono disponibili diverse attività di pulizia da eseguire prima di eseguire lo script DeploySqlProvider.ps1 per rimuovere il provider di risorse.

L'operatore di Azure Stack è responsabile per le seguenti attività di pulizia:

* Eliminare i piani che fanno riferimento all'Adapter SQL.
* Eliminare eventuali quote associati con l'Adapter SQL.

## <a name="to-remove-the-sql-resource-provider"></a>Per rimuovere il provider di risorse SQL

1. Verificare di aver rimosso tutte le dipendenze delle provider di risorse SQL esistente.

   > [!NOTE]
   > Disinstallazione del provider di risorse SQL viene continuato anche se le risorse dipendenti utilizzano attualmente il provider di risorse.
  
2. Ottenere una copia del pacchetto di installazione di provider di risorse SQL e quindi eseguire il programma di autoestrazione per estrarre il contenuto in una directory temporanea.

3. Aprire una finestra della console PowerShell nuovo con privilegi elevata e passare alla directory in cui sono stati estratti i file di installazione del provider di risorse SQL.

4. Eseguire lo script DeploySqlProvider.ps1 usando i parametri seguenti:

    * **Disinstallare**. Rimuove il provider di risorse e tutte le risorse associate.
    * **PrivilegedEndpoint**. L'indirizzo IP o nome DNS dell'endpoint con privilegi.
    * **AzureEnvironment**. L'ambiente di Azure usato per la distribuzione di Azure Stack. Obbligatorio solo per le distribuzioni di Azure AD.
    * **CloudAdminCredential**. Le credenziali per l'amministratore del cloud, necessaria per accedere all'endpoint con privilegi.
    * **AzCredential**. Le credenziali per l'account di amministratore del servizio di Azure Stack. Usare le stesse credenziali usate per la distribuzione di Azure Stack.

## <a name="next-steps"></a>Passaggi successivi

[Offrire servizi App come PaaS](azure-stack-app-service-overview.md)
