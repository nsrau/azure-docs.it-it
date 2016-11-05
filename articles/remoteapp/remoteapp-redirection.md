---
title: Uso del reindirizzamento in Azure RemoteApp | Microsoft Docs
description: Informazioni su come configurare e usare il reindirizzamento in RemoteApp
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo

---
# Uso del reindirizzamento in Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l'[annuncio](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Il reindirizzamento dei dispositivi consente agli utenti di interagire con applicazioni remote tramite i dispositivi collegati al proprio computer, telefono o tablet locale. Ad esempio, se Skype è accessibile tramite Azure RemoteApp, sul computer dell'utente deve essere installata una telecamera per poter usare Skype. Lo stesso vale per stampanti, altoparlanti, monitor e vari dispositivi con connessione USB.

RemoteApp usa il protocollo RDP (Remote Desktop Protocol) e RemoteFX per il reindirizzamento.

## Quale reindirizzamento è abilitato per impostazione predefinita?
Quando si usa RemoteApp, i reindirizzamenti seguenti sono abilitati per impostazione predefinita. Le informazioni tra parentesi mostrano l'impostazione RDP.

* Riproduzione di suoni sul computer locale (**Riproduci nel computer locale**). (audiomode:i:0)
* Acquisizione di audio dal computer locale e invio al computer remoto (**Registra da questo computer**). (audiocapturemode:i:1)
* Stampa su stampanti locali (redirectprinters:i:1)
* Porte COM (redirectcomports:i:1)
* Dispositivo smart card (redirectsmartcards:i:1)
* Appunti (funzionalità di copia e incolla) (redirectclipboard:i:1)
* Disattivazione caratteri smussati (allowfontsmoothing:i:1)
* Reindirizzamento di tutti i dispositivi Plug and Play supportati (devicestoredirect:s:*)

## Quali altri reindirizzamenti sono disponibili?
Due opzioni di reindirizzamento sono disabilitate per impostazione predefinita:

* Reindirizzamento delle unità (mapping di unità): le unità del computer locale diventano unità mappate nella sessione remota. In questo modo è possibile salvare o aprire file da unità locali mentre si lavora nella sessione remota.
* Reindirizzamento USB: è possibile usare i dispositivi USB collegati al computer locale all'interno della sessione remota.

## Modificare le impostazioni di reindirizzamento in RemoteApp
È possibile modificare le impostazioni di reindirizzamento del dispositivo per una raccolta usando Microsoft Azure PowerShell con SDK. Dopo aver installato il nuovo PowerShell e l'SDK, configurarlo per gestire la sottoscrizione, come descritto in [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Usare quindi un comando simile al seguente per impostare le proprietà RDP personalizzate:

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

(Si noti che *'n* viene usato come delimitatore tra le singole proprietà)

Per un elenco delle proprietà RDP configurate, eseguire il cmdlet seguente. Si noti che solo le proprietà personalizzate vengono visualizzate come risultati di output, non le proprietà predefinite:

    Get-AzureRemoteAppCollection -CollectionName <collection name>

Quando si impostano proprietà personalizzate, è necessario specificare tutte le proprietà personalizzate ogni volta. In caso contrario, l'impostazione viene nuovamente disabilitata.

### Esempi comuni
Per abilitare il reindirizzamento delle unità, usare il cmdlet seguente:

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

Usare questo cmdlet per abilitare sia il reindirizzamento delle unità che il reindirizzamento USB:

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

Usare questo cmdlet per disabilitare la condivisione degli Appunti:

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

> [!IMPORTANT]
> Assicurarsi di disconnettere completamente tutti gli utenti nella raccolta (non solo scollegarli) prima di verificare la modifica. Per assicurarsi che gli utenti siano disconnessi completamente, andare alla scheda **Sessioni** nella raccolta nel portale di Azure e disconnettere tutti gli utenti scollegati o collegati. Talvolta è necessario attendere alcuni secondi perché le unità locali vengano mostrate in Esplora risorse all'interno della sessione.
> 
> 

## Modificare le impostazioni di reindirizzamento USB nel client Windows
Se si desidera usare il reindirizzamento USB su un computer che si connette a RemoteApp, è necessario eseguire 2 azioni. 1 - L'amministratore deve abilitare il reindirizzamento USB a livello di raccolta tramite Azure PowerShell. 2 - Su ogni dispositivo su cui si desidera usare il reindirizzamento USB, è necessario abilitare un criterio di gruppo che lo consente. È necessario eseguire questo passaggio per ogni utente che desidera usare il reindirizzamento USB.

> [!NOTE]
> Il reindirizzamento USB con Azure RemoteApp è supportato solo per i computer Windows.
> 
> 

### Abilitare il reindirizzamento USB per la raccolta di RemoteApp
Per abilitare il reindirizzamento USB a livello di raccolta, utilizzare il cmdlet seguente:

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### Abilitare il reindirizzamento USB per il computer client
Per configurare le impostazioni di reindirizzamento USB sul computer:

1. Aprire l'Editor Criteri di gruppo locali (GPEDIT.MSC) (eseguire gpedit.msc da un prompt dei comandi).
2. Aprire **Configurazione computer\\Criteri\\Modelli amministrativi\\Componenti di Windows\\Servizi Desktop remoto\\Client di connessione desktop remoto\\Reindirizzamento dispositivo USB RemoteFX**.
3. Fare doppio clic su **Consenti il reindirizzamento RDP di altri dispositivi USB RemoteFX supportati da questo computer**.
4. Selezionare **Attivato** e quindi selezionare **Amministratori e utenti nei diritti di accesso del reindirizzamento USB RemoteFX**.
5. Aprire un prompt dei comandi con autorizzazioni amministrative ed eseguire il comando seguente:
   
        gpupdate /force
6. Riavviare il computer.

È anche possibile usare lo strumento Gestione criteri di gruppo per creare e applicare i criteri di reindirizzamento USB per tutti i computer nel dominio:

1. Accedere al controller di dominio come amministratore di dominio.
2. Aprire Console Gestione criteri di gruppo (fare clic su **Start > Strumenti di amministrazione > Gestione criteri di gruppo**).
3. Passare al dominio o unità organizzativa per cui si desidera creare il criterio.
4. Fare clic con il pulsante destro del mouse su **Criterio dominio predefinito** e quindi fare clic su **Modifica**.
5. Aprire **Configurazione computer\\Criteri\\Modelli amministrativi\\Componenti di Windows\\Servizi Desktop remoto\\Client di connessione desktop remoto\\Reindirizzamento dispositivo USB RemoteFX**.
6. Fare doppio clic su **Consenti il reindirizzamento RDP di altri dispositivi USB RemoteFX supportati da questo computer**.
7. Selezionare **Attivato** e quindi selezionare **Amministratori e utenti nei diritti di accesso del reindirizzamento USB RemoteFX**.
8. Fare clic su **OK**.

<!---HONumber=AcomDC_0817_2016-->