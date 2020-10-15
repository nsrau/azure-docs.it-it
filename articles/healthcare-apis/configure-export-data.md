---
title: Configurare le impostazioni di esportazione nell'API di Azure per FHIR
description: Questo articolo descrive come configurare le impostazioni di esportazione nell'API di Azure per FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: e4adceea5c2cd2a36d7a867ca9b9d2ad7c33c155
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91529984"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>Configurare l'impostazione di esportazione e impostare l'account di archiviazione

API di Azure per FHIR supporta $export comando che consente di esportare i dati fuori dall'API di Azure per l'account FHIR in un account di archiviazione.

Per la configurazione dell'esportazione nell'API di Azure per FHIR sono necessari tre passaggi:

1. Abilitare l'identità gestita nell'API di Azure per il servizio FHIR
2. Creazione di un account di archiviazione di Azure (se non eseguito in precedenza) e assegnazione dell'autorizzazione all'API di Azure per FHIR all'account di archiviazione
3. Selezione dell'account di archiviazione nell'API di Azure per FHIR come esportazione dell'account di archiviazione

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Abilitazione dell'identità gestita nell'API di Azure per FHIR

Il primo passaggio per la configurazione dell'API di Azure per FHIR per l'esportazione consiste nell'abilitare l'identità gestita a livello di sistema nel servizio. [Qui](../active-directory/managed-identities-azure-resources/overview.md)è possibile leggere tutte le informazioni sulle identità gestite in Azure.

A tale scopo, passare a API di Azure per il servizio FHIR e selezionare Pannello identità. Se si modifica lo stato su on, viene abilitata l'identità gestita nell'API di Azure per il servizio FHIR.

![Abilita identità gestita](media/export-data/fhir-mi-enabled.png)

È ora possibile passare al passaggio successivo e creare un account di archiviazione e assegnare l'autorizzazione al servizio.

## <a name="adding-permission-to-storage-account"></a>Aggiunta dell'autorizzazione per l'account di archiviazione

Il passaggio successivo nell'esportazione consiste nell'assegnare l'autorizzazione per l'API di Azure per il servizio FHIR per la scrittura nell'account di archiviazione.

Dopo aver creato un account di archiviazione, passare al pannello controllo di accesso (IAM) nell'account di archiviazione e selezionare Aggiungi assegnazioni di ruolo.

![Esporta assegnazione di ruolo](media/export-data/fhir-export-role-assignment.png)

Qui viene quindi aggiunto un collaboratore dei dati BLOB di archiviazione del ruolo al nome del servizio.

![Aggiungi ruolo](media/export-data/fhir-export-role-add.png)

A questo punto si è pronti per il passaggio successivo, in cui è possibile selezionare l'account di archiviazione nell'API di Azure per FHIR come account di archiviazione predefinito per $export.

## <a name="selecting-the-storage-account-for-export"></a>Selezione dell'account di archiviazione per $export

Il passaggio finale consiste nell'assegnare l'account di archiviazione di Azure che verrà usato dall'API di Azure per FHIR per esportare i dati in. A tale scopo, passare al pannello integrazione nel servizio API di Azure per FHIR in portale di Azure e selezionare l'account di archiviazione

![FHIR Esporta archiviazione](media/export-data/fhir-export-storage.png)

Al termine, è possibile esportare i dati usando $export comando.

>[!div class="nextstepaction"]
>[Impostazioni aggiuntive](azure-api-for-fhir-additional-settings.md)
