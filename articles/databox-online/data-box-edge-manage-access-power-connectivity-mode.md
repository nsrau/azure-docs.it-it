---
title: Accesso al dispositivo di Azure al bordo casella dei dati, potenza e la modalità di connettività | Microsoft Docs
description: Viene descritto come gestire l'accesso, potenza e la modalità di connettività per il dispositivo perimetrale finestra dati di Azure che consente di trasferire i dati in Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 2426ecd9c480b0cc236295384ae04c60051f37ce
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442963"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Gestire l'accesso, potenza e la modalità di connettività per il bordo casella dati di Azure

Questo articolo descrive come gestire la modalità di accesso, potenza e la connettività per il bordo casella dati di Azure. Queste operazioni vengono eseguite tramite l'interfaccia utente Web locale o il portale di Azure.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Gestire l'accesso al dispositivo
> * Gestire la modalità di connessione
> * Gestire l'avvio/arresto


## <a name="manage-device-access"></a>Gestire l'accesso al dispositivo

L'accesso per dispositivo Edge casella dei dati viene controllato mediante l'utilizzo di una password del dispositivo. È possibile modificare la password tramite l'interfaccia utente web locale. È anche possibile reimpostare la password del dispositivo nel portale di Azure.

### <a name="change-device-password"></a>Modificare la password del dispositivo

Seguire questi passaggi nell'interfaccia utente locale per modificare la password del dispositivo.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > Modifica password**.
2. Immettere la password corrente e quindi quella nuova. La password specificata deve essere costituita da 8-16 caratteri. La password deve contenere almeno tre di questi caratteri: lettera maiuscola, lettera minuscola, numero e caratteri speciali. Confermare la nuova password.

    ![Cambia password](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Selezionare **Modifica password**.
 
### <a name="reset-device-password"></a>Reimpostazione della password del dispositivo

Il flusso di lavoro di reimpostazione non richiede che l'utente ricordi la vecchia password ed è utile in caso di smarrimento della password. Questo flusso di lavoro viene eseguito nel portale di Azure.

1. Nel portale di Azure passare a **Panoramica > Reimposta password amministratore**.

    ![Reimposta password](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Immettere la nuova password e quindi confermarla. La password specificata deve essere costituita da 8-16 caratteri. La password deve contenere almeno tre di questi caratteri: lettera maiuscola, lettera minuscola, numero e caratteri speciali. Selezionare **reimpostare**.

    ![Reimposta password](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Gestire l'accesso alle risorse

Per creare il Gateway finestra di dati finestra Edge/Data, l'IoT Hub e risorsa di archiviazione di Azure, sono necessarie autorizzazioni di collaboratore o versione successiva a livello di gruppo di risorse. È necessario anche i provider di risorse corrispondente da registrare. Per eventuali operazioni che coinvolgono le credenziali e la chiave di attivazione, sono necessarie anche le autorizzazioni per Graph API di Azure Active Directory. Questi elementi sono descritti nelle sezioni seguenti.

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>Gestire le autorizzazioni delle API di Graph di Microsoft Azure Active Directory

Quando si genera la chiave di attivazione per il dispositivo perimetrale casella dei dati o eseguire qualsiasi operazione che richiedono credenziali, sono necessarie autorizzazioni per Graph API di Azure Active Directory. Le operazioni che richiedono le credenziali possono essere:

-  Creazione di una condivisione con un account di archiviazione associato.
-  Creazione di un utente che può accedere le condivisioni nel dispositivo.

È necessario disporre di un `User` accedere nel tenant di Active Directory che è necessario essere in grado di `Read all directory objects`. È non può essere un utente Guest che dispongano delle autorizzazioni per `Read all directory objects`. Se sei un utente guest, quindi le operazioni, ad esempio la generazione di un'attivazione delle chiavi, la creazione di una condivisione in cui il dispositivo perimetrale casella dei dati, la creazione di un utente, la configurazione del bordo ruolo di calcolo, la reimpostazione della password del dispositivo avrà esito negativo.

Per altre informazioni su come fornire accesso agli utenti di Azure Active Directory Graph API, vedere [predefinito di accesso per gli amministratori, utenti e gli utenti guest](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).

### <a name="register-resource-providers"></a>Registrare i provider di risorse

Per eseguire il provisioning di una risorsa in Azure (nel modello di Azure Resource Manager), è necessario un provider di risorse che supporta la creazione di tale risorsa. Per eseguire il provisioning di una macchina virtuale, ad esempio, è necessario un provider di risorse 'Microsoft. COMPUTE' disponibile nella sottoscrizione.
 
Provider di risorse sono registrati il livello di sottoscrizione. Per impostazione predefinita, qualsiasi nuova sottoscrizione di Azure è già registrato con un elenco di provider di risorse usate. Il provider di risorse per 'Microsoft.DataBoxEdge' non è incluso in questo elenco.

Non è necessario concedere autorizzazioni di accesso a livello di sottoscrizione per gli utenti siano in grado di creare le risorse, ad esempio 'Microsoft.DataBoxEdge' in gruppi le risorse che dispongono di diritti di proprietario, purché i provider di risorse per queste risorse è già presente registrato.

Prima di provare a creare tutte le risorse, assicurarsi che il provider di risorse viene registrato nella sottoscrizione. Se non è registrato il provider di risorse, è necessario assicurarsi che l'utente che crea la nuova risorsa disponga di diritti sufficienti per registrare il provider di risorse necessarie per il livello di abbonamento. Se è ancora stato fatto anche, si verrà visualizzato l'errore seguente:

*La sottoscrizione <Subscription name> non dispone delle autorizzazioni per registrare il provider di risorse: Microsoft.DataBoxEdge.*


Per ottenere un elenco di provider di risorse registrato nella sottoscrizione corrente, eseguire il comando seguente:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Per dispositivo Edge casella dei dati, `Microsoft.DataBoxEdge` deve essere registrato. Per registrare `Microsoft.DataBoxEdge`, amministratore della sottoscrizione deve essere eseguito il comando seguente:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Per altre informazioni su come registrare un provider di risorse, vedere [risolvere gli errori di registrazione del provider di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Gestire la modalità di connessione

Oltre la modalità sempre connessi predefinita, il dispositivo può anche eseguire in modalità connessa parzialmente o completamente disconnessa. Ognuna di queste modalità viene descritta di seguito:

- **Completamente connesso** -si tratta della modalità predefinita normale in cui opera il dispositivo. In questa modalità è abilitato il caricamento di cloud e il download dei dati. È possibile usare il portale di Azure o l'interfaccia utente web locale per gestire il dispositivo.

- **Parzialmente disconnesso** : In questa modalità, il dispositivo non è possibile caricare o scaricare qualsiasi condivisione di dati possono tuttavia essere gestiti tramite il portale di Azure.

    Questa modalità viene in genere usata in caso di rete satellitare a consumo e l'obiettivo consiste nel ridurre al minimo il consumo di larghezza di banda di rete. Può comunque avvenire un uso di rete minimo per le operazioni di monitoraggio del dispositivo.

- **Disconnesso**: in questa modalità il dispositivo è completamente disconnesso dal cloud e le operazioni di caricamento e download nel cloud sono entrambe disabilitate. Il dispositivo può essere gestito solo tramite l'interfaccia utente Web locale.

    Questa modalità viene usata in genere quando si vuole portare offline il dispositivo.

Per modificare la modalità del dispositivo, completare questi passaggi:

1. Nell'interfaccia utente Web locale del dispositivo passare a **Configurazione > Impostazioni del cloud**.
2. Nell'elenco a discesa, selezionare la modalità che si desidera far funzionare il dispositivo in. È possibile selezionare **completamente connesso**, **parzialmente connessi**, e **completamente disconnessa**. Per eseguire il dispositivo in modalità parzialmente disconnessa, attivare **Azure portal management** (Gestione portale di Azure).

    ![Modalità di connessione](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Gestire l'avvio/arresto

È possibile arrestare o riavviare il dispositivo fisico usando l'interfaccia utente web locale. Si consiglia di prima del riavvio, portare offline le condivisioni nel server di dati e quindi nel dispositivo. Questa operazione riduce al minimo il rischio di danneggiamento dei dati.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > Power settings** (Impostazioni di alimentazione).
2. Selezionare **arresto** oppure **riavviare** a seconda che si desidera eseguire.

    ![Impostazioni di alimentazione](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando viene richiesta la conferma, selezionare **Sì** per procedere.

> [!NOTE]
> Se si arresta il dispositivo fisico, è necessario premere il pulsante di alimentazione sul dispositivo per attivarlo.

## <a name="next-steps"></a>Passaggi successivi

- Vedere le informazioni su come [gestire le condivisioni](data-box-edge-manage-shares.md).
