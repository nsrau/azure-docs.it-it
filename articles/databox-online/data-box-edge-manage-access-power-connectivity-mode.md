---
title: Modalità di accesso, alimentazione e connettività del dispositivo Azure Data Box Edge | Microsoft Docs
description: Viene descritto come gestire l'accesso, l'alimentazione e la modalità di connettività per il dispositivo Azure Data Box Edge che consente di trasferire i dati in Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 7ce4b9dda853e63e427757317abc2f7c878ba3a4
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253179"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Gestire l'accesso, l'alimentazione e la modalità di connettività per il Azure Data Box Edge

Questo articolo descrive come gestire l'accesso, la potenza e la modalità di connettività per la Azure Data Box Edge. Queste operazioni vengono eseguite tramite l'interfaccia utente Web locale o il portale di Azure.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Gestire l'accesso al dispositivo
> * Gestire la modalità di connessione
> * Gestire l'avvio/arresto


## <a name="manage-device-access"></a>Gestire l'accesso al dispositivo

L'accesso al dispositivo Data Box Edge è controllato dall'uso di una password del dispositivo. È possibile modificare la password tramite l'interfaccia utente Web locale. È anche possibile reimpostare la password del dispositivo nel portale di Azure.

### <a name="change-device-password"></a>Modificare la password del dispositivo

Per modificare la password del dispositivo, attenersi alla seguente procedura nell'interfaccia utente locale.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > Modifica password**.
2. Immettere la password corrente e quindi quella nuova. La password specificata deve essere costituita da 8-16 caratteri. La password deve contenere almeno tre di questi caratteri: lettera maiuscola, lettera minuscola, numero e caratteri speciali. Confermare la nuova password.

    ![Cambia password](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Selezionare **Modifica password**.
 
### <a name="reset-device-password"></a>Reimposta la password del dispositivo

Il flusso di lavoro di reimpostazione non richiede che l'utente ricordi la vecchia password ed è utile in caso di smarrimento della password. Questo flusso di lavoro viene eseguito nel portale di Azure.

1. Nel portale di Azure passare a **Panoramica > Reimposta password amministratore**.

    ![Reimposta password](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Immettere la nuova password e quindi confermarla. La password specificata deve essere costituita da 8-16 caratteri. La password deve contenere almeno tre di questi caratteri: lettera maiuscola, lettera minuscola, numero e caratteri speciali. Selezionare **Reimposta**.

    ![Reimposta password](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Gestire l'accesso alle risorse

Per creare il Data Box Edge/Data Box Gateway, l'hub Internet e la risorsa di archiviazione di Azure, è necessario disporre delle autorizzazioni come collaboratore o superiore a livello di gruppo di risorse. È anche necessario registrare i provider di risorse corrispondenti. Per tutte le operazioni che coinvolgono la chiave di attivazione e le credenziali, sono necessarie anche le autorizzazioni per Azure Active Directory API Graph. Questi elementi sono descritti nelle sezioni seguenti.

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>Gestisci autorizzazioni API Microsoft Azure Active Directory Graph

Quando si genera la chiave di attivazione per il dispositivo Data Box Edge o si eseguono operazioni che richiedono credenziali, è necessario disporre delle autorizzazioni per Azure Active Directory API Graph. Le operazioni che richiedono credenziali potrebbero essere:

-  Creazione di una condivisione con un account di archiviazione associato.
-  Creazione di un utente che può accedere alle condivisioni nel dispositivo.

È necessario avere `User` accesso al tenant di Active Directory, perché è necessario `Read all directory objects`poterlo. Non è possibile essere un utente guest perché non dispongono delle autorizzazioni `Read all directory objects`per. Se si è un Guest, le operazioni, ad esempio la generazione di una chiave di attivazione, la creazione di una condivisione sul dispositivo Data Box Edge, la creazione di un utente, la configurazione del ruolo di calcolo Edge e la reimpostazione della password del dispositivo avranno esito negativo.

Per ulteriori informazioni su come fornire l'accesso agli utenti per Azure Active Directory API Graph, vedere [l'accesso predefinito per amministratori, utenti e utenti Guest](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).

### <a name="register-resource-providers"></a>Registrare i provider di risorse

Per eseguire il provisioning di una risorsa in Azure (nel modello di Azure Resource Manager), è necessario un provider di risorse che supporti la creazione di tale risorsa. Per eseguire il provisioning di una macchina virtuale, ad esempio, è necessario che nella sottoscrizione sia disponibile un provider di risorse "Microsoft. Compute".
 
I provider di risorse sono registrati a livello di sottoscrizione. Per impostazione predefinita, qualsiasi nuova sottoscrizione di Azure è già registrata con un elenco di provider di risorse usate comunemente. Il provider di risorse per ' Microsoft. DataBoxEdge ' non è incluso nell'elenco.

Non è necessario concedere le autorizzazioni di accesso al livello di sottoscrizione affinché gli utenti siano in grado di creare risorse come ' Microsoft. DataBoxEdge ' all'interno dei gruppi di risorse di cui dispongono dei diritti di proprietario, purché i provider di risorse per queste risorse siano già presenti registrato.

Prima di provare a creare una risorsa, verificare che il provider di risorse sia registrato nella sottoscrizione. Se il provider di risorse non è registrato, è necessario assicurarsi che l'utente che crea la nuova risorsa disponga di diritti sufficienti per registrare il provider di risorse richiesto a livello di sottoscrizione. Se questa operazione non è stata eseguita correttamente, verrà visualizzato l'errore seguente:

*Il nome \<della sottoscrizione di sottoscrizione > non dispone delle autorizzazioni necessarie per registrare i provider di risorse: Microsoft.DataBoxEdge.*


Per ottenere un elenco dei provider di risorse registrati nella sottoscrizione corrente, eseguire il comando seguente:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Per data box Edge dispositivo, `Microsoft.DataBoxEdge` deve essere registrato. Per eseguire `Microsoft.DataBoxEdge`la registrazione, l'amministratore della sottoscrizione deve eseguire il comando seguente:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Per altre informazioni su come registrare un provider di risorse, vedere [risolvere gli errori per la registrazione del provider di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Gestire la modalità di connessione

Oltre alla modalità predefinita con connessione completa, il dispositivo può essere eseguito anche in modalità parzialmente connessa o completamente disconnessa. Ognuna di queste modalità viene descritta di seguito:

- **Completamente connesso** : questa è la normale modalità predefinita in cui il dispositivo funziona. In questa modalità sono abilitati sia il caricamento del cloud che il download dei dati. Per gestire il dispositivo, è possibile usare il portale di Azure o l'interfaccia utente Web locale.

- **Parzialmente disconnesso** : in questa modalità, il dispositivo non è in grado di caricare o scaricare dati di condivisione, tuttavia può essere gestito tramite il portale di Azure.

    Questa modalità viene in genere usata in caso di rete satellitare a consumo e l'obiettivo consiste nel ridurre al minimo il consumo di larghezza di banda di rete. Può comunque avvenire un uso di rete minimo per le operazioni di monitoraggio del dispositivo.

- **Disconnesso**: in questa modalità il dispositivo è completamente disconnesso dal cloud e le operazioni di caricamento e download nel cloud sono entrambe disabilitate. Il dispositivo può essere gestito solo tramite l'interfaccia utente Web locale.

    Questa modalità viene usata in genere quando si vuole portare offline il dispositivo.

Per modificare la modalità del dispositivo, completare questi passaggi:

1. Nell'interfaccia utente Web locale del dispositivo passare a **Configurazione > Impostazioni del cloud**.
2. Nell'elenco a discesa selezionare la modalità in cui si vuole usare il dispositivo. È possibile scegliere tra **completamente connesso**, **parzialmente connesso**e **completamente disconnesso**. Per eseguire il dispositivo in modalità parzialmente disconnessa, attivare **Azure portal management** (Gestione portale di Azure).

    ![Modalità di connessione](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Gestire l'avvio/arresto

È possibile arrestare o riavviare il dispositivo fisico usando l'interfaccia utente Web locale. Prima di riavviare, è consigliabile portare offline le condivisioni sul server dati e quindi sul dispositivo. Questa operazione riduce al minimo il rischio di danneggiamento dei dati.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > Power settings** (Impostazioni di alimentazione).
2. Selezionare **Arresta** o **Riavvia** a seconda delle operazioni che si intende eseguire.

    ![Impostazioni di alimentazione](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando viene richiesta la conferma, selezionare **Sì** per continuare.

> [!NOTE]
> Se si arresta il dispositivo fisico, sarà necessario premere il pulsante di alimentazione sul dispositivo per accenderlo.

## <a name="next-steps"></a>Passaggi successivi

- Vedere le informazioni su come [gestire le condivisioni](data-box-edge-manage-shares.md).
