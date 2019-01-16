---
title: Azure Stack archivio delle credenziali dell'entità servizio in Azure Key Vault | Microsoft Docs
description: Informazioni su come insieme di credenziali delle chiavi memorizza le credenziali dell'entità servizio in Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: sethm
ms.openlocfilehash: 3fcbf2b3160d57e56a59ba9c374c9b1b2a75a159
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330246"
---
# <a name="store-service-principal-credentials-in-key-vault"></a>Store credenziali dell'entità servizio in Azure Key Vault

Lo sviluppo di applicazioni in Azure Stack in genere, è necessario creare un'entità servizio e l'utilizzo di tali credenziali per l'autenticazione prima della distribuzione. Tuttavia, spesso vengono spostate le credenziali archiviate per l'entità servizio. Questo articolo descrive come creare un'entità servizio e archiviare i valori in Azure Key Vault per il successivo recupero.

Per altre informazioni su Key Vault, vedere [questo articolo](azure-stack-key-vault-intro.md).

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione a un'offerta che include il servizio Azure Key Vault.
- PowerShell è configurato per l'uso con Azure Stack.

## <a name="key-vault-in-azure-stack"></a>Insieme di credenziali delle chiavi in Azure Stack

Key Vault in Azure Stack aiuta a proteggere le chiavi crittografiche e segreti che le applicazioni e servizi cloud usano. Con Key Vault, è possibile crittografare chiavi e segreti.

Per creare un insieme di credenziali delle chiavi, seguire questa procedura:

1. Accedere al portale di Azure Stack.

2. Dal dashboard, selezionare **+ crea una risorsa**, quindi **sicurezza e identità**, quindi selezionare **Key Vault.**

   ![Creare un insieme di credenziali delle chiavi](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. Nel **creazione Key Vault** riquadro, assegnare un **nome** dell'insieme di credenziali. I nomi dell'insieme di credenziali possono contenere solo caratteri alfanumerici e il carattere trattino (-). Essi non deve iniziare con un numero.

4. Scegliere una sottoscrizione dall'elenco delle sottoscrizioni disponibili.

5. Selezionare un gruppo di risorse esistente o crearne uno nuovo.

6. Selezionare il piano tariffario.

7. Scegliere uno dei criteri di accesso esistente o crearne uno nuovo. Un criterio di accesso consente di concedere le autorizzazioni per un utente, applicazione o un gruppo di sicurezza eseguire operazioni con questo insieme di credenziali.

8. Facoltativamente, scegliere un criterio di accesso avanzati per abilitare l'accesso alle funzionalità.

9. Dopo aver configurato le impostazioni, selezionare **OK**, quindi selezionare **crea**. Avviare la distribuzione di insieme di credenziali delle chiavi.

## <a name="create-a-service-principal"></a>Creare un'entità servizio

1. Accedere al proprio account Azure tramite il portale di Azure.

2. Selezionare **Azure Active Directory**, quindi **registrazioni per l'App**, quindi **Add**.

3. Specificare un nome e un URL per l'applicazione. Selezionare **App Web/API** o **Nativa** come tipo di applicazione da creare. Dopo aver impostato i valori selezionare **Crea**.

4. Selezionare **Active Directory**, quindi **registrazioni per l'App**e selezionare l'applicazione.

5. Copiare l'**ID applicazione** e archiviarlo nel codice dell'applicazione. Le applicazioni nelle applicazioni di esempio usano **ID client** quando si fa riferimento per il **ID applicazione**.

6. Per generare una chiave di autenticazione selezionare **Chiavi**.

7. Fornire una descrizione per la chiave e una durata.

8. Selezionare **Salva**.

9. Copia il **key** che diventa disponibile dopo avere scelto **salvare**.

## <a name="store-the-service-principal-inside-key-vault"></a>Store all'entità servizio all'interno di Key Vault

1. Accedere al portale per gli utenti per Azure Stack, quindi selezionare l'insieme di credenziali delle chiavi creato in precedenza e quindi selezionare il **segreto** riquadro.

2. Nel **segreto** riquadro, selezionare **genera/Importa**.

3. Nel **creare un segreto** riquadro, nell'elenco delle opzioni select **manuale**. Se è stato creato il servizio dell'entità mediante certificati, selezionare i certificati dall'elenco a discesa e quindi caricare il file.

4. Immettere **l'ID applicazione** copiato dall'entità servizio come nome per la chiave. Il nome della chiave può contenere solo caratteri alfanumerici e il carattere trattino (-).

5. Incollare il valore della chiave dall'entità nel servizio di **valore** scheda.

6. Selezionare **Service Principal** per il **tipo di contenuto**.

7. Impostare il **data di attivazione** e **data di scadenza** i valori per la chiave.

8. Selezionare **Create** per avviare la distribuzione.

Dopo aver creata la chiave privata, verranno archiviate le informazioni dell'entità servizio. È possibile selezionarlo in qualsiasi momento sotto **segreti**e visualizzare o modificare le relative proprietà. La sezione properties contiene l'identificatore del segreto, che è un URI Uniform Resource Identifier () usata dalle applicazioni esterne per accedere a questo segreto.

## <a name="next-steps"></a>Passaggi successivi

- [Usare le entità servizio](azure-stack-create-service-principals.md)
- [Gestire Key Vault in Azure Stack tramite il portale](azure-stack-key-vault-manage-portal.md)  
- [Gestire Key Vault in Azure Stack usando PowerShell](azure-stack-key-vault-manage-powershell.md)