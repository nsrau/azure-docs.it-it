---
title: Accesso, avvio/arresto e modalità di connessione per il dispositivo Microsoft Azure Data Box Gateway | Microsoft Docs
description: Descrive come gestire l'accesso, l'avvio/arresto e la modalità di connessione per il dispositivo Azure Data Box Gateway che permette di trasferire dati in Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 44343f6bc6f48a6caa056f3336af55613a1e74d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476789"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Gestire l'accesso, potenza e la modalità di connettività per il Gateway di finestra di dati di Azure

Questo articolo descrive come gestire l'accesso, l'avvio/arresto e la modalità di connessione per Azure Data Box Gateway. Queste operazioni vengono eseguite tramite l'interfaccia utente Web locale o il portale di Azure.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Gestire l'accesso al dispositivo
> * Gestire la modalità di connessione
> * Gestire l'avvio/arresto

## <a name="manage-device-access"></a>Gestire l'accesso al dispositivo

L'accesso al dispositivo Gateway di dati finestra viene controllato mediante l'utilizzo di una password del dispositivo. È possibile modificare la password tramite l'interfaccia utente web locale. È anche possibile reimpostare la password del dispositivo nel portale di Azure.

### <a name="change-device-password"></a>Modificare la password del dispositivo

Seguire questi passaggi nell'interfaccia utente locale per modificare la password del dispositivo.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > Modifica password**.
2. Immettere la password corrente e quindi quella nuova. La password specificata deve essere costituita da 8-16 caratteri. La password deve contenere almeno tre di questi caratteri: lettera maiuscola, lettera minuscola, numero e caratteri speciali. Confermare la nuova password.

    ![Cambia password](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Fare clic su **Cambia password**.
 
### <a name="reset-device-password"></a>Reimpostazione della password del dispositivo

Il flusso di lavoro di reimpostazione non richiede che l'utente ricordi la vecchia password ed è utile in caso di smarrimento della password. Questo flusso di lavoro viene eseguito nel portale di Azure.

1. Nel portale di Azure passare a **Panoramica > Reimposta password amministratore**.

    ![Reimposta password](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Immettere la nuova password e quindi confermarla. La password specificata deve essere costituita da 8-16 caratteri. La password deve contenere almeno tre di questi caratteri: lettera maiuscola, lettera minuscola, numero e caratteri speciali. Fare clic su **Reimposta**.

    ![Reimposta password](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Gestire l'accesso alle risorse

Per creare il Gateway finestra di dati finestra Edge/Data, l'IoT Hub e risorsa di archiviazione di Azure, sono necessarie autorizzazioni di collaboratore o versione successiva a livello di gruppo di risorse. È necessario anche i provider di risorse corrispondente da registrare. Per eventuali operazioni che coinvolgono le credenziali e la chiave di attivazione, sono necessarie anche le autorizzazioni per Graph API di Azure Active Directory. Questi elementi sono descritti nelle sezioni seguenti.

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>Gestire le autorizzazioni delle API di Graph di Microsoft Azure Active Directory

Quando si genera la chiave di attivazione per il dispositivo perimetrale casella dei dati o eseguire qualsiasi operazione che richiedono credenziali, sono necessarie autorizzazioni per Graph API di Azure Active Directory. Le operazioni che richiedono le credenziali possono essere:

-  Creazione di una condivisione con un account di archiviazione associato.
-  Creazione di un utente che può accedere le condivisioni nel dispositivo.

È necessario disporre di un `User` accedere nel tenant di Active Directory che è necessario essere in grado di `Read all directory objects`. È non può essere un utente Guest che dispongano delle autorizzazioni per `Read all directory objects`. Se sei un utente guest, quindi le operazioni, ad esempio la generazione di una chiave di attivazione, la creazione di una condivisione nel dispositivo Edge casella dei dati, la creazione di un utente avrà tutti esito negativo.

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

Oltre alla modalità normale predefinita, il dispositivo può anche essere eseguito in modalità parzialmente disconnessa o disconnessa. Ognuna di queste modalità viene descritta di seguito:

- **Parzialmente disconnesso**: in questa modalità il dispositivo non può caricare dati nelle condivisioni, ma può essere gestito tramite il portale di Azure.

    Questa modalità viene in genere usata in caso di rete satellitare a consumo e l'obiettivo consiste nel ridurre al minimo il consumo di larghezza di banda di rete. Può comunque avvenire un uso di rete minimo per le operazioni di monitoraggio del dispositivo.

- **Disconnesso**: in questa modalità il dispositivo è completamente disconnesso dal cloud e le operazioni di caricamento e download nel cloud sono entrambe disabilitate. Il dispositivo può essere gestito solo tramite l'interfaccia utente Web locale.

    Questa modalità viene usata in genere quando si vuole portare offline il dispositivo.

Per modificare la modalità del dispositivo, completare questi passaggi:

1. Nell'interfaccia utente Web locale del dispositivo passare a **Configurazione > Impostazioni del cloud**.
2. Disabilitare **Cloud upload and download** (Caricamento e download nel cloud).
3. Per eseguire il dispositivo in modalità parzialmente disconnessa, attivare **Azure portal management** (Gestione portale di Azure).

    ![Modalità di connessione](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Per eseguire il dispositivo in modalità parzialmente disconnessa, disabilitare **Azure portal management** (Gestione portale di Azure). A questo punto, il dispositivo può essere gestito solo tramite l'interfaccia utente Web locale.

    ![Modalità di connessione](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Gestire l'avvio/arresto

È possibile arrestare o riavviare il dispositivo virtuale tramite l'interfaccia utente Web locale. Prima di riavviare, si consiglia di portare offline le condivisioni sull'host e quindi il dispositivo. Questa operazione riduce al minimo il rischio di danneggiamento dei dati.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > Power settings** (Impostazioni di alimentazione).
2. Fare clic su **Arresta** o **Riavvia** in base all'operazione desiderata.

    ![Impostazioni di alimentazione](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando viene richiesta una conferma, fare clic su **Sì** per continuare.

> [!NOTE]
> Se si arresta il dispositivo virtuale, è necessario avviare il dispositivo tramite la gestione dell'hypervisor.
