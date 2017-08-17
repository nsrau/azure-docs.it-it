---
title: Migrare i dati utente da Azure RemoteApp | Documentazione Microsoft
description: Informazioni su come migrare i dati utente in entrata e in uscita da Azure RemoteApp.
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: d7e4fbf1-cb42-4430-94a0-ed6d4676fc86
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: ba3cf4c6834279bbd7f94d666fd8abbb7ac05bf0
ms.contentlocale: it-it
ms.lasthandoff: 07/22/2017

---
# <a name="how-to-migrate-data-into-and-out-of-azure-remoteapp"></a>Come eseguire la migrazione di dati in entrata e in uscita da Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

È possibile usare numerosi metodi e strumenti diversi per trasferire i [dati utente](remoteapp-upd.md) in entrata e in uscita da Azure RemoteApp. Ecco alcuni metodi:

* Copiare e incollare i dati usando la condivisione degli Appunti
* Copiare i file e i dati in un file server
* Copiare i file in OneDrive for Business tramite un browser
* Copiare i file mediante il reindirizzamento

> [!NOTE]
> Non è possibile abilitare gli agenti di sincronizzazione di OneDrive for Business/Consumer poiché [non sono supportati](remoteapp-onedrive.md) in Azure RemoteApp.
> 
> 

## <a name="use-copy-and-paste-in-file-explorer"></a>Usare l'operazione di copia e incolla in Esplora file
[Per impostazione predefinita](remoteapp-redirection.md)l'operazione di copia e incolla mediante gli Appunti è abilitata nelle distribuzioni di RemoteApp. Gli utenti possono quindi copiare i file tra le app RemoteApp e del PC locale. Spesso, usando in modo usuale le app in RemoteApp, gli utenti hanno salvato i file negli UPD e lo spostamento di dati all'esterno di RemoteApp è semplice:

1. [Pubblicare Esplora file come app](remoteapp-publish.md) in una raccolta RemoteApp. Si noti che questa è un'attività amministrativa.
2. Indicare agli utenti di avviare l'app Esplora file pubblicata e usarla per copiare e incollare i file da e verso il proprio UPD.

## <a name="upload-files-and-data-to-a-file-server-by-using-standard-network-file-copy"></a>Caricare i file e i dati in un file server tramite la copia di file di rete standard
Spesso le organizzazioni usano file server per archiviare i dati generali. Se si conosce il nome del server o il relativo percorso, gli utenti possono esplorare la rete locale per il server e quindi copiare i file, proprio come avveniva in precedenza. Di nuovo, è possibile pubblicare Esplora file su RemoteApp e quindi condividerla con gli utenti.

> [!NOTE]
> Il file server deve trovarsi sulla rete instradabile in cui è stata distribuita RemoteApp.
> 
> 

## <a name="copy-files-to-onedrive-for-business"></a>Copiare i file in OneDrive for Business
Anche se non è possibile abilitare l'agente di sincronizzazione OneDrive for Business in RemoteApp, è tuttavia possibile copiare file dall'UPD su OneDrive for Business tramite un browser. 

1. Pubblicare Esplora file su RemoteApp e chiedere agli utenti di accedere ai file tramite questa app. 
2. È più semplice trasferire file in modalità compressa, pertanto gli utenti devono creare un file con estensione zip che contiene tutti i file da spostare in OneDrive for Business.
3. Chiedere agli utenti di accedere al portale di Office 365, quindi passare a OneDrive e caricare il file con estensione zip.

## <a name="copy-files-by-using-drive-redirection"></a>Copiare i file usando il reindirizzamento delle unità
Se è stato abilitato il [reindirizzamento delle unità](remoteapp-redirection.md), è già stata creata un'unità mappata per gli utenti. In questo caso, è possibile comprimere i file sull'unità reindirizzata e salvarli nel PC locale.

## <a name="how-administrators-can-export-data"></a>Modalità di esportazione dei dati degli amministratori

Gli amministratori per Azure RemoteApp possono esportare tutti i dischi dei profili utente (UPD) per tutte le raccolte all'interno di una sottoscrizione di Archiviazione di Azure usando il cmdlet di Azure PowerShell, Export-AzureRemoteAppUserDisk.  Non è possibile selezionare singoli UPD.  Quando viene eseguito il comando PowerShell, le dimensioni del disco rigido di ogni disco utente sarà di 50 GB e l'esportazione verrà eseguita nell'archiviazione di Azure.  I costi di archiviazione di Azure verranno generati immediatamente per questa risorsa di archiviazione.  Quando si esegue questo comando verificare che non esistano sessioni; in caso contrario l'esportazione avrà esito negativo.

Gli UPD per le distribuzioni di Azure RemoteApp aggiunte a un dominio possono solo essere riusati in una distribuzione di Servizi desktop remoto; non è possibile usare distribuzioni non aggiunte a dominio.  Se questi dischi verranno usati in una distribuzione di Servizi desktop remoto è consigliabile usare gli [script automatizzati](https://github.com/arcadiahlyy/aramigration) che esporteranno, convertiranno e importeranno gli UPD in una distribuzione di Servizi desktop remoto.


