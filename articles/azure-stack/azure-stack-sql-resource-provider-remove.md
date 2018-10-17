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
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: f5aa67ad0588e3f42e68056c8ffca97767975e8b
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49361482"
---
# <a name="remove-the-sql-resource-provider"></a>Rimuovere il provider di risorse SQL

Prima di rimuovere il provider di risorse SQL, è necessario rimuovere tutte le dipendenze del provider. È necessario anche una copia del pacchetto di distribuzione che è stato usato per installare il provider di risorse.

Sono presenti diverse attività di pulizia da eseguire prima di eseguire la _DeploySqlProvider.ps1_ script per rimuovere il provider di risorse.
I tenant sono responsabili per le seguenti attività di pulizia:

* Eliminare tutti i database dal provider di risorse. (Eliminare i database tenant non eliminare i dati).
* Annullare la registrazione dallo spazio dei nomi di provider di risorse.

L'amministratore è responsabile per le seguenti attività di pulizia:

* Elimina il server di hosting dal provider di risorse di SQL.
* Elimina i piani che fa riferimento al provider di risorse SQL.
* Elimina eventuali quote che sono associate al provider di risorse SQL.

## <a name="to-remove-the-sql-resource-provider"></a>Per rimuovere il provider di risorse SQL

1. Verificare di aver rimosso tutte le dipendenze delle provider di risorse SQL esistente.

   > [!NOTE]
   > Disinstallazione del provider di risorse SQL viene continuato anche se le risorse dipendenti utilizzano attualmente il provider di risorse.
  
2. Ottenere una copia del provider di risorse SQL binario e quindi eseguire il programma di autoestrazione per estrarre il contenuto in una directory temporanea.

3. Aprire una finestra della console PowerShell nuovo con privilegi elevata e passare alla directory in cui sono stati estratti i file binari di risorsa del provider SQL.

4. Eseguire lo script DeploySqlProvider.ps1 usando i parametri seguenti:

    * **Disinstallare**. Rimuove il provider di risorse e tutte le risorse associate.
    * **PrivilegedEndpoint**. L'indirizzo IP o nome DNS dell'endpoint con privilegi.
    * **AzureEnvironment**. L'ambiente di Azure usato per la distribuzione di Azure Stack. Obbligatorio solo per le distribuzioni di Azure AD.
    * **CloudAdminCredential**. Le credenziali per l'amministratore del cloud, necessaria per accedere all'endpoint con privilegi.
    * **AzCredential**. Le credenziali per l'account di amministratore del servizio di Azure Stack. Usare le stesse credenziali usate per la distribuzione di Azure Stack.

## <a name="next-steps"></a>Passaggi successivi

[Offrire servizi App come PaaS](azure-stack-app-service-overview.md)
