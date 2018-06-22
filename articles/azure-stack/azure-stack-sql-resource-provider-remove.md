---
title: Rimuovere il provider di risorse SQL nello Stack di Azure | Documenti Microsoft
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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 150d1c40463aa04527bdd6e356a4c24ef68b02ef
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301899"
---
# <a name="remove-the-sql-resource-provider"></a>Rimuovere il provider di risorse SQL

Prima di rimuovere il provider di risorse SQL, è necessario rimuovere tutte le dipendenze di provider. È necessario anche una copia del pacchetto di distribuzione che è stato utilizzato per installare il provider di risorse.

## <a name="to-remove-the-sql-resource-provider"></a>Per rimuovere il provider di risorse SQL

1. Verificare di aver rimosso tutte le esistente SQL provider dipendenze della risorsa.

   > [!NOTE]
   > Disinstallazione del provider di risorse SQL viene continuato anche se le risorse dipendenti utilizzano attualmente il provider di risorse.
  
2. Ottenere una copia del provider di risorse SQL binario e quindi eseguire il programma di autoestrazione per estrarre il contenuto in una directory temporanea.

3. Aprire una finestra della console di PowerShell new con privilegi elevata e passare alla directory in cui sono stati estratti i file binari di risorse del provider SQL.

4. Eseguire lo script DeploySqlProvider.ps1 usando i parametri seguenti:

    - **Disinstallare**. Rimuove il provider di risorse e tutte le risorse associate.
    - **PrivilegedEndpoint**. L'indirizzo IP o nome DNS dell'endpoint con privilegi.
    - **CloudAdminCredential**. Le credenziali per l'amministratore del cloud, necessaria per accedere all'endpoint con privilegi.
    - **AzCredential**. Le credenziali per l'account amministratore del servizio Azure Stack. Utilizzare le stesse credenziali utilizzate per la distribuzione dello Stack di Azure.

## <a name="next-steps"></a>Passaggi successivi

[Servizi App offerta come PaaS](azure-stack-app-service-overview.md)
