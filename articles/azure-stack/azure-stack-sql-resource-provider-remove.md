---
title: Utilizzo di database SQL Azure stack | Documenti Microsoft
description: Informazioni su come distribuire i database SQL come servizio in Azure Stack e la procedura per distribuire l'adapter di provider di risorse di SQL Server.
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: c2686a2d5241af46e70263d1827028aa7e9b2138
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
---
# <a name="remove-the-sql-resource-provider"></a>Rimuovere il provider di risorse SQL

Per rimuovere il provider di risorse SQL, è necessario innanzitutto rimuovere tutte le dipendenze:

1. Assicurarsi di disporre il pacchetto di distribuzione originale scaricati per questa versione di adattatore di provider di risorse SQL.

2. È necessario eliminare tutti i database utente dal provider di risorse. (L'eliminazione dei database utente non elimina i dati.) Questa attività deve essere eseguita dagli utenti stessi.

3. L'amministratore deve eliminare il server di hosting dalla scheda provider di risorse di SQL.

4. L'amministratore deve eliminare i piani che fanno riferimento l'adapter di provider di risorse SQL.

5. L'amministratore deve eliminare qualsiasi SKU e quote che sono associate all'adapter di provider di risorse SQL.

6. Eseguire nuovamente lo script di distribuzione con gli elementi seguenti:
    - -Disinstallare parametro
    - Gli endpoint di gestione risorse di Azure
    - Il DirectoryTenantID
    - Le credenziali per l'account amministratore del servizio

