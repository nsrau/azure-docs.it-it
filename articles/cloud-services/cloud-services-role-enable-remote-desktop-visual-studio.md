---
title: Impostare una connessione Desktop remoto per un ruolo nei servizi cloud di Azure | Microsoft Docs
description: Come configurare l'applicazione del servizio cloud di Azure per consentire le connessioni Desktop remoto
services: cloud-services
documentationcenter: na
author: kraigb
manager: ghogen
editor: ''
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: kraigb
ms.openlocfilehash: bcb7d24a302b57a1739c9c98763cd658c29b17bd
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Abilitare una connessione Desktop remoto per un ruolo in Servizi cloud di Azure con Visual Studio

> [!div class="op_single_selector"]
> * [Portale di Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Desktop remoto consente di accedere al desktop di un ruolo in esecuzione in Azure. È possibile usare una connessione Desktop remoto per risolvere e diagnosticare i problemi dell'applicazione mentre è in esecuzione.

La pubblicazione guidata fornita da Visual Studio per i servizi cloud include un'opzione per abilitare Desktop remoto durante il processo di pubblicazione, usando le credenziali fornite dall'utente. L'uso di questa opzione è indicato quando si usa Visual Studio 2017 versione 15.4 e precedenti.

Con Visual Studio 2017 versione 15.5 e successive, tuttavia, si consiglia di evitare di abilitare Desktop remoto tramite la pubblicazione guidata, a meno che non si stia lavorando solo come sviluppatore singolo. Per qualsiasi situazione in cui il progetto può essere aperto da altri sviluppatori, Desktop remoto dovrà invece essere abilitato tramite il portale di Azure, tramite PowerShell o da una definizione di versione in un flusso di lavoro di distribuzione continua. Questa indicazione è dovuta a una modifica nel modo in cui Visual Studio comunica con Desktop remoto nella macchina virtuale del servizio cloud, come illustrato in questo articolo.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Configurare Desktop remoto tramite Visual Studio 2017 versione 15.4 e precedenti

Quando si usa Visual Studio 2017 versione 15.4 e precedenti, è possibile usare l'opzione **Abilita Desktop remoto per tutti i ruoli** nella pubblicazione guidata. È comunque possibile usare la procedura guidata con Visual Studio 2017 versione 15.5 e successive, ma senza usare l'opzione Desktop remoto.

1. In Visual Studio avviare la pubblicazione guidata facendo clic con il pulsante destro del mouse sul progetto di servizio cloud in Esplora soluzioni e scegliendo **Pubblica**.

2. Accedere alla sottoscrizione di Azure se necessario e selezionare **Avanti**.

3. Nella pagina **Settings** (Impostazioni) selezionare **Abilita Desktop remoto per tutti i ruoli** e quindi selezionare il collegamento **Settings** (Impostazioni) per aprire la finestra di dialogo **Configurazione Desktop remoto**.

4. Nella parte inferiore della finestra di dialogo selezionare **Altre opzioni**. Verrà visualizzato un elenco a discesa che consente di creare o scegliere un certificato in modo che sia possibile crittografare le informazioni sulle credenziali per la connessione tramite Desktop remoto.

   > [!Note]
   > I certificati necessari per una connessione Desktop remoto sono diversi da quelli usati per altre operazioni di Azure. Il certificato di accesso remoto deve avere una chiave privata.

5. Selezionare un certificato dall'elenco o scegliere **&lt;Create &gt;** (Crea). Se si crea un nuovo certificato, specificare un nome descrittivo per il nuovo certificato quando viene richiesto e selezionare **OK**. Il nuovo certificato verrà visualizzato nell'elenco a discesa.

6. Specificare un nome utente e una password. Non è possibile usare un account esistente. Non usare "Administrator" come nome utente per il nuovo account.

7. Scegliere una data di scadenza dell'account, dopo la quale le connessioni Desktop remoto verranno bloccate.

8. Dopo aver fornito tutte le informazioni necessarie, fare clic su **OK**. Visual Studio aggiungerà le impostazioni di Desktop remoto ai file `.cscfg` e `.csdef` del progetto, inclusa la password crittografata usando il certificato scelto.

9. Completare tutti i passaggi rimanenti usando il pulsante **Avanti**, quindi selezionare **Pubblica** quando si è pronti a pubblicare il servizio cloud. Se non si è pronti a pubblicare, selezionare **Cancel** (Annulla) e rispondere **Sì** quando viene richiesto di salvare le modifiche. È possibile pubblicare il servizio cloud in un secondo momento con queste impostazioni.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Configurare Desktop remoto quando si usa Visual Studio 2017 versione 15.5 e successive

Con Visual Studio 2017 versione 15.5 e successive, è comunque possibile usare la pubblicazione guidata con un progetto di servizio cloud. È anche possibile usare l'opzione **Abilita Desktop remoto per tutti i ruoli** se si lavora solo come sviluppatore singolo.

Se si lavora nell'ambito di un team, è invece necessario abilitare Desktop remoto nel servizio cloud di Azure usando il [portale di Azure](cloud-services-role-enable-remote-desktop-new-portal.md) o [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

Questa indicazione è dovuta a una modifica nel modo in cui Visual Studio 2017 versione 15.5 e successive comunica con la macchina virtuale del servizio cloud. Quando si abilita Desktop remoto tramite la pubblicazione guidata, le versioni precedenti di Visual Studio comunicano con la macchina virtuale tramite il cosiddetto "plug-in RDP". Visual Studio 2017 versione 15.5 e successive comunica usando invece l'"estensione RDP", più sicura e flessibile. Questa modifica si allinea inoltre al fatto che anche il portale di Azure e i metodi di PowerShell per abilitare Desktop remoto usano l'estensione RDP.

Quando Visual Studio comunica con l'estensione RDP, trasmette una password in testo normale tramite SSL. I file di configurazione del progetto, tuttavia, memorizzano solo una password crittografata, che può essere decrittografata in testo normale solo con il certificato locale usato originariamente per crittografarla.

Se ogni volta si distribuisce il progetto di servizio cloud dallo stesso computer di sviluppo, il certificato locale sarà disponibile. In questo caso si potrà comunque usare l'opzione **Abilita Desktop remoto per tutti i ruoli** nella pubblicazione guidata.

Se tuttavia l'utente o altri sviluppatori intendono distribuire il progetto di servizio cloud da computer diversi, tali computer non avranno il certificato necessario per decrittografare la password. Di conseguenza verrà visualizzato il messaggio di errore seguente:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

È possibile modificare la password ogni volta che si distribuisce il servizio cloud, ma si tratta di un'operazione poco pratica per tutti gli utenti che devono accedere a Desktop remoto.

Se si condivide il progetto con un team, è quindi consigliabile deselezionare l'opzione nella pubblicazione guidata e abilitare invece Desktop remoto direttamente tramite il [portale di Azure](cloud-services-role-enable-remote-desktop-new-portal.md) oppure tramite [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Distribuzione da un server di compilazione con Visual Studio 2017 versione 15.5 e successive

È possibile distribuire un progetto di servizio cloud da un server di compilazione (ad esempio, con Visual Studio Team Services) dove Visual Studio 2017 versione 15.5 o successive è installato nell'agente di compilazione. In questo modo la distribuzione avviene dallo stesso computer in cui è disponibile il certificato di crittografia.

Per usare l'estensione RDP da Visual Studio Team Services, includere i dettagli seguenti nella definizione di compilazione:

1. Includere `/p:ForceRDPExtensionOverPlugin=true` negli argomenti di MSBuild per assicurarsi che la distribuzione funzioni con l'estensione RDP anziché con il plug-in RDP. Ad esempio: 

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Dopo le istruzioni di compilazione, aggiungere il passaggio **Distribuzione del servizio cloud di Azure** e impostarne le proprietà.

1. Dopo il passaggio di distribuzione, aggiungere un passaggio **Azure Powershell**, impostare la proprietà **Nome visualizzato** su "Distribuzione Azure: abilitare l'estensione RDP" o un altro nome appropriato e selezionare la sottoscrizione Azure appropriata.

1. Impostare **Tipo di script** su "Inline" e incollare il codice seguente nel campo **Script inline**. È anche possibile creare un file `.ps1` nel progetto con questo script, impostare **Tipo di script** su "Percorso del file di script" e impostare **Percorso script** in modo da puntare al file.

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Connettersi a un ruolo di Azure tramite Desktop remoto

Dopo aver pubblicato il servizio cloud in Azure e avere abilitato Desktop remoto, è possibile usare Esplora server di Visual Studio per accedere alla macchina virtuale del servizio cloud:

1. In Esplora server espandere il nodo **Azure** e quindi espandere il nodo di un servizio cloud e uno dei relativi ruoli per visualizzare un elenco di istanze.

2. Fare clic con il pulsante destro del mouse su un nodo dell'istanza e scegliere **Connessione tramite desktop remoto**.

3. Immettere il nome utente e la password creati in precedenza. L'accesso alla sessione remota è stato completato.

## <a name="additional-resources"></a>Risorse aggiuntive

[Come configurare i servizi cloud](cloud-services-how-to-configure-portal.md)